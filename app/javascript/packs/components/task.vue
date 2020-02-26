<template>
  <div class="row">
    <div class="col s12 m12">
      <div class="card">
        <div class="card-content">
          <span class="card-title">{{task.name}}</span>
          <p>I am a very simple card. I am good at containing small bits of information.
          I am convenient because I require little markup to use effectively.</p>
        </div>
        <div class="card-action">
          <a href="#">This is a link</a>
          <a href="#">This is a link</a>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import axios from 'axios';

export default {
  data: function () {
    return {
      task: ''
    }
  },
  mounted: function () {
    this.fetchTask();
  },
  methods: {
    fetchTask: function () {
      axios.get('/api/tasks/' + this.$route.params.id).then((response) => {
        this.task = response.data.task
      }, (error) => {
        console.log(error);
      });
    }
  }
}
</script>

<style scoped>
[v-cloak] {
  display: none;
}
.display_none {
  display:none;
}
.line-through {
  text-decoration: line-through;
}
</style>
