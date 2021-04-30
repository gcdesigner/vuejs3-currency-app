<template>
  <div class="container grid-lg my-2">
    <div class="card mb-2" v-if="listenQuotes.length > 0">
      <div class="card-header">
        <div class="h4">Acompanhando</div>
      </div>
      <div class="card-body">
        <WatchListQuotes :listen-quotes="listenQuotes" @unlisten="onUnlisten"></WatchListQuotes>
      </div>
    </div>

    <div class="card">
      <div class="card-header">
        <div class="h4">Todas as moedas</div>
      </div>
      <div class="card-body">
        <ListQuotes
          :quotes="quotes"
          :listen-quotes="listenQuotes"
          @listen="onListen"
          @unlisten="onUnlisten"
        />
      </div>
    </div>
  </div>
</template>

<script>
import { onMounted, reactive, toRefs } from 'vue'
import api from '@/services/api'
import ListQuotes from './components/ListQuotes.vue'
import WatchListQuotes from './components/WatchListQuotes.vue'

export default {
  name: 'App',
  components: {
    ListQuotes,
    WatchListQuotes,
  },
  setup() {
    const data = reactive({
      quotes: {},
      listenQuotes: [],
    })

    onMounted(async () => {
      const response = await api.all()
      data.quotes = response.data
    })

    const methods = reactive({
      onListen(code) {
        data.listenQuotes.push(code)
      },

      onUnlisten(code) {
        data.listenQuotes = data.listenQuotes.filter((key) => key !== code)
      },
    })

    return { ...toRefs(data), ...toRefs(methods) }
  },
}
</script>

<style lang="scss"></style>
