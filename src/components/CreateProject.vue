<template>
  <v-app id="example-1" dark>
    <v-toolbar fixed class="darken-2" dark>
    <v-btn icon to="/">
      <v-icon>arrow_back</v-icon>
    </v-btn>
      <v-toolbar-title>My Project</v-toolbar-title>
      <v-spacer></v-spacer>

      <v-flex xs3 sm3>
        <v-select id='select-label'
          prepend-icon="label"
          v-bind:items="datasets"
          v-model="selectedDataset"
          label="Select dataset"
          return-object
          :autocomplete="autocompleteLabels"
        ></v-select>
      </v-flex>
    <v-btn icon>
      <v-icon large>more_vert</v-icon>
    </v-btn>
    </v-toolbar>

    <main>
      <v-container fluid>
        <!-- HTML BODY HERE -->
        <v-flex xs3 sm3>
        <v-form v-model="valid">
          <v-text-field
            label="Model Name"
            v-model="name"
            :rules="nameRules"
            required
          ></v-text-field>
          <v-text-field
            label="Description"
            v-model="message"
            required
          ></v-text-field>
          <v-slider
          label="Adjust:"
          v-on="adjustThreshold()"
          v-model="sliderValue"
          :step="5"
          snap
          thumb-label
          dark>
          </v-slider>
        </v-form>
        </v-flex>

      </v-container>
    </main>

    <v-footer dark>
      <span class="white--text">© 2017</span>
    </v-footer>
  </v-app>

</template>

<script>
import RangeSlider from 'vue-range-slider'

import 'vue-range-slider/dist/vue-range-slider.css'

// Example Queries/Constants
import example from '../constants/example.js';
import { SAVE_OBJ_DETECT_IMAGE } from '../constants/graphql'
import { NEXT_OBJ_DETECT_IMG_QUERY } from '../constants/graphql'

var print = function(text) {
  console.log(text);
}

export default {
  name: 'createProject',
  components: {
    RangeSlider
  },
  props: [],

  data() {
    return {
      id: '',
      datasets: ['MRI', 'EHR', 'ClinicalTrial', 'CT'],
      selectedDataset: 'MRI',
      sliderValue: 1.0
    }
  },

  apollo: {
    // This fires automatically on page load
    nextObjDetectImage: {
      query: NEXT_OBJ_DETECT_IMG_QUERY,
      variables () {
        return {
          project: "example_data"
        }
      },
      result({ data, loader, networkStatus }) {
        this.image = data.nextObjDetectImage;
        console.log("do something when data returned");
      },
    },
  },

  computed: {
      autocompleteLabels: function () {
        return false;
    },
  },

  filters: {
    capitalize: function (value) {
      if (!value) return ''
      value = value.toString()
      return value.charAt(0).toUpperCase() + value.slice(1)
    }
  },

  mounted: function() {
    console.log("fires when vue mounted?");
  },

  created: function () {
    console.log("fires when vue created?");
  },

  methods: {
    getDatasets: function () {
      return this.datasets;
    },

    adjustThreshold: function () {
      console.log("Threshold: ", this.sliderValue);
    },

    getRandId: function () {
        return Math.random().toString(36).substr(2, 10);
    },

    // Example GET
    nextImage: function () {
      this.$apollo.queries.nextObjDetectImage.refetch();
    },

    // Example POST
    save: function () {
      this.$apollo.mutate({
        mutation: SAVE_OBJ_DETECT_IMAGE,
        variables: {
          id: "ID",
          project: "MYPROJECT",
          annotations: []
        }
      }).then((data) => {
        this.$apollo.queries.nextObjDetectImage.refetch();
      })
    },

  }
}
</script>



<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}

.slider {
  /* overwrite slider styles */
  width: 200px;
}
</style>
