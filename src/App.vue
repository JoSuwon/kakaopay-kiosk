<template>
  <div id="app">
    <button @click="pay(1000)">1000원 결제</button>
    <div id="nav">
      <router-link to="/">Home</router-link> |
      <router-link to="/about">About</router-link>
    </div>
    <router-view />
  </div>
</template>

<script>
import axios from 'axios';
import { ipcRenderer } from 'electron';

export default {
  mounted() {
    ipcRenderer.on('kakaoPayCancel', () => {
      console.log('결제 취소');
    });

    ipcRenderer.on('kakaoPayFinish', () => {
      console.log('결제 완료');
    });
  },
  methods: {
    async pay(amount) {
      const res = await axios({
        timeout: 3000,
        method: 'POST',
        url: 'http://localhost:3000/ready',
        data: {
          amount,
        },
      });

      const payURL = res.data.next_redirect_pc_url;
      const tid = res.data.tid;
      ipcRenderer.invoke('kakaoPay', payURL, tid);
    },
  },
}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#nav {
  padding: 30px;

  a {
    font-weight: bold;
    color: #2c3e50;

    &.router-link-exact-active {
      color: #42b983;
    }
  }
}
</style>
