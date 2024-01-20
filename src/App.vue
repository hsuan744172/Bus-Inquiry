<script setup>
import { onMounted, ref, watch } from 'vue'

const routeName = ref(null)
const stationName = ref(null)
const go = ref(null)

const remainTime = ref(0)
const stationsRemainTime = ref([])

const routesName = ref([])
const stationsName = ref([])
const showSubmissionInfo = ref(false)
function processTime(time) {
  let condition = ''

  if (time >= 0 && time <= 60) {
    condition = '即將到站'
  } else if (time > 180) {
    condition = Math.floor(time / 60) + '分' + (time % 60) + '秒'
  } else if (time === -2) {
    condition = '交管不停'
  } else if (time === -3) {
    condition = '末班車已過'
  } else {
    condition = '未發車'
  }

  return condition
}
const onGoChange = () => {
  if (routeName.value === '') return

  fetch('https://simple-api-0pge.onrender.com/api/routeStationsName', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      route_name: routeName.value,
      go: go.value
    })
  })
    .then((res) => res.json())
    .then((data) => {
      stationsName.value = data.stations_name
    })
    .catch((err) => console.log(err))

  stationName.value = null
}

const onSubmit = (e) => {
  e.preventDefault()

  fetch('https://simple-api-0pge.onrender.com/api/routeStationsRemainTime', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      route_name: routeName.value,
      go: go.value
    })
  })
    .then((res) => res.json())
    .then((data) => {
      stationsRemainTime.value = data.remain_times.map((item) => ({
        name: item.name,
        time: processTime(parseInt(item.time, 10))
      }))
      remainTime.value = stationsRemainTime.value.find(
        (item) => item.name === stationName.value
      ).time
      showSubmissionInfo.value = true
    })
    .catch((err) => console.log(err))
}

onMounted(() => {
  fetch('https://simple-api-0pge.onrender.com/api/routesName')
    .then((res) => res.json())
    .then((data) => {
      routesName.value = data.routes_name
    })
    .catch((err) => console.log(err))
  watch(routeName, () => {
    stationsRemainTime.value = []
    remainTime.value = 0
    go.value = null
  })
})
</script>

<template>
  <v-app>
    <v-app-bar title="Taiwan BUS">
      <v-card
        class="my-8"
        max-width="344"
        title="myGitHub"
        prepend-icon="mdi-github"
        append-icon="mdi-open-in-new"
        href="https://github.com/hsuan744172?tab=repositories"
        target="_blank"
        rel="noopener"
      ></v-card>
    </v-app-bar>

    <v-main>
      <v-form @submit="onSubmit" class="ma-4">
        <v-container>
          <v-title class="custom-title">Bus Remain Time Inquiry</v-title>
          <v-row class="mt-4">
            <v-col cols="12" md="4">
              <v-autocomplete
                v-if="routesName.length > 0"
                label="Route name"
                clearable
                v-model="routeName"
                :items="routesName"
                hide-details
                required
                variant="outlined"
              ></v-autocomplete>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12" md="4">
              <div class="d-flex align-center">
                <span>Direction </span>
                <v-switch
                  class="ml-2"
                  v-model="go"
                  inset
                  hide-details
                  true-value="去程"
                  false-value="回程"
                  :label="go"
                  @click="onGoChange"
                ></v-switch>
              </div>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12" md="4">
              <v-autocomplete
                label="Station name"
                clearable
                v-model="stationName"
                :items="stationsName"
                hide-details
                required
                variant="outlined"
              ></v-autocomplete>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12" md="4">
              <v-btn
                type="submit"
                block
                text="Submit"
                small
                :disabled="!routeName || !stationName"
              ></v-btn>
            </v-col>
          </v-row>
          <v-card height="80" width="150" class="mt-4">
            <v-card-text
              >Remain time<br />
              <h2>{{ remainTime }}</h2></v-card-text
            ></v-card
          >

          <v-card-text class="text-h6"
            >Find all in bus {{ routeName }}
            <v-icon left class="mr-1">mdi-arrow-down</v-icon>
          </v-card-text>

          <v-menu>
            <template v-slot:activator="{ props }">
              <v-btn color="primary" dark v-bind="props">
                <v-icon left class="mr-1">mdi-bus</v-icon>
                All Station Times
              </v-btn>
            </template>

            <v-list>
              <v-list-item v-for="item in stationsRemainTime" :key="item.name">
                <v-list-item-title>{{ item.name }}: {{ item.time }}</v-list-item-title>
              </v-list-item>
            </v-list>
          </v-menu>
          <v-row v-if="showSubmissionInfo">
            <v-col cols="12" md="4">
              <v-card class="mt-4">
                <v-card-text>
                  <p>Route Name: {{ routeName }}</p>
                  <p class="mt-4">direct: {{ go }}</p>
                  <p class="mt-4">Station Name: {{ stationName }}</p>
                </v-card-text>
              </v-card>
            </v-col>
          </v-row>
        </v-container>
      </v-form>
      
    </v-main>
    <v-footer></v-footer>
  </v-app>
</template>

<style scoped>
.custom-title{
  font-size: 24px;
}
</style>
