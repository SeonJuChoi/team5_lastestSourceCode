<!-- 
※ 현재 페이지 참고사항

1. 작성된 리뷰를 보는 페이지, 리뷰기능의 메인페이지에 해당
2. 작성일, 좋아요 정렬
3. 국가별 필터링

-->

<template>
    <transition name="fade">
        <v-content grid-list-md text-xs-center class="ma-0 pt-3" id="review-page-style">
            <!-- 경고창 -->
            <v-snackbar
            :timeout="loginCheckSnackbarTimeout"
            :top="loginCheckSnackbarY === 'top'"
            :bottom="loginCheckSnackbarY === 'bottom'"
            :right="loginCheckSnackbarX === 'right'"
            :left="loginCheckSnackbarX === 'left'"
            :multi-line="loginCheckSnackbarMode === 'multi-line'"
            :vertical="loginCheckSnackbarMode === 'vertical'"
            v-model="loginCheckSnackbar"
            >
            {{this.loginCheckErrorCode}}
                <v-btn flat color="pink" @click.native="loginCheckSnackbar = false">Close</v-btn>
            </v-snackbar>

            <v-layout>
                <v-spacer></v-spacer>
                <!-- 리뷰 작성 버튼 -->
                <v-flex xs10 sm10>
                    <v-btn outline id="review-write-button" v-on:click="checkLogin" :to="writeCheck" block>
                        {{this.reviewWriteButton}}
                    </v-btn>
                </v-flex>
                <v-spacer></v-spacer>
            </v-layout>
            <v-layout>
                <v-flex xs4 sm2 offset-sm1 offset-xs1>
                    <!-- 정렬 기준 선택 -->
                    <v-select 
                    :items      ="sortItems" 
                    v-model     ="sortSelect" 
                    label       ="日付順" 
                    item-text   ="sort" 
                    item-value  ="sortNum" 
                    single-line 
                    return-object>
                    </v-select> 
                </v-flex>
                <v-flex xs4 sm2>
                    <!-- 국가 필터링 기준 선택 -->
                    <v-select 
                    :items      ="filterItems" 
                    v-model     ="filterSelect" 
                    :label       = "this.filterSelect['country']"
                    item-text   ="country"
                    single-line
                    return-object>
                    </v-select> 
                </v-flex>
                <v-spacer></v-spacer>
            </v-layout>
            <v-layout>
                <v-flex>
                    <!-- 리뷰 내용 출력 -->
                    <UserReviewData 
                    :countryNum     ="this.filterSelect.countryNum" 
                    :countryName    ="this.filterSelect.country" 
                    :sortNum        ="this.sortSelect.sortNum">
                    </UserReviewData>
                </v-flex>
            </v-layout>

            <!-- 최상단 이동 버튼 -->
            <!-- <v-btn class="up-button" color="grey lighten-2" outline small fixed bottom left fab
            @click="$vuetify.goTo(0, {duration:250, offset:0, easing:'easeInQuad'})">
                <v-icon>arrow_upward</v-icon>
            </v-btn> -->

            <!-- SNS 공유 -->
            <v-speed-dial fixed bottom left direction="top"
            transition="slide-y-reverse-transition" v-model="fab">
                <v-btn slot="activator" id="share-button" dark v-model="fab" fab>
                    <v-icon>share</v-icon>
                    <v-icon>close</v-icon>
                </v-btn>
                <social-sharing v-bind:url= "url" inline-template>
                    <network network="facebook">
                        <!-- facebook svg태그 -->
                        <img src= "/images/review/facebook.svg" height="60" onmousedown="return false;">
                    </network>
                </social-sharing>
                <social-sharing v-bind:url= "url" inline-template>
                    <network network="twitter">
                        <!-- twitter svg태그 -->
                        <img src= "/images/review/twitter.svg" height="60" onmousedown="return false;">
                    </network>
                </social-sharing>
                <social-sharing v-bind:url= "url" inline-template>
                    <network network="weibo">
                        <!-- weibo svg태그 -->
                        <img src= "/images/review/weibo.svg" height="60" onmousedown="return false;" class="logo-img">
                    </network>
                </social-sharing>     
            </v-speed-dial> 
        </v-content>
    </transition>
</template>

<script>
// 리뷰 데이터를 전달받아 생성하고 정렬하는 컴퍼넌트 import
import UserReviewData from './UserReviewData';

// axios 라이브러리 import
import VueAxios from 'vue-axios';
import axios    from 'axios';

export default {
    components : {
        'UserReviewData' : UserReviewData,
    },

    data() {
        return {
            // sns 이미지 주소 배열
            snsImageList : ['/images/review/facebook.svg', '/images/review/twitter.svg', '/images/review/weibo.svg'],
            url         : "",                               // 리뷰를 하는 페이지 URL
            sortSelect  : { sort: '日付順', sortNum: 1 }, // 선택된 정렬 기준
            sortItems   : [                                 // 정렬 기준들
                { sort  : '日付順', sortNum: 1 },
                { sort  : 'お勧め順',   sortNum: 2 },
            ],

            filterSelect    : { country: '国籍', countryNum: 0 },  // 선택된 국가 필터링 기준
            filterItems     : [                                         // 국가 필터링 기준들
                { country   : 'all',   countryNum: 0 },
                { country   : 'China', countryNum: 1 },
                { country   : 'Japan', countryNum: 2 },
                { country   : 'Korea', countryNum: 3 },
                { country   : 'USA',   countryNum: 4 },
            ],

            fab         : false,    
            writeCheck  : "",       // 리뷰 작성페이지 주소가 저장 되는 변수
            loginCheckErrorCode         : "ログインしてください。",   // 경고창에 띄울 문장이 저장되는 변수
            loginCheckSnackbar          : false,                // snackbar 출력여부 확인
            loginCheckSnackbarY         : 'top',                // snackbar Y축 값
            loginCheckSnackbarX         : null,                 // snackbar X축 값
            loginCheckSnackbarMode      : '',                   // snackbar mode 값
            loginCheckSnackbarTimeout   : 2000,                 //snackbar 지속시간

            reviewWriteButton : "レビュー作成",
        }
    },
// this.$session.get('user_country')
    methods:{
        // 리뷰 작성시 로그인 여부 체크 함수
        checkLogin() {
            if(!(this.$session.get('loginStatus'))){
                this.loginCheckSnackbar = true;
            }
            else {
                this.loginCheckSnackbar = false;
            }
        },

        // 이미지를 preloading 하는 함수
        preloading (imageArray){
            let n = imageArray.length; 
        
            for (let i = 0; i < n; i++){
            let img = new Image(); 
            img.src = imageArray[i]; 
            } 
        },

        // 국적에 따라 UI의 언어를 바꾸는 함수
        languageChange() {
            if(this.$session.get('user_country') == 'Korea'){
                this.loginCheckErrorCode        = '로그인 해주세요.';
                this.reviewWriteButton          = '리뷰 작성';
                this.sortItems[0]['sort']       = '날짜순';
                this.sortItems[1]['sort']       = '좋아요순';
                this.filterSelect['country']    = '국적';
            } else if(this.$session.get('user_country') == 'China') {
                this.loginCheckErrorCode        = '请登录';
                this.reviewWriteButton          = '写评论';
                this.sortItems[0]['sort']       = '按日期';
                this.sortItems[1]['sort']       = '良好';
                this.filterSelect['country']    = '国籍';
            } else if(this.$session.get('user_country') == 'USA') {
                this.loginCheckErrorCode        = 'Please login.';
                this.reviewWriteButton          = 'Write a review';
                this.sortItems[0]['sort']       = 'By date';
                this.sortItems[1]['sort']       = 'Good';
                this.filterSelect['country']    = 'Nationality';
            }
        }
    },

    created() {
        // url 주소를 현재 페이지로 url로 변경합니다.
        this.url = window.location.href;

        // 로그인 여부를 확인합니다.
        // 로그인을 한 경우 리뷰 작성이 가능하도록 리뷰작성페이지 주소를 해당 변수에 대입합니다.
        if(this.$session.get('loginStatus')){
            this.writeCheck = "writeReview";
        }

        // sns이미지를 preroding 합니다.
        this.preloading(this.snsImageList);

        this.languageChange();
    }
}
</script>

<style scoped>
    /* svg태그의 cursor를 설정합니다. */
    svg {
        cursor: pointer;
    }
    .fade-enter-active, .fade-leave-active {
        transition: opacity .5s
    }
    .fade-enter, .fade-leave-active {
        opacity: 0
    }
    /* 최상단 버튼 관련 CSS */
    .up-button {
        z-index: 10;
    }

    #review-write-button {
        background-color: #9d724b;
        color: #9d724b;
    }

    #share-button {
        background-color: #d2b07d;
        color: #ffffff;
    }

    #review-page-style {
        background-color: #ffffff;
    }
</style>