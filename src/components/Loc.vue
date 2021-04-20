<template>
  <div>
    <!-- <div id="preloader">
      <div class="preloader"><span></span><span></span></div>
    </div> -->
    <!-- start of header and input buttons -->
    <div
      class="image-cover search-header-banner"
      style="background: url(../../public/images/centrelocation.jpg)"
      data-overlay="6"
    >
      <div class="container">
        <div class="hero-search">
          <h1 class="text-center">Find our Offices & Centers Closest to you</h1>
        </div>
        <div
          class="full-search-2 Reveal-search Reveal-search-radius box-style mb-4"
        >
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
                class="btn btn-warning mb-2 shadow-none laptop_btn"
                @click="locate()"
              >
                Search
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- end of header and input buttons -->
    <!-- start all centers in grid view -->
    <section class="gray">
      <div class="container">
        <div class="row mb-3">
          <div class="col-lg-12 col-md-12 col-sm-12">
            <div class="shorting-wrap mb-1">
              <h5 class="shorting-title">
                Search:
                <span class="theme-cl ml-2">{{ searchCount }}</span> Results
                Found
              </h5>
              <div class="shorting-rights">
                <a href="#" class="elip-btn mr-1"
                  ><i class="lni lni-grid-alt"></i
                ></a>
                <a href="#" class="elip-btn mr-1"
                  ><i class="lni lni-menu"></i
                ></a>
                <a
                  class="elip-btn"
                  href="javascript:void(0);"
                  onclick="openFilterSearch()"
                  ><i class="ti-filter"></i
                ></a>
              </div>
            </div>
          </div>
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
        <!-- <div class="row"> -->
        <div class="items">
          <div
            class="Reveal-hotel-item"
            v-for="d in data.result"
            :key="d.center_id"
          >
            <figure class="Reveal-hotel-wrap">
              <a class="place-link" href="">
                <img class="cover" src="images/london.jpg" alt="room" />
              </a>
            </figure>
            <div class="Reveal-hotel-detail">
              <span class="Reveal-Reveal-onsale-pre"
                ><span class="Reveal-onsale-pre"
                  ><a :href="`tel:${d.phone}`"></a>View Contact</span
                ></span
              >
              <div class="Reveal-hotel-detail-left">
                <br />
                <h4 class="title text-secondary">
                  {{ d.name }}
                </h4>
                <span class="Reveal-hotel-detail-detail"
                  ><i class="ti-location-pin"></i>{{ d.city }},
                  {{ d.region }}</span
                >
              </div>
            </div>
          </div>
        </div>
        <!-- </div> -->
      </div>
    </section>
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
      searchCount: "",
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
          if (
            this.country == "" &&
            this.state == "" &&
            this.city == "" &&
            this.community == ""
          ) {
            alert("country parameter is required.");
            this.searchCount = 0;
          }
          this.loading = true;
          this.data = response.data;
          this.searchCount = this.data.result.length;
          if (this.data.result == "") {
            this.loading = false;
            this.empty = true;
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

<style scoped>
.gray {
  background: #f7f9fc;
}

.shorting-wrap {
  background: #ffffff;
  border-radius: 0.3rem;
  margin-bottom: 2rem;
  padding: 0.5rem 1rem;
  border: 1px solid #ebedf1;
  display: flex;
  justify-content: flex-start;
  align-items: center;
}
.shorting-title {
  flex: 1;
  font-size: 14px;
  color: #47566b;
  margin: 0;
}
.shorting-right {
  width: auto;
  background: #f0f3f7;
  border-radius: 2px;
  padding: 5px 10px;
  display: flex;
  align-items: center;
}
.shorting-right .dropdown.show {
  display: inline-block;
}
.shorting-right .btn.btn-filter {
  color: #8891a7;
  padding: 2px 5px;
  font-size: 14px;
  line-height: unset;
}
.shorting-right label {
  display: inline-block;
  margin-bottom: 0;
}
.Reveal-hotel-item {
  background-color: transparent;
  margin-bottom: 1.875rem;
  position: relative;
  width: 300px;
}
.Reveal-hotel-item.light {
  background-color: #fff;
}
.Reveal-hotel-wrap {
  border-radius: 6px;
  height: 230px;
  margin-bottom: 0;
  overflow: hidden;
  position: relative;
  -webkit-mask-image: -webkit-radial-gradient(white, black);
}
.Reveal-hotel-wrap::before {
  content: "";
  background: linear-gradient(
    180deg,
    rgba(42, 43, 48, 0) 48.92%,
    #2a2b30 81.35%
  );
  height: 100%;
  opacity: 0.5;
  pointer-events: none;
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  z-index: 2;
}
.Reveal-Reveal-onsale-pre {
  position: absolute;
  top: -7px;
  right: 15px;
  z-index: 1;
}

.Reveal-Reveal-onsale-pre:after {
  position: absolute;
  content: "";
  display: block;
  width: 0;
  height: 0;
  border-left: 44px solid transparent;
  border-right: 42px solid transparent;
  border-top: 6px solid #ffd700;
}

.Reveal-onsale-pre {
  position: relative;
  display: inline-block;
  text-align: center;
  color: #fff;
  background: #ffd700;
  font-size: 12px;
  line-height: 1;
  padding: 10px 8px 6px;
  border-top-right-radius: 8px;
  width: 84px;
  letter-spacing: 0.5px;
  font-weight: 500;
}
.Reveal-onsale-pre small {
  font-size: 7px;
  font-weight: 500;
  margin-left: 2px;
}
.Reveal-onsale-pre:before,
.Reveal-onsale-pre:after {
  position: absolute;
  content: "";
  display: block;
}

.Reveal-onsale-pre:before {
  background: #ffd700;
  height: 7px;
  width: 6px;
  left: -6px;
  top: 0;
}

.Reveal-onsale-pre:after {
  background: #ffd700;
  height: 7px;
  width: 8px;
  border-radius: 8px 8px 0 0;
  left: -8px;
  top: 0;
}
.Reveal-hotel-item:hover .cover {
  transform: scale(1.05);
}
.Reveal-hotel-detail {
  padding: 1rem 1.5rem;
  position: relative;
  bottom: 0;
  left: 0;
  z-index: 3;
  background: #ffffff;
  width: 90%;
  margin: -50px auto 0px;
  border-radius: 0.6rem;
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  box-shadow: 0 0 20px rgba(124, 136, 160, 0.25);
  -webkit-box-shadow: 0 0 20px rgba(124, 136, 160, 0.25);
}
.Reveal-hotel-detail-left {
  flex: 1;
}
.Reveal-hotel-detail-detail {
  font-size: 14px;
}
.Reveal-hotel-detail-left .title {
  font-size: 15px;
  margin: 3px 0 0px;
  font-weight: 500;
}
span.Reveal-hotel-detail-detail i {
  margin-right: 4px;
}
.Reveal-placebox {
  text-align: right;
}
.price-title-cut {
  font-weight: 500;
  font-size: 13px;
  opacity: 0.6;
  margin: 0;
  text-decoration: line-through;
}
.Reveal-placebox .price-title {
  margin: 2px 0;
  font-size: 18px;
  font-weight: 700;
}

.items {
  display: flex;
  flex-direction: row;
  justify-content: space-evenly;
  flex-wrap: wrap;
}
.form-group {
  margin: 0px 10px;
}
#btn {
  color: white !important;
  font-weight: bold;
  margin-left: 40px;
}

.search_btn {
  margin-left: 30px;
  margin-top: 8px;
}
.form-inline {
  margin-left: 50px;
}
input::placeholder {
  padding: 10px 10px;
}

@media (min-width: 320px) and (max-width: 991px) {
  .form-group {
    width: 100%;
  }

  input {
    width: 100%;
    margin: 10px 0px;
  }
  .form-inline {
    margin: 0px;
  }
}
</style>
