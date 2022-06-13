<template>
  <v-container fluid>
    <v-data-iterator :items="listData" hide-default-footer>
      <template v-slot:default="props">
        <v-row>
          <v-col v-for="(item, index) in props.items" :key="index" cols="12" sm="6" md="4" lg="3"
            @click="clickNewsItem(item)">
            <v-card>
              <v-img lazy-src="https://picsum.photos/id/11/10/6" height="250" :src="item.imageUrl"></v-img>
              <v-card-title class="subheading font-weight-bold">
                {{ item.title }}
              </v-card-title>
              <v-card-subtitle>{{ item.keywords }}</v-card-subtitle>
              <v-divider></v-divider>
              <v-card-text>{{ item.summary }}</v-card-text>
              <v-card-text style="color:">{{ item.createdAt }}</v-card-text>
            </v-card>
          </v-col>
        </v-row>
      </template>

      <template v-slot:footer>
        <v-row class="mt-2" align="center" justify="center">
          <span class="grey--text">每页条数</span>
          <v-menu offset-y>
            <template v-slot:activator="{ on, attrs }">
              <v-btn dark text color="primary" class="ml-2" v-bind="attrs" v-on="on">
                {{ pageSize }}
                <v-icon>mdi-chevron-down</v-icon>
              </v-btn>
            </template>
            <v-list>
              <v-list-item v-for="(number, index) in itemsPerPageArray" :key="index"
                @click="updateItemsPerPage(number)">
                <v-list-item-title>{{ number }}</v-list-item-title>
              </v-list-item>
            </v-list>
          </v-menu>

          <v-spacer></v-spacer>

          <span class="mr-4
            grey--text">
            页数 {{ pageNo }} / {{ numberOfPages }}
          </span>
          <v-btn fab dark color="blue darken-3" class="mr-1" @click="formerPage">
            <v-icon>mdi-chevron-left</v-icon>
          </v-btn>
          <v-btn fab dark color="blue darken-3" class="ml-1" @click="nextPage">
            <v-icon>mdi-chevron-right</v-icon>
          </v-btn>
        </v-row>
      </template>
    </v-data-iterator>
  </v-container>

</template>

<script>
import axios from "axios";

export default {
  name: "IndexPage",
  data() {
    return {
      listData: [],
      totalSize: 0,
      pageNo: 1,
      pageSize: 10,
      numberOfPages: 0,
      itemsPerPageArray: [5, 10, 15],
      cateCode: ""
    };
  },
  watch: {
    $route() {
      this.startGetList()
    }
  },
  mounted() {
    this.startGetList()
  },
  methods: {
    startGetList() {
      if (this.$route.query.lng == 'cn') {
        this.cateCode = 'news.center.domestic'
      } else {
        this.cateCode = 'news.center.world'
      }
      this.getNewsList()
    },
    getNewsList() {
      axios.get(
        'https://wsy.cosmoplat.com/dev-api/cms/articles/page?cateCode=' + this.cateCode + '&pageNo=' + this.pageNo + '&pageSize=' + this.pageSize
      ).then(res => {
        this.totalSize = res.data.data.total
        this.numberOfPages = this.getTotalPageNum(this.totalSize, this.pageSize)
        if (this.pageNo == 1) {
          this.listData = res.data.data.records
        }else {
          this.listData.push(...res.data.data.records)
        }
        
      })
    },
    getTotalPageNum(totalRecord, pageSize) {
      return parseInt((totalRecord + pageSize - 1) / pageSize);
    },
    updateItemsPerPage(number) {
      this.pageNo = number
      this.getNewsList()
    },
    formerPage() {
      if (this.pageNo == 1) return
      this.pageNo--
      this.getNewsList()
    },
    nextPage() {
      if (this.pageNo == this.numberOfPages) {
        return
      }
      this.pageNo++
      this.getNewsList()
    },
    clickNewsItem(item) {
      this.$router.push({ path: '/detail', query: { id: item.id } })
    }
  }
};
</script>
