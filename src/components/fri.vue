<template>
  <div class="f" :style="customStyle">
    <div class="f_head">好友列表</div>
    <div class="f_body">
      <div class="online">
        <div class="online_title">在线</div>
        <div class="online_list"></div>
      </div>
      <div class="offline">
        <div class="offline_title">离线</div>
        <div class="offline_list"></div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'f',
  data () {
    return {}
  },
  props: {
    list: {
      type: Array,
      default: () => []
    },
    customStyle: {
      type: Object,
      default: () => {
        return {}
      }
    }
  },
  computed: {},
  watch: {},
  methods: {},
  created () {},
}
</script>
<style scoped lang='less'>
.f {
  width: 20vw;
  height: 60vh;
  background-color: #fff;
  opacity: 0.6;
  border: 0.2vw solid black;
  border-right-width: 0.25vw;
  border-radius: 1vw;
  &_head {
    width: 100%;
    height: 8vh;
    line-height: 8vh;
    text-align: center;
    .pxfont(40)
  }
  &_body {
    width: 100%;
    height: calc(100% - 8vh);
  }
}
.online, .offline {
  width: 100%;
  height: 50%;
}
</style>
