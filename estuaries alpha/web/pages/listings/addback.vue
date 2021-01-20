<template>
  <main class="container my-5">
    <div class="row">
      <div class="col-md-12 mb-4 text-center my-3">
        <form @submit.prevent="submitListings">
          <div class="form-group">
            <h1>Categories</h1>
            <template v-model="listings.categories" class="form-control">
              <div class="tree" v-for="categorie in categories">
                <ul v-if="categorie.children.length > 0"> 
                  <span  class="name" @click="visible = !visible"><i class="ni ni-bullet-list-67" ></i>{{ categorie.name }}</span>
                    <ul v-if="visible" v-model="listings.categories">
                      <p v-for="child in categorie.children">    
                        <base-radio :name="child.id" valueclass="mb-1" v-model="listings.categories">
                          {{ child.name }}
                        </base-radio>
                      </p>
                    </ul>
                </ul>
                <ul v-else>
                  <base-radio :name="categorie.id" valueclass="mb-1" v-model="listings.categories">
                    <span class="name">{{ categorie.name }}</span>
                  </base-radio>
                </ul>
              </div>
            </template>
          <div class="form-group">
            <label for>Name</label>
            <input type="text" class="form-control" v-model="listings.name">
          </div>
          <div class="form-group">
            <label for>Price</label>
            <input type="numer" class="form-control" v-model="listings.price">
          </div>
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
          <br></br>
          <div class="form-group">
            <label for>Image</label>
            <input type="file" name="image" @change="onFileChange">
          </div>
          <div class="form-group">
            <label for>Description</label>
            <textarea v-model="listings.description" class="form-control" rows="8"></textarea>
          </div>
          <button type="submit" class="btn btn-primary">Submit</button>
      </div>
              </form>
      </div>
      <div class="col-md-6 mb-4">

      </div>
      <div class="col-md-4">
        <form @submit.prevent="submitListings">
          <div class="form-group">








          </div>


        </form>
      </div>
    </div>
  </main>
</template>


<script>
export default {
  head() {
    return {
      title: "Add Listings"
    };
  },
  name: "node",
  middleware: ['auth'],
    computed: {
        state () {
            return JSON.stringify(this.$auth.$state, undefined, 2)
        },
        errorMessage () {
            const {error} = this
            if (!error || typeof error === 'string') {
                return error
            }
            let msg = ''
            if (error.message) {
                msg += error.message
            }
            if (error.errors) {
                msg += `(${JSON.stringify(error.errors)
                    .replace(/[{}"[\]]/g, '')
                    .replace(/:/g, ': ')
                    .replace(/,/g, ' ')})`
            }
            return msg
        }
    },
    data(){
        return{
            selected: [],
            categoriesid:[],
            value: false,
            nameRules: [
                    v => !! v || 'Any contents is required'
                ],
      visible: false,
     listings: 
     {
        name: "",
        categories: "",
        subcategories: "",
        description: "",
        price: "",
        image: "",
        error: null
      },
      preview: ""
    };
  },
    async asyncData({$axios, params}) {
        try{
            let categories = await $axios.$get(`/server/categorieslist/`)
            console.log(categories)
            return{ categories}
        } catch{}
            console.log()
    },
  methods: {
      toggleChildren() {
        this.showChildren = !this.showChildren;
      },
    onFileChange(e) {
      let files = e.target.files || e.dataTransfer.files;
      if (!files.length) {
        return;
      }
      this.listings.image = files[0]
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
    async submitListings() {

      this.error = null

      const config = {
        headers: { "content-type": "multipart/form-data" }
      };
      let formData = new FormData();
      for (let data in this.listings) {
        formData.append(data, this.listings[data]);
      }
      try {
        let response = await this.$axios.$post("/server/listings/", formData, config);
        this.$router.push("/");
      } catch (e)  {
        this.error = e.response.data
      }
    }
  }
};
</script>

<style scoped>
</style>