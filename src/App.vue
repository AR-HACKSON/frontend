<template>
  <div>
    <transition name="starting">
      <StartingUp v-if="starting" @start="start" />
    </transition>

    <div class="desktop">
      <div class="app" @click="open('aSearch')">
        <img src="@/assets/icon/Search.png" />
        <span>Search</span>
      </div>
      <a class="app" @click="connectWallet">
        <img src="@/assets/icon/Meta.png" />
        <span>Wallet</span>
      </a>
      <a class="app">
        <img src="@/assets/icon/Covid.png" />
        <span>Covid</span>
      </a>
      <a class="app">
        <img src="@/assets/icon/Docs.png" />
        <span>Docs</span>
      </a>
      <a class="app" href="mailto:team@asearch.io">
        <img src="@/assets/icon/Mail.png" />
        <span>Mail-us</span>
      </a>
      <a class="app" href="https://github.com/asearch-AR" target="_blank">
        <img src="@/assets/icon/Github.png" />
        <span>Github</span>
      </a>
      <a
        class="app"
        href="https://medium.com/@kristiancy/web3-search-engine-in-the-future-5b5ad48e55d1"
        target="_blank"
      >
        <img src="@/assets/icon/Medium.png" />
        <span>Medium</span>
      </a>
      <a class="app" href="https://twitter.com/Asearch_io" target="_blank">
        <img src="@/assets/icon/Twitter.png" />
        <span>Twitter</span>
      </a>
      <a class="app" href="https://discord.com" target="_blank">
        <img src="@/assets/icon/Dis.png" />
        <span>Discord</span>
      </a>

      <!--      <a-->
      <!--        v-for="item in links"-->
      <!--        :key="item.name"-->
      <!--        class="app"-->
      <!--        :href="item.url"-->
      <!--        target="_blank"-->
      <!--      >-->
      <!--        <img src="" />-->
      <!--        <span>{{ item.name }}</span>-->
      <!--      </a>-->

      <!-- BACKGROUND -->
      <img class="logo" src="@/assets/textlogo.png" />
    </div>

    <div class="as-taskbar">
      <div class="start" @click="starting = true">
        <img src="@/assets/logo.png" />
      </div>
      <div class="minimized">
        <span v-for="(item, i) in minimized" :key="i" @click="reopen(item)">
          {{ item }}
        </span>
      </div>
    </div>

    <!-- WINDOWS -->
    <transition name="window">
      <keep-alive>
        <Window v-if="show" @close="close" @minimize="minimize" />
      </keep-alive>
    </transition>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import { ref, onMounted, defineAsyncComponent } from '@vue/runtime-core'
import Window from '@/components/Window.vue'
import StartingUp from '@/components/StartingUp.vue'

const address = ref('')
const isMetamaskSupported = ref(false)
onMounted(() => {
  isMetamaskSupported.value = typeof (window as any).ethereum !== 'undefined'
})

export default defineComponent({
  name: 'App',
  components: {
    Window,
    StartingUp,
  },
  data() {
    return {
      starting: true,
      show: false,
      minimized: [] as string[],
    }
  },
  methods: {
    start() {
      this.starting = false
      setTimeout(() => {
        this.reopen('aSearch')
      }, 500)
    },
    open(name: string) {
      this.minimized = this.minimized.filter((item) => item != name)
      this.show = true
    },
    reopen(name: string) {
      this.minimized = this.minimized.filter((item) => item != name)
      this.show = true
    },
    close() {
      this.show = false
    },
    minimize() {
      this.show = false
      this.minimized.push('aSearch')
    },
    async connectWallet() {
      if (!isMetamaskSupported) {
        alert('Please install Metamask extension.')
      } else {
        const accounts = await (window as any).ethereum.request({
          method: 'eth_requestAccounts',
        })
        address.value = accounts[0]
      }
    },
  },
})
</script>

<style lang="stylus">
.window-enter-from
  transform scale(0) translateY(20px)
  opacity 0
.window-enter-active
  transition all .16s ease-out

.starting-enter-active
  animation shake .16s
.starting-leave-to
  background-position-y 100% !important
  opacity 0
.starting-leave-active
  transition background-position-y .3s, opacity .2s .1s

@keyframes shake
  0%
    opacity 0
    transform translateX(0)
  10%, 90%
    transform translateX(-6px)
  50%
    transform translateX(-10px)
  30%, 70%
    transform translateX(8px)
  100%
    opacity 1
    transform translateX(0)
</style>

<style lang="stylus" scoped>
.desktop
  position absolute
  top 0
  left 0
  right 0
  bottom 40px
  padding 24px
  display grid
  grid-auto-flow column
  grid-template-rows repeat(auto-fill, 100px)
  grid-template-columns repeat(auto-fill, 84px)
  gap 6px
  background linear-gradient(to top, #3e5fa940, #1c9eb32b), linear-gradient(-225deg, #35ded4, #05232c)
  .app
    padding 3px
    border dashed 1px transparent
    width 84px
    height fit-content
    display flex
    flex-direction column
    align-items center
    transition all .1s
    user-select none
    &:hover
      background-color #0002
      border dashed 1px #0003
    > img
      width 56px
      height 56px
      //background-color #eee
    > span
      margin-top 4px
      font-size 16px
      line-height 16px
      color #fff
      text-shadow 0 0 2px #000
      word-break break-all
      text-overflow ellipsis
      display -webkit-box
      -webkit-box-orient vertical
      -webkit-line-clamp 2
      overflow hidden
  .logo
    position absolute
    top 50vh
    left 50vw
    width 704px
    height 137px
    transform translate3d(-50%, -50%, 0)
    opacity .1
    user-select none

.as-taskbar
  position absolute
  left 0
  right 0
  bottom 0
  height 40px
  background-image linear-gradient(to left, #25394a, #4b8a96)
  display flex
  z-index 1000
  .start
    width 64px
    height 100%
    background-color #fff0
    display flex
    justify-content center
    align-items center
    font-size 24px
    color var(--gray)
    &:hover
      background-color #fff
  .minimized
    margin-left 18px
    display flex
    > span
      margin 0 4px
      width 160px
      padding 8px
      height 100%
      display flex
      align-items center
      transition background .1s
    > span:hover
      background-color #0001
</style>
