<template>
  <div class="wrapper">
    <div class="header">
        <span @click="goBack" class="iconfont">&#xe600;</span>
      <span>关于</span>
    </div>
    <img class="haowai" src="../../assets/images/号外.jpg">
    <div class="aboutContent">
      <h2>团队介绍及开发需求</h2>
      <p>你好哇，我们终于见面啦~
        经过小组四个人两个星期夜以继日的辛勤劳作（码农本农QAQ），最终我们的第一个小组前端项目--"时现"终于和大家见面了！！
        虽然时间不能重现，但是我们要做的就是把握住现在的每分每秒，做好时间规划，列好待办事项清单，完成一个个的小计划。
        这就是我们这款产品的设计初衷：为每一个忙绿的你记录待办事项，记录成长。
        一个个待办事项的完成见证着我们成长的一步步脚印，我们一起加油吧！
        感谢四位小组成员为此项目的辛勤付出：张看起 郑宇杰 刘芳林 傅诚！奥力给！！
      </p>
    </div>
    <img class="bg" src="../../assets/images/时现logoBg.png">
  </div>
</template>

<script>
  export default {
    // name: "about",
    methods: {
      goBack() {
        this.$router.go(-1)
      }
    }
  }
</script>

<style lang="stylus" scoped>
  .wrapper
    max-width 420px
    margin 0 auto
    .header
      position relative
      height 1rem
      line-height: 1rem
      text-align: center
      font-size: 24px
      font-weight: bold
      .iconfont
        position absolute
        left 0
        padding-left 10px
        font-size 24px
    .haowai
      width 100%
      height 153px
    .aboutContent
      max-width: 420px
      width 8.4rem
      margin 0 auto
      font-size 14px
      white-space pre-wrap  //使文本保持原来的格式，不被隐藏，又有换行等
      word-break break-all
      line-height 24px
      h2
       margin-bottom -10px
       margin-top 10px
       font-size 23px
       color #0082FF
    .bg
      position fixed
      left 0
      right 0
      top 7rem
      width 205px
      height 270px
      margin 0 auto
</style>
