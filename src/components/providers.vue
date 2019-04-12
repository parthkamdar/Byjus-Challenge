<template>
  <div>
    <h4>Providers</h4>
    <autocomplete
    :source="autocompleteProvidersList"
    @selected="setSelectedValue"
    >
    </autocomplete>
    <!-- <div v-for="provider in uniqueProviders">
      <input type="checkbox" name="provider-name"/>
      <label>{{provider}}</label>
    </div> -->
  </div>
</template>
<script>
import Autocomplete from 'vuejs-auto-complete'
export default {
  data () {
    return {
      providers: [],
      uniqueProviders: [],
      autocompleteProvidersList: [],
      selectedValue: '',
    }
  },
  props: ['courses'],
  methods: {
    setAutcompleteProvidersList () {
      for (var i = 0; i < this.uniqueProviders.length; i++){
        this.autocompleteProvidersList.push({'id':i ,'name':this.uniqueProviders[i]})
      }
    },
    getProviders () {
      for(var i= 0;i <this.courses.length; i++){
        if(this.courses[i].Provider.length > 0){
          this.providers.push(this.courses[i].Provider)
        }
      }
      this.uniqueProviders = [...new Set(this.providers)]
    },
    setSelectedValue (selectedValue) {
      this.selectedValue = selectedValue.display

      this.$emit('selectedProvider', this.selectedValue.toString())
    }
  },
  mounted () {
    this.getProviders(),
    this.setAutcompleteProvidersList()
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
