<template>
  <div>
    <div class="container">
      <h1 class="text-secondary text-center">
        Find Our Offices & Centers Closest To You
      </h1>
      <!-- form inputs -->
      <div class="form-inline">
        <div class="form-group">
          <label for="country" class="sr-only">Country</label>
          <input type="text" placeholder="country" v-model="country" />
        </div>
        <div class="form-group">
          <label for="state" class="sr-only">State</label>
          <input type="text" placeholder="State" v-model="state" />
        </div>
        <div class="form-group">
          <label for="city" class="sr-only">City</label>
          <input type="text" placeholder="city" v-model="city" />
        </div>
        <div class="form-group">
          <label for="community" class="sr-only">Community</label>
          <input type="text" placeholder="community" v-model="community" />
        </div>
        <div class="search_btn">
          <button
            type="submit"
            class="btn btn-primary mb-2 shadow-none laptop_btn"
            @click="locate()"
          >
            Search
          </button>
          <button
            type="submit"
            class="btn btn-primary mb-2 shadow-none mobile_btn btn-block"
            @click="locate()"
          >
            Search
          </button>
        </div>
      </div>
      <div class="result-number">
        <p class="text-secondary">
          <span class="text-warning">{{ searchCount }}</span> results found
        </p>
      </div>
      <div v-if="errored" class="alert alert-warning">
        <p class="text-danger text-center">An error occured</p>
      </div>
      <div v-if="loading" class="alert alert-primary">
        <p class="text-secondary text-center">Loading, Please wait...</p>
      </div>
      <div v-if="empty" class="alert alert-primary">
        <p class="text-secondary text-center">
          There are no centers in this location
        </p>
      </div>
      <div class="items">
        <div class="box" v-for="d in data.result" :key="d.center_id">
          <img src="/images/setup.jpg" alt="" />
          <div class="inner-box mt-3">
            <div>
              <h4 class="text-secondary">{{ d.name }}</h4>
              <p class="text-dark">
                {{ d.address }}
              </p>
              <p class="text-secondary country">
                {{ d.city }},
                {{ d.region }}
              </p>
            </div>
            <div>
              <a
                :href="`tel:${d.phone}`"
                type="button"
                class="btn btn-warning shadow-none"
                >Contact</a
              >
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      errored: false,
      loading: true,
      country: "",
      state: "",
      city: "",
      community: "",
      data: [],
      empty: false,
      searchCount: 0,
      AllRepCenters: "https://ovaboss-ems.dev.i.ng/api/rep_centres/all",
    };
  },
  methods: {
    async fetchCenters() {
      return axios({
        method: "get",
        url: this.AllRepCenters,
      })
        .then((response) => {
          this.data = response.data;
          this.searchCount = this.data.result.length;
          console.log(response.data);
          this.loading = false;
        })
        .catch((error) => {
          this.errored = true;
          this.loading = false;
          console.log(error);
        });
    },

    async locate() {
      return axios({
        method: "get",
        url: `https://ovaboss-ems.dev.i.ng/api/rep_centres/locate/${this.country}/${this.state}/${this.city}/${this.community}`,
        // url:
        //   "https://ovaboss-ems.dev.i.ng/api/rep_centres/locate/Nigeria/Lagos/Lagos/Surulere",
      })
        .then((response) => {
          this.loading = true;
          this.data = response.data;
          this.searchCount = this.data.result.length;
          if (response.data.result == "") {
            this.loading = false;
            this.empty = true;
          } else if (
            this.country == "" &&
            this.state == "" &&
            this.city == "" &&
            this.community == ""
          ) {
            alert("Provide a country, state, or city for us to search");
          }
          this.loading = false;
        })
        .catch((error) => {
          this.errored = true;
          this.loading = false;
          console.log(error);
        });
    },

    getLength() {
      return this.data.result.length;
    },
  },

  mounted() {
    this.fetchCenters();
  },
};
</script>

<style lang="css" scoped>
h1 {
  margin-top: 120px;
}
.input-box {
}

input {
  width: 230px;
}

input::placeholder {
  padding: 10px 10px;
}

.result-number p {
  font-size: 20px;
  padding: 20px;
}

.inner-box {
  display: flex;
  justify-content: space-between;
}

.country {
  margin-top: -11px;
}

img {
  width: 100%;
  height: 300px;
  border-radius: 20px;
}

#btn {
  color: white;
  font-weight: bold;
  margin-left: 40px;
}

.search_btn {
  margin-left: 30px;
  margin-top: 8px;
}

.items {
  display: flex;
  flex-direction: row;
  justify-content: space-evenly;
  flex-wrap: wrap;
}

.items > div {
  margin: 0px 10px;
}

.box {
  width: 300px;
}

.form-group {
  margin: 0px 10px;
}

.mobile_btn {
  display: none;
}

@media (min-width: 320px) and (max-width: 991px) {
  .items {
    flex-direction: column;
  }

  img {
    width: 100%;
    height: 300px;
    margin: 10px 0px;
  }

  .mobile_btn {
    display: block;
  }

  .laptop_btn {
    display: none;
  }

  .form-group {
    /* margin: 10px 0px; */
    width: 100%;
  }

  input {
    width: 100%;
    margin: 10px 0px;
  }
}
</style>
