<template>
  <div>
    <div class="p-5 text-center bg-dark" filter-color="brown">
      <div id="search-box" class="container">
        <h1 class="title">Find a Brewery</h1>
        <div class="form-group">
          <label for="exampleDropdownFormEmail1"></label>
          <table class="container">
            <tr>
              <td>
                <input
                  class="form-control"
                  v-model="breweryFilter.name"
                  placeholder="Name"
                />
              </td>
              <td>
                <input
                  class="form-control"
                  v-model="breweryFilter.typeName"
                  placeholder="Brewery Type"
                />
              </td>
            </tr>
          </table>
        </div>
      </div>
    </div>
    <div class="main"></div>

    <!-- Topic Cards -->
    <div id="cards_landscape_wrap-2">
      <div class="container">
        <div class="row">
          <div
            class="col-xs-12 col-sm-6 col-md-3 col-lg-3"
            v-for="brewery in fiteringBrew"
            v-on:click="sendToBrewery(brewery.breweryId)"
            v-bind:key="brewery.breweryId"
          >
            <a href="">
              <div class="card-flyer">
                <div class="text-box">
                  <div class="image-box">
                    <img
                      src="/img/bg1.jpg"
                      alt=""
                    />
                  </div>
                  <div class="text-container">
                    <h6>{{ brewery.name }}</h6>
                    <p>
                      {{ brewery.description }}
                    </p>
                  </div>
                </div>
              </div>
            </a>
          </div>

          <div
            class="col-xs-12 col-sm-6 col-md-3 col-lg-3"
          v-for="brewery in openBreweries"
          v-on:click="sendToOpenBrewery(brewery.breweryId)"
          v-bind:key="brewery.breweryId"
          >
            <a href="">
              <div class="card-flyer">
                <div class="text-box">
                  <div class="image-box">
                    <img
                      src="/img/bg1.jpg"
                      alt=""
                    />
                  </div>
                  <div class="text-container">
                    <h6>{{ brewery.name }}</h6>
                    <p>
                      {{ brewery.description }}
                    </p>
                  </div>
                </div>
              </div>
            </a>
          </div>

        </div>
      </div>
    </div>
  </div>
</template>

<script>
import breweryService from "../services/BreweryService.js";

export default {
  name: "brewery-search",
  data() {
    return {
      // breweryFilter: "",
      breweryFilter: {
        breweryId: "",
        name: "",
        rating: "",
        description: "",
        typeName: "",
        websiteUrl: "",
        phoneNumber: "",
        streetAddress: "",
        city: "",
        state: "",
        zip: "",
      },
      breweries: [],
      isLoading: true,
      openBreweries: [],
    };
  },
  computed: {
    filteredBreweries() {
      return this.breweries.filter((brewery) => {
        return this.breweryFilter == ""
          ? true
          : this.breweryFilter == brewery.name;
      });
    },
    fiteringBrew() {
      return this.breweries.filter((brewery) => {
        return (
          brewery.name
            .toLowerCase()
            .includes(this.breweryFilter.name.toLowerCase()) &&
          brewery.typeName
            .toLowerCase()
            .includes(this.breweryFilter.typeName.toLowerCase())
        );
      });
    },
  },
  methods: {
    sendToBrewery(breweryId) {
      this.$router.push(`/breweries/${breweryId}`);
    },
    sendToOpenBrewery(website_url) {
      this.$router.push(`${website_url}`);
    },
  },
  created() {
    breweryService.retrieve().then((response) => {
      this.breweries = response.data;
      this.isLoading = false;
    });
    fetch(
      "https://api.openbrewerydb.org/breweries?by_state=ohio&by_city=columbus&per_page=50"
    )
      .then((response) => {
        if (response.status !== 200) {
          console.log(
            "Looks like there was a problem. Status Code: " + response.status
          );
          return;
        }

        // Examine the text in the response
        response.json().then((data) => {
          this.brewery = data;
          this.breweryList = JSON.parse(JSON.stringify(data));
          this.breweryList.forEach((brewery) => {
            this.breweryId = brewery.id;
            this.name = brewery.name;
            this.typeName = brewery.brewery_type;
            this.streetAddress = brewery.street;
            this.city = brewery.city;
            this.state = brewery.state;
            this.zip = brewery.zip;
            this.websiteUrl = brewery.website_url;
            this.openBreweries.push(brewery);
          });
        });
      })
      .catch((err) => {
        console.log("Fetch Error", err);
      });
  },
};
</script>

<style scoped>
@import url("https://fonts.googleapis.com/css?family=Open+Sans&display=swap");

.ratings {
  background-color: #fff;
  padding: 54px;
  border: 1px solid rgba(0, 0, 0, 0.1);
  box-shadow: 0px 10px 10px #e0e0e0;
}

.product-rating {
  font-size: 50px;
}

.stars i {
  font-size: 18px;
  color: #28a745;
}

.rating-text {
  margin-top: 10px;
}

.title {
  color: white;
}

/*----  Main Style  ----*/
#cards_landscape_wrap-2 {
  text-align: center;
  background: #f7f7f7;
}
#cards_landscape_wrap-2 .container {
  padding-top: 20px;
  padding-bottom: 50px;
}
#cards_landscape_wrap-2 a {
  text-decoration: none;
  outline: none;
}
#cards_landscape_wrap-2 .card-flyer {
  border-radius: 5px;
}
#cards_landscape_wrap-2 .card-flyer .image-box {
  background: #ffffff;
  overflow: hidden;
  box-shadow: 0px 2px 15px rgba(0, 0, 0, 0.5);
  border-radius: px;
}
#cards_landscape_wrap-2 .card-flyer .image-box img {
  -webkit-transition: all 0.9s ease;
  -moz-transition: all 0.9s ease;
  -o-transition: all 0.9s ease;
  -ms-transition: all 0.9s ease;
  width: 100%;
  height: 200px;
}
#cards_landscape_wrap-2 .card-flyer:hover .image-box img {
  opacity: 0.7;
  -webkit-transform: scale(1.15);
  -moz-transform: scale(1.15);
  -ms-transform: scale(1.15);
  -o-transform: scale(1.15);
  transform: scale(1.15);
}
#cards_landscape_wrap-2 .card-flyer .text-box {
  text-align: center;
}
#cards_landscape_wrap-2 .card-flyer .text-box .text-container {
  padding: 30px 18px;
}
#cards_landscape_wrap-2 .card-flyer {
  background: #ffffff;
  margin-top: 50px;
  -webkit-transition: all 0.2s ease-in;
  -moz-transition: all 0.2s ease-in;
  -ms-transition: all 0.2s ease-in;
  -o-transition: all 0.2s ease-in;
  transition: all 0.2s ease-in;
  box-shadow: 0px 3px 4px rgba(0, 0, 0, 0.4);
}
#cards_landscape_wrap-2 .card-flyer:hover {
  background: #fff;
  box-shadow: 0px 15px 26px rgba(0, 0, 0, 0.5);
  -webkit-transition: all 0.2s ease-in;
  -moz-transition: all 0.2s ease-in;
  -ms-transition: all 0.2s ease-in;
  -o-transition: all 0.2s ease-in;
  transition: all 0.2s ease-in;
  margin-top: 50px;
}
#cards_landscape_wrap-2 .card-flyer .text-box p {
  margin-top: 10px;
  margin-bottom: 0px;
  padding-bottom: 0px;
  font-size: 14px;
  letter-spacing: 1px;
  color: #000000;
}
#cards_landscape_wrap-2 .card-flyer .text-box h6 {
  margin-top: 0px;
  margin-bottom: 4px;
  font-size: 18px;
  font-weight: bold;
  text-transform: uppercase;
  font-family: "Roboto Black", sans-serif;
  letter-spacing: 1px;
  color: #00acc1;
}

.text-container {
  height: 200px;
}
</style>