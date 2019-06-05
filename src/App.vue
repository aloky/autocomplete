<template>
  <v-app>
    <v-toolbar class="mb-5">
      <v-autocomplete
        v-model="select"
        :loading="loading"
        :items="items"
        :search-input.sync="search"
        cache-items
        class="mx-3"
        autofocus
        flat
        hide-no-data
        hide-details
        label="Search"
        solo-inverted
        @change="selected"
      />
    </v-toolbar>
    <v-content>
      <v-flex xs12 sm6 offset-sm3>
        <v-card v-if="item" flat>
          <v-img
            v-if="item.image"
            class="white--text"
            height="200px"
            :src="item.image.original"
          >
            <v-container fill-height fluid>
              <v-layout fill-height>
                <v-flex xs12 align-end flexbox>
                  <span class="headline">{{ item.name }}</span>
                </v-flex>
              </v-layout>
            </v-container>
          </v-img>
          <v-card-title>
            <div>
              <span class="grey--text">{{ item.status }}</span><br>
              <div v-html="item.summary"></div>
            </div>
          </v-card-title>
          <v-card-actions>
            <v-btn
              :href="item.url"
              target="_blank"
              flat
            >
              Film
            </v-btn>
            <v-btn
              :href="item.officialSite"
              target="_blank"
              flat
              color="success"
            >
              Official Site
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-flex>
    </v-content>
  </v-app>
</template>

<script>
import axios from 'axios'

export default {
  data () {
    return {
      loading: false,
      items: [],
      result: [],
      item: null,
      search: null,
      select: null
    }
  },
  watch: {
    search (val) {
      val && val !== this.select && this.querySelections(val)
    }
  },
  methods: {
    selected () {
      this.item = this.result.find(({ name }) => this.select === name)
    },
    querySelections (val) {
      this.loading = true
      axios.get(`http://api.tvmaze.com/search/shows?q=${val}`)
        .then(({ data }) => {
          data.forEach(({ show }) => {
            this.result.push(show)
            if (!this.items.includes(show.name)) {
              this.items.push(show.name)
            }
          })
          this.loading = false
        })
    }
  }
}
</script>
