<template>
  <div class="city_body">
    <div class="city_list">
      <div class="city_hot">
        <h2>热门城市</h2>
        <ul class="clearfix">
          <li v-for="item in hotList" :key="item.cityId">{{item.name}}</li>
        </ul>
      </div>
      <div class="city_sort" ref="city_sort">
        <div v-for="item in cityList" :key="item.index">
          <h2>{{item.index}}</h2>
          <ul>
            <li v-for="data in item.list" :key="data.cityId">{{data.name}}</li>
          </ul>
        </div>
      </div>
    </div>
    <div class="city_index">
      <ul>
        <li v-for="(item,index) in cityList" :key="item.index" @touchstart="handleToIndex(index)">{{item.index}}</li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: 'City',
  data() {
    return {
      cityList: [],
      hotList: []
    }
  },
  mounted() {
    this.axios({
      url: 'https://m.maizuo.com/gateway?k=2919183',
      headers: {
        'X-Client-Info': '{"a":"3000","ch":"1002","v":"5.0.4","e":"16163307772462618283409409","bc":"150700"}',
        'X-Host': 'mall.film-ticket.city.list'
      }
    }).then(res => {
      if(res.status === 200){
        var cities = res.data.data.cities
        // [ { index:'A', list: [ {name: '', cityId: } ] } ]
        var {cityList, hotList} = this.formatCityList(cities)
        this.cityList = cityList
        this.hotList = hotList
      }
    })
  },
  methods: {
    handleToIndex(index){
      var h2 = this.$refs.city_sort.getElementsByTagName('h2')
      this.$refs.city_sort.parentNode.scrollTop = h2[index].offsetTop
    },
    formatCityList(cities){
      var cityList = []
      var hotList = []
      for(var i = 0; i < cities.length; i++){
        if(cities[i].isHot === 1){
          hotList.push(cities[i])
        }
        var firstLetter = cities[i].pinyin[0].toUpperCase()
        var exist = false
        for(var j = 0; j < cityList.length; j++){
          if(firstLetter === cityList[j].index){
            cityList[j].list.push({name: cities[i].name, cityId: cities[i].cityId})
            exist = true
            break
          }
        }
        if(!exist){
          cityList.push({index: firstLetter, list: [{name: cities[i].name, cityId: cities[i].cityId}]})
        }
      }
      cityList.sort((a, b) => {
        if(a.index < b.index) return -1
        else if(a.index > b.index) return 1
        else return 0
      })
      return {
        cityList,
        hotList
      }
    }
  }
}
</script>
<style scoped lang='scss'>
#content .city_body{ margin-top: 45px; display: flex; width:100%; position: absolute; top: 0; bottom: 0;}
.city_body .city_list{ flex:1; overflow: auto; background: #FFF5F0;}
.city_body .city_list::-webkit-scrollbar{
    background-color:transparent;
    width:0;
}
.city_body .city_hot{ margin-top: 20px;}
.city_body .city_hot h2{ padding-left: 15px; line-height: 30px; font-size: 14px; background:#F0F0F0; font-weight: normal;}
.city_body .city_hot ul li{ float: left; background: #fff; width: 29%; height: 33px; margin-top: 15px; margin-left: 3%; padding: 0 4px; border: 1px solid #e6e6e6; border-radius: 3px; line-height: 33px; text-align: center; box-sizing: border-box;}
.city_body .city_sort div{ margin-top: 20px;}
.city_body .city_sort h2{ padding-left: 15px; line-height: 30px; font-size: 14px; background:#F0F0F0; font-weight: normal;}
.city_body .city_sort ul{ padding-left: 10px; margin-top: 10px;}
.city_body .city_sort ul li{ line-height: 30px; line-height: 30px;}
.city_body .city_index{ width:20px; display: flex; flex-direction:column; justify-content:center; text-align: center; border-left:1px #e6e6e6 solid;}
</style>
