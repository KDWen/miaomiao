<template>
  <div class="cinema_body">
    <ul>
      <li v-for="item in cinemaList" :key="item.cinemaId">
        <div class="info">
          <span class="name">{{item.name}}</span>
          <span class="q"><span class="price"> {{item.lowPrice | filterPrice}}</span> 元起</span>
        </div>
        <div class="address">
          <span>{{item.address}}</span>
          <span>{{item.Distance | filterDistance}}km</span>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'CiList',
  data() {
    return {
      cinemaList: []
    }
  },
  mounted() {
    this.axios({
      url: 'https://m.maizuo.com/gateway?cityId=110100&ticketFlag=1&k=79795',
      headers: {
        'X-Client-Info': '{"a":"3000","ch":"1002","v":"5.0.4","e":"16163307772462618283409409","bc":"110100"}',
        'X-Host': 'mall.film-ticket.cinema.list'
      }
    }).then((res) => {
      if(res.status == 200){
        this.cinemaList = res.data.data.cinemas
      }
    })
  },
  filters: {
    filterPrice(price){
      let str = price.toString()
      if(str.substr(-2,2) === '00') return str.substr(0, str.length-2)
      else return str.substr(0, str.length-2)+'.'+str.substr(-2,1)
    },
    filterDistance(d){
      return d.toFixed(1)
    }
  }
}
</script>
<style scoped lang='scss'>
#content .cinema_body{ flex:1; overflow:auto;}
.cinema_body ul{ padding:20px;}
.cinema_body li{  border-bottom:1px solid #e6e6e6; margin-bottom: 20px;}
.cinema_body div{ margin-bottom: 10px; overflow: auto;}
.cinema_body .q{ font-size: 11px; color:#f03d37;}
.cinema_body .price{ font-size: 18px;}
.cinema_body .address{ font-size: 13px; color:#666; }
.cinema_body .address span:nth-of-type(1){ float:left; display: block; width: calc(100% - 50px); white-space: nowrap; overflow: hidden; text-overflow: ellipsis;}
.cinema_body .address span:nth-of-type(2){ float:right; }
.cinema_body .info .name{float: left; display: block; width: calc(100% - 80px); white-space: nowrap; overflow: hidden; text-overflow: ellipsis;}
.cinema_body .info .q{ float:right; }
</style>
