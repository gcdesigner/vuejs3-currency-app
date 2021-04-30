<template>
  <ListQuotes :quotes="quotes" :listen-quotes="listenQuotes" @unlisten="onUnlisten"></ListQuotes>

  <div class="mt-2 text-right">
    <cite class="text-small">
      Atualizará novamente em <b>{{ nextUpdateTime }} segundos</b>
    </cite>
  </div>
</template>

<script>
import { onMounted, onUnmounted, reactive, toRefs, watch } from 'vue'
import api from '@/services/api'
import ListQuotes from './ListQuotes.vue'

const CURRENT_UPDATE_TIME = 30

export default {
  components: { ListQuotes },

  props: {
    listenQuotes: { type: Array, required: true },
  },

  emits: ['unlisten'],

  setup(props, { emit }) {
    const vars = reactive({
      quotes: {},
      nextUpdateTime: CURRENT_UPDATE_TIME,
      interval: null,
    })

    const methods = reactive({
      onUnlisten(code) {
        emit('unlisten', code)
      },

      restartInterval() {
        clearInterval(vars.interval)
        vars.nextUpdateTime = CURRENT_UPDATE_TIME
        vars.interval = setInterval(() => {
          vars.nextUpdateTime -= 1
          if (vars.nextUpdateTime === 0) {
            vars.nextUpdateTime = CURRENT_UPDATE_TIME
            this.refreh()
          }
        }, 1000)
      },

      async refreh() {
        const { data } = await api.listen(props.listenQuotes)
        vars.quotes = data
      },
    })

    onMounted(() => {
      methods.refreh()
      methods.restartInterval()
    })

    onUnmounted(() => {
      clearInterval(vars.interval)
    })

    watch(props, () => {
      methods.refreh()
      methods.restartInterval()
    })

    return { ...toRefs(vars), ...toRefs(methods) }
  },
}
</script>

<style></style>
