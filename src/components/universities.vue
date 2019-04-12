<template>
  <div>
    <h4>Universities</h4>
    <autocomplete
    :source="autocompleteUniversitiesList"
    @selected="setSelectedValue">
    </autocomplete>
    <!-- <div v-for="university in uniqueUniversities">
      <input type="checkbox" name="provider-name"/>
      <label>{{university}}</label>
    </div> -->
  </div>
</template>
<script>
import Autocomplete from 'vuejs-auto-complete'

export default  {
  data () {
    return {
      universities: [],
      uniqueUniversities: [],
      autocompleteUniversitiesList: [],
      selectedValue: ''
    }
  },
  props: ['courses'],
  methods: {
    getUniversities () {
      for(var i= 0;i <this.courses.length; i++){
        if(this.courses[i].Universities.Institutions.length > 0){
          this.universities.push(this.courses[i].Universities.Institutions)
        }
      }
      this.uniqueUniversities = [...new Set(this.universities)]
    },
    setAutoCompleteUniversitiesList () {
      for (var i = 0; i < this.uniqueUniversities.length; i++) {
        this.autocompleteUniversitiesList.push({'id':i ,'name':this.uniqueUniversities[i]})
      }
    },
    setSelectedValue (selectedValue) {
      this.selectedValue = selectedValue.display
      this.$emit('selectedUniversity', this.selectedValue.toString())
    }
  },
  mounted () {
    this.getUniversities(),
    this.setAutoCompleteUniversitiesList()
  },
  components: {
    'autocomplete' :Autocomplete,
  }
}
</script>
<style scoped>
div{
  width: 80%;
  margin: 0 5%;
  text-align: left;
}
h4{
  color: white;
}
</style>
