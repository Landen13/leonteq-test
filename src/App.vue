<template>
  <div id="app">
    <div class="filters">
      <h2 class="filters__title">Try some filters</h2>
      <div class="filters__fields">
        <template v-for="(value, key) in filters.fields">
          <div class="form-item" :key="key">
            <div class="form-item__label">
              <label :for="key">{{ startCase(key) }}</label>
            </div>
            <div class="form-item__control">
              <input type="text" v-model="filters.fields[key]" :id="key">
            </div>
          </div>
        </template>
      </div>
      <div class="filters__mode">
        <label>
          <input type="radio" value="every" name="filters-mode" v-model="filters.mode">
          AND (&&)
        </label>
        <br>
        <label>
          <input type="radio" value="some" name="filters-mode" v-model="filters.mode">
          OR (||)
        </label>
      </div>
    </div>

    <hr>

    <pager
      :total-items="booksFiltered.length"
      :per-page="pagination.perPage"
      :initial-page="pagination.currentPage"
      @goto="pagination.currentPage = $event"
    />

    <table class="table-books" v-if="booksPageData.length > 0">
      <thead>
        <tr>
          <th>Title</th>
          <th>Description</th>
          <th>Publish date</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="book in booksPageData" :key="book.id">
          <td>{{ book.title }}</td>
          <td>{{ book.description }}</td>
          <td>{{ book.publishDate }}</td>
        </tr>
      </tbody>
    </table>
    <p v-else>Nothing found. Please, try another search.</p>

    <pager
      :total-items="booksFiltered.length"
      :per-page="pagination.perPage"
      :initial-page="pagination.currentPage"
      @goto="pagination.currentPage = $event"
    />
  </div>
</template>

<script>
import axios from 'axios'
import {
  pick,
  startCase
} from 'lodash'

import Pager from '@/components/Pager'

const api = axios.create({
  baseURL: 'https://fakerestapi.azurewebsites.net/api/v1'
})

export default {
  name: 'App',
  components: {
    Pager
  },
  data () {
    return {
      books: [],
      filters: {
        fields: {
          title: null,
          publishDate: null,
          description: null
        },
        mode: 'every'
      },
      pagination: {
        perPage: 20,
        currentPage: 0
      }
    }
  },
  computed: {
    booksFiltered () {
      if (Object.values(this.filters.fields).every(v => v === null)) return this.books

      return this.books.filter(book => {
        return Object.keys(this.filters.fields)
          .filter(field => {
            const filterValue = this.filters.fields[field]
            return typeof filterValue === 'string' && filterValue.length > 0
          })[this.filters.mode](field => book[field].indexOf(this.filters.fields[field]) > -1)
      })
    },
    booksPageData () {
      const { currentPage, perPage } = this.pagination
      return this.booksFiltered.slice(currentPage * perPage, currentPage * perPage + perPage)
    }
  },
  watch: {
    booksFiltered (val, prevVal) {
      if (JSON.stringify(val) !== JSON.stringify(prevVal)) this.pagination.currentPage = 0
    }
  },
  async created () {
    const { data } = await api.get('books')
    this.books = Object.freeze(data.map(book => ({
      ...pick(book, ['id', 'title', 'description']),
      publishDate: new Date(book.publishDate).toLocaleString()
    })))
  },
  methods: {
    startCase
  }
}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}

.table-books {
  border-collapse: collapse;
  th, td {
    padding: 5px 15px;
  }
  thead {
    th {
      &:nth-child(2) {
        text-align: left;
      }
    }
  }
  tbody {
    tr:nth-child(even) td {background: #eaeaea;}
    tr:nth-child(odd) td {background: #e0e0e0;}
    td {
      & + td {border-left: 2px solid #ccc;}
      &:first-child,
      &:last-child {
        white-space: nowrap;
        width: 1%;
      }
    }
  }
}

.filters {
  padding: 20px;
  background-color: #f2f2f2;

  &__title {
    margin: 0;
    font-size: 24px;
    line-height: 30px;
    & + * {margin-top: 10px;}
  }

  &__fields {
    display: flex;

    .form-item {
      margin: 0 5px;
      &:first-child { margin-left: 0; }
      &:last-child { margin-right: 0; }
    }

    & + * {margin-top: 10px;}
  }
}
</style>

<style lang="scss">
.form-item {
  &__label {
    padding: 0 15px;

  }
  &__control {
    input[type="text"] {
      height: 30px;
      border-radius: 6px;
      font-size: 20px;
      line-height: 24px;
      padding: 3px 13px;
      margin: 0;
      appearance: none;
      outline: none;
      border: 2px solid #ccc;
    }
  }
}
</style>
