<template>
    <div>
        <div class="odometer-options-header-title">
            <h2>Odometer Options</h2>
        </div>
        <div class="odometer-options-container">
            <div class="option" v-for="option in odometerOptions">
                <label>
                    <input type="checkbox"
                           class="checkboxOptions"
                           v-model="option.checked"
                           @change="onFilterOdometer()"/>
                    <span class="option-name">{{option.name}}</span>
                </label>
            </div>
        </div>
        <div v-if="!showVehicles" class="odometer-options-container-title">
            <h2>Please select an option</h2>
        </div>
    </div>
</template>

<script>
    export default {
        name: "Categories",
        data() {
            return {
                showVehicles : false,
                odometerOptions: [
                    {type: "odometer", name: "> 75.000Km", value: 75.000, checked: false },
                    {type: "odometer", name: ">= 130.000Km", value: 130.000, checked: false },
                    {type: "odometer", name: ">= 250.000Km", value: 250.000, checked: false },
                    {type: "asInsurance", name: "As insurance", value: true, checked: false }
                ]
            }
        },
        methods: {
            // Emit value and type of options to be filtered
            onFilterOdometer() {
                this.$emit('filter-option',this.odometerOptions.filter((opt) => opt.checked).map((opt) => {
                    return {
                        value: opt.value,
                        type: opt.type
                    }
                }));
                this.showVehicles = !this.showVehicles;
            },

        }
    }
</script>

<style scoped>
    .odometer-options-header-title {
        background: #1abc9c;
        padding: 50px;
    }

    .odometer-options-header-title h2 {
        color: #ffffff;
        font-size: 2em;
    }

    .odometer-options-container-title {
        padding: 40px 0 0 0;
    }

    .odometer-options-container {
        display: flex;
        flex-direction: row;
        justify-content: center;
        padding: 50px 0;
    }

    .option {
        margin: 0 10px;
        padding: 10px;
        border-radius: 10px;
        background-color: #1abc9c;
    }

    .option-name {
        font-size: 1.1em;
        margin-left: 5px;
        color: #ffffff;
        font-weight: bold;
    }

</style>
