<template>
    <div v-if="admin">
        <div class="container page" >
            <div class="row" >
            <div class="col-xs-12 col-md-3">
                <div class="sidebar">
                    <p>Menu</p>
                <row></row>
                    <p>Dashboard</p>
                <row></row>
                    <p>Listings</p>
                    <p>Users</p>
                    <p>Messages</p>
                    <p>Reviews</p>
                    <p>Transactions</p>
                <row></row>
                    <p>Theme</p>
                    <p>Settings</p>
                    <a href="/admin/categories">Categories</a>

                </div>
            </div>
            <b-container class="col-xs-12 col-md-9">
      <img
          v-if="preview"
          class="img-fluid"
          style="width: 400px; border-radius: 10px; box-shadow: 0 1rem 1rem rgba(0,0,0,.7);"
          :src="preview"
          alt>
        <img
          v-else
          class="img-fluid"
          style="width: 400px; border-radius: 10px; box-shadow: 0 1rem 1rem rgba(0,0,0,.7);"
          src="@/static/placeholder.png">
        <form @submit.prevent="submitCategorie">
          <div class="form-group">
            <label for>Image</label>
            <input type="file" name="img" @change="onFileChange">
          </div>
          <div class="form-group">
            <label for>Name</label>
            <input type="text" v-model="categorie.name" class="form-control">
          </div>
          <div class="form-group">
            <label for>Slug</label>
            <input type="text" v-model="categorie.slug" class="form-control">
          </div>
          <div class="form-group">
            <label for>Description</label>
            <textarea  class="form-control" v-model="categorie.description" rows="8"></textarea>
          </div>
          <button type="submit" class="btn btn-primary">Submit</button>
        </form>
        <div style=height:400px;>
          <h2>Categories</h2>
            <vue-nestable v-model="categories">
              <vue-nestable-handle
                slot-scope="{ item }"
                :item="item">
                {{item.name}}
              </vue-nestable-handle>
            </vue-nestable>
          </div>
            </b-container>
            </div>
        </div>
    </div>
</template>

<script>
import { VueNestable, VueNestableHandle } from 'vue-nestable'

export default {
    middleware: ['auth'],
    components: {
      VueNestable,
      VueNestableHandle
    },
    computed: {
        state () {
            return JSON.stringify(this.$auth.$state, undefined, 2)
        },
        admin() {
            return this.$auth.user.admin === true;
        },
    },
    data(){
        return{
            categories:[],
            value: false,
            nameRules: [
                    v => !! v || 'Any contents is required'
                ],
            categorie: [],
      preview: ""
    };
  },
  head() {
    return {
      title: "Listings list"
    };
  },
  async asyncData({ $axios, params }) {
    try {
      let categories = await $axios.$get(`/server/categorieslist/`);
      return { categories };
    } catch (e) {
      return {  categories: [], };
    }
  },
  methods: {
    onFileChange(e) {
      let files = e.target.files || e.dataTransfer.files;
      if (!files.length) {
        return;
      }
      this.categorie.img = files[0]
      this.createImage(files[0]);
    },
    createImage(file) {
      let reader = new FileReader();
      let vm = this;
      reader.onload = e => {
        vm.preview = e.target.result;
      };
      reader.readAsDataURL(file);
    },
    async submitCategorie() {

      this.error = null

      const config = {
        headers: { "content-type": "multipart/form-data" }
      };
      let formData = new FormData();
      for (let data in this.categorie) {
        formData.append(data, this.categorie[data]);
      }
      try {
        let response = await this.$axios.$post("/server/categorieslist/", formData, config);
        this.$router.push("/");
      } catch (e)  {
        this.error = e.response.data
      }
    }
  }
};
</script>
