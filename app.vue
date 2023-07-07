<script setup>
import VueDatePicker from '@vuepic/vue-datepicker'
import '@vuepic/vue-datepicker/dist/main.css'

function formatDate (date) {
  const year = date.getFullYear()
  const month = String(date.getMonth() + 1).padStart(2, '0')
  const day = String(date.getDate()).padStart(2, '0')
  
  return `${year}-${month}-${day}`
}

function createDateObject (dateString = null, timeString = null) {
  if (!dateString  || !timeString) return new Date()

  const [year, month, day] = dateString.split('-')
  const [hours, minutes] = timeString.split(':')
  
  // Note: Months in JavaScript's Date object are zero-based (0-11)
  return new Date(year, month - 1, day, hours, minutes)
}

const route = useRoute()

const affiliates = ['vueschool', 'vuejsdevelopers', 'vuejsfeed', 'vuerouter', 'vuejs', 'vueuse', 'dmitripavlutin', 'vuejobs']

const scriptUrl = ref('')

const form = ref({
  affiliate: route.query?.affiliate || affiliates[0],
  date: createDateObject(route.query?.emulateDate, route.query?.emulateHour)
})

function preview () {
  const affiliate = form.value.affiliate
  const date = form.value.date

  const emulateDate = formatDate(date)
  const emulateHour = date.toLocaleString('en-GB', { timeStyle: 'short' })

  const target = `?affiliate=${affiliate}&emulateDate=${emulateDate}&emulateHour=${emulateHour}`
  window.location.href = target
}

onMounted(() => {
  const { emulateDate, emulateHour, affiliate } = route.query
  let url = `https://vueschool.io/banner.js?affiliate=${affiliate || affiliates[0]}&type=top`

  if (emulateDate && emulateHour) {
    url = url + `&emulateDate=${emulateDate}&emulateHour=${emulateHour}`
  }

  scriptUrl.value = url

  const script = document.createElement('script')
  script.async = true
  script.src = scriptUrl.value
  document.head.appendChild(script)
})
</script>

<template>
  <div>
    <div class="app">
      <pre>{{ scriptUrl }}</pre>
      <hr>
      <form @submit.prevent="preview">
        <select v-model="form.affiliate">
          <option v-for="affiliate in affiliates">
            {{ affiliate }}
          </option>
        </select>
        <VueDatePicker v-model="form.date"></VueDatePicker>
        <button>
          Preview
        </button>
      </form>
    </div>
  </div>
</template>

<style type="text/css">
body {
  font-family: sans-serif; font-size: 18px;
}

.app {
  max-width: 380px;
}

button,
select {
  width: 100%;
  margin-top: 16px;
  margin-bottom: 16px;
}
</style>