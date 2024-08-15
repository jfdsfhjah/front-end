<template>
    <div id="app">
      <UserManagement />
    </div>
  </template>
  
  <script>
  import UserManagement from './components/UserManagement.vue';
  
  export default {
    name: 'App',
    components: {
      UserManagement
    }
  };
  </script>
  
  <style>
  /* 可选：全局样式 */
  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    text-align: center;
    margin-top: 60px;
  }
  </style>
  