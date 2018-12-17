<!--[ 가게 정보 입력 페이지 ] 가게 사장이 가게 정보 입력하면 입력 받은 정보들을 서버 딴으로 보냄.-->
<template>
<div class="container" style="border:1px solid"><br>
<form id="upload_info" enctype="multipart/form-data"> 
<v-container style="width:98%; margin:auto">
    <!--스낵바 : 경고 창 출력-->
    <v-snackbar 
        :timeout="timeout" 
        :top="'top'" 
        v-model="snackbar"
    >
        {{ snackbar_text }}

        <v-btn  
            @click.native="snackbar=false"
            color="pink" 
            flat icon
        >
            <v-icon large> close </v-icon>
        </v-btn>
    </v-snackbar>

    <v-layout style="overflow:hi">
        <v-flex xs8>
            <h1 style="font-size:60px;"> 
                お店の情報を入力 
            </h1>    
        </v-flex>
        <v-flex xs2>
            <!-- <v-btn color="error" class="submit_btn" @click="QrCodeDialog = true" block> 
                QR 코드 보기
            </v-btn>  -->
        </v-flex>
        <v-spacer></v-spacer>
        <v-flex xs2>
            <v-btn 
                style=" background-color: #ff9a55; 
                        color:white; 
                        font-weight:bold;" 
                class="submit_btn" 
                @click="geoCoderFunction"
                block
            > 
                セーブ
            </v-btn> 
        </v-flex>
    </v-layout> <br>

    <!-- <v-dialog
        v-model="QrCodeDialog" 
        width="500px"
    >        
        <v-card>           
            <img 
                id="qrcode" 
                @click="QrCodeDialog = false"
                style="width:500px; height:500px;"
            >
        </v-card>
    </v-dialog> -->

<!--***************************************************** 가게 기본 정보 ************************************************-->       
    
    <h1> 基本情報</h1> 
    <!-- 가게 명 -->
    <v-layout mt-1> 
        <v-flex xs12 mr-4> 
            <v-text-field 
                v-if="resData !== null"  
                label="店の名前" 
                v-model="resData[0].name" 
                :rules="nameRule" 
                prepend-icon="home">                             
            </v-text-field>

            <v-text-field 
                v-else 
                label="店の名前" 
                v-model="ipt_data.name" 
                :rules="nameRule"
                prepend-icon="home" > 
            </v-text-field>
        </v-flex> 
    </v-layout> 
    
    <!-- 가게 명 (로마자) -->
    <v-layout mt-1> 
        <v-flex xs12 mr-4> 
            <v-text-field 
                v-if="resData !== null"  
                label="店の名前(ローマ字)"  
                v-model="resData[0].namer" 
                :rules="nameRule"
                prepend-icon="home">                             
            </v-text-field>

            <v-text-field 
                v-else 
                v-model="ipt_data.namer"  
                label="店の名前(ローマ字)" 
                :rules="nameRule" 
                prepend-icon="home" > 
            </v-text-field>
        </v-flex> 
    </v-layout> 

    <!-- 가게 설명 -->
    <v-layout mt-1> 
        <v-flex xs12 mr-4>
            <v-text-field
                v-if="resData !== null"  
                label="紹介" 
                v-model="resData[0].explanation" 
                :rules="explanationRule"
                prepend-icon="assignment" 
                multi-line>
            </v-text-field>

            <v-text-field 
                v-else 
                label="紹介"
                v-model="ipt_data.explanation"   
                :rules="explanationRule" 
                prepend-icon="assignment"
                multi-line>
            </v-text-field>
        </v-flex> 
    </v-layout> 
        
    <!-- 가게 주소 -->
    <v-layout mt-1> 
        <v-flex xs2 mr-4> 
            <v-select 
                v-if="resData !== null"
                label="都道府県" 
                v-model="resData[0].dodobuken" 
                :items="ddbkList" 
                item-value="text" 
                prepend-icon="location_on"
                persistent-hint 
                single-line>
            </v-select>

            <v-select 
                v-else
                label="都道府県"  
                v-model="ipt_data.dodobuken" 
                :items="ddbkList" 
                prepend-icon="location_on"
                persistent-hint 
                single-line>
            </v-select>
        </v-flex>   
        <v-flex xs5 mr-4> 
            <v-text-field
                v-if="resData !== null"  
                label="住所1" 
                v-model="resData[0].cities" 
                :rules="citiesRule">
            </v-text-field> 

            <v-text-field 
                v-else 
                label="住所1" 
                v-model="ipt_data.cities" 
                :rules="citiesRule">
            </v-text-field> 
        </v-flex>                
        <v-flex xs5 mr-4> 
            <v-text-field 
                v-if="resData !== null" 
                label="住所2" 
                v-model="resData[0].address" 
                :rules="addressRule">
            </v-text-field> 

            <v-text-field 
                v-else 
                label="住所2" 
                v-model="ipt_data.address" 
                :rules="addressRule">
            </v-text-field> 
        </v-flex>
    </v-layout>

    <!-- 업종 -->
    <v-layout mt-1>
        <v-flex xs6 mr-4> 
            <v-select 
                v-if="resData !== null"  
                label="料理ジャンル" 
                v-model="resData[0].type" 
                :items="typeList"
                item-value="text" 
                prepend-icon="local_dining"
                persistent-hint 
                single-line>
            </v-select> 

            <v-select 
                v-else  
                label="料理ジャンル" 
                v-model="ipt_data.type" 
                :items="typeList" 
                prepend-icon="local_dining"
                persistent-hint 
                single-line>
            </v-select> 
        </v-flex>                
        <v-flex xs6 mr-4> 
            <v-text-field 
                v-if="resData !== null"  
                label="電話番号 ex) 000-000-0000" 
                v-model="resData[0].phone" 
                :rules="phoneRule" 
                prepend-icon="phone">
            </v-text-field> 

            <v-text-field 
                v-else 
                label="電話番号 ex) 000-000-0000" 
                v-model="ipt_data.phone" 
                :rules="phoneRule" 
                prepend-icon="phone">
            </v-text-field> 
        </v-flex>
    </v-layout>
    
    <v-layout mt-1>
        <!-- 결제 방식 -->
        <v-flex xs6 mr-4>  
            <v-select 
                v-if="resData !== null"  
                label="支払い方法" 
                v-model="resData[0].payment" 
                :items="paymentList" 
                item-value="text" 
                prepend-icon="attach_money"
                persistent-hint 
                single-line>
            </v-select>  

            <v-select 
                v-else  
                label="支払い方法" 
                v-model="ipt_data.payment" 
                :items="paymentList" 
                prepend-icon="attach_money"
                persistent-hint 
                single-line>
            </v-select> 
        </v-flex>   

        <!-- 좌석 수 -->            
        <v-flex xs6 mr-4> 
            <v-text-field 
                v-if="resData !== null" 
                label="席数" 
                v-model="resData[0].seat_num" 
                :rules="seat_numRule" 
                prepend-icon="airline_seat_recline_normal">
            </v-text-field> 

            <v-text-field 
                v-else
                label="席数"
                v-model="ipt_data.seat_num" 
                :rules="seat_numRule" 
                prepend-icon="airline_seat_recline_normal">
            </v-text-field> 
        </v-flex>
    </v-layout>

<!--**************************************************** 영업 시간 정보 ************************************************-->      
    
    <br><br><h1> 営業時間 </h1> 

    <v-layout>
        <v-flex xs4 mr-4> 
            <v-text-field 
                v-if="resData !== null" 
                label="ランチオープン ex) 00:00" 
                v-model="resData[0].lunch_open" 
                :rules="lunch_openRule" 
                prepend-icon="schedule">
            </v-text-field> 

            <v-text-field  
                v-else
                label="ランチオープン ex) 00:00"
                v-model="ipt_data.lunch_open" 
                :rules="lunch_openRule" 
                prepend-icon="schedule">
            </v-text-field> 
        </v-flex>               
        
        <v-flex xs4 mr-4> 
            <v-text-field 
                v-if="resData !== null" 
                label="ランチ終了 ex) 00:00" 
                v-model="resData[0].lunch_close" 
                :rules="lunch_closeRule" 
                prepend-icon="schedule">
            </v-text-field> 

            <v-text-field 
                v-else
                v-model="ipt_data.lunch_close" 
                label="ランチ終了 ex) 00:00" 
                :rules="lunch_closeRule" 
                prepend-icon="schedule">
            </v-text-field> 
        </v-flex> 

        <v-flex xs4 mr-4> 
            <v-text-field 
                v-if="resData !== null" 
                label="ランチラストオーダーex) 00:00" 
                v-model="resData[0].lunch_lo" 
                :rules="lunch_loRule" 
                prepend-icon="schedule">
            </v-text-field> 

            <v-text-field 
                v-else
                label="ランチラストオーダー ex) 00:00" 
                v-model="ipt_data.lunch_lo" 
                :rules="lunch_loRule" 
                prepend-icon="schedule">
            </v-text-field> 
        </v-flex> 
    </v-layout>

    <v-layout>       
        <v-flex xs4 mr-4> 
            <v-text-field 
                v-if="resData !== null" 
                label="ディナオープン ex) 00:00" 
                v-model="resData[0].dinner_open" 
                :rules="dinner_openRule" 
                prepend-icon="schedule">
            </v-text-field> 

            <v-text-field 
                v-else
                v-model="ipt_data.dinner_open" 
                label="ディナオープン ex) 00:00" 
                :rules="dinner_openRule"
                prepend-icon="schedule">
            </v-text-field> 
        </v-flex>    
            
        <v-flex xs4 mr-4> 
            <v-text-field 
                v-if="resData !== null" 
                label="ディナ終了 ex) 00:00" 
                v-model="resData[0].dinner_close" 
                :rules="dinner_closeRule" 
                prepend-icon="schedule">
            </v-text-field> 

            <v-text-field 
                v-else
                label="ディナ終了 ex) 00:00" 
                v-model="ipt_data.dinner_close" 
                :rules="dinner_closeRule" 
                prepend-icon="schedule">
            </v-text-field> 
        </v-flex>    

        <v-flex xs4 mr-4> 
            <v-text-field 
                v-if="resData !== null" 
                label="ディナラストオーダー ex) 00:00" 
                v-model="resData[0].dinner_lo" 
                :rules="dinner_loRule" 
                prepend-icon="schedule">
            </v-text-field> 

            <v-text-field 
                v-else
                label="ディナラストオーダー ex) 00:00" 
                v-model="ipt_data.dinner_lo" 
                :rules="dinner_loRule" 
                prepend-icon="schedule">
            </v-text-field> 
        </v-flex>
    </v-layout> 

<!--***************************************************** 기타 정보 ****************************************************-->
    
    <br><br><h1> その外 </h1> 

    <v-layout>  
        <v-flex xs6 mr-4> 
            <v-select 
                v-if="resData !== null" 
                label="お子様連れ" 
                v-model="resData[0].children" 
                :items="YesOrNo" 
                item-value="text" 
                prepend-icon="child_care"
                persistent-hint 
                single-line>
            </v-select>

            <v-select 
                v-else 
                label="お子様連れ" 
                v-model="ipt_data.children" 
                :items="YesOrNo" 
                prepend-icon="child_care"
                persistent-hint 
                single-line>
            </v-select>
        </v-flex>                

        <v-flex xs6 mr-4> 
            <v-select 
                v-if="resData !== null" 
                label="ペット" 
                v-model="resData[0].pet" 
                :items="YesOrNo" 
                item-value="text" 
                prepend-icon="pets"
                persistent-hint 
                single-line>
            </v-select> 

            <v-select 
                v-else 
                label="ペット" 
                v-model="ipt_data.pet" 
                :items="YesOrNo" 
                prepend-icon="pets"
                persistent-hint 
                single-line>
            </v-select> 
        </v-flex>
    </v-layout>

    <v-layout>  
        <v-flex xs6 mr-4> 
            <v-select 
                v-if="resData !== null" 
                label="駐車場" 
                v-model="resData[0].parking" 
                :items="YesOrNo" 
                item-value="text" 
                prepend-icon="directions_car"
                persistent-hint 
                single-line>
            </v-select> 

            <v-select 
                v-else 
                label="駐車場" 
                v-model="ipt_data.parking" 
                :items="YesOrNo" 
                prepend-icon="directions_car"
                persistent-hint 
                single-line>
            </v-select> 
        </v-flex>                

        <v-flex xs6 mr-4> 
            <v-select 
                v-if="resData !== null" 
                label="喫煙" 
                v-model="resData[0].smoking" 
                :items="YesOrNo" 
                item-value="text" 
                prepend-icon="smoking_rooms"
                persistent-hint 
                single-line>
            </v-select> 

            <v-select 
                v-else 
                label="喫煙" 
                v-model="ipt_data.smoking" 
                :items="YesOrNo" 
                prepend-icon="smoking_rooms"
                persistent-hint 
                single-line>
            </v-select> 
        </v-flex>
    </v-layout>
    
    <v-layout>  
        <v-flex xs6 mr-4> 
            <v-select 
                v-if="resData !== null" 
                label="貸切" 
                v-model="resData[0].privateroom" 
                :items="YesOrNo" 
                item-value="text" 
                prepend-icon="account_box"
                persistent-hint 
                single-line>
            </v-select>

            <v-select 
                v-else 
                label="貸切" 
                v-model="ipt_data.privateroom" 
                :items="YesOrNo" 
                prepend-icon="account_box"
                persistent-hint 
                single-line>
            </v-select>
        </v-flex>   

        <v-flex xs6 mr-4></v-flex>  
    </v-layout>

<!--*********************************************** 갤러리, 타이틀 이미지 등록 *******************************************-->
    <br><br>
    <h1> イメージ登録 </h1> 
    <br>

    <v-layout mt-2>
        <v-icon x-large> image </v-icon>

        <label for="TitleImgUpload" class="upload_btn"> 
            [タイトルイメージ登録] 
        </label> 

        <input 
            id="TitleImgUpload" 
            type="file" 
            accept=".png, .jpg, .jpeg" 
            class="upload_btn_hidden" 
            @change="title_img_load"
        > 
    </v-layout>

    <v-layout mt-2>
        <v-flex xs12>
            <div class="title_div" style="background-color: #efe2bd;">
                <img 
                    v-if="resData !== null" 
                    id="TitleImg" 
                    class="img_style" 
                    :src="resData[2].path + resData[2].filename"
                > 
                <img v-else id="TitleImg" class="img_style"> 
            </div>
        </v-flex>
    </v-layout> <br>

    <v-layout mt-2>
        <v-icon x-large> image </v-icon>

        <label for="GalleryImgUpload" class="upload_btn"> 
            [ギャラリーイメージ登録] 
        </label> 

        <input 
            id="GalleryImgUpload" 
            type="file" 
            accept=".png, .jpg, .jpeg" 
            class="upload_btn_hidden"
            @change="gallery_img_load"
        > 
    </v-layout>

    <div id="gallery"></div>
    
    <v-dialog v-model="CancelDialog" width="30%" persistent>
        <v-card>
            <v-card-title class="headline">
                選んだイメージの登録をキャンセルしますか。
            </v-card-title> 

            <v-card-actions> 
                <v-btn 
                    @click="removeImg"                    
                    @click.native="CancelDialog = false" 
                    color="success" 
                    style="margin:auto"
                > 
                    はい
                </v-btn>

                <v-btn 
                    @click.native="CancelDialog = false" 
                    color="error" 
                    style="margin:auto"
                > 
                    いいえ 
                </v-btn>
            </v-card-actions>
        </v-card>
    </v-dialog>
</v-container>
</form>
</div> 
</template>

<script type="text/javascript"> 
import Vue      from 'vue';
import VueAxios from 'vue-axios';
import axios from 'axios';  
import * as VueGoogleMaps from 'vue2-google-maps'; 

Vue.use(VueGoogleMaps, {
    load: {
        key: "AIzaSyDTHKQzISVxAAfuBGp0HKj5GpMPNqR_Ovo",
        libraries: "places" // necessary for places input
    }
})

var coordinateLat = null;
var coordinateLng = null;

export default {     
    mounted(){ 
        let url = '/restaurant/' + this.$route.params.shop_id + '/getInfo';       

        axios.get(url).then( (response) => { 
            if(typeof response.data.restaurant !== 'null'){           
                this.resData = response.data.restaurant;     
                this.setInfo();
                this.resImgSet(); 
                // this.MakeQrCode();
            }  
        })
        .catch((ex)=>{ 
        });       
    },

    data(){
        return{
            // 스낵바 설정값
            timeout         : 2000,
            snackbar_text   : '',
            snackbar        : false,

            resData      : null,            // 서버한테 받는 데이터. 
            CancelDialog : false,           // 갤러리 이미지 등록 취소.
            CancelImg    : null,            // 취소할 이미지

            SaveImg : [],                   // 갤러리 이미지 저장용

            QrCodeDialog : false,           // 큐알코드 팝업 div 용
 
            // 최초 등록 시 사용
            ipt_data : {
                // 기본 정보            
                name        : '',         // 가게 명
                namer       : '',         // 가게 명 - 로마자
                explanation : '',         // 가게 설명
                dodobuken   : '',         // 주소 - 도도부현
                cities      : '',         // 주소 - 상세주소1
                address     : '',         // 주소 - 상세주소2
                type        : '',         // 업종
                phone       : '',         // 전화번호
                payment     : '',         // 결제 방법
                seat_num    : '',         // 좌석 수
                
                // 영업 시간
                lunch_open   : '',        // 런치 오픈 시간
                lunch_close  : '',        // 런치 마감 시간
                lunch_lo     : '',        // 런치 라스터 오더 시간
                dinner_open  : '',        // 디너 오픈 시간
                dinner_close : '',        // 디너 마감 시간
                dinner_lo    : '',        // 디너 라스트 오더 시간

                // 기타 정보 yes or no
                children    : '',         // 아이 동반 가능 여부
                pet         : '',         // 애완 동물 동반 가능 여부
                parking     : '',         // 주차 공간 유무
                smoking     : '',         // 흡연실 유무
                privateroom : '',         // 개인실 유무
            },  
            
            // 경고 메세지 출력
            nameRule         : [ v => !!v || '書いてください' ],
            explanationRule  : [ v => !!v || '書いてください' ],
            citiesRule       : [ v => !!v || '書いてください' ],
            addressRule      : [ v => !!v || '書いてください' ],
            phoneRule        : [ v => !!v || '書いてください' ],
            seat_numRule     : [ v => !!v || '書いてください' ],
            lunch_openRule   : [ v => !!v || '書いてください' ],
            lunch_closeRule  : [ v => !!v || '書いてください' ],
            lunch_loRule     : [ v => !!v || '書いてください' ],            
            dinner_openRule  : [ v => !!v || '書いてください' ],
            dinner_closeRule : [ v => !!v || '書いてください' ],
            dinner_loRule    : [ v => !!v || '書いてください' ],        
            
            // 도도부현 리스트 배열
            ddbkList : [
                { text: '東京'},    { text: '北海道'},  { text: '札幌'},    { text: '京都'},
                { text: '大阪'},    { text: '靑森'},    { text: '岩手'},    { text: '宮城'},
                { text: '秋田'},    { text: '山形'},    { text: '福島'},    { text: '茨城'},
                { text: '栃木'},    { text: '群馬'},    { text: '埼玉'},    { text: '千葉'},
                { text: '神奈川'},  { text: '新潟'},    { text: '富山'},    { text: '石川'},
                { text: '福井'},    { text: '山梨'},    { text: '長野'},    { text: '岐阜'},
                { text: '靜岡'},    { text: '愛知'},    { text: '三重'},    { text: '滋賀'},
                { text: '兵庫'},    { text: '奈良'},    { text: '和歌山'},  { text: '鳥取'}, 
                { text: '島根'},    { text: '岡山'},    { text: '廣島'},    { text: '山口'},     
                { text: '德島'},    { text: '香川'},    { text: '愛媛'},    { text: '高知'},     
                { text: '福岡'},    { text: '佐賀'},    { text: '長崎'},    { text: '熊本'},     
                { text: '大分'},    { text: '宮崎'},    { text: '鹿兒島'},  { text: '沖繩'},
            ],

            // 업종
            typeList : [
                { text: '韓国料理'}, { text: '和食'}, { text: '中華料理'}, { text: '洋食'}, { text: '分食'},
                { text: '丼'}, { text: '寿司'}, { text: 'ファーストフード'}, { text: 'チム'}, { text: '汁物'},
                { text: '弁当'}, { text: 'カフェー&デザート'}, { text: '居酒屋'}, { text: '面流'}, { text: '製菓'}
            ],

            // 결제 방법
            paymentList : [{ text: '現金決済'},{ text: 'カード決済'},{ text: 'すべて可能'}],
            
            // 기타 정보 선택 사항
            YesOrNo : [ { text: 'yes'},  { text: 'no'} ],
        }
    },

    methods : {
        // 기타정보 세팅
        setInfo : function(){
            let arr = ['children','pet','parking','smoking','privateroom'];

            if(this.resData !== null){
                for(let i=0; i<arr.length; i++){ 
                    if(this.resData[0][arr[i]] == true)
                        this.resData[0][arr[i]] = 'yes';                    
                    else 
                        this.resData[0][arr[i]] = 'no';
                }
            }
        },

        // 서버에서 받은 이미지 출력
        resImgSet : function(){            
            let gallery = document.getElementById('gallery');    // 갤러리        

            if(this.resData.length -3 !== 0){
                for(let i=3; i < this.resData.length; i++){                     
                    let createdDiv = document.createElement("div");                     // 새 div 생성
                    let createdImg = document.createElement("img");                     // 새 img 생성
                    let imgPath    = this.resData[i].path + this.resData[i].filename;   // 서버한테 받은 이미지의 경로.     

                    createdImg.src = imgPath;                                           // 이미지 삽입
                    createdImg.classList.add("img_style");                              // 새 img alt 속성 추가
                    createdImg.addEventListener("click", this.clickImg);

                    createdDiv.classList.add("gallery_div");                            // css 적용
                    createdDiv.appendChild(createdImg);                                 // 추가하기.          
                    gallery.appendChild(createdDiv);                                    // 갤러리에 이미지 추가
                    
                    this.SaveImg.push(this.resData[i].id);                              // 이미지 관리 배열에 추가하기.
                }  
            }            
        },

        // MakeQrCode : function(){            
        //     let qrcode  = document.getElementById('qrcode'); 

        //     // 가게 정보 수정인 경우  
        //     if(this.resData !== null){
        //         qrcode.src = this.resData[0].qrcode;
        //     }
        //     // 가게 정보 최초 입력인 경우
        //     else if(this.resData === null || this.resData[0].qrcode === null){
        //         let address = "http://13.125.166.127/restaurant/" 
        //                     + this.$route.params.shop_id 
        //                     + "/info";

        //         qrcode.src  = 'http://api.qrserver.com/v1/create-qr-code/?data='
        //                     + address 
        //                     + '&size=100x100';
        //     }
        // },

        // 타이틀 이미지 업로드 메서드
        title_img_load : function(evnet){ 
            let titleImg = document.getElementById("TitleImg");  // 타이틀 이미지
            let reader   = new FileReader();

            // 이미지 미리보기 띄우기
            reader.onload = function() {
                titleImg.src = reader.result; 
            };
            reader.readAsDataURL(event.target.files[0]);
        },

        // 갤러리 이미지 업로드 메서드
        gallery_img_load : function(event){
            let file       = event.target.files[0];
            let reader     = new FileReader();
            let gallery    = document.getElementById('gallery');             
            let createdDiv = document.createElement("div");     // 새 div 생성
            let createdImg = document.createElement("img");     // 새 img 생성

            reader.onload = function() { 
                createdImg.src = reader.result; 
            };            
            createdImg.classList.add("img_style");              // 새 img alt 속성 추가
            createdImg.addEventListener("click", this.clickImg);

            createdDiv.classList.add("gallery_div");            // css 적용
            createdDiv.appendChild(createdImg);                 // 추가하기.          
            gallery.appendChild(createdDiv);

            reader.readAsDataURL(file); 
            this.SaveImg.push(file); 
        },  

        // 갤러리 이미지 클릭 - 삭제하실?
        clickImg : function(){
            this.CancelDialog = true;
            this.CancelImg = event.target; 
        },

        // 이미지 삭제
        removeImg : function(){
            let div = this.CancelImg.parentNode; 
            let gallery = document.getElementById('gallery');  

            // 이미지 파일 저장 배열에서도 제거.
            for(let i=0; i < gallery.children.length; i++){
                if(div === gallery.children[i]){ 
                    this.SaveImg[i] = 'dd';
                }
            }             
            gallery.removeChild(div);  
        },  

        // 구글 맵스 설정하기.
        geoCoderFunction : function () {        
            let geocoder = new google.maps.Geocoder();     
            let address  = null;
            let saveData = this.save_data;

            // 가게 정보 최초 입력 시
            if(this.resData == null){
                address = this.ipt_data.dodobuken + " " 
                        + this.ipt_data.cities + " " 
                        + this.ipt_data.address; 
            }     
            // 가게 정보 수정 시
            else {
                address = this.resData[0].dodobuken + " " 
                        + this.resData[0].cities + " " 
                        + this.resData[0].address;
            }

            if(address.replace(/ /gi, "") === '') {          
                this.snackbar_text = '住所を書いてください。';
                this.snackbar      = true; 
                return null;
            } 

            // 지오 코드 실행 : 텍스트 주소로 해당 주소 경위도 구하기.
            geocoder.geocode( { 'address': address}, function(results, status) {
                if (status == 'OK') { 
                    var lat = results[0].geometry.location.lat();
                    var lng = results[0].geometry.location.lng();
                    coordinateLat = Number(lat);
                    coordinateLng = Number(lng); 
                    saveData();
                }  
            });
        },
 
        // 입력한 데이터 저장하기 
        save_data :function() {     
            let formData        = new FormData(document.getElementById("upload_info"));    // 입력 값들을 담아 전송함.
            let TitleImgUpload  = document.getElementById('TitleImgUpload');               // 업로드한 타이틀 이미지 파일
            let TitleImg        = document.getElementById('TitleImg');                     // 타이틀 이미지 img 태그
            // let qrcode          = document.getElementById('qrcode');
            let iterator        = null;
            let datas           = null;                                                    // 입력한 데이터들 
            let address         = null;
             
            // 가게 정보 최초 입력 시
            if(this.resData == null){ 
                iterator = Object.keys(this.ipt_data);                 
                datas    = this.ipt_data;
            }     
            // 가게 정보 수정 시
            else {                
                iterator = Object.keys(this.resData[0]); 
                datas    = this.resData[0];
            }            
            // 지정하지 않는 data 배열을 숫자 키값 제외시키기
            for(let key of iterator) {   
                if(this.ipt_data[key] !== undefined){
                    let appendData = null;
                
                    // text input 값 담기
                    if (isNaN(key) === true && typeof datas[key] === 'object'){
                        appendData = datas[key].text;
                    }
                    else {
                        appendData = datas[key];
                    }
                    appendData = appendData.toString();
                    
                    // 모든 정보가 입력되었는지 검사함.
                    if(appendData.replace(/ /gi, "") === ''){ 
                        this.snackbar_text = 'まだ書かない情報があります。' + appendData;
                        this.snackbar = true;
                        return null;
                    } 
                    else {
                        if(appendData == 'yes'){
                            appendData = true;
                        }else if (appendData == 'no'){
                            appendData = false;
                        }
                        formData.append(key, appendData);
                    }      
                }                                      
            } 

            if(coordinateLat !== null){                        
                formData.append('coordinateLat', coordinateLat);
            }

            if(coordinateLng !== null){                        
                formData.append('coordinateLng', coordinateLng);
            }
            
            // 타이틀 이미지 append
            if(TitleImgUpload.files.length !== 0) {
                formData.append('titleImg', TitleImgUpload.files[0]);
            }
            // 타이틀 이미지 없음
            else if(TitleImg.src ===''){
                formData.append('titleImg', null);
            }
            // 가게 정보 수정인 상황에서 타이틀 이미지 수정 안함.
            else if(this.resData !== null && TitleImgUpload.files.length == 0){
                formData.append('titleImg', this.resData[2].path + this.resData[2].filename);
            }

            // 갤러리 이미지 넣기.
            for(let i=0; i < this.SaveImg.length; i++){
                formData.append('galleryImg'+ i, this.SaveImg[i]);
            }

            // 갤러리 이미지 갯수 보내기
            formData.append('num', this.SaveImg.length);
            formData.append('shop_id', this.$route.params.shop_id); 

            // QR 코드 
            // formData.append('qrcode', qrcode.src);

            // 콘솔창에 띄우기
            for(var pair of formData.entries()) {
                console.log(pair[0]+ ': '+ pair[1]); 
            }

            // 수정하기인 경우 
            if(this.resData !== null){ 
                // 값 보내기
                axios.post('/owner/updateRestaurantInfo',formData)
                .then( (response) => {
                    this.snackbar_text = response.data.msg;
                    this.snackbar = true;
                    this.link = response.data.link;
                    window.location.href = this.link;
                })
                .catch((ex)=>{
                    console.log('updatePhoto failed',ex);
                })                 
            }
            // 가게 정보 최초 등록인 경우 
            else {      
                // 값 보내기
                axios.post('/owner/createRestaurant',formData)
                .then( (response) => {
                    this.snackbar_text = response.data.msg;
                    this.snackbar = true;
                    this.link = response.data.link;
                    window.location.href = this.link;
                })
                .catch((ex)=>{
                    console.log('updatePhoto failed',ex);
                })
            }              
        }
    }
};
</script>

<style>  
/* 이미지 업로드 버튼의 라벨 스타일 */
.upload_btn {
    width: 100%;
    font-size: 1.5rem;
    font-weight: bold;
    position: relative;  
    color: #9d724b;
    user-select:none;               /* 드래그 방지 */
    -ms-user-select: none; 
    -moz-user-select: -moz-none; 
    -webkit-user-select: none; 
    -khtml-user-select: none; 
}
/* 마우스 클릭하고있을때 */
.upload_btn:active{
    color: #d2b07d;
}
/* 마우스 한번클릭후 */
.upload_btn:visited{
    color: #9d724b;
} 
/* 이미지 업로드 버튼 스타일 */
.upload_btn_hidden {
    width: 0%;
    font-size: 0px;
    position: relative; 
    opacity: 0;
}
/* 타이틀 이미지 상위 card */
.title_div {
    position: relative;
    width: 100%;
    height: 0;
    overflow: hidden;
    padding-bottom: 35%;
}

#gallery {
    width:100%; 
    min-height: 200px;
    background-color: #efe2bd; 
    margin:auto; 
    overflow:hidden
}

/* 갤러리 이미지 상위 card */
.gallery_div {
    position: relative;
    float: left;
    margin: 1%;   
    width: 31%;
    height: 0;
    overflow: hidden; 
    padding-bottom: 22%; 
    border: none;
}
/* 타이틀, 갤러리 이미지 스타일 */
.img_style { 
    top: 0;
    left: 0;
    width: 105%;
    height: 105%;  
    object-fit: cover;
    position: absolute;
}   
/* 저장하기 버튼 스타일 */
.submit_btn{
    width:80%; height: 80%; font-size:1.5rem; 
}
</style>