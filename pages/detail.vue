<template>
    <div class="content">
        <h1 style="text-align: center;font-size: 38px;">
            {{ detail.title }}
        </h1>
        <div style="font-size:18px;color:#999" class="mt-5">
            <span>{{ detail.createdAt }}</span>
            <span class="ml-3">{{ detail.createdBy }}</span>
        </div>
        <v-divider class="mt-7"></v-divider>
        <div v-html="detail.content" class="mt-5"></div>
        
    </div>


</template>

<script>
import axios from 'axios'

export default {
    data() {
        return {
            title: "",
            createTime: "",
            createBy: "",
            content: ""
        }
    },
    async asyncData(context) {
        console.log(context.query.id)
        let { data } = await axios.get('https://wsy.cosmoplat.com/dev-api/cms/articles/' + context.query.id)
        return {
            detail: data.data
        }
    },
}
</script>

<style scoped>
.content {
    width: 100%;

}
</style>