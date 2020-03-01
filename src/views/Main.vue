<template>
  <div class="container">
    <h1>
      <font-awesome-icon :icon="['fab', 'github-alt']" />
      Repositórios
    </h1>
    <form @submit.prevent="handleSubmit">
      <input 
        type="text"
        placeholder="Adicionar repositório"
        v-model="newRepo"
      />
      <button type="submit">
        <font-awesome-icon v-if="loading" :icon="['fas', 'spinner']" spin />
        <font-awesome-icon class="fa-plus-write" v-else :icon="['fas', 'plus']" />
      </button>
    </form>

    <ul class="list">
      <li v-for="repository in repositories" :key="repository.name">
        <span>{{repository.name}}</span>
        <router-link 
          :to="{ name: 'Repository', params: { repositoryId: repository.name } }"
        >
          Detalhes
        </router-link>      
      </li>
    </ul>
  </div>
</template>

<script>

import api from '../services/api'

export default {
  data () {
    return {
      newRepo: '',
      repositories: [],
      loading: false
    }
  },
  created () {
    const repositories = localStorage.getItem('repositories')
    this.repositories = repositories ? JSON.parse(repositories) : []
  },
  watch: {
    repositories (repositories) {
      localStorage.setItem('repositories', JSON.stringify(repositories))
    }
  }, 
  methods: {
    async handleSubmit (e) {
      console.log(e)

      this.loading = true

      // Checks if that repository already exists
      const exists = this.repositories.filter(
        repository => 
        repository.name.toLowerCase() === this.newRepo.toLowerCase()
      )
        
      if (!exists.length && this.newRepo.length > 3) {
        try {
          const response = await api.get(`/repos/${this.newRepo}`)

          if (response) {
            const data = {
              name: response.data.full_name
            }          
            this.repositories = [...this.repositories, data]
          }        

        } catch (err) {
          console.log(err)
        } 
      }

      this.newRepo = ''        
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

  form {
    margin-top: 30px;
    display: flex;
    flex-direction: row;
  }

  form input {
    flex: 1;
    border: 1px solid #eee;
    padding: 10px 15px;
    border-radius: 4px;
    font-size: 16px;
  }

  form button[type=submit] {
    width: 44px;
    color: #ffffff;
    background: #7159c1;
    border: 0;
    padding: 0 15px;
    margin-left: 10px;
    border-radius: 4px;

    display: flex;
    justify-content: center;
    align-items: center;
  }

  form button[type=submit]:disabled {
    cursor: not-allowed;
    opacity: 0.6;
  }

  .list {
    list-style: none;
    margin-top: 30px;
  }

  .list li {
    padding: 15px 0;
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    align-items: center;
  }

  .list li + li {
    border-top: 1px solid #eee;
  }

  .list li a {
    color: #7159c1;
    text-decoration: none;
  }

</style>