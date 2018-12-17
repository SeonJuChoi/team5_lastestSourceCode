<template>
    <div>
        <div style="background-color: #ff9a55; height: 10px"></div>
        <v-layout row>
            <v-flex xs12 sm10 offset-sm1>
                <v-card>
                    <google-map :currentCenter="$session.get('searchAddress')">

                    </google-map>
                </v-card>
            </v-flex>
        </v-layout>
        <v-layout row>
            <v-flex xs12 sm10 offset-sm1>
                <v-card>
                    <div style="background-color: #ff9a55; height: 10px"></div>
                    <v-alert
                        :value="true"
                        color="brown"
                        outline
                        class="text-xs-center display-1 mx-4"
                    >
                        <strong class="bestBanner">{{ restaurantGPS }}</strong>
                    </v-alert>
                    <v-container fluid grid-list-sm>
                        <v-layout row wrap v-if="restaurantGPSResult.length != 0">
                            <v-flex md4 xs12 v-for="(item, i) in restaurantGPSResult" :key="i">
                                <v-card>
                                    <div style="background-color: #ff9a55; height: 5px"></div>
                                    <v-card-media
                                            :src="`/images/${item.shop_id}/${item.shop_id}_titleImg.jpg`"
                                            height="250px"
                                            @click="moveRestaurant(item.shop_id)"
                                    ></v-card-media>
                                    <v-card-title class="headline">{{ item.shop_name }}<v-spacer></v-spacer><span class="orange--text">{{ item.totalRating }}</span></v-card-title>
                                    <v-card-actions><h2>{{ item.shop_address }} - {{ item.shop_type }}</h2></v-card-actions>
                                </v-card>
                            </v-flex>
                        </v-layout>
                        <v-card-text v-else>
                            <strong style="font-size: 22pt">{{ noneResult }}</strong>
                        </v-card-text>
                    </v-container>
                </v-card>
                <br>
            </v-flex>
        </v-layout>
    </div>
</template>

<script>
    import GoogleMap from "./GoogleMap";
    import axios from 'axios';
    export default {
        components: {
            GoogleMap
        },

        data(){
            return {
                searchKeyword: this.$session.get('searchKeyword'),
                restaurantGPSResult: this.$session.get('GPSData'),
                dodobukenList: [
                    '전체','東京', '北海道', '札幌', '京都',
                    '大阪', '靑森', '岩手', '宮城',
                    '秋田', '山形', '福島', '茨城',
                    '栃木', '群馬', '埼玉', '千葉',
                    '神奈川', '新潟', '富山', '石川',
                    '福井', '山梨', '長野', '岐阜',
                    '靜岡', '愛知', '三重', '滋賀',
                    '兵庫', '奈良', '和歌山', '鳥取',
                    '島根', '岡山', '廣島', '山口',
                    '德島', '香川', '愛媛', '高知',
                    '福岡', '佐賀', '長崎', '熊本',
                    '大分', '宮崎', '鹿兒島', '沖繩'
                ],
                restaurantGPS: "",
                noneResult: "",
                searching: "",
            }
        },

        mounted() {
            if(this.$session.get('user_country') == "Korea") {
                this.restaurantGPS = '위치 검색 결과';
                this.noneResult = '검색 결과가 없습니다.';
                this.searching = '검색 결과';
            } else if(this.$session.get('user_country') == "USA") {
                this.restaurantGPS = 'Result of searching GPS';
                this.noneResult = 'No results were found for your search.';
                this.searching = 'Searching';
            } else if(this.$session.get('user_country') == "China") {
                this.restaurantGPS = '';
                this.noneResult = '';
                this.searching = '';
            } else {
                this.restaurantGPS = '位置検索結果';
                this.noneResult = '検索結果がありません。';
                this.searching = 'の検索結果';
            }
        },

        methods: {
            moveRestaurant(shop_id) {
                this.$router.push('/restaurant/' + shop_id + '/info');
            },
        }
    }
</script>

<style>

</style>