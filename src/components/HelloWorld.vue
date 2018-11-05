<template>
  <div class="index">
    <div class="header">Journey</div>
    <div class="descs">
      <div  class="desc">
        <div class="desc-title">
          <svg class="icon" aria-hidden="true">
            <use xlink:href="#icon-flower"></use>
          </svg>
          每天写一写
        </div>
        <div class="desc-content">
          <div v-for="(i,index) in dailyDescs" class="desc-single" :key="index">
            <div v-if="!i.update">{{i.descs}}</div>
            <div v-else>
              <textarea v-model="i.descs"></textarea>
              <span @click="confirmUpdate(i.descs, i.id)">确认修改</span>
            </div>
            <div class="desc-func">
              <span>{{dateFormat(i.createTime)}}</span>
              <span> ~ </span>
              <span @click="updateDesc(i, index)">编辑</span>
              <span> ~ </span>
              <span @click="deleteDesc(i)">删除</span>
            </div>
          </div>
        </div>
      </div>
      <div class="input-desc">
        <input type="text" v-model="dailyDesc">
        <button @click="inputDesc">确定</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      dailyDescs: [],
      singleDesc: {},
      dailyDesc: ''
    }
  },
  created () {
    this.getDesc()
    console.log(this.dailyDescs)
  },
  methods: {
    getDesc () {
      this.$http({
        method: 'get',
        url: '/DailyDesc'
      }).then(res => {
        this.dailyDescs = res.data
      })
    },
    getDescById (id) {
      return this.$http({
        method: 'get',
        url: `/DailyDesc/${id}`
      }).then(res => res.data)
    },
    dateFormat (time) {
      let d = new Date(time)
      return d.getFullYear() + '-' + (d.getMonth() + 1) + '-' + d.getDate() + ' ' + (d.getHours() < 10 ? '0' + d.getHours() : d.getHours()) + ':' + (d.getMinutes() < 10 ? '0' + d.getMinutes() : d.getMinutes()) + ':' + (d.getSeconds() < 10 ? '0' + d.getSeconds() : d.getSeconds())
    },
    inputDesc () {
      this.$http({
        method: 'post',
        url: '/DailyDesc',
        data: {
          descs: this.dailyDesc
        }
      }).then(res => {
        console.log(res)
        this.getDesc()
      })
    },
    deleteDesc (i) {
      console.log(i)
      this.$http({
        method: 'get',
        url: `/DailyDesc/delete/${i.id}`
      }).then(res => {
        console.log(res)
        this.getDesc()
      })
    },
    updateDesc (i, index) {
      console.log(i)
      this.$set(this.dailyDescs[index], 'update', true)
      console.log(this.dailyDescs[index])
    },
    confirmUpdate (desc, id) {
      this.$http({
        method: 'post',
        url: `/DailyDesc/update`,
        data: {
          descs: desc,
          id: id
        }
      }).then(async res => {
        console.log(res)
        let updatedData = await this.getDescById(id)
        console.log(updatedData)
        for (let i = 0; i < this.dailyDescs.length; i++) {
          if (this.dailyDescs[i].id === id) {
            this.$set(this.dailyDescs[i], 'update', false)
            this.$set(this.dailyDescs[i], 'descs', updatedData.descs)
          }
        }
      })
    }
  },
  computed: {
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.index {
  .header{
    height: 60px;
    line-height: 60px;
    padding-left: 28px;
    font-size: 30px;
    font-style: italic;
    width: 100%;
    background-color: rgba(0,0,0,0.6);
    color: white;
  }
}
.descs{
  display: flex;
  margin: 40px 60px;
  .desc{
    flex: 3;
    display: inline-block;
    .desc-title{

    }
    .desc-content{
      margin: 20px 60px 0 20px;
      .desc-single{
        text-indent: 30px;
        margin: 20px 0 0;
        border-bottom: 1px solid #e3e3e3;
        .desc-func{
          text-align: right;
          padding: 10px 20px 4px;
          color: #524d4d;
          font-size: 12px;
        }
      }
    }
  }
  .input-desc {
    flex: 1;
    display: inline-block;
  }
}
</style>
