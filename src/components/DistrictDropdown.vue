<template>
  <select v-model="selectedDistrictId" @change="emitSelectedDistrictId" class="form-select" aria-label="Default select example">
    <option selected :value="0">Vali linnaosa</option>
    <option v-for="district in districts" :value="district.districtId" :key="district.districtId">
      {{ district.districtName }}</option>
  </select>
</template>

<script>
import router from "@/router";

export default {
  name: 'DistrictDropdown',
  data() {
    return {
      selectedDistrictId: 0,

      districts: [
        {
          districtId: 0,
          districtName: ''
        }
      ]
    }
  },

  methods: {
    getDistricts() {
      this.$http.get("/districts")
          .then(response => {
            this.districts = response.data
          })
          .catch(error => {
            router.push({name: 'errorRoute'})
          })
    },
    emitSelectedDistrictId() {
      this.$emit('event-update-selected-district-id', this.selectedDistrictId)
    },
    setSelectedDistrictId(selectedDistrictId) {
      this.selectedDistrictId = selectedDistrictId
    },
  },
  beforeMount() {
    this.getDistricts()
  }

}
</script>