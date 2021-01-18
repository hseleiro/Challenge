<template>
  <section id="app">
    <Categories v-on:filter-option="filterOptions"/>
    <Gallery v-bind:filteredVehicles=filteredVehicles />
  </section>
</template>

<script lang="ts">
  import Vue from 'vue';
  import Categories from "@/components/Categories.vue";
  import Gallery from "@/components/Gallery.vue";
  import { uuid } from 'vue-uuid';
  import vehiclesJson from "./static/vehicles.json"

  type VehiclesArrayInterface = {
    id: number,
    driverName: string
    vehicleFeatures: [
      {
        licencePlate: string,
        manufacturer: string,
        acquisitionDate: string,
        odometer: number,
        hasInsurance: boolean,
        dateNextInspection: string,
      }
    ],
    time: Date
  }

  type vehicleFeatures = {
    licencePlate: string,
    manufacturer: string,
    acquisitionDate: string,
    odometer: number,
    hasInsurance: boolean,
    dateNextInspection: string,
  }

  export default Vue.extend({
    name: 'App',
    components: {
      Categories,
      Gallery
    },
    data() {
      return {
        filteredVehicles: Object as () => Array<VehiclesArrayInterface>,
        vehicles: vehiclesJson
      }
    },
    beforeMount(): void {
      // Check if vehicles array is store in sessionStore.
      if (sessionStorage.getItem('vehicles') === null) {
        this.setSessionStorage();
      }
      this.setTimeInterval();
    },
    methods: {
      // Set session storage
      setSessionStorage(): void {
        // Set time when vehicles array is storage, with 30 minutes plus.
        const time = new Date();
        time.setMinutes((time.getMinutes() + 30));

        // Push new proprety to vehicles array.
        this.vehicles.push(time);

        // Parse and save vehicles array on sessionStorage.
        const vehiclesParsed = JSON.stringify(this.vehicles);
        sessionStorage.setItem('vehicles', vehiclesParsed);
      },
      // Set a time interval to watch if the sessionStorage as expired.
      setTimeInterval(): void {
        const watchInterval = 15*60*1000;
        setInterval(() =>{ this.watchIfSessionStorageAsExpired(); }, watchInterval );
      },
      // Watch if session as expired.
      watchIfSessionStorageAsExpired(): void {
        const vehiclesParsed = JSON.parse(sessionStorage.getItem('vehicles'));
        // If moment time is greater 30 minutes plus than where vehicles array where store in sessionStorage
        // we remove the array from local storage.
        // console.log('avé', vehiclesParsed[50] < new Date())
        if (vehiclesParsed[50] < new Date()) {
          localStorage.removeItem("vehicles");
          this.setSessionStorage();
        }
      },
      // Filter category options.
      filterOptions(checkedValues: number[]): void {
        if(checkedValues && this.vehicles.length) {
          this.filteredVehicles = this.vehicles.filter((vehicle: { vehicleFeatures: vehicleFeatures[]; }) => {
            if(vehicle.vehicleFeatures) {
              return vehicle.vehicleFeatures
              .map(vehicleFeatures => vehicleFeatures.odometer)
              .find(featureOdometer => {
                return checkedValues.find((odometerOption: number) => {
                  return featureOdometer >= odometerOption;
                });
              });
            }
          });
        }
      }
    }
  });
</script>

<style lang="scss">
  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }
</style>

