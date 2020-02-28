<template>
  <div class="loading" v-if="loading">
    Carregando
  </div>
  <div class="container" v-else>
    <header class="owner">
      <router-link to="/">
        Voltar aos repositórios
      </router-link>
      <img 
        :src="repository.owner.avatar_url" 
        :alt="repository.owner.login"
      />
      <h1>{{repository.name}}</h1>
      <p>{{repository.description}}</p>      
    </header>
    <ul class="issue-list">
      <li v-for="issue in issues" :key="String(issue.id)">
        <img
          :src="issue.user.avatar_url"
          :alt="issue.user.login"
        />
        <div>
          <strong>
            <a :href="issue.html_url">{{issue.title}}</a>
            <span 
              v-for="label in issue.labels"
              :key="String(label.id)"
            >{{label.name}}</span>
          </strong>
          <p>{{issue.user.login}}</p>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>

import api from '../services/api'

export default {
  props: ['repositoryId'],
  data () {
    return {
      repository: {},
      issues: [],
      loading: true
    }
  },
  created () {
    this.fetchRepositoryAndIssues()
  },
  methods: {
    async fetchRepositoryAndIssues () {
      if (this.repositoryId) {

        const [repository, issues] = await Promise.all([

          api.get(`/repos/${this.repositoryId}`),
          api.get(`/repos/${this.repositoryId}/issues`, {
            params: {
              state: 'open',
              per_page: 5
            }
          })
        ])

        this.repository = repository ? repository.data : {}
        this.issues = issues ? issues.data : []        
      }
      this.loading = false
    }
  }
}

</script>

<style scoped>

  .container {
    max-width: 700px;
    background: #fff;
    border-radius: 4px;
    box-shadow: 0 0 20px rgba(0, 0, 0, 0.1);
    padding: 30px;
    margin: 80px auto;
  }

  .container h1 {
    font-size: 20px;
    display: flex;
    flex-direction: row;
    align-items: center;
  }

  .container h1 svg {
    margin-right: 10px
  }

  .loading {
    color: #fff;
    font-size: 30px;
    font-weight: bold;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
  }

  .owner {
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .owner a {
    color: #7159c1;
    font-size: 16px;
    text-decoration: none;
  }

  .owner img {
    width: 120px;
    border-radius: 50%;
    margin-top: 20px;
  }

  .owner h1 {
    font-size: 24px;
    margin-top: 10px;
  }

  .owner p {
    margin-top: 5px;
    font-size: 14px;
    color: #666;
    line-height: 1.4;
    text-align: center;
    max-width: 400px;
  }

  .issue-list {
    padding-top: 30px;
    margin-top: 30px;
    border-top: 1px solid #eee;
    list-style: none;
  }

  .issue-list li {
    display: flex;
    padding: 15px 10px;
    border: 1px solid #eee;
    border-radius: 4px;
  }

  .issue-list li + li {
    margin-top: 10px;
  }

  .issue-list li img {
    width: 36px;
    height: 36px;
    border-radius: 50%;
    border: 2px solid #eee;
  }

  .issue-list li div {
    flex: 1;
    margin-left: 15px;
  }

  .issue-list li div p {
    margin-top: 5px;
    font-size: 12px;
    color: #999;
  }  

  .issue-list li div strong {
    font-size: 16px;
  }

  .issue-list li div strong a {
    text-decoration: none;
    color: #333;
  }

  .issue-list li div strong a:hover {
    color: #7159c1;
  }

  .issue-list li div strong span {
    background: #eee;
    color: #333;
    border-radius: 2px;
    font-size: 12px;
    font-weight: 600;
    height: 20px;
    padding: 3px 4px;
    margin-left: 10px;
  }

</style>