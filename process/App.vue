<template>
  <div id="main-app">
    <add-appointment @addReq="appAdd"/>
    <search-appointment :filKey="filterKey" :filDir="filterDir" @searchRec="searchApts" @keyChange="changeKey" @dirChange="changeDir"/>
    <appointment-list
      :appointments = 'filteredApts' @remove="removeItem"/>
  </div>
</template>

<script>
import _ from 'lodash'
import moment from 'moment'
import AppointmentList from './AppointmentList.vue';
import AddApp from './AddAppointment.vue'
import SearchApp from './SearchAppointment.vue'

export default {
  name: 'MainApp',
  data() {
    return {
      theAppointments: [],
      searchTerms: '',
      filterKey: 'petName',
      filterDir: 'asc'
    } //return
  }, //data

  components: {
    'appointment-list': AppointmentList,
    'add-appointment': AddApp,
    'search-appointment': SearchApp
  }, //components

  created: function() {
    $.getJSON('./builds/appointments.json')
      .done( info =>  {
        this.theAppointments = info;
    }); //getJSON
  }, //created
  methods: {
    appAdd(apt){
      this.theAppointments.push(apt)
    },
    removeItem(apt){
      this.theAppointments = _.without(this.theAppointments, apt)
    },
    searchApts(terms){
      this.searchTerms = terms
    },
    changeKey(key){
      this.filterKey = key
    },
    changeDir(dir){
      this.filterDir = dir
    }
  },
  computed: {
    searchedApt(){
      return this.theAppointments.filter(item => 
        item.petName.toLowerCase().match(this.searchTerms.toLowerCase()) ||
        item.petOwner.toLowerCase().match(this.searchTerms.toLowerCase()) ||
        item.aptNotes.toLowerCase().match(this.searchTerms.toLowerCase())
        )
    },
    filteredApts(){
      return _.orderBy(this.searchedApt, item => item[this.filterKey].toLowerCase(), this.filterDir)
    }
  }
} //default
</script>