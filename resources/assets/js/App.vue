<template>
    <div>
        <v-app style="background-color:#efe2bd">
            <v-navigation-drawer
                    v-model="menu"
                    fixed
                    app
                    temporary
                    touchless
                    hide-overlay
            >
                <v-list>
                    <v-list-tile avatar to="/editInformation">
                        <v-list-tile-avatar>
                            <v-icon large>account_circle</v-icon>
                        </v-list-tile-avatar>
                        <v-list-tile-content>
                            <v-list-tile-title>{{ user_id+"("+user_name+")" }}</v-list-tile-title>
                            <v-list-tile-sub-title v-if="checkRestaurant()">{{ businessUser }}</v-list-tile-sub-title>
                            <v-list-tile-sub-title v-else>{{ personalUser }}</v-list-tile-sub-title>
                        </v-list-tile-content>
                    </v-list-tile>
                </v-list>
                <v-divider></v-divider>
                <v-list v-if="!checkRestaurant()">
                    <v-list-tile to="/userOrderHistory">
                        <v-list-tile-action>
                            <v-icon large>assignment</v-icon>
                        </v-list-tile-action>
                        <v-list-tile-content>
                            <v-list-tile-title>{{ orderHistory }}</v-list-tile-title>
                        </v-list-tile-content>
                    </v-list-tile>
                    <v-list-tile to="/userReviewHistory">
                        <v-list-tile-action>
                            <v-icon large>rate_review</v-icon>
                        </v-list-tile-action>
                        <v-list-tile-content>
                            <v-list-tile-title>{{ reviewHistory }}</v-list-tile-title>
                        </v-list-tile-content>
                    </v-list-tile>
                    <v-list-tile to="/userPageReservation">
                        <v-list-tile-action>
                            <v-icon large>history</v-icon>
                        </v-list-tile-action>
                        <v-list-tile-content>
                            <v-list-tile-title>{{ reservationdetails }}</v-list-tile-title>
                        </v-list-tile-content>
                    </v-list-tile>
                    <v-list-tile to="/userCoupon">
                        <v-list-tile-action>
                            <v-icon large>loyalty</v-icon>
                        </v-list-tile-action>
                        <v-list-tile-content>
                            <v-list-tile-title>{{ couponList }}</v-list-tile-title>
                        </v-list-tile-content>
                    </v-list-tile>
                    <v-list-tile to="/userLikeRestaurant">
                        <v-list-tile-action>
                            <v-icon large>favorite</v-icon>
                        </v-list-tile-action>
                        <v-list-tile-content>
                            <v-list-tile-title>{{ likeRestaurant }}</v-list-tile-title>
                        </v-list-tile-content>
                    </v-list-tile>
                    <!-- 커뮤니케이션 버튼 -->
                    <v-dialog v-model="communicationDialog" fullscreen hide-overlay transition="dialog-bottom-transition" full-width>
                        <v-list-tile @click="" slot="activator">
                            <v-list-tile-action>
                                <v-icon large>sentiment_very_satisfied</v-icon>
                            </v-list-tile-action>
                            <v-list-tile-content>
                                <v-list-tile-title>
                                    Communication
                                </v-list-tile-title>
                            </v-list-tile-content>
                        </v-list-tile>
                        <!-- 출력될 modal창 내용-->
                        <v-card>
                            <v-toolbar dark dense fixed  id="communication-toolbar">
                                <v-toolbar-title id="communication-title">Communication</v-toolbar-title>
                                <v-spacer></v-spacer>
                                <!-- 이 버튼을 누르면 communicationDialog의 값을 false로 만들어
                                출력된 모달창을 사라지도록 한다는 것 -->
                                <v-btn flat icon @click.native="communicationDialog = false" color="black">
                                    <v-icon>close</v-icon>
                                </v-btn>
                            </v-toolbar>
                            <v-content>
                                <v-list three-line subheader>
                                    <!-- 커뮤니케이션 버튼 기능 -->
                                    <UserCommunication></UserCommunication>
                                </v-list>
                            </v-content>
                        </v-card>
                    </v-dialog>
                    <!-- 커뮤니케이션 버튼 끝 -->
                </v-list>
                <v-list v-else-if="checkRestaurant() != 'needCreate'">
                    <v-subheader>가게 관리</v-subheader>
                    <v-list-tile @click="moveMyMenu()">
                        <v-list-tile-action>
                            <v-icon large color="green">developer_board</v-icon>
                        </v-list-tile-action>
                        <v-list-tile-content>
                            <v-list-tile-title>메뉴</v-list-tile-title>
                        </v-list-tile-content>
                    </v-list-tile>
                    <v-list-tile @click="moveMyReservation()">
                        <v-list-tile-action>
                            <v-icon large color="green">playlist_add_check</v-icon>
                        </v-list-tile-action>
                        <v-list-tile-content>
                            <v-list-tile-title>예약</v-list-tile-title>
                        </v-list-tile-content>
                    </v-list-tile>
                    <v-list-tile @click="moveMyStats()">
                        <v-list-tile-action>
                            <v-icon large color="green">timeline</v-icon>
                        </v-list-tile-action>
                        <v-list-tile-content>
                            <v-list-tile-title>통계</v-list-tile-title>
                        </v-list-tile-content>
                    </v-list-tile>
                    <v-divider></v-divider>
                    <v-subheader>내 가게 페이지</v-subheader>
                    <v-list-tile @click="moveMyRestaurant()">
                        <v-list-tile-action>
                            <v-icon large color="green">restaurant</v-icon>
                        </v-list-tile-action>
                        <v-list-tile-content>
                            <v-list-tile-title>내 가게로 이동</v-list-tile-title>
                        </v-list-tile-content>
                    </v-list-tile>
                </v-list>
                <v-list v-else>
                    <v-subheader>가게 관리</v-subheader>
                    <v-list-tile @click="moveMyRestaurant()">
                        <v-list-tile-action>
                            <v-icon large color="green">restaurant</v-icon>
                        </v-list-tile-action>
                        <v-list-tile-content>
                            <v-list-tile-title>가게 생성하기</v-list-tile-title>
                        </v-list-tile-content>
                    </v-list-tile>
                </v-list>
            </v-navigation-drawer>
            <v-toolbar
                    app
                    color='white'
                    scroll-off-screen
                    :scroll-threshold="0"
            ><!-- scroll-off-screen: 스크롤을 내리면 toolbar가 숨겨짐 -->
                <!-- scroll-threshold: 스크롤 민감도 -->
                <v-layout align-center justify-space-between row fill-height>
                    <v-btn class="mr-0 ml-1" icon @click="openMenu()">
                        <img src="../../../storage/app/public/img/menu.png" style="width:35px">
                    </v-btn>
                    <v-btn flat @click="$router.push('/')">
                        <img src="../../../storage/app/public/img/logo.png" style="width:120px">
                    </v-btn>
                    <v-btn v-if="!checkLogin()" class="mx-1" icon @click="loginForm=true">
                        <img src="../../../storage/app/public/img/user_nologin.png" style="width:40px">
                    </v-btn>
                    <v-menu v-else offset-y :close-on-content-click="false">
                        <v-btn class="mx-1" icon slot="activator">
                            <img src="../../../storage/app/public/img/user.png" style="width:40px">
                        </v-btn>
                        <v-list>
                            <v-list-tile>
                                <div class="text-xs-center">
                                    <v-menu offset-y>
                                        <v-btn
                                            slot="activator"
                                            flat
                                        >
                                            <v-icon large color="green">language</v-icon>
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
                            </v-list-tile>
                            <v-list-tile @click="searchForm = true">
                                <v-list-tile-action>
                                    <v-icon large color="orange">search</v-icon>
                                </v-list-tile-action>
                                <v-list-tile-title>Search</v-list-tile-title>
                            </v-list-tile>
                            <v-list-tile @click="gps_search = true">
                                <v-list-tile-action>
                                    <v-icon large color="red">gps_fixed</v-icon>
                                </v-list-tile-action>
                                <v-list-tile-title>GPS</v-list-tile-title>
                            </v-list-tile>
                            <v-list-tile @click="logout()">
                                <v-list-tile-action>
                                    <v-icon large color="blue">directions_run</v-icon>
                                </v-list-tile-action>
                                <v-list-tile-title>Logout</v-list-tile-title>
                            </v-list-tile>
                        </v-list>
                    </v-menu>
                </v-layout>
            </v-toolbar>
            <!-- 컴포넌트 출력 -->
            <v-content v-scroll="onScroll" column align-center justify-center>
                <router-view></router-view>
            </v-content>
        </v-app>
        <v-dialog v-model="gps_modal" max-width="400">
            <v-card>
                <v-card-title class="headline">GPS 위치 지정<v-spacer></v-spacer>
                    <v-btn icon @click.native.stop="gps_modal = false">
                        <v-icon large color="red">close</v-icon>
                    </v-btn>
                </v-card-title>
                <v-card-text>위치를 기반으로 식당을 검색하거나 추천해 드립니다 ^^</v-card-text>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="amber lighten-1" @click.native="gps_search = true">직접 입력</v-btn>
                    <v-dialog v-model="gps_search" max-width="500">
                        <v-card>
                            <v-card-title class="headline">{{ inputAddress }}</v-card-title>
                            <v-card-actions>
                                <v-flex>
                                    <gmap-autocomplete
                                            class="searchBar"
                                            @place_changed="setPlace"
                                            @keypress="setPlace"
                                    ></gmap-autocomplete>
                                </v-flex>
                                <v-btn color="amber lighten-1" @click.native="gps_search = false; gps_modal = false; searchingAddress()">{{ addressButton }}</v-btn>
                            </v-card-actions>
                        </v-card>
                    </v-dialog>
                    <v-btn color="red" @click.native="gps_modal = false">GPS로 위치 찾기</v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
        <v-dialog
            v-model='loginForm'
            max-width='500px'
        >
            <v-card>
                <v-card-text class="text-xs-center">
                    <span class="display-1" style="color: #ff9a55"><strong>Login</strong></span>
                    <v-form>
                        <v-text-field
                                placeholder="ID"
                                autofocus
                                required
                                color='orange darken-1'
                                v-model='idValue'
                        ></v-text-field>
                        <v-text-field
                                placeholder="Password"
                                type="password"
                                color='orange darken-1'
                                required
                                @keypress.enter="login()"
                                v-model='pwValue'
                        ></v-text-field>
                    </v-form>
                </v-card-text>
                <v-card-actions>
                    <v-flex xs6>
                        <v-btn color="orange darken-1" block large outline @click="login()">Login</v-btn>
                    </v-flex>
                    <v-flex xs6>
                        <router-link to="register" style="text-decoration: none">
                            <v-btn color="blue-grey darken-1" block large outline @click="loginForm = false">Sign up</v-btn>
                        </router-link>
                    </v-flex>
                </v-card-actions>
                <br>
            </v-card>
        </v-dialog>
        <v-dialog
            v-model='searchForm'
            max-width='500px'
        >
            <v-card>
                <v-card-title>
                    <Strong class="headline">Search</Strong>
                </v-card-title>
                <v-card-actions>
                    <v-layout column fill-height>
                    <v-text-field
                            v-model="searchDataInput"
                            color="orange"
                            solo
                            :label=labelSearch
                            @keypress.enter="searching()"
                    ></v-text-field>
                    <v-btn color="orange" block large outline @click="searching()">검색</v-btn>
                    </v-layout>
                </v-card-actions>
                <br>
            </v-card>
        </v-dialog>
        <v-snackbar
                v-model="snackbar"
                :timeout="timeout"
                top
                vertical
        >
            {{ snackText }}
            <v-btn flat color="pink" @click.native="snackbar = false">Close</v-btn>
        </v-snackbar>
        <v-fab-transition>
            <v-btn
                    v-if="scrollTopButton"
                    @click="scrollToTop()"
                    color="orange"
                    dark
                    fixed
                    bottom
                    right
                    fab
            >
                <v-icon>keyboard_arrow_up</v-icon>
            </v-btn>
        </v-fab-transition>
    </div>
</template>

<script>
    import axios  from 'axios';
    // 커뮤니케이션 버튼
    import UserCommunication from './components/user/user_communication/UserCommunication.vue';

    export default {
        components : {
            'UserCommunication' : UserCommunication,            //  커뮤니케이션 버튼
        },

        data() {
            return {
                menu: false,        // navigation-drawer(왼쪽 메뉴바)
                loginForm: false,   // login dialog
                searchForm: false,  // search dialog
                gps_modal: false,   // gps dialog
                gps_search: false,  // gps search dialog
                idValue: '',        // login form user id
                pwValue: '',        // login form user pw

                user_id: this.$session.get('user_id'),      // user id
                user_name: this.$session.get('user_name'),  // user name

                communicationDialog: false,     // 커뮤니케이션 버튼을 통해 모달창을 출력하는데 사용

                snackbar:   false,  // snackbar 상태
                snackText:  "",     // snackbar 내용
                timeout:    3000,   // snackbar 표시 시간

                searchAddressInput: "", // 사용자가 입력한 address value
                searchDataInput: "",    // 사용자가 입력한 search value

                offsetTop: 0,
                scrollTopButton: false,

                labelSearch: '',

                businessUser: '',
                personalUser: '',
                orderHistory: '',
                reviewHistory: '',
                reservationdetails: '',
                couponList: '',
                likeRestaurant: '',
                inputAddress: '',
                addressButton: '',
            }
        },

        created: function() {
            if(this.$session.get('user_country') == "Korea") {
                this.labelSearch        = '식당 또는 음식';
                this.businessUser       = '사업자 회원';
                this.personalUser       = '개인 회원';
                this.orderHistory       = '주문 내역';
                this.reviewHistory      = '리뷰 내역';
                this.reservationdetails = '예약 내역';
                this.couponList         = '쿠폰함';
                this.likeRestaurant     = '찜목록';
                this.inputAddress       = '위치를 입력하세요';
                this.addressButton      = '검색';
            } else if(this.$session.get('user_country') == "USA") {
                this.labelSearch        = 'Restaurant or food';
                this.businessUser       = 'Business User';
                this.personalUser       = 'Personal User';
                this.orderHistory       = 'Order History';
                this.reviewHistory      = 'Review History';
                this.reservationdetails = 'Reservation Details';
                this.couponList         = 'Coupon List';
                this.likeRestaurant     = 'Like Restaurants';
                this.inputAddress       = 'Please input your address';
                this.addressButton      = 'search';
            } else if(this.$session.get('user_country') == "China") {
                this.labelSearch        = '';
                this.businessUser       = '';
                this.personalUser       = '';
                this.orderHistory       = '';
                this.reviewHistory      = '';
                this.reservationdetails = '';
                this.couponList         = '';
                this.likeRestaurant     = '';
                this.inputAddress       = '';
                this.addressButton      = '';
            } else {
                this.labelSearch        = '食堂あるいは食べ物';
                this.businessUser       = '事業者会員';
                this.personalUser       = '個人会員';
                this.orderHistory       = '注文履歴';
                this.reviewHistory      = 'レビュー履歴';
                this.reservationdetails = '予約リスト';
                this.couponList         = 'クーポンリスト';
                this.likeRestaurant     = '気になる店リスト';
                this.inputAddress       = 'アドレスを入力してください';
                this.addressButton      = '検索';
            }
        },

        methods: {
            login() {
                var url = "/login";
                axios.post(url, {
                    user_id     : this.idValue,
                    password    : this.pwValue
                })
                    .then(response => {
                        if(!response.data.login) { // 로그인 실패시
                            this.snackbar = true;
                            this.snackText = response.data.msg;
                            return;
                        }
                        if(response.data.restaurant_id != "/") { // 사장인지 손님인지 체크
                            if(response.data.restaurant_id != "noneRestaurant") // 가게를 만든 사장인지 체크
                                this.$session.set('restaurant_id', response.data.restaurant_id); // 가게를 만든 사장이라면, 가게 id set
                            else
                                this.$session.set('restaurant_id', 'needCreate');
                        } else {
                            if(response.data.favorite_region)
                                this.$session.set('region', response.data.favorite_region);
                            if(response.data.favorite_1) {
                                this.$session.set('favorite_1', response.data.favorite_1);
                                if(response.data.favorite_2) {
                                    this.$session.set('favorite_2', response.data.favorite_2);
                                    if(response.data.favorite_3)
                                        this.$session.set('favorite_3', response.data.favorite_3);
                                }
                            }
                        }

                        this.$session.set('loginStatus', true);                     // 로그인 상태 true
                        this.$session.set('user_id', response.data.user_id);        // user_id set
                        this.$session.set('user_name', response.data.user_name);    // user_name set
                        this.$session.set('user_country', response.data.country);    // user_country set

                        if(this.$session.get('restaurant_id')) { // 사장이라면 가게페이지, 손님이라면 메인페이지로 이동
                            if(!(this.$session.get('restaurant_id') == 'needCreate')) { // 가게를 만들지 않은 사장인지 체크
                                var restaurant_id = this.$session.get('restaurant_id');
                                location.replace('/owner/' + restaurant_id + '/editRestaurant');
                            } else {
                                location.replace('/owner/createRestaurant');
                            }
                        } else
                            location.replace(response.data.restaurant_id);
                    })
                    .catch(error => {
                        this.snackbar = true;
                        this.snackText = error;
                    });
            },

            logout() {
                this.$session.clear();
                location.replace('/');
            },

            checkLogin() {
                return this.$session.get('loginStatus');
            },

            checkRestaurant() {
                if(!this.$session.get('restaurant_id' == 'needCreate')) // 가게 등록한 사장인지 아닌지 체크
                    return this.$session.get('restaurant_id');
                else
                    return "needCreate";
            },

            moveMyMenu() {
                var restaurant_id = this.$session.get('restaurant_id');
                location.replace('/owner/' + restaurant_id + '/menuOperate');
            },

            moveMyReservation() {
                var restaurant_id = this.$session.get('restaurant_id');
                location.replace('/owner/' + restaurant_id + '/ownerReservationList');
            },

            moveMyStats() {
                var restaurant_id = this.$session.get('restaurant_id');
                location.replace('/owner/' + restaurant_id + '/totalStatistics');
            },

            moveMyRestaurant() {
                var restaurant_id = this.$session.get('restaurant_id');

                if(restaurant_id != "needCreate") // 가게를 등록했으면 가게 페이지로, 아니면 생성 페이지로
                    location.replace('/restaurant/' + restaurant_id + '/info');
                else
                    location.replace('/owner/createRestaurant');
            },

            openMenu() {
                if(this.checkLogin()) {
                    this.menu = !this.menu;
                } else {
                    this.snackbar = true;
                    switch(this.$session.get('user_country')) {
                        case 'Korea':
                            this.snackText = "로그인이 필요합니다";
                            break;
                        case 'USA':
                            this.snackText = "You need login";
                            break;
                        case 'China':
                            this.snackText = "";
                            break;
                        default:
                            this.snackText = "ログインが必要です";
                    }
                    
                    this.loginForm = true;
                }
            },

            setPlace(AddressInput) {
                this.searchAddressInput = AddressInput;
            },

            searchingAddress() {
                if(this.searchAddressInput.formatted_address) {
                    this.$session.set('searchAddress', this.searchAddressInput.geometry.location);

                    var url = "/getGPSData";
                    var lat = this.searchAddressInput.geometry.location.lat();
                    var lng = this.searchAddressInput.geometry.location.lng();

                    axios.post(url, {'coordinateLat': lat, 'coordinateLng': lng})
                    .then(response => {
                        this.$session.set('GPSData', response.data.GPSData);

                        if(this.$route.path != '/searchAddress')
                            this.$router.push('/searchAddress');
                        else
                            this.$router.go(this.$router.currentRoute);
                    })
                    .catch(error => {
                        alert(error);
                    })
                }
            },

            searching() {
                if(this.searchDataInput == "")
                    return;
                var url = "/getSearchData";
                axios.post(url, {'searchData': this.searchDataInput})
                    .then(response => {
                        this.$session.set('searchKeyword', this.searchDataInput);
                        this.$session.set('searchData', response.data);
                        this.searchDataInput = "";

                        if(this.$route.path != '/search')
                            this.$router.push('/search');
                        else
                            this.$router.go(this.$router.currentRoute);

                        this.searchForm = false;
                    })
                    .catch(error => {
                        alert(error);
                    })
            },

            onScroll (e) {
                this.offsetTop = document.documentElement.scrollTop
                if(this.offsetTop >= 300) {
                    this.scrollTopButton = true;
                } else {
                    this.scrollTopButton = false;
                }
            },

            scrollToTop() {
                document.documentElement.scrollTop = 0;
            }
        }
    }
</script>
<style>
    #communication-toolbar {
        background-color : #ffffff;
    }

    #communication-title {
        color: #ff9800;
    }
</style>