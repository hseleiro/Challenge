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
    vehicleFeatures: [
      {
        driverName: string
        licencePlate: string,
        manufacturer: string,
        acquisitionDate: string,
        odometer: number,
        hasInsurance: boolean,
        dateNextInspection: string,
      }
    ],
  }

  type checkedCategoryInterface = {
    value: number,
    type: string
  }

  type vehicleFeaturesInterface = {
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
        // Set time with 30 minutes plus.
        const currentDate = new Date();
        const timePlus30Minutes = new Date(currentDate.getTime() + (30*60*1000));

        // Push new proprety to vehicles array.
        this.vehicles.push(timePlus30Minutes);
        console.log('this.vehicles', this.vehicles);

        // Parse and save vehicles array on sessionStorage.
        const vehiclesParsed = JSON.stringify(this.vehicles);
        sessionStorage.setItem('vehicles', vehiclesParsed);
      },
      // Set a time interval to watch if the sessionStorage as expired.
      setTimeInterval(): void {
        const watchInterval = 15*60*1000;
        setInterval(() =>{ this.watchIfSessionStorageAsExpired(); }, 1000);
      },
      // Watch if session as expired.
      watchIfSessionStorageAsExpired(): void {
        const vehiclesParsed = JSON.parse(sessionStorage.getItem('vehicles'));
        if (vehiclesParsed[50] < new Date()) {
          localStorage.removeItem("vehicles");
          this.setSessionStorage();
        }
      },
      // Filter category options.
      filterOptions(checkedValues: checkedCategoryInterface[]): void {
        if(checkedValues && this.vehicles.length) {
          this.filteredVehicles = this.vehicles.filter((vehicle: { vehicleFeatures: vehicleFeaturesInterface[]; }) => {
            if(vehicle.vehicleFeatures) {
              return vehicle.vehicleFeatures
              .map(vehicleFeatures => vehicleFeatures)
              .find(vehicleFeature => {
                return checkedValues.find((checkedValue: checkedCategoryInterface) => {
                  switch(checkedValue.type) {
                    case 'asInsurance':
                      return vehicleFeature.hasInsurance
                    case 'odometer':
                      return vehicleFeature.odometer >= checkedValue.value;
                  }
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

