<template>

  <div class="task" >
    <div class="header">
      <div class="completeness">
        <button class="btn_complete">
          <span class="iconfont">&#xe83c;</span>
          <span>{{this.checked}}</span>
          /
          <span>{{this.unchecked}}</span>
        </button>
      </div>
      <span class="task_title">任务</span>
      <div class="more" @click="showSetting">
        <button  class="iconfont">&#xe842;</button>
      </div>
      <!--<div class="switch">-->
        <!--<button  class="btn_switch iconfont">&#xe844;</button>-->
      <!--</div>-->
    </div>

    <div class="moreSetting"  v-if="settingVisable">
       <div @click="settingCancel">
        <span class="iconfont">&#xe881;</span>
        <span>隐藏 (显示) 已完成</span>
      </div>
      <!--下个版本迭代-->
      <!--<div>-->
        <!--<span class="iconfont">&#xe841;</span>-->
        <!--<span>按标签分类</span>-->
      <!--</div>-->
    </div>
<!--任务渲染模块开始-->
    <div class="default" v-if="defaultBg">
      <!--默认的背景背景图，提醒用户添加任务-->
      <img src="../../assets/images/add.png" >
    </div>

    <div class="list">
      <ul>
          <li v-for="(item,index) in unfinishDate"  class="unfinishList" :key="index">
            <a href="#" :title="item.priority">
              <input id="radio1" class="radio1" type="radio" hidden >
              <label @click="removeList(index, item.taskId)" for="radio1" class="unCheck" ></label>
              <span class="taskTitle">{{item.title}}</span>
              <span class="taskTime">{{item.startDate}}</span>
              <span class="taskLabel">{{item.label}}</span>
              <i @click="delUncheck(item.title, index, item.taskId)">删除</i>
            </a>
          </li>
      </ul>
      <ul>
        <li v-for="(item, index) in finishDate"  class="complete" v-if="finishDisplay" :key="index">
          <a href="#" >
            <input id="radio2" class="radio1" type="radio"  hidden checked>
            <label @click="addList(index, item.taskId)" for="radio2" class="check" ></label>
            <span class="taskTitle">{{item.title}}</span>
            <!--<span class="taskTime">{{item.startDate}}</span>-->
            <!--<span class="taskLabel">{{item.label}}</span>-->
            <i @click="delCheck(item.title, index, item.taskId)">删除</i>
          </a>
        </li>
      </ul>
    </div>

    <div class="add">
      <button @click="addEvent()" class="btn_add iconfont">
        <span>&#xe64d;</span>
      </button>
    </div>

    <div class="settingBarrier" v-if="settingVisable"></div>

  </div>

</template>

<script>
  export default {
    name: "task",
    data () {
      return {
        settingVisable: false,  //设置栏显隐
        unfinishDate: [],
        finishDate: [],
        unchecked: 0,
        checked: 0,
        checkedList: [],
        finishDisplay: true,
        token: localStorage.jwtToken,
        expansion: null,
        defaultBg: true,  //默认的背景图，提醒用户添加任务
      }
    },
    methods: {
      addEvent () {
        this.$router.push({path: '/Task/add'})
      },
      renderList (res) {
          if(res.data.status){
            console.log("获取任务列表成功")
            this.unfinishDate = res.data.data.unfinish
            this.finishDate = res.data.data.finish
            this.unchecked = this.unfinishDate.length +  this.finishDate.length
            this.checked = this.finishDate.length
          }
          // this.elDisply = true //渲染完毕勾选立即显示
      },

      settingCancel () {
        this.settingVisable = false
        this.finishDisplay = !this.finishDisplay
      },
      open (picker) {
        this.$refs[picker].open();
      },

      removeList (index, taskId) {
      //把添加的任务勾选为已完成
        let lists = document.querySelectorAll(".unfinishList")
        lists[index].style.opacity = '0.5'
        setTimeout( () => {
          this.finishDate.push(this.unfinishDate[index])
          this.unfinishDate.splice(index,1)
          this.checked = this.finishDate.length
        },800)
        let data = {"taskId" : taskId}
        this.axios({
          method: 'post',
          data: data,
          url: '/api/task/finish',
          headers: {
            Authorization: this.token
          }
        })
      },
      addList (index,taskId) {
        let lists = document.querySelectorAll(".complete")
        lists[index].style.opacity = '0.5'
        setTimeout(()=> {
          this.unfinishDate.push(this.finishDate[index])
          this.finishDate.splice(index,1)
          this.checked = this.finishDate.length
        },800)
        let data = {"taskId" : taskId}
        this.axios({
          method:'post',
          url:'/api/task/unfinish',
          data:data,
          headers: {
            Authorization: this.token
          }
        })
      },
      delUncheck: function(name, idx, taskId){
        if(confirm("确认删除"+ name)){
          this.unfinishDate.splice(idx,1);                                 //删除List这条数据 DOM随之更新渲染
          var container = document.querySelector('.swipeleft');           //将展开的DOM归位 除掉样式类
          container.className="";
          this.expansion = null;
          let data = {"taskId" : taskId}
          this.axios({
            method:'delete',
            url:'/api/task/delete',
            data: data,
            headers: {
              Authorization: this.token
            }
          }).then(this.count).catch(function () {
            alert("删除失败")
          })
        }
      },
      count (res) {
        if (res.data.status){
          this.unchecked = this.unfinishDate.length +  this.finishDate.length
          this.checked = this.finishDate.length
          setTimeout( ()=> {
            alert("删除成功")
          })
        }
      },
      showSetting () {
        this.settingVisable = !this.settingVisable
      },
      delCheck: function(name, idx, taskId){
        if (confirm("确认删除"+ name)) {
          this.finishDate.splice(idx, 1);                                 //删除List这条数据 DOM随之更新渲染
          var container = document.querySelector('.swipeleft');           //将展开的DOM归位 除掉样式类
          container.className= "";
          this.expansion= null;
          let data = {"taskId" : taskId}
          this.axios({
            method: 'delete',
            url: '/api/task/delete',
            data: data,
            headers: {
              Authorization: this.token
            }
          }).then(this.count).catch(function () {
            alert("删除失败")
          })
        };
      },
      //验证token是否已过期
      loginFail () {
        alert("登录已过期，请重新登录")
        this.$router.push({path:'/User/login'})
      }
    },
    created () {
      console.log("created")
      console.log("state: ",this.$store.state.data) //测试state
      setTimeout(()=>{
        if (this.unchecked === 0) {
          this.defaultBg = true
        } else {
          this.defaultBg = false
        }
      }, 200)
      this.axios({
        method: 'get',
        url: '/api/task/list',
        headers: {
          Authorization: this.token
        }
      }).then(this.renderList).catch(this.loginFail)
    },
    updated () {
      setTimeout(()=>{
        if (this.unchecked === 0) {
          this.defaultBg = true
        } else {
          this.defaultBg = false
        }
      }, 200)
      setTimeout( () => {
        var $this = this;														//将$this保存 区分以下触发事件的this
        var container = document.querySelectorAll('.list li a');
        for(var i = 0; i < container.length; i++){                          //为每个特定DOM元素绑定touchstart touchmove时间监听 判断滑动方向
          var x,  X;
          container[i].addEventListener('touchstart', function(event) {   //记录初始触控点横坐标
            x = event.changedTouches[0].pageX;
          });
          container[i].addEventListener('touchmove', function(event){
            X = event.changedTouches[0].pageX;                          //记录当前触控点横坐标
            if($this.expansion){                                       //判断是否展开，如果展开则收起
              $this.expansion.className = "";
            }
            if(X - x > 10){                                             //右滑
              this.className = "";                                    //右滑收起
            }
            if(x - X > 10){                                             //左滑
              this.className = "swipeleft";                           //左滑展开
              $this.expansion = this;
            }
          });
        }
      },1000)
      //给紧急的任务 选项框与蓝色，加以识别
      var lis = document.querySelectorAll(".unfinishList")
      for (var i =0; i<lis.length; i++){
        if( lis[i].firstChild.getAttribute("title") === "1"){
          lis[i].firstChild.childNodes[2].style.backgroundImage = "url(http://images.fucheng360.top/unCheckP.png)"
        }
      }
    }
  }
</script>
<style lang="stylus" scoped>
  .iconfont{
    font-size: 25.5px;
  }
  a{
    color: #393939;
    text-decoration: none;
  }
  .default img{
    padding-top: 30px;
    width: 10rem;
    height: 456px;
  }
  .list{
    overflow: hidden;
    margin-top: 1.2rem;
    padding-bottom: 1rem;
    z-index: -1;
    /*padding-left:.3rem;*/
  }
  .list ul{
    float: left;
  }
  .list li{
    overflow: hidden;
    width: 120%;
    height: 66px;
    line-height: 66px;
    list-style: none;
  }
  .list li a{
    display: block;
    -webkit-transition: all 0.3s;
    transition:all 0.3s;
    position: relative;
    width: 10rem;
  }
  .list li i{
    position: absolute;
    right: -16%;
    width: 15%;
    text-align: center;
    background: #E2421B;
    font-style: normal
    color: #fff;
  }
  .swipeleft{
    transform: translateX(-15%);
    -webkit-transform: translateX(-15%);
  }
  ul li.complete {
    transition 0.8s all
    margin-top 10px
    margin-bottom 10px
  }
  .complete a span{
    text-decoration: line-through;
  }
  .complete .taskTitle{
    color: #C0C0C0;
  }
  .btn_complete .iconfont{
    font-size: 20px;
  }
  .task {
    position: absolute;
    top: 0;
    bottom: 1rem;
    left: 0;
    right: 0;
    font-family: PingFangSC-Semibold, sans-serif;
    max-width: 420px;
    margin: 0 auto;
    overflow hidden
  }
  .header {
    position: fixed;
    width: 9.4rem;
    height: 1rem;
    line-height: 1rem;
    padding 6px 0.3rem 4px 0.3rem
    text-align: center;
    font-size: 18px;
    background: white;
    border-bottom: 1px solid #f3f3f3;
    box-shadow: 0px 1px 1px #f7f7f714;
  }
  .header div {
    display: inline-block;
  }
  .header button {
    border: 0;
    background: white;
  }
  .completeness {
    float: left;
  }
  .task_title {
    font-size: 24px;
  }
  .more{
    float: right;
    font-size: 25px;
  }
 .list .unfinishList {
    transition 0.8s all
 }
 .unCheck {
    height 20px
    width 20px
    display inline-block
    background-image url('../../assets/images/unCheck.png')
    background-size 20px 20px
    background-repeat: no-repeat
    background-position: center
    vertical-align: middle
    margin-top -4px
    margin-left 10px
 }
  .check {
    height 20px
    width 20px
    display inline-block
    background-image url('../../assets/images/check.png');
    background-size 20px 20px
    background-repeat no-repeat;
    background-position center;
    vertical-align middle;
    margin-bottom 9px
    margin-left 10px
  }
  .taskTitle{
    margin-left 20px
    font-size: 20px;
    color: #242921;
  }
  .taskTime{
    float: right;
    font-size: 14px;
    padding-right: 10px;
    color: #999999;
  }
  .taskLabel{
    position: absolute;
    font-size: 14px;
    top: 23px;
    left: 58px;
    height: 16px;
    color: #c0c0c0;
  }
  .add{
    position: fixed;
    right: 1.2rem;
    bottom: 2rem;
    font-size: 30px;
  }
  .add button{
    border: 0;
    background: #fff;
    color: #0082ff;
    font-size: 45px;
  }
  .add span{
    border: 0;
    padding: 0;
    border-radius: 50%;
    text-shadow: 0px 1px 1px #999999;
  }
  .settingBarrier{
    background: rgba(192,192,192,0.2);
    position: absolute;
    top: 0;
    bottom:0;
    left: 0;
    right: 0;
    z-index: 10;
  }
  .timeLength{
    position: absolute;
    top: 420px;
    left: -300px;
    display: flex;
    justify-content: flex-start;
  }
  .moreSetting{
    position: absolute;
    width: 5rem;
    background: white;
    right: 0;
    top: 1.2rem;
    font-size: 16px;
    z-index:999;
    overflow: auto;
  }
  .moreSetting :first-child{
    margin-top: 14px;
  }
  .moreSetting div{
    float: left;
    margin-left: 14px;
    margin-bottom: 14px;
  }
</style>
