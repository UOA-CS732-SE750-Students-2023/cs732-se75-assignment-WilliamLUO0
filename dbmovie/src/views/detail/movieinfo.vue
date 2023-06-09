<template>
    <div class="movie-info" v-if="detailData.id">
        <h1 class="title">{{detailData.title}} {{detailData.original_title}} <span class="year">({{detailData.year}})</span></h1>

        <!-- 简介 -->
        <div class="desc-content">
            <img :src="detailData.cover_url" class="mv-img">
            <div class="mv-desc">
                <p>Director: <a href="#" v-if="detailData.directors && detailData.directors.length">{{detailData.directors[0].name}}</a></p>
                <p>Lead Actor: 
                    <a href="" v-for="(item, index) in actors.short" :key="index">{{item.name}}<template v-if="index != actors.short.length-1">/</template></a>
                    <span v-if="actors.isShowMore" class="more-actors" @click="expand">更多...</span>
                    <span v-if="!actors.isShowMore" class="more-actors" @click="shrink">收起</span>
                </p>
                <p>Type: <span class="it">{{detailData.genres.join(' / ')}}</span></p>
                <p>Production country/region: <span class="it">{{detailData.countries.join(' / ')}}</span></p>
                <p>Language: <span class="it">{{detailData.languages.join(' / ')}}</span></p>
                <p>Release Date: <span class="it">{{detailData.pubdate.join(' / ')}}</span></p>
                <p>Film length: <span class="it">{{detailData.durations.join(' / ')}}</span></p>
                <p>Also known as: <span class="it">{{detailData.aka.join(' / ')}}</span></p>
            </div>
            <div class="mv-rank">
                <div class="rank-title">Douban rating</div>
                <div class="rank-score">
                    <div class="score"><strong class="num">{{detailData.rating.value}}</strong></div>
                    <div class="score-star">
                        <rankstar :score="detailData.rating.value" class="rankstar"></rankstar>
                        <div class="score-comment"><a href="#">{{detailData.rating.count}}</a>people comment</div>
                    </div>
                </div>
                <div class="score-rate">
                    <div v-for="item in rate.list" class="rate-item" :key="item.index">
                        <span>{{item.index}}</span>
                        <span class="wline" :style="{width:item.width+'px'}"></span>
                        <span class="percent">{{item.count.toFixed(1)}}%</span>
                    </div>
                </div>
                <div class="better-content">
                    <div v-for="(item, index) in rate.betterList" class="better-item">better than<a href="#">{{Math.round(item.rank * 100)}}%{{item.type}}</a></div>
                </div>
            </div>
        </div>

        <!-- 评分写影评 -->
        <div class="opera-box">
            <div>Please rate: </div>
            <div>
                <img src="https://img3.doubanio.com/f/shire/5bbf02b7b5ec12b23e214a580b6f9e481108488c/pics/add-review.gif" alt="" />
                <a href="javascript:void(0)" class="comment-link" @click="goPublish"> Writing Movie Reviews</a>
            </div>
        </div>

        <!-- 剧情简介 -->
        <div class="intro">
            <h2 class="intro-title">
                Plot summary of {{detailData.title}}......
            </h2>
            <div class="intro-content">
                {{detailData.intro}}
            </div>
        </div>
    </div>
</template>

<script>
    import rankstar from '@/components/rankstar/rankstar.vue'
    import service from '@/utils/service'
    import configapi from '@/utils/configapi'
    import { useRoute, useRouter } from 'vue-router'
    import { onMounted, computed, ref, reactive } from 'vue'
    import { useStore } from 'vuex'
    export default {
        name: 'movieinfo',
        components: {
            rankstar
        },
        setup(){
            
            const store = useStore()
            const route = useRoute()
            const router = useRouter()
            const userInfo = computed(() => store.state.userInfo)


            let detailData = ref({})

            let actors = reactive({
                origin:[],
                short:[],
                isShowMore: true
            })

            let rate = reactive({
                list: [],
                betterList: []
            })


            let id = computed(() => route.query.id)
            onMounted(async () => {
                detailData.value = await service.get(configapi.detail(id.value))

                store.commit('setTitle',detailData.value.title)
                actors.origin = detailData.value.actors || []
                actors.short = actors.origin.slice(0, 3)
                actors.isShowMore = actors.origin.length > 3

                let rateData = await service.get(configapi.rate(id.value))
                rate.list = dealRateData(rateData)

                rate.betterList = rateData.type_ranks || []

            })
            

            const goPublish = () => {
                if(userInfo.value.nickname) {
                    router.push('/publish?id='+detailData.value.id)
                } else {
                    router.push('/login')
                }
            }


            //排行预处理
            const dealRateData = (rateData) => {
                let maxwidth = 70
                let list = []

                for(let i = 0; i < rateData.stats.length; i++) {
                    let r = rateData.stats[i].toFixed(3) *100
                    list.push({
                        index: i + 1,
                        count: r,
                        width: r * maxwidth / 100
                    })
                }

                return list.reverse()
            }

            //展开所有名字
            const expand = () => {
                actors.short = actors.origin
                actors.isShowMore = false
            }

            //收起
            const shrink = () => {
                actors.short = actors.origin.slice(0, 3)
                actors.isShowMore = true
            }

            

            return {
                goPublish,
                detailData,
                actors,
                expand,
                shrink,
                rate
            }
        }
    }
</script>

<style lang="less" scoped>
.title {
    font-size: 26px;
    font-weight: bold;
    color: #494949;
    .year{
        color: #888
    }
}

.desc-content {
    display: flex;
    margin-top: 13px;
}

.mv-img {
    width: 135px;
    height: 200px;
}

.mv-desc {
    font-size: 13px;
    margin-left: 15px;
    max-width: 450px;
    padding: 0 30px;
    p {
        margin: 2px 0;
        color: #666;
        .it {
            color: #111;
        }
    }
}

.more-actors {
    color:#aaa;
    cursor: pointer;
    &:hover {
        color: #fff;
        background-color: #aaa;
    }
}

.mv-rank {
    width: 155px;
    margin: 2px 0 0 0;
    padding: 0 0 0 15px;
    border-left: 1px solid #eaeaea;
    color: #9b9b9b;
}

.rank-score {
    margin-top: 5px;
    display:flex;
    .num {
        color: #494949;
        padding: 0;
        font-size: 28px;
    }
}

.score-star {
    margin-left: 10px;
    padding-top: 5px;
    .rankstar {
        transform: scale(1.3);
        transform-origin: 0 0;
    }
}

.score-comment {
    margin-top: 7px;
}

.rate-item {
    display: flex;
}

.wline {
    height: 10px;
    margin: 1px 4px;
    background: #ffd596 none repeat scroll 0 0;
    align-self: center;
}

.better-content {
    position: relative;
    padding:10px 0;
    border-top: 1px solid #eaeaea;
    color: #9b9b9b;
    margin-top: 20px;
}

.better-item {
    margin-top: 1px;
}

.intro-title {
    margin: 24px 0 12px 0;
    font-size: 16px;
    color: #007722;
}

.comment-link {
    font-size: 13px;
}
</style>