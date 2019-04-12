<template>
  <div>
    <h4>Subjects</h4>
    <autocomplete
    :source="autocompleteSubjectsList"
    @selected="setSelectedValue">
    </autocomplete>
    <!-- <div v-for="subject in uniqueSubjects">
      <input type="checkbox" name="provider-name"/>
      <label>{{subject}}</label>
    </div> -->
  </div>
</template>
<script>
import Autocomplete from 'vuejs-auto-complete'
export default{
  props: ['courses'],
  data () {
    return {
      subjects: [],
      uniqueSubjects: [],
      key: ['Parent Subject'],
      autocompleteSubjectsList: [],
      selectedValue: ''
    }
  },
  methods : {
    getSubjects () {
      for (var i= 0;i < this.courses.length; i++) {
        this.course = this.courses[i]
        if (this.course['Parent Subject'].length > 0) {
          this.subjects.push(this.course['Parent Subject'])
        }
      }
      this.uniqueSubjects = [...new Set(this.subjects)]
    },
    setAutoCompleteSubjectsList () {
      for (var i = 0; i < this.uniqueSubjects.length; i++) {
        this.autocompleteSubjectsList.push({'id':i ,'name':this.uniqueSubjects[i]})
      }
    },
    setSelectedValue (selectedValue) {
      this.selectedValue = selectedValue.display
      this.$emit('selectedSubject', this.selectedValue.toString())
    }
  },
  mounted () {
    this.getSubjects (),
    this.setAutoCompleteSubjectsList ()
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
