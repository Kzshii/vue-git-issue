<template>
  <div class="container">
    <h1>{{ title }}</h1>
      <p class="lead">
        {{ msg }}
      </p>
      <div v-if="!isValid.writted" class="alert alert-danger">
        <!-- {{ response.message }} -->
        OMG, don't forget the {{ isValid.missedInput }}
      </div>
      <div v-if="response.error" class="alert alert-danger">
        <!-- {{ response.message }} -->
        ERROR: {{ response.message }}
      </div>
    <div class="row">
      <div class="col">
        <div class="form-group">
          <input
            v-model="username"
            type="text"
            class="form-control"
            placeholder="Git User"
          >
        </div>
      </div>
      <div class="col">
        <div class="form-group">
          <input
            v-model="repository"
            type="text"
            class="form-control"
            placeholder="Git Repo"
          >
        </div>
      </div>
      <div class="col-3">
        <div class="form-group">
          <button @click.prevent.stop="getIssues" class="btn btn-success">Search</button>
          <button @click.prevent.stop="reset" class="btn btn-danger">Clear</button>
        </div>
      </div>
    </div>

    <br><hr><br>

    <template v-if="selectedIssue.id">
      <h2>Issue: {{ selectedIssue.number}} - {{ selectedIssue.title }}</h2>
      <p>{{ selectedIssue.body }}</p>
      <div>Created at: {{ selectedIssue.created_at.slice(0, 10) }}</div>
      <div>Last Update: {{ selectedIssue.updated_at.slice(0, 10) }}</div>
      <br><hr><br>
      <button @click.prevent.stop="back" class="btn btn-danger">Back</button>
    </template>

    <table v-if="!selectedIssue.id" class="table table-sm table-bordered">
      <thead>
      <tr>
        <th width="100">Number</th>
        <th>Title</th>
      </tr>
      </thead>
      
      <tbody>
      <tr v-if="loader.getIssues">
        <td class="text-center" colspan="2">Loading</td>
      </tr>
      <tr v-for="issue in issues" :key="issue.number">
        <td><a href="" @click.prevent.stop="getIssueInfo(issue.number)">{{ issue.number }}</a></td>
        <td>{{ issue.title }}</td>
      </tr>
      </tbody>

      <tfoot>
      <tr v-if="issues.length === 0">
        <td class="text-center" colspan="2">Well...nothing yet</td>
      </tr>
      </tfoot>

    </table>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'Dashboard',
  data() {
    return {
      title: 'Vue.js + Github',
      username: '',
      repository: '',
      isValid: {
        writted: true,
        missedInput: '',
      },
      response: {
        error: false,
        message: '',
      },
      issues: [''],
      loader: {
        getIssues: false,
      },
      selectedIssue: [],
    };
  },
  props: {
    msg: String,
  },
  methods: {
    reset() {
      this.username = '',
      this.repository = ''
    },
    getIssues() {
      const url = `https://api.github.com/repos/${this.username}/${this.repository}/issues`;
      if(this.repository && this.username) {
        this.loader.getIssues = true;
        axios.get(url)
          .then(response => {
            this.issues = response.data;
          })
          .catch(error => {
            this.response.error = true;
            this.response.message = 'Not Found';
            console.log(error)
          })
          .finally(() => {
            this.loader.getIssues = false;
          });
      } 
      else if(!this.repository && this.username) {
        this.isValid.writted = false;
        this.isValid.missedInput = 'repository'
      }
      else if(!this.username && this.repository) {
        this.isValid.writted = false;
        this.isValid.missedInput = 'repository'
      } else {
        this.isValid.writted = false;
        this.isValid.missedInput = 'username AND REPOSITORY!!!!'
      }
    },
    getIssueInfo(issueId) {
      const url = `https://api.github.com/repos/${this.username}/${this.repository}/issues/${issueId}`;
      axios.get(url)
        .then(response => {
          this.selectedIssue = response.data;
        })
        .catch(error => {
          this.response.error = true;
          this.response.message = 'Not Found';
          console.log(error)
        })
    },
    back() {
      this.selectedIssue = [];
    }
  },
};
</script>

