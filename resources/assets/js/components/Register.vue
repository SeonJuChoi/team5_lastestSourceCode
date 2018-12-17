
<!-- 회원가입 -->
<template>
    <v-app>
        <v-container>
            <v-flex xs12 sm6 offset-sm3>
                <div class="text-xs-center">
                    <v-menu offset-y>
                        <v-btn
                            slot="activator"
                            color="primary"
                            dark
                        >
                            Language
                        </v-btn>
                        <v-list>
                            <v-list-tile @click="$session.set('user_country', 'Korea'); $router.go('/register')">
                                <v-list-tile-title>Korean</v-list-tile-title>
                            </v-list-tile>
                            <v-list-tile @click="$session.set('user_country', 'USA'); $router.go('/register')">
                                <v-list-tile-title>English</v-list-tile-title>
                            </v-list-tile>
                            <v-list-tile @click="$session.set('user_country', 'China'); $router.go('/register')">
                                <v-list-tile-title>Chinese</v-list-tile-title>
                            </v-list-tile>
                            <v-list-tile @click="$session.set('user_country', 'Japan'); $router.go('/register')">
                                <v-list-tile-title>Japanese</v-list-tile-title>
                            </v-list-tile>
                        </v-list>
                    </v-menu>
                </div>
                <v-btn v-if="$session.get('user_country') == 'Korea'" color="green" outline block large @click="user_categoty = true">개인 회원</v-btn>
                <v-btn v-else-if="$session.get('user_country') == 'USA'" color="green" outline block large @click="user_categoty = true">Personal user</v-btn>
                <v-btn v-else-if="$session.get('user_country') == 'China'" color="green" outline block large @click="user_categoty = true"></v-btn>
                <v-btn v-else color="green" outline block large @click="user_categoty = true">個人会員</v-btn>
                <v-btn v-if="$session.get('user_country') == 'Korea'" color="red" outline block large @click="user_categoty = false">사업자 회원</v-btn>
                <v-btn v-else-if="$session.get('user_country') == 'USA'" color="red" outline block large @click="user_categoty = false">Business user</v-btn>
                <v-btn v-else-if="$session.get('user_country') == 'China'" color="red" outline block large @click="user_categoty = false"></v-btn>
                <v-btn v-else color="red" outline block large @click="user_categoty = false">事業者会員</v-btn>
            </v-flex>
            <br>
            <v-flex xs12 sm6 offset-sm3>
                <v-card>
                    <v-card-text>
                        <v-text-field
                                v-model="user_id"
                                placeholder="ID"
                                required
                                color="green"
                                :rules="idRules"
                        ></v-text-field>
                        <v-text-field
                                v-model="user_pw1"
                                :append-icon="hidePw1 ? 'visibility' : 'visibility_off'"
                                :append-icon-cb="() => (hidePw1 = !hidePw1)"
                                :type="hidePw1 ? 'password' : 'text'"
                                label="Enter your password"
                                color="green"
                                :rules="pwRules"
                        ></v-text-field>
                        <v-text-field
                                v-model="user_pw2"
                                :append-icon="hidePw2 ? 'visibility' : 'visibility_off'"
                                :append-icon-cb="() => (hidePw2 = !hidePw2)"
                                :type="hidePw2 ? 'password' : 'text'"
                                label="Enter your password confirm"
                                color="green"
                                :rules="pwRules"
                        ></v-text-field>
                    </v-card-text>
                </v-card>
                <br>
                <v-card>
                    <v-card-text>
                        <v-text-field
                                v-model="user_name"
                                placeholder="Name"
                                required
                                color="green"
                        ></v-text-field>
                        <v-radio-group v-model="user_gender" row>
                            <v-radio label="Male" value="Male" color="green"></v-radio>
                            <v-radio label="Female" value="Female" color="green"></v-radio>
                        </v-radio-group>
                        <v-layout row wrap>
                            <v-flex xs4>
                                <v-text-field
                                        v-model="user_year"
                                        placeholder="Year"
                                        required
                                        color="green"
                                ></v-text-field>
                            </v-flex>
                            <v-flex xs4>
                                <v-select
                                        v-model="user_month"
                                        :items="month"
                                        label="Month"
                                        color="green"
                                        single-line
                                ></v-select>
                            </v-flex>
                            <v-flex xs4>
                                <v-text-field
                                        v-model="user_day"
                                        placeholder="Day"
                                        required
                                        append-icon="cake"
                                        color="green"></v-text-field>
                            </v-flex>
                        </v-layout>
                        <v-text-field
                                v-model="user_email"
                                placeholder="Email"
                                required
                                append-icon="email"
                                color="green"></v-text-field>
                        <v-select
                                v-model="user_country"
                                :items="country"
                                label="Country"
                                single-line
                                auto
                                append-icon="language"
                                hide-details
                                color="green"
                        ></v-select>
                    </v-card-text>
                </v-card>
                <br>
                <v-card v-if="user_categoty">
                    <v-card-text>
                        <v-select
                                v-model="user_favorite"
                                :items="food"
                                label="Favorite Food (max 3 items)"
                                single-line
                                auto
                                hide-details
                                multiple
                                chips
                                color="green"
                        ></v-select>
                        <v-select
                                v-model="user_region"
                                :items="ddbkList"
                                label="Favorite Region"
                                single-line
                                auto
                                append-icon="location_on"
                                hide-details
                                chips
                                color="green"
                        ></v-select>
                    </v-card-text>
                </v-card>
            </v-flex>
            <v-flex v-if="user_categoty" xs12 sm6 offset-sm3>
                <br>
                <v-btn v-if="$session.get('user_country') == 'Korea'" color="green" block outline large @click="register()">개인 회원 가입</v-btn>
                <v-btn v-else-if="$session.get('user_country') == 'USA'" color="green" block outline large @click="register()">Personal user register</v-btn>
                <v-btn v-else-if="$session.get('user_country') == 'China'" color="green" block outline large @click="register()"></v-btn>
                <v-btn v-else color="green" block outline large @click="register()">個人会員登録</v-btn>
            </v-flex>
            <v-flex v-else xs12 sm6 offset-sm3>
                <v-btn v-if="$session.get('user_country') == 'Korea'" color="red" block outline large @click="register()">사업자 회원 가입</v-btn>
                <v-btn v-else-if="$session.get('user_country') == 'USA'" color="red" block outline large @click="register()">Business user register</v-btn>
                <v-btn v-else-if="$session.get('user_country') == 'China'" color="red" block outline large @click="register()"></v-btn>
                <v-btn v-else color="red" block outline large @click="register()">作業者会員登録</v-btn>
            </v-flex>
        </v-container>
        <v-snackbar
                v-model="snackbar"
                :timeout="timeout"
                top
                vertical
        >
            {{ snackText }}
            <v-btn flat color="pink" @click.native="snackbar = false">Close</v-btn>
        </v-snackbar>
    </v-app>
</template>

<script>
    import axios  from 'axios';
    export default {
        data() {
            return {
                user_categoty:  true,
                user_id:        "",
                user_pw1:       "",
                user_pw2:       "",

                user_name:      "",
                user_gender:    "",
                user_year:      "",
                user_month:     "",
                user_day:       "",

                user_email:     "",
                user_country:   "",
                user_favorite:  "",
                user_region:    "",

                hidePw1: true,
                hidePw2: true,

                idRules: [
                    v => !!v || 'ID is required'
                ],
                pwRules: [
                    v => !!v || 'Password is required'
                ],
                month: [
                    '01', '02', '03', '04', '05', '06', '07', '08', '09', '10', '11', '12'
                ],
                country: [
                    '韓国', 'アメリカ', '日本', '中国'
                ],
                food: [
                    '韓国料理', '和食', '中華料理', '洋食', '分食', '丼', '寿司', 'ファーストフード', 'チム', '汁物',
                    '弁当', 'カフェー&デザート', '居酒屋', '面流', '製菓'
                ],
                ddbkList : [
                    '東京', '福岡', '札幌', '京都',
                    '大阪', '沖縄'
                ],

                snackbar:   false,
                snackText:  "",
                timeout:    3000,

                korTranslate: {
                    '한국': 'Korea',
                    '미국': 'USA',
                    '일본': 'Japan',
                    '중국': 'China',
                    '한식': '한식',
                    '일식': '일식',
                    '중식': '중식',
                    '양식': '양식',
                    '분식': '분식',
                    '덮밥': '덮밥',
                    '스시': '스시',
                    '패스트 푸드': '패스트 푸드',
                    '찜': '찜',
                    '탕': '탕',
                    '도시락': '도시락',
                    '카페&디저트': '카페&디저트',
                    '술집': '술집',
                    '면류': '면류',
                    '제과': '제과',
                    '도쿄': '東京',
                    '후쿠오카': '福岡',
                    '삿포로': '札幌',
                    '교토': '京都',
                    '오사카': '大阪',
                    '오키나와': '沖縄',
                },

                engTranslate: {
                    'Korea': 'Korea',
                    'USA': 'USA',
                    'Japan': 'Japan',
                    'China': 'China',
                    'Korean food': '한식',
                    'Japenese food': '일식',
                    'Chinese food': '중식',
                    'Western food': '양식',
                    'Snack': '분식',
                    'A bowl of rice served with toppings': '덮밥',
                    'Sushi': '스시',
                    'Fast food': '패스트 푸드',
                    'Steamed dish': '찜',
                    'Soup': '탕',
                    'Box lunch': '도시락',
                    'Cafe&Dessert': '카페&디저트',
                    'Bar': '술집',
                    'Noodles': '면류',
                    'Confectionery': '제과',
                    'Tokyo': '東京',
                    'Fukuoka': '福岡',
                    'Sapporo': '札幌',
                    'Kyoto': '京都',
                    'Osaka': '大阪',
                    'Okinawa': '沖縄',
                },

                japTranslate: {
                    '韓国': 'Korea',
                    'アメリカ': 'USA',
                    '日本': 'Japan',
                    '中国': 'China',
                    '韓国料理': '한식',
                    '和食': '일식',
                    '中華料理': '중식',
                    '洋食': '양식',
                    '分食': '분식',
                    '丼': '덮밥',
                    '寿司': '스시',
                    'ファーストフード': '패스트 푸드',
                    'チム': '찜',
                    '汁物': '탕',
                    '弁当': '도시락',
                    'カフェー&デザート': '카페&디저트',
                    '居酒屋': '술집',
                    '面流': '면류',
                    '製菓': '제과',
                    '東京': '東京',
                    '福岡': '福岡',
                    '札幌': '札幌',
                    '京都': '京都',
                    '大阪': '大阪',
                    '沖縄': '沖縄',
                },

                chiTranslate: {
                    '': 'Korea',
                    '': 'USA',
                    '': 'Japan',
                    '': 'China',
                    '': '한식',
                    '': '일식',
                    '': '중식',
                    '': '양식',
                    '': '분식',
                    '': '덮밥',
                    '': '스시',
                    '': '패스트 푸드',
                    '': '찜',
                    '': '탕',
                    '': '도시락',
                    '': '카페&디저트',
                    '': '술집',
                    '': '면류',
                    '': '제과',
                    '': '東京',
                    '': '福岡',
                    '': '札幌',
                    '': '京都',
                    '': '大阪',
                    '': '沖縄',
                },
            }
        },

        mounted() {
            if(this.$session.get('user_country') == "Korea") {
                this.country = ['한국', '미국', '일본', '중국'];
                this.food = [
                    '한식', '일식', '중식', '양식', '분식', '덮밥', '스시', '패스트 푸드', '찜', '탕',
                    '도시락', '카페&디저트', '술집', '면류', '제과'
                ];
                this.ddbkList = [
                    '도쿄', '후쿠오카', '삿포로', '교토',
                    '오사카', '오키나와'
                ]
            } else if(this.$session.get('user_country') == "USA") {
                this.country = ['Korea', 'USA', 'Japan', 'China'];
                this.food = [
                    'Korean food', 'Japenese food', 'Chinese food', 'Western food', 'Snack', 'A bowl of rice served with toppings', 'Sushi', 'Fast food', 'Steamed dish', 'Soup',
                    'Box lunch', 'Cafe&Dessert', 'Bar', 'Noodles', 'Confectionery'
                ];
                this.ddbkList = [
                    'Tokyo', 'Fukuoka', 'Sapporo', 'Kyoto',
                    'Osaka', 'Okinawa'
                ]
            } else if(this.$session.get('user_country') == "China") {
                this.country = ['', '', '', ''];
                this.food = [
                    '', '', '', '', '', '', '', '', '', '',
                    '', '', '', '', ''
                ];
                this.ddbkList = [
                    '', '', '', '',
                    '', ''
                ]
            }
        },

        methods: {
            register() {
                if(this.user_id == "") {
                    this.snackbar = true;
                    if(this.$session.get('user_country') == 'Korea')
                        this.snackText = "아이디는 필수 입력 사항입니다.";
                    else if(this.$session.get('user_country') == 'USA')
                        this.snackText = "ID is required";
                    else if(this.$session.get('user_country') == 'China')
                        this.snackText = "";
                    else
                        this.snackText = "IDは必ず入力してください";
                    return;
                }

                if(this.user_pw1 == "") {
                    this.snackbar = true;
                    if(this.$session.get('user_country') == 'Korea')
                        this.snackText = "비밀번호는 필수 입력 사항입니다.";
                    else if(this.$session.get('user_country') == 'USA')
                        this.snackText = "PW is required";
                    else if(this.$session.get('user_country') == 'China')
                        this.snackText = "";
                    else
                        this.snackText = "パスワードは必ず入力してください";
                    return;
                }

                if(this.user_pw1 != this.user_pw2) {
                    this.snackbar = true;
                    if(this.$session.get('user_country') == 'Korea')
                        this.snackText = "비밀번호와 비밀번호 확인이 서로 다릅니다.";
                    else if(this.$session.get('user_country') == 'USA')
                        this.snackText = "PW is not matching to PW2";
                    else if(this.$session.get('user_country') == 'China')
                        this.snackText = "";
                    else
                        this.snackText = "パスワードと確認が違います";
                    return;
                }

                if(this.user_name == "") {
                    this.snackbar = true;
                    if(this.$session.get('user_country') == 'Korea')
                        this.snackText = "이름은 필수 입력 사항입니다.";
                    else if(this.$session.get('user_country') == 'USA')
                        this.snackText = "Name is required";
                    else if(this.$session.get('user_country') == 'China')
                        this.snackText = "";
                    else
                        this.snackText = "名前は必ず入力してください";
                    return;
                }

                if(this.user_gender == "") {
                    this.snackbar = true;
                    if(this.$session.get('user_country') == 'Korea')
                        this.snackText = "성별은 필수 입력 사항입니다.";
                    else if(this.$session.get('user_country') == 'USA')
                        this.snackText = "Gender is required";
                    else if(this.$session.get('user_country') == 'China')
                        this.snackText = "";
                    else
                        this.snackText = "性別は必ず入力してください";
                    return;
                }

                if(this.user_year == "" || this.user_month == "" || this.user_day == "") {
                    this.snackbar = true;
                    if(this.$session.get('user_country') == 'Korea')
                        this.snackText = "생년월일은 필수 입력 사항입니다.";
                    else if(this.$session.get('user_country') == 'USA')
                        this.snackText = "Birthday is required";
                    else if(this.$session.get('user_country') == 'China')
                        this.snackText = "";
                    else
                        this.snackText = "生年月日は必ず入力してください";
                    return;
                }

                if(this.user_year < 1900 || this.user_year > 2018) {
                    this.snackbar = true;
                    if(this.$session.get('user_country') == 'Korea')
                        this.snackText = "년도를 확인해주세요.";
                    else if(this.$session.get('user_country') == 'USA')
                        this.snackText = "Check your year";
                    else if(this.$session.get('user_country') == 'China')
                        this.snackText = "";
                    else
                        this.snackText = "年度を確認してください";
                    return;
                }

                if(this.user_email == "") {
                    this.snackbar = true;
                    if(this.$session.get('user_country') == 'Korea')
                        this.snackText = "이메일은 필수 입력 사항입니다.";
                    else if(this.$session.get('user_country') == 'USA')
                        this.snackText = "Mail is required";
                    else if(this.$session.get('user_country') == 'China')
                        this.snackText = "";
                    else
                        this.snackText = "メールは必ず入力してください";
                    return;
                }

                if(this.user_country == "") {
                    this.snackbar = true;
                    if(this.$session.get('user_country') == 'Korea')
                        this.snackText = "국가는 필수 입력 사항입니다.";
                    else if(this.$session.get('user_country') == 'USA')
                        this.snackText = "Country is required";
                    else if(this.$session.get('user_country') == 'China')
                        this.snackText = "";
                    else
                        this.snackText = "国は必ず入力してください";
                    return;
                }

                if(this.user_categoty) {
                    var temp = this.user_favorite;
                    if(temp[3]) {
                        this.snackbar = true;
                        if(this.$session.get('user_country') == 'Korea')
                            this.snackText = "선호하는 음식은 최대 3가지 입니다.";
                        else if(this.$session.get('user_country') == 'USA')
                            this.snackText = "Favorite food max is 3";
                        else if(this.$session.get('user_country') == 'China')
                            this.snackText = "";
                        else
                            this.snackText = "好みの食べ物は最大三つまでです";
                        return;
                    }
                }

                var url = "/register";

                if(this.$session.get('user_country') == 'Korea') {
                    var sendData = {
                        user_id         : this.user_id,
                        password        : this.user_pw1,
                        email           : this.user_email,
                        name            : this.user_name,
                        country         : this.korTranslate[this.user_country],
                        birthday        : this.user_year + '-' + this.user_month + '-' + this.user_day,
                        category        : this.user_categoty,
                        gender          : this.user_gender,
                        favorite_1      : this.korTranslate[this.user_favorite[0]],
                        favorite_2      : this.korTranslate[this.user_favorite[1]],
                        favorite_3      : this.korTranslate[this.user_favorite[2]],
                        favorite_region : this.korTranslate[this.user_region]
                    }
                } else if(this.$session.get('user_country') == 'USA') {
                    var sendData = {
                        user_id         : this.user_id,
                        password        : this.user_pw1,
                        email           : this.user_email,
                        name            : this.user_name,
                        country         : this.engTranslate[this.user_country],
                        birthday        : this.user_year + '-' + this.user_month + '-' + this.user_day,
                        category        : this.user_categoty,
                        gender          : this.user_gender,
                        favorite_1      : this.engTranslate[this.user_favorite[0]],
                        favorite_2      : this.engTranslate[this.user_favorite[1]],
                        favorite_3      : this.engTranslate[this.user_favorite[2]],
                        favorite_region : this.engTranslate[this.user_region]
                    }
                } else if(this.$session.get('user_country') == 'China') {
                    var sendData = {
                        user_id         : this.user_id,
                        password        : this.user_pw1,
                        email           : this.user_email,
                        name            : this.user_name,
                        country         : this.chiTranslate[this.user_country],
                        birthday        : this.user_year + '-' + this.user_month + '-' + this.user_day,
                        category        : this.user_categoty,
                        gender          : this.user_gender,
                        favorite_1      : this.chiTranslate[this.user_favorite[0]],
                        favorite_2      : this.chiTranslate[this.user_favorite[1]],
                        favorite_3      : this.chiTranslate[this.user_favorite[2]],
                        favorite_region : this.chiTranslate[this.user_region]
                    }
                } else {
                    var sendData = {
                        user_id         : this.user_id,
                        password        : this.user_pw1,
                        email           : this.user_email,
                        name            : this.user_name,
                        country         : this.japTranslate[this.user_country],
                        birthday        : this.user_year + '-' + this.user_month + '-' + this.user_day,
                        category        : this.user_categoty,
                        gender          : this.user_gender,
                        favorite_1      : this.japTranslate[this.user_favorite[0]],
                        favorite_2      : this.japTranslate[this.user_favorite[1]],
                        favorite_3      : this.japTranslate[this.user_favorite[2]],
                        favorite_region : this.japTranslate[this.user_region]
                    }
                }

                axios.post(url, sendData)
                    .then(response => {
                        if(response.data.url != "/") { // 사장인지 손님인지 체크
                            this.$session.set('restaurant_id', 'needCreate');
                        } else {
                            if(this.$session.get('user_country') == 'Korea') {
                                this.$session.set('favorite_1', this.korTranslate[this.user_favorite[0]]);
                                this.$session.set('favorite_2', this.korTranslate[this.user_favorite[1]]);
                                this.$session.set('favorite_3', this.korTranslate[this.user_favorite[2]]);
                                this.$session.set('region', this.korTranslate[this.user_region]);
                                this.$session.set('user_country', this.korTranslate[this.user_country]);
                            } else if(this.$session.get('user_country') == 'USA') {
                                this.$session.set('favorite_1', this.engTranslate[this.user_favorite[0]]);
                                this.$session.set('favorite_2', this.engTranslate[this.user_favorite[1]]);
                                this.$session.set('favorite_3', this.engTranslate[this.user_favorite[2]]);
                                this.$session.set('region', this.engTranslate[this.user_region]);
                                this.$session.set('user_country', this.engTranslate[this.user_country]);
                            } else if(this.$session.get('user_country') == 'China') {
                                this.$session.set('favorite_1', this.chiTranslate[this.user_favorite[0]]);
                                this.$session.set('favorite_2', this.chiTranslate[this.user_favorite[1]]);
                                this.$session.set('favorite_3', this.chiTranslate[this.user_favorite[2]]);
                                this.$session.set('region', this.chiTranslate[this.user_region]);
                                this.$session.set('user_country', this.chiTranslate[this.user_country]);
                            } else {
                                this.$session.set('favorite_1', this.japTranslate[this.user_favorite[0]]);
                                this.$session.set('favorite_2', this.japTranslate[this.user_favorite[1]]);
                                this.$session.set('favorite_3', this.japTranslate[this.user_favorite[2]]);
                                this.$session.set('region', this.japTranslate[this.user_region]);
                                this.$session.set('user_country', this.japTranslate[this.user_country]);
                            }
                        }

                        this.$session.set('loginStatus', true);                     // 로그인 상태 true
                        this.$session.set('user_id', this.user_id);        // user_id set
                        this.$session.set('user_name', this.user_name);    // user_name set

                        location.replace(response.data.url);
                    })
                    .catch(error => {
                        alert(error);
                    });
            },

            getKeyByValue(val, array) {
                for (var key in array) {
                    if(array[key] == val) {
                        return key;
                    }
                }
                return false;
            }
        }
    }
</script>