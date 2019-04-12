<template>
  <div class="container">
    <div class="error" v-if="errored">
      <p>Opps Something went wrong!</p>
    </div>
    <div v-if="loading" class="loading">
    </div>
    <button class="all-filter-btn" @click="showFilters">All Filters</button>
    <div v-if="!loading" class="left" id="filters">
      <div class="row filter-row">
        <h3 class="left-headings" v-if="dataFiltered">Active Filters</h3>
        <p v-if="provider.length > 0" class="filter-show"><strong>Provider Selected: </strong>{{provider}}  <span class="clear-all-btn" @click="clearAll">X</span></p>
        <p v-if="subject.length > 0" class="filter-show"><strong>Primary Subject Selected: </strong>{{subject}} <span class="clear-all-btn" @click="clearAll">X</span></p>
        <p v-if="university.length > 0" class="filter-show"><strong>University Selected:  </strong>{{university}} <span class="clear-all-btn" @click="clearAll">X</span></p>
        <h3 class="left-headings">Filters</h3>
        <!-- <span class="clear-all-btn" @click="clearAll">Clear All</span> -->
      </div>
      <div class="row">
        <my-providers :courses="courses" @selectedProvider="selectedProvider"></my-providers>
        <my-subjects :courses="courses" @selectedSubject="selectedSubject"></my-subjects>
        <my-universities :courses="courses" @selectedUniversity="selectedUniversity"></my-universities>
      </div>
      <div class="row">
        <h3 class="left-headings">Sort By</h3>
        <p class="sort-item" @click="sortData('length')" v-bind:class="{bold: islengthActive}">Lenght</p>
        <p class="sort-item" @click="sortData('date')" v-bind:class="{bold: isdateActive}">Next Session Date</p>
      </div>
      <div class="row">
        <button class="reset-all-btn" @click="resetAll">Reset All</button>
      </div>
    </div>
    <div class="right" v-if="!loading">
      <div v-if="dataFiltered">
        <h4 class="results-found">Total Results found: {{ filteredDatas.length }} </h4>
        <h2 class="title">{{ title }}</h2>
        <div class="row list-item" v-for="(filteredData, index) in filteredDatas" v-if="index <= indexLimit">
          <h2 v-if="filteredData['Course Name'].length > 0">
            {{ filteredData['Course Name'] }}
          </h2>
          <p v-if="filteredData['Provider'].length > 0">Provider:
            <strong>
              {{ filteredData['Provider'] }}
            </strong>
            <span v-if="filteredData['Universities'].Institutions.length > 0">
              by
              <strong>
                {{ filteredData['Universities'].Institutions }}
              </strong>
            </span>
          </p>
          <p v-if="filteredData['Parent Subject'].length > 0">
            <strong>Parent Subject: </strong>
              {{ filteredData['Parent Subject'] }}
          </p>
          <p v-if="filteredData['Child Subject'].length > 0">
            <strong>Child Subject: </strong>
              {{ filteredData['Child Subject'] }}
          </p>
          <p v-if="filteredData['Next Session Date'].length > 0">
            <strong>Start Date: </strong>
              {{ filteredData['Next Session Date'] }}
          </p>
          <p v-if="filteredData['Length'] > 0">
            <strong>Course Length: </strong>
              {{ filteredData['Length'] }}
          </p>
          <div class="btn-div">
            <button v-if="filteredData['Url'].length > 0">
              <a v-bind:href="filteredData['Url']">Course Link</a>
            </button>
            <button v-if="filteredData['Video(Url)'].length > 0">
              <a v-bind:href="filteredData['Video(Url)']">Course Demo</a>
            </button>
          </div>
        </div>
        <button @click="loadMore" class="load-more-btn">Load More .. </button>
      </div>
      <div v-if="!dataFiltered">
        <h4 class="results-found">Total Results found: {{ courses.length }} </h4>
        <h2 class="title">{{ title }}</h2>
        <div class="row list-item" v-for="(course, index) in courses" v-if="index <= indexLimit">
          <h2 v-if="course['Course Name'].length > 0">
            {{ course['Course Name'].trim() }}
          </h2>
          <p v-if="course['Provider'].length > 0">Provider:
            <strong>
              {{ course['Provider'] }}
            </strong>
            <span v-if="course['Universities'].Institutions.length > 0">
              by
              <strong>
                {{ course['Universities'].Institutions }}
              </strong>
            </span>
          </p>
          <p v-if="course['Parent Subject'].length > 0">
            <strong>Parent Subject: </strong>
              {{ course['Parent Subject'] }}
          </p>
          <p v-if="course['Child Subject'].length > 0">
            <strong>Child Subject: </strong>
              {{ course['Child Subject'] }}
          </p>
          <p v-if="course['Next Session Date'].length > 0">
            <strong>Start Date: </strong>
              {{ course['Next Session Date'] }}
          </p>
          <p v-if="course['Length'] > 0">
            <strong>Course Length: </strong>
              {{ course['Length'] }}
          </p>
          <div class="btn-div">
            <button v-if="course['Url'].length > 0">
              <a v-bind:href="course['Url']">Course Link</a>
            </button>
            <button v-if="course['Video(Url)'].length > 0">
              <a v-bind:href="course['Video(Url)']">Course Demo</a>
            </button>
          </div>
        </div>
        <button @click="loadMore" class="load-more-btn">Load More .. </button>
      </div>
    </div>
  </div>
</template>

<script>
import uniq from 'lodash/uniq'
import providers from './providers'
import universities from './universities'
import subjects from './subjects'
export default {
  data () {
    return {
      courses: [],
      indexLimit: 10,
      provider: '',
      subject: '',
      university: '',
      dataFiltered: false,
      filteredDatas: [],
      sortedData: [],
      datas: [],
      loading: true,
      errored: false,
      title: 'All Courses',
      islengthActive: false,
      isdateActive: false,
      filtesVisible: false
    }
  },
  mounted () {
    const baseURI = 'https://api.myjson.com/bins/1fq8pm'
    this.$http.get(baseURI)
    .then((result) => {
      this.courses = result.data
    })
    .catch(error => {
      console.log(error)
      this.errored = true
    })
    .finally(() => this.loading = false)
  },
  methods: {
    fetchData () {
      const baseURI = 'https://api.myjson.com/bins/1fq8pm'
      this.$http.get(baseURI)
      .then((result) => {
        this.courses = result.data
      })
      .catch(error => {
        console.log(error)
        this.errored = true
      })
      .finally(() => this.loading = false)
    },
    selectedProvider (event) {
      this.filteredDatas = []
      this.provider = event
      this.subject = ''
      this.university = ''
      this.dataFiltered = true
      this.title = 'Courses with Provider - ' + this.provider
      for (var i = 0; i < this.courses.length; i++) {
        if (this.courses[i].Provider === this.provider) {
          this.filteredDatas.push(this.courses[i])
        }
      }
    },
    selectedSubject (event) {
      this.filteredDatas = []
      this.subject = event
      this.provider = ''
      this.university = ''
      this.dataFiltered = true
      this.title = 'Courses with Parent Subject - ' + this.subject
      for (var i = 0; i < this.courses.length; i++) {
        var course = this.courses[i]
        if (course['Parent Subject'] === this.subject) {
          this.filteredDatas.push(this.courses[i])
        }
      }
    },
    selectedUniversity (event) {
      this.filteredDatas = []
      this.university = event
      this.provider = ''
      this.subject = ''
      this.dataFiltered = true
      this.title = 'Courses with University - ' + this.university
      for (var i = 0; i < this.courses.length; i++) {
        if (this.courses[i].Universities.Institutions === this.university) {
          this.filteredDatas.push(this.courses[i])
        }
      }
    },
    clearAll () {
      this.subject = ''
      this.provider = ''
      this.university = ''
      this.dataFiltered = false
      this.title = 'All Courses'
    },
    compareDates () {
       return Date.parse(this.courses[0]['Next Session Date'])
    },
    sortData (d) {
      var data
      if(this.dataFiltered) {
        this.datas = this.filteredDatas
      }else {
        this.datas = this.courses
      }
      if(d === 'length') {
        this.islengthActive = !this.islengthActive
        this.isdateActive = false
        this.datas.sort(function compare(a, b) {
          // Use toUpperCase() to ignore character casing
          const genreA = a.Length
          const genreB = b.Length

          let comparison = 0
          if (genreA > genreB) {
            comparison = 1
          } else if (genreA < genreB) {
            comparison = -1
          }
          return comparison * -1
        });
        this.datas = this.courses
      } else if (d === 'date') {
        this.isdateActive = !this.isdateActive
        this.islengthActive = false
        this.datas.sort(function compare (a, b) {
          // Use toUpperCase() to ignore character casing
          let comparison = 0
          if (!isNaN(a['Next Session Date']) && !isNaN(b['Next Session Date'])) {
            const genreA = a['Next Session Date']
            const genreB = b['Next Session Date']
            if (!isNaN(Date.parse(genreA)) && !isNaN(Date.parse(genreA))) {
              if (genreA > genreB) {
                comparison = 1
              } else if (genreA < genreB) {
                comparison = -1
              }
            }
          } else if (!isNaN(b['Next Session Date']) && isNaN(a['Next Session Date'])) {
            comparison = -1
          } else if (isNaN(b['Next Session Date']) && !isNaN(a['Next Session Date'])) {
            comparison = 1
          }
          return comparison * -1
        });
      }
    },
    loadMore () {
      if(this.dataFiltered) {
        if((this.indexLimit + 10) < this.filteredDatas.length) {
          this.indexLimit += 10
        }else {
          this.indexLimit = this.filteredDatas.length
        }
      } else {
        if ((this.indexLimit + 10) < this.courses.length) {
          this.indexLimit += 10
        } else {
          this.indexLimit = this.courses.length
        }
      }
    },
    resetAll () {
      this.subject = ''
      this.provider = ''
      this.university = ''
      this.dataFiltered = false
      this.title = 'All Courses'
      this.islengthActive = false
      this.isdateActive = false
      this.loading = true;
      this.fetchData()
    },
    showFilters () {
      if (this.filtesVisible) {
        var filters = document.getElementById('filters');
        filters.style.display = 'none'
        this.filtesVisible = false
      } else {
        var filters = document.getElementById('filters');
        filters.style.display = 'block'
        this.filtesVisible = true
      }
    }
  },
  components: {
    'my-providers': providers,
    'my-universities': universities,
    'my-subjects': subjects
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
