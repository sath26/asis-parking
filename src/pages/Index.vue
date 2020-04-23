<template>
  <q-page class="flex flex-center">
    <div class="q-pa-md">
      <q-btn
        @click="resetLocalStorage()"
        flat
        label="click me and press f5 to reset"
      ></q-btn>
      <q-table title="Treats" :data="data" :columns="columns" row-key="name" />
      <form
        @submit.prevent.stop="onSubmit"
        @reset.prevent.stop="onReset"
        class="q-gutter-md"
      >
        <q-input
          ref="car_detail"
          filled
          v-model="car_detail"
          label="Car Detail"
          lazy-rules
          :rules="[
            val => (val && val.length > 0) || 'Please type you car details!'
          ]"
        />

        <q-input
          ref="car_width"
          filled
          type="number"
          v-model="car_width"
          label="Car Width"
          lazy-rules
          :rules="[
            val => (val !== null && val !== '') || 'Please type your car  width'
          ]"
        />

        <div>
          <q-btn label="Submit" type="submit" color="primary" />
          <q-btn
            label="Reset"
            type="reset"
            color="primary"
            flat
            class="q-ml-sm"
          />
        </div>
      </form>
    </div>
  </q-page>
</template>

<script>
let STORAGE_KEY = "asis-parking-managemnt1";
let data_json = JSON.stringify([
  {
    parking_no: "1",
    car_width: null,
    parking_width: 159,
    car_detail: "6.0"
  },
  {
    parking_no: "2",
    car_width: null,
    parking_width: 237,
    car_detail: "9.0"
  },
  {
    parking_no: "3",
    car_width: null,
    parking_width: 262,
    car_detail: "16.0"
  },
  {
    parking_no: "4",
    car_width: null,
    parking_width: 305,
    car_detail: ""
  },
  {
    parking_no: "5",
    car_width: null,
    parking_width: 356,
    car_detail: "16.0"
  }
]);
let dataStorage = {
  fetch: function() {
    var data = JSON.parse(localStorage.getItem(STORAGE_KEY) || data_json);
    console.log(data);
    return data;
  },
  save: function(data) {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(data));
  }
};
export default {
  name: "PageIndex",
  data() {
    return {
      car_detail: null,
      car_width: null,
      parked: null,
      accept: false,
      elements: [],
      dataIndex: null,
      columns: [
        {
          name: "parking_no",
          required: true,
          label: "Parking number",
          align: "left",
          field: row => row.parking_no,
          format: val => `${val}`,
          sortable: true
        },
        {
          name: "car_width",
          align: "center",
          label: "Car Width",
          field: "car_width",
          sortable: true
        },
        {
          name: "parking_width",
          label: "Parking Width",
          field: "parking_width",
          sortable: true
        },
        {
          name: "car_detail",
          label: "Car Detail",
          field: "car_detail",
          sortable: true
        }
      ],
      data: dataStorage.fetch()
    };
  },
  //to save data on browser
  watch: {
    data: {
      handler: function(data) {
        dataStorage.save(data);
      }
    }
  },
  methods: {
    resetLocalStorage() {
      localStorage.removeItem(STORAGE_KEY);
    },
    onSubmit() {
      this.$refs.car_width.validate();
      this.$refs.car_detail.validate();

      if (this.$refs.car_width.hasError || this.$refs.car_detail.hasError) {
        this.formHasError = true;
      } else {
        this.dataIndex = this.findParkingSpot(this.car_width);
        if (this.dataIndex !== -1) {
          //parking no is predefined by array_index+1
          this.data[this.dataIndex].car_detail = this.car_detail;
          this.data[this.dataIndex].car_width = this.car_width;
        } else {
          this.$q.notify({
            icon: "done",
            color: "negative",
            message: "Error"
          });
        }
      }
    },
    findParkingSpot(inputWidth) {
      for (let i = 0; i < this.data.length; i++) {
        this.elements.push(this.data[i].parking_width);
      }
      console.log(this.elements);
      this.elements = this.elements.sort();

      let index = this.elements.findIndex(element => element >= inputWidth);
      console.log(index);
      return index;
    },
    onReset() {
      this.car_detail = null;
      this.car_width = null;

      this.$refs.car_detail.resetValidation();
      this.$refs.car_width.resetValidation();
    }
  }
};
</script>
