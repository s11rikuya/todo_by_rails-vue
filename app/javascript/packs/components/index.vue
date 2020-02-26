<template>
  <div>
    <!-- 新規作成部分 -->
    <div class="row">
      <div class="col s10 m11">
        <input v-model="newTask" class="form-control" placeholder="Add your task!!">
      </div>
      <div class="col s2 m1">
        <div v-on:click="createTask" class="btn-floating waves-effect waves-light red">
          <i class="material-icons">add</i>
        </div>
      </div>
    </div>
    <div id="tasks">
      <ul class="collection">
        <li v-for="task in tasks" v-if="!task.is_done" v-bind:id="'row_task_' + task.id" class="collection-item">
          <label>
            <input type="checkbox" v-on:change="doneTask(task.id)" v-bind:id="'task_' + task.id" :checked="task.is_done" />
            <span v-bind:for="'task_' + task.id" class="black-text">{{ task.name }}</span>
          </label>
          <router-link :to="{ name: 'task', params: { id: task.id }}">Show</router-link>
        </li>
      </ul>
    </div>
    <div class="btn" v-on:click="displayFinishedTasks">Display finished tasks</div>
    <div id="finished-tasks" class="display_none">
      <ul class="collection">
        <li v-for="task in tasks" v-if="task.is_done"v-bind:id="'row_task_' + task.id" class="collection-item">
          <label>
            <input type="checkbox" v-on:change="doneTask(task.id)" v-bind:id="'task_' + task.id" :checked="task.is_done" />
            <span v-bind:for="'task_' + task.id" class="line-through">{{ task.name }}</span>
          </label>
          <router-link :to="{ name: 'task', params: { id: task.id }}">Show</router-link>
        </li>
      </ul>
    </div>
  </div>
</template>
<script>
import axios from 'axios';

export default {
  data: function () {
    return {
      tasks: [],
      newTask: ''
    }
  },
  mounted: function () {
    this.fetchTasks();
  },
  methods: {
    fetchTasks: function () {
      axios.get('/api/tasks').then((response) => {
        for(var i = 0; i < response.data.tasks.length; i++) {
          this.tasks.push(response.data.tasks[i]);
        }
      }, (error) => {
        console.log(error);
      });
    },
    displayFinishedTasks: function() {
      document.querySelector('#finished-tasks').classList.toggle('display_none');
    },
    createTask: function () {
      if (!this.newTask) return;

      axios.post('/api/tasks', { task: { name: this.newTask } }).then((response) => {
        this.tasks.unshift(response.data.task);
        this.newTask = '';
      }, (error) => {
        console.log(error);
      });
    },
    doneTask: function (task_id) {
      var el = document.querySelector('#row_task_' + task_id);
      var el_clone = el.cloneNode(true);
      if (el.getElementsByTagName('input')[0].checked == true) {
        axios.put('/api/tasks/' + task_id, { task: { is_done: 1 } }).then((response) => {
          this.moveFinishedTask(task_id);
          console.log('finish');
          console.log(task_id);
        }, (error) => {
          console.log(error);
        });
      } else {
        axios.put('/api/tasks/' + task_id, { task: { is_done: 0 } }).then((response) => {
          this.moveTask(task_id);
          console.log('open');
          console.log(task_id);
        }, (error) => {
          console.log(error);
        });
      }
    },
    moveFinishedTask: function(task_id) {
      var el = document.querySelector('#row_task_' + task_id);
      el.getElementsByTagName('span')[0].classList.add('line-through');
      el.getElementsByTagName('span')[0].classList.remove('black-text');
      var li = document.querySelector('#finished-tasks > ul > li:first-child');
      document.querySelector('#finished-tasks > ul').insertBefore(el, li);
    },
    moveTask: function(task_id) {
      var el = document.querySelector('#row_task_' + task_id);
      el.getElementsByTagName('span')[0].classList.remove('line-through');
      el.getElementsByTagName('span')[0].classList.add('black-text');
      var li = document.querySelector('#tasks > ul > li:first-child');
      document.querySelector('#tasks > ul').insertBefore(el, li);
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
