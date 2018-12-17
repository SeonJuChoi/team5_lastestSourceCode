<template>
    <div>
        <div style="background-color: #ff9a55; height: 10px"></div>
        <v-card>
            <v-jumbotron
                gradient=" 
                    rgba(0,0,0,0),
                    rgba(0,0,0,0.3),
                    rgba(0,0,0,0)"
                :src="require('../../../../../../storage/app/public/img/main.png')"
                height="500px"
            >
                <v-container fill-height fluid>
                    <v-layout fill-height>
                        <v-flex fill-height>
                            <v-card-title>
                                <v-layout align-center justify-center column fill-height>
                                    <img src="../../../../../../storage/app/public/img/title_logo.png" style="width: 200px">
                                    <br>
                                    <strong v-if="$session.get('user_country') == 'Korea'" class="white--text text-xs-center title-text-size">
                                    두렵지 않은 해외 여행<br>
                                    맛있는 추천
                                    </strong>
                                    <strong v-else-if="$session.get('user_country') == 'USA'" class="white--text text-xs-center title-text-size">
                                    Very simple overseas trip<br>
                                    Delicious recommedation
                                    </strong>
                                    <strong v-else-if="$session.get('user_country') == 'China'" class="white--text text-xs-center title-text-size">
                                    두렵지 않은 해외 여행<br>
                                    맛있는 추천
                                    </strong>
                                    <strong v-else class="white--text text-xs-center title-text-size">
                                    怖がらずに堂々と海外旅行<br>
                                    美味しいおすすめ
                                    </strong>
                                </v-layout>
                            </v-card-title>
                            <v-card-actions>
                                <v-text-field
                                        v-model="$parent.$parent.$parent.searchDataInput"
                                        prepend-icon="search"
                                        color="orange"
                                        solo
                                        :label=labelSearch
                                        @keypress.enter="$parent.$parent.$parent.searching()"
                                ></v-text-field>
                                <v-btn color="orange" large @click="$parent.$parent.$parent.searching()">검색</v-btn>
                            </v-card-actions>
                        </v-flex>
                    </v-layout>
                </v-container>
            </v-jumbotron>
        </v-card>
        <v-layout row>
            <v-flex xs12 sm10 offset-sm1>
                <div style="background-color: #ff9a55; height: 10px"></div>
                <v-card>
                    <div v-if="$session.get('region') != undefined || $session.get('favorite_1') != undefined">
                        <v-alert
                            :value="true"
                            color="brown"
                            outline
                            class="text-xs-center display-1 mx-4"
                        >
                            <strong class="bestBanner">{{ userRecommend }}</strong>
                        </v-alert>
                        <v-container fluid grid-list-sm>
                            <v-layout row wrap>
                                <v-flex md4 xs12 v-for="(item, i) in favorite" :key="i">
                                    <div style="background-color: #ff9a55; height: 5px"></div>
                                    <v-card>
                                        <div @click="item.function(item.name, item.listLimit, item.postName)">
                                            <v-jumbotron
                                                gradient="to right,
                                                    rgba(0,0,0,0.8),
                                                    rgba(0,0,0,0),
                                                    rgba(0,0,0,0)"
                                                :src="item.src"
                                                height="200px"
                                            >
                                                <v-container fill-height fluid>
                                                    <v-layout align-end justify-start row fill-height>
                                                        <span v-if="$session.get('user_country') == 'Korea'" class="display-1 white--text"><b>{{ translateKorValue[item.name] }}<br><span class="bestText">Best {{ item.listLimit }}</span></b></span>
                                                        <span v-if="$session.get('user_country') == 'USA'" class="display-1 white--text"><b>{{ translateEngValue[item.name] }}<br><span class="bestText">Best {{ item.listLimit }}</span></b></span>
                                                        <span v-if="$session.get('user_country') == 'China'" class="display-1 white--text"><b>{{ translateChiValue[item.name] }}<br><span class="bestText">Best {{ item.listLimit }}</span></b></span>
                                                        <span v-if="$session.get('user_country') == 'Japan'" class="display-1 white--text"><b>{{ translateJapValue[item.name] }}<br><span class="bestText">Best {{ item.listLimit }}</span></b></span>
                                                    </v-layout>
                                                </v-container>
                                            </v-jumbotron>
                                        </div>
                                    </v-card>
                                </v-flex>
                            </v-layout>
                        </v-container>
                    </div>
                    <br>
                    <v-alert
                        :value="true"
                        color="brown"
                        outline
                        class="text-xs-center display-1 mx-4"
                    >
                        <strong class="bestBanner">{{ regionRecommend }}</strong>
                    </v-alert>
                    <v-container fluid grid-list-sm>
                        <v-layout row wrap>
                            <v-flex md4 xs12 v-for="(item, i) in area" :key="i">
                                <div style="background-color: #ff9a55; height: 5px"></div>
                                <v-card>
                                    <div @click="clickRegionList(item.postRegion, item.listLimit, item.name)">
                                        <v-jumbotron
                                                gradient="to right,
                                                    rgba(0,0,0,0.8),
                                                    rgba(0,0,0,0),
                                                    rgba(0,0,0,0)"
                                                :src="item.src"
                                                height="200px"  
                                        >
                                            <v-container fill-height fluid>
                                                <v-layout align-end justify-start row fill-height>
                                                    <span class="display-1 white--text"><b>{{ item.name }}<br><span class="bestText">Best {{ item.listLimit }}</span></b></span>
                                                </v-layout>
                                            </v-container>
                                        </v-jumbotron>
                                    </div>
                                </v-card>
                            </v-flex>
                        </v-layout>
                    </v-container>
                    <br>
                    <v-alert
                        :value="true"
                        color="brown"
                        outline
                        class="text-xs-center display-1 mx-4"
                    >
                        <strong class="bestBanner">{{ typeRecommend }}</strong>
                    </v-alert>
                    <v-container fluid grid-list-sm>
                        <v-layout row wrap>
                            <v-flex md4 xs12 v-for="(item, i) in food" :key="i">
                                <div style="background-color: #ff9a55; height: 5px"></div>
                                <v-card>
                                    <div @click="clickTypeList(item.postType, item.listLimit, item.name)">
                                        <v-jumbotron
                                            gradient="to right,
                                                    rgba(0,0,0,0.8),
                                                    rgba(0,0,0,0),
                                                    rgba(0,0,0,0)"
                                            :src="item.src"
                                            height="200px"
                                        >
                                            <v-container fill-height fluid>
                                                    <v-layout align-end justify-start row fill-height>
                                                        <span class="display-1 white--text"><b>{{ item.name }}<br><span class="bestText">Best {{ item.listLimit }}</span></b></span>
                                                    </v-layout>
                                                </v-container>
                                        </v-jumbotron>
                                    </div>
                                </v-card>
                            </v-flex>
                        </v-layout>
                    </v-container>
                </v-card>
                <br>
            </v-flex>
        </v-layout>
    </div>
</template>

<script>
    import GoogleMap from "../../GoogleMap";
    import axios from 'axios';
    export default {
        components: {
            GoogleMap
        },

        props: ['searchAddress'],

        data(){
            return {
                area: [
                    {
                        name: '東京',
                        src: '/images/toukyou.jpg',
                        postRegion: '東京',
                        listLimit: 7
                    },
                    {
                        name: '大阪',
                        src: '/images/oosaka.jpg',
                        postRegion: '大阪',
                        listLimit: 7
                    },
                    {
                        name: '京都',
                        src: '/images/kyouto.jpg',
                        postRegion: '京都',
                        listLimit: 7
                    },
                    {
                        name: '福岡',
                        src: '/images/hukuoka.jpg',
                        postRegion: '福岡',
                        listLimit: 5
                    },
                    {
                        name: '札幌',
                        src: '/images/hokkaido.jpg',
                        postRegion: '札幌',
                        listLimit: 5
                    },
                    {
                        name: '沖縄',
                        src: '/images/okinawa.jpg',
                        postRegion: '沖縄',
                        listLimit: 5
                    },
                ],

                food: [
                    {
                        name: '韓国料理',
                        src: '/images/koreanFood.jpg',
                        postType: '한식',
                        listLimit: 10
                    },
                    {
                        name: '和食',
                        src: '/images/japaneseFood.jpg',
                        postType: '일식',
                        listLimit: 10
                    },
                    {
                        name: '中華料理',
                        src: '/images/chineseFood.jpg',
                        postType: '중식',
                        listLimit: 10
                    },
                ],

                favorite: [

                ],

                changeEnglish: {
                    '東京': 'toukyou',
                    '北海道': 'hokkaido',
                    '大阪': 'oosaka',
                    '京都': 'kyouto',
                    '福岡': 'hukuoka',
                    '札幌': 'hokkaido',
                    '沖縄': 'okinawa',
                    '한식': 'koreanFood',
                    '일식': 'japaneseFood',
                    '중식': 'chineseFood',
                    '양식': 'westernfood',
                    '분식': 'bunsik',
                    '덮밥': 'dupbab',
                    '스시': 'sushi',
                    '패스트 푸드': 'fastfood',
                    '찜': 'jjim',
                    '탕': 'tang',
                    '도시락': 'bento',
                    '카페&디저트': 'cafe',
                    '술집': 'osake',
                    '면류': 'men',
                    '제과': 'bbang',
                },

                translateKorValue: {
                    '東京': '도쿄',
                    '大阪': '오사카',
                    '京都': '교토',
                    '福岡': '후쿠오카',
                    '札幌': '삿포로',
                    '沖縄': '오키나와',
                    '한식': '한식',
                    '일식': '일식',
                    '중식': '중식',
                    '양식': '양식',
                    '분식': '분식',
                    '덮밥': '덮밥',
                    '스시': '스시',
                    '패스트 푸드': '푸드',
                    '찜': '찜',
                    '탕': '탕',
                    '도시락': '도시락',
                    '카페&디저트': '카페&디저트',
                    '술집': '술집',
                    '면류': '면류',
                    '제과': '제과',
                },

                translateEngValue: {
                    '東京': 'Tokyo',
                    '大阪': 'Osaka',
                    '京都': 'Kyoto',
                    '福岡': 'Fukuoka',
                    '札幌': 'Sapporo',
                    '沖縄': 'Okinawa',
                    '한식': 'Korean food',
                    '일식': 'Japenese food',
                    '중식': 'Chinese food',
                    '양식': 'Western food',
                    '분식': 'Snack',
                    '덮밥': 'A bowl of rice served with toppings',
                    '스시': 'Sushi',
                    '패스트 푸드': 'Fast food',
                    '찜': 'Steamed dish',
                    '탕': 'Soup',
                    '도시락': 'Box lunch',
                    '카페&디저트': 'Cafe&Dessert',
                    '술집': 'Bar',
                    '면류': 'Noodles',
                    '제과': 'Confectionery',
                },

                translateChiValue: {
                    '東京': '',
                    '大阪': '',
                    '京都': '',
                    '福岡': '',
                    '札幌': '',
                    '沖縄': '',
                    '한식': '',
                    '일식': '',
                    '중식': '',
                    '양식': '',
                    '분식': '',
                    '덮밥': '',
                    '스시': '',
                    '패스트 푸드': '',
                    '찜': '',
                    '탕': '',
                    '도시락': '',
                    '카페&디저트': '',
                    '술집': '',
                    '면류': '',
                    '제과': '',
                },
                
                translateJapValue: {
                    '東京': '東京',
                    '大阪': '大阪',
                    '京都': '京都',
                    '福岡': '福岡',
                    '札幌': '札幌',
                    '沖縄': '沖縄',
                    '한식': '韓国料理',
                    '일식': '和食',
                    '중식': '中華料理',
                    '양식': '洋食',
                    '분식': '分食',
                    '덮밥': '丼',
                    '스시': '寿司',
                    '패스트 푸드': 'ファーストフード',
                    '찜': 'チム',
                    '탕': '汁物',
                    '도시락': '弁当',
                    '카페&디저트': 'カフェー&デザート',
                    '술집': '居酒屋',
                    '면류': '面流',
                    '제과': '製菓',
                },

                labelSearch: '食堂あるいは食べ物',
                userRecommend: 'ユーザー向けおすすめ',
                regionRecommend: '地域別ベストレーティング',
                typeRecommend: '食事別ベストレーティング',
            }
        },

        mounted() {
            if(this.$session.get('region')) {
                this.favorite.push({
                    name: this.$session.get('region'),
                    src: "/images/" + this.changeEnglish[this.$session.get('region')] + ".jpg",
                    postName: this.$session.get('region'),
                    function: this.clickRegionList,
                    listLimit: 7
                });
            }

            if(this.$session.get('favorite_1')) {
                this.favorite.push({
                    name: this.$session.get('favorite_1'),
                    src: "/images/" + this.changeEnglish[this.$session.get('favorite_1')] + ".jpg",
                    postName: this.$session.get('favorite_1'),
                    function: this.clickTypeList,
                    listLimit: 5
                });
                if(this.$session.get('favorite_2')) {
                    this.favorite.push({
                        name: this.$session.get('favorite_2'),
                        src: "/images/" + this.changeEnglish[this.$session.get('favorite_2')] + ".jpg",
                        postName: this.$session.get('favorite_2'),
                        function: this.clickTypeList,
                        listLimit: 5
                    });
                    if(this.$session.get('favorite_3')) {
                        this.favorite.push({
                            name: this.$session.get('favorite_3'),
                            src: "/images/" + this.changeEnglish[this.$session.get('favorite_3')] + ".jpg",
                            postName: this.$session.get('favorite_3'),
                            function: this.clickTypeList,
                            listLimit: 5
                        });
                    }
                }
            }

            if(this.$session.get('user_country') == "Korea") {
                this.labelSearch = '식당 또는 음식';
                this.userRecommend = '사용자 맞춤 추천';
                this.regionRecommend = '지역별 평점 베스트';
                this.typeRecommend = '식종 평점 베스트';
                this.area[0].name = '도쿄';
                this.area[1].name = '오사카';
                this.area[2].name = '교토';
                this.area[3].name = '후쿠오카';
                this.area[4].name = '삿포로';
                this.area[5].name = '오키나와';
                this.food[0].name = '한식';
                this.food[1].name = '일식';
                this.food[2].name = '중식';
                if(this.$session.get('region'))
                    this.favorite[0].postName = this.translateKorValue[this.favorite[0].postName];
                if(this.$session.get('favorite_1'))
                    this.favorite[1].postName = this.translateKorValue[this.favorite[1].postName];
                if(this.$session.get('favorite_2'))
                    this.favorite[2].postName = this.translateKorValue[this.favorite[2].postName];
                if(this.$session.get('favorite_3'))
                    this.favorite[3].postName = this.translateKorValue[this.favorite[3].postName];
            } else if(this.$session.get('user_country') == "USA") {
                this.labelSearch = 'Restaurant or food';
                this.userRecommend = 'Custom recommendations';
                this.regionRecommend = 'Best rated by region';
                this.typeRecommend = 'Best rated by food type';
                this.area[0].name = 'Tokyo';
                this.area[1].name = 'Osaka';
                this.area[2].name = 'Kyoto';
                this.area[3].name = 'Fukuoka';
                this.area[4].name = 'Sapporo';
                this.area[5].name = 'Okinawa';
                this.food[0].name = 'Korean Food';
                this.food[1].name = 'Japanese food';
                this.food[2].name = 'Chinese food';
                if(this.$session.get('region'))
                    this.favorite[0].postName = this.translateEngValue[this.favorite[0].postName];
                if(this.$session.get('favorite_1'))
                    this.favorite[1].postName = this.translateEngValue[this.favorite[1].postName];
                if(this.$session.get('favorite_2'))
                    this.favorite[2].postName = this.translateEngValue[this.favorite[2].postName];
                if(this.$session.get('favorite_3'))
                    this.favorite[3].postName = this.translateEngValue[this.favorite[3].postName];
            } else if(this.$session.get('user_country') == "China") {
                this.labelSearch = '';
                this.userRecommend = '';
                this.regionRecommend = '';
                this.typeRecommend = '';
                this.area[0].name = '';
                this.area[1].name = '';
                this.area[2].name = '';
                this.area[3].name = '';
                this.area[4].name = '';
                this.area[5].name = '';
                this.food[0].name = '';
                this.food[1].name = '';
                this.food[2].name = '';
                if(this.$session.get('region'))
                    this.favorite[0].postName = this.translateChiValue[this.favorite[0].postName];
                if(this.$session.get('favorite_1'))
                    this.favorite[1].postName = this.translateChiValue[this.favorite[1].postName];
                if(this.$session.get('favorite_2'))
                    this.favorite[2].postName = this.translateChiValue[this.favorite[2].postName];
                if(this.$session.get('favorite_3'))
                    this.favorite[3].postName = this.translateChiValue[this.favorite[3].postName];
            } else {
                if(this.$session.get('region'))
                    this.favorite[0].postName = this.translateJapValue[this.favorite[0].postName];
                if(this.$session.get('favorite_1'))
                    this.favorite[1].postName = this.translateJapValue[this.favorite[1].postName];
                if(this.$session.get('favorite_2'))
                    this.favorite[2].postName = this.translateJapValue[this.favorite[2].postName];
                if(this.$session.get('favorite_3'))
                    this.favorite[3].postName = this.translateJapValue[this.favorite[3].postName];
            }
        },

        methods: {
            clickRegionList(postRegion, listLimit, showRegion) {
                var url = "/getRegionShopData";
                axios.post(url, {'region': postRegion, 'limit': listLimit})
                    .then(response => {
                        this.$session.set('top_mode', 'region');
                        this.$session.set('top_region', postRegion);
                        this.$session.set('top_show_region', showRegion);
                        this.$session.set('top_listLimit', listLimit);
                        this.$session.set('top_restaurantList', response.data.regionShopData);
                        this.$router.push('topList');
                    })
                    .catch(error => {
                        alert(error);
                    })
            },

            clickTypeList(postType, listLimit, showType) {
                var url = "/getTypeShopData";
                axios.post(url, {'type': postType, 'limit': listLimit})
                    .then(response => {
                        this.$session.set('top_mode', 'type');
                        this.$session.set('top_type', postType);
                        this.$session.set('top_show_type', showType);
                        this.$session.set('top_listLimit', listLimit);
                        this.$session.set('top_restaurantList', response.data.typeShopData);
                        this.$router.push('topList');
                    })
                    .catch(error => {
                        alert(error);
                    })
            },
        }
    }
</script>

<style>
    .bestText {
        font-size: 30pt;
    }

    @media (max-width: 639px) {
        .bestBanner {
            font-size: 20pt;
        }
        
        .title-text-size {
            font-size: 18pt;
        }
    }

    @media (min-width: 640px) {
        .title-text-size {
            font-size: 24pt;
        }
    }

</style>