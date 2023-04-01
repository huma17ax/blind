<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <p>
      For a guide and recipes on how to configure / customize this project,<br>
      check out the
      <a href="https://cli.vuejs.org" target="_blank" rel="noopener">vue-cli documentation</a>.
    </p>
    <h3>Installed CLI Plugins</h3>
    <ul>
      <li><a href="https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/cli-plugin-babel" target="_blank" rel="noopener">babel</a></li>
      <li><a href="https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/cli-plugin-eslint" target="_blank" rel="noopener">eslint</a></li>
    </ul>
    <h3>Essential Links</h3>
    <ul>
      <li><a href="https://vuejs.org" target="_blank" rel="noopener">Core Docs</a></li>
      <li><a href="https://forum.vuejs.org" target="_blank" rel="noopener">Forum</a></li>
      <li><a href="https://chat.vuejs.org" target="_blank" rel="noopener">Community Chat</a></li>
      <li><a href="https://twitter.com/vuejs" target="_blank" rel="noopener">Twitter</a></li>
      <li><a href="https://news.vuejs.org" target="_blank" rel="noopener">News</a></li>
    </ul>
    <h3>Ecosystem</h3>
    <ul>
      <li><a href="https://router.vuejs.org" target="_blank" rel="noopener">vue-router</a></li>
      <li><a href="https://vuex.vuejs.org" target="_blank" rel="noopener">vuex</a></li>
      <li><a href="https://github.com/vuejs/vue-devtools#vue-devtools" target="_blank" rel="noopener">vue-devtools</a></li>
      <li><a href="https://vue-loader.vuejs.org" target="_blank" rel="noopener">vue-loader</a></li>
      <li><a href="https://github.com/vuejs/awesome-vue" target="_blank" rel="noopener">awesome-vue</a></li>
    </ul>
  </div>
  <button @click="recStart" :disabled="state!=0">Start</button>
  <button @click="recStop" :disabled="state!=1">Stop</button>
  <div>{{error}}</div>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data () {
    return {
      stream: undefined,
      chunks: [],
      recorder: undefined,
      state: 0
    }
  },
  methods: {
    async recStart() {
      this.error = undefined
      this.stream = await navigator.mediaDevices.getUserMedia({
        video: true,
        audio: false
      })

      this.state = 1

      this.recorder = new MediaRecorder(this.stream)
      this.recorder.ondataavailable = (event) => {
        this.chunks.push(event.data)
      }
      this.recorder.onstop = () => {
        const blob = new Blob(this.chunks, {type: 'video/mp4'})
        const url = URL.createObjectURL(blob);

        const anchor = document.createElement('a')
        anchor.download = 'video.mp4'
        anchor.href = url
        anchor.click()

        URL.revokeObjectURL(url)
      }
      this.recorder.start()
    },
    recStop() {
      console.log('atop')
      this.recorder.stop()
      this.state = 0
      this.stream = undefined
      this.recorder = undefined
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
