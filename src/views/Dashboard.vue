<template>
  <div class="dashboard">
    <h1 class="subtitle-1 grey--text">Dashboard</h1>
    <v-container class="my-5">

      <v-row class="mb-3">

        <v-tooltip top>
          <template v-slot:activator="{ on, attrs }">
            <v-btn v-bind="attrs" v-on="on" small text color="grey" @click="sortBy('title')">
              <v-icon left small>mdi-folder</v-icon>
              <span class="caption text-lowercase">by project name</span>
            </v-btn>
          </template>
          <span>sort by project name</span>
        </v-tooltip>

        <v-tooltip top>
          <template v-slot:activator="{ on, attrs }">
            <v-btn v-bind="attrs" v-on="on" small text color="grey" @click="sortBy('person')">
              <v-icon left small>mdi-account</v-icon>
              <span class="caption text-lowercase">by person</span>
            </v-btn>
          </template>
          <span>sort by person</span>
        </v-tooltip>

      </v-row>
      
      <v-card flat class="px-3" v-for="project in projects" :key="project.title">
        <v-row row wrap :class="`pa-3 project ${project.status}`">
          <v-col cols="12" md="6">
            <div class="caption grey--text">Project Title</div>
            <div>{{ project.title }}</div>
          </v-col>
          <v-col xs="2">
            <div class="caption grey--text">Person</div>
            <div>{{ project.person }}</div>
          </v-col>
          <v-col xs="2">
            <div class="caption grey--text">Due Date</div>
            <div>{{ project.due }}</div>
          </v-col>
          <v-col xs="2">
            <div class="text-right">
              <v-chip small :class="`${project.status} white--text caption my-2`">
                {{ project.status }}
              </v-chip>
            </div>
          </v-col>
        </v-row>
        <v-divider></v-divider>
      </v-card>
    </v-container>
  </div>
</template>

<script>
import db from '@/fb'

export default {
  data() {
    return {
      projects: []
    }
  },
  methods: {
    sortBy(prop){
      this.projects.sort((a,b) => a[prop] < b[prop] ? -1 : 1)
    } 
  },
  created() {
    db.collection('projects').onSnapshot(res => {
      const changes = res.docChanges();

      changes.forEach(change => {
        if (change.type === 'added'){
          this.projects.push({
            ...change.doc.data(),
            id: change.doc.id
          })
        }
      });
    })
  }
}
</script>

<style>

.project.complete{
  border-left: 4px solid #3CD1C2;
}

.project.ongoing{
  border-left: 4px solid orange;
}

.project.overdue{
  border-left: 4px solid tomato;
}

.v-chip.v-chip--no-color.theme--light.complete{
  background: #3CD1C2;
}

.v-chip.v-chip--no-color.theme--light.ongoing{
  background:  orange;
}

.v-chip.v-chip--no-color.theme--light.overdue{
  background: tomato;
}

</style>
