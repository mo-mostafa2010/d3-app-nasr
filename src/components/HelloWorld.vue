<template>
  <v-container>
    <v-file-input
      accept="image/png, image/jpeg, image/bmp"
      placeholder="Pick an avatar"
      prepend-icon="mdi-camera"
      label="Avatar"
      v-model="imagge"
      @change="postForm()"
    ></v-file-input>
    <v-row>
      <v-col cols="6">
        <v-container>
          <img height="300" v-bind:src="`${url}`">
        </v-container>
      </v-col>
      <v-col cols="6">
        <plotly
          v-if="plot"
          :data="data"
          :layout="layout"
          :displayModeBar="false"
        />
      </v-col>
    </v-row>
    <v-row>
      <v-col cols="6">
        <v-container> 
                 <plotly
          v-if="plot2"
          :data="data2"
          :layout="layout"
          :displayModeBar="false"
        />
        </v-container>
      </v-col>
      <v-col cols="6">
        <v-col class="d-flex" cols="12" sm="6">
          <v-select  v-if="plot" v-model="selected" :items="itemss" @change="changeSelect()" label="Select type"></v-select>
        </v-col>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import { Plotly } from "vue-plotly";
import axios from "axios";
export default {
  name: "HelloWorld",
  components: {
    Plotly,
  },
  data: () => ({
    plot: false,
    plot2: false,
    items: ['background', 'cap','ring', 'stipe', 'gills', 'volva', 'mycelium'], 
    selected: 0,
    data: [
      {
        z: [],
        type: "contour",
        autorange: "reversed",
      },
    ],
    data2: [
      {
        z: [],
        type: "contour",
        autorange: "reversed",
      },
    ],
    layout: {
      title: "My graph",
      yaxis: { autorange: "reversed" },
    },
    imagge: null,  
    imagge2: null,  
    url: null,
  }),
  computed: {
    itemss() {
      return this.items.map((item, index) => {
        return {
          text: item,
          value: index
        }
      })
    }
  },
  methods: {
    postForm() {
      console.log("hhere", this.imagge);
      this.url = URL.createObjectURL(this.imagge);
      let formData = new FormData();
      formData.append("image", this.imagge);
      axios
        .post("http://localhost:105/form-example", formData)
        .then((response) => {
          this.data[0].z = response.data;
          this.changeSelect()
          this.plot = true;
        });
    },
    changeSelect() {
      this.plot2 = false;
      let self = this
       this.url = URL.createObjectURL(this.imagge);
      let formData = new FormData();
      formData.append("image", this.imagge);
      formData.append("item", this.selected);
      axios
        .post("http://localhost:105/change", formData)
        .then((response) => {
          self.data2[0].z = response.data;
          console.log('data', self.data);
          console.log('data', self.data2);
          this.plot2 = true;
        }).finally((res) => {
          console.log(res);
        });
    },
  },
};
</script>
