<template>
    <div class="container"> 
    <v-container>
        <!-- 스낵바 : 경고 창 출력 -->
        <v-snackbar 
            v-model="snackbar"
            :timeout="timeout" 
            :top="'top'" 
        >
            {{ snackbar_text }}

            <v-btn 
                @click.native="snackbar = false"
                color="pink" 
                flat icon 
            >
                <v-icon large> close </v-icon>
            </v-btn>
        </v-snackbar>
 
        <v-card class="pa-3">
            <v-layout> 
                <v-flex xs10> 
                    <h1 style="font-size:3.5rem"> 
                        メニュー登録 
                    </h1>
                </v-flex>  

                <v-flex xs2>
                    <v-btn 
                        @click="save_data"
                        style=" height:90%; 
                                margin:0; 
                                background-color:#ff9a55; 
                                color:white; 
                                font-size:2rem; 
                                font-weight:bold;"                             
                        block 
                    > 
                        セーブ
                    </v-btn>
                </v-flex>  
            </v-layout> 
        </v-card>
         
        <v-layout> 
            <!-- 메뉴 이미지 영역 -->
            <v-flex xs8>          
                <v-card class="My_card_style">                
                    <v-layout mb-3> 
                        <v-flex xs12>          
                            <h2 class="My_card_title"> 
                                メニューのイメージ
                            </h2>

                            <input 
                                type="file" 
                                id="img_upload_btn" 
                                accept=".png, .jpg, .jpeg" 
                                @change="img_upload"
                            >    

                            <label 
                                for="img_upload_btn" 
                                class="btn_label"
                            >
                                イメージ登録　
                            </label>

                            <div class="img_div">
                                <img id="menu_img">
                            </div>  
                        </v-flex>
                    </v-layout>

                    <v-layout>
                        <v-flex xs6 mr-3>          
                            <v-text-field 
                                label="メニューの名前" 
                                v-model="name" 
                                :rules="nameRule" 
                            >
                            </v-text-field>
                                
                            <v-text-field 
                                label="メニューの価格" 
                                v-model="price" 
                                :rules="priceRule"
                            >
                            </v-text-field>                        
                        </v-flex>
                        
                        <v-flex xs6>   
                            <v-select 
                                label="カテゴリー"
                                v-model="category" 
                                :items="states"
                                item-text="name" 
                                :filter="customFilter" 
                                autocomplete
                            >
                            </v-select>

                            <v-select 
                                label="ランチ / ディナー"
                                v-model="remark" 
                                :items="remarkList" 
                            >
                            </v-select>                
                        </v-flex>
                    </v-layout>
                
                    <v-layout>                     
                        <v-text-field 
                            v-model="explanation" 
                            label="メニューの説明" 
                            multi-line
                        >
                        </v-text-field> 
                    </v-layout>              
                </v-card>          
            </v-flex> 

            <!-- 메뉴 옵션 추가하기 -->
            <v-flex xs4>
                <v-card class="My_card_style" style="height: 100%;" elevation-24>
                    <v-flex xs12 style="border-bottom: 2px solid #ff9a55;">
                        <h2 class="My_card_title"> 
                            オプション登録
                        </h2> 

                        <label 
                            for="create_option" 
                            class="btn_label"
                        > 
                            オプション追加
                        </label>

                        <input 
                            type="button" 
                            id="create_option" 
                            @click="create_option" 
                            style="opacity:0;"
                        >
                    </v-flex>                    
                    <div id="options"></div>                
                </v-card>
            </v-flex>
        </v-layout>         
    </v-container>
    </div>
</template>
        
<script type="text/javascript"> 
import VueAxios from 'vue-axios';
import axios from 'axios';
 
// var shop_id = this.$route.params.shop_id;  
 
export default {
    data() {
        return { 
            // 스낵바 설정값
            timeout         : 2000,
            snackbar_text   : '',
            snackbar        : false,

            // 입력 받을 거
            name        : null,            
            category    : null,
            price       : null,
            remark      : null,
            explanation : null,

            // 입력하지 않으면 경고 메세지 출력
            nameRule        : [ v => !!v || '名前を書いてください。' ],        
            priceRule       : [ v => !!v || '値段を書いてください。' ], 

            states: [
                { name: '特選'}, { name: '推薦メニュー'}, { name: '丼'},
                { name: '御汁'}, { name: '蒸かし'}, { name: '肴'},
                { name: '酒'}, { name: '飲み物'}
            ],
            customFilter (item, queryText, itemText) {
                const hasValue = val => val != null ? val : ''
                const text = hasValue(item.name)
                const query = hasValue(queryText)
                return text.toString()
                .toLowerCase()
                .indexOf(query.toString().toLowerCase()) > -1
            },

            // 점심, 저녁 메뉴 구분
            remarkList : [{ text: '関係ない'},{ text: 'ランチ'},{ text: 'ディナー'}]            
        }
    },

    methods : {
        // 저장
        save_data : function () {
            let formData = new FormData(); 
            let menu_img = document.getElementById('img_upload_btn');   // 등록한 이미지 
            let options  = document.getElementById('options');          // 카테고리 리스트 가져오기
            
            // 업로드한 이미지가 있는지 체크 후 진행함
            if(menu_img.files[0] !== undefined){   
                formData.append('menu_img',menu_img.files[0]);
                formData.append('shop_id', this.$route.params.shop_id);

                // 모든 값이 입력 되어 있으면 실행
                if( this.name !== null && this.price !== null && this.category !== null && 
                    this.explanation !== null && this.remark !== null) {
                    
                    formData.append('name', this.name);                     // 메뉴 명
                    formData.append('price', this.price);                   // 메뉴 가격
                    formData.append('category', this.category.name);        // 메뉴 카테고리
                    formData.append('remark', this.remark.text);            // 런치 / 디너 구분
                    formData.append('explanation', this.explanation);       // 메뉴 설명
 
                    if(options.children.length !== 0){
                        for(let i=0; i < options.children.length; i++){
                            let optionBox = options.children[i];   
                            let optionName = optionBox.children[0].children[0];               
 
                            // 옵션 명이 입력 창인 상태이거나 옵션 명이 입력되지 않은 경우 경고 메세지 출력
                            if(optionName.tagName !== 'B' || optionName.innerText === ''){
                                this.snackbar_text = 'まだ書かなかったオプションがあります。';
                                this.snackbar = true;
                                return null; 
                            }
                            // 옵션 이름, 옵션 값 갯수 담기.
                            formData.append('option['+ i +'][name]', optionName.innerText);
                            formData.append('option['+ i +'][num]',  optionBox.children.length -1);

                            // 옵션 값 담기 (선택 값이 2개 이하면 경고 메세지 출력)
                            if(optionBox.children.length -1 > 1){
                                for(let j=0; j < optionBox.children.length -1; j++){
                                    let optionValue = optionBox.children[j+1].children[0];  // 옵션 값

                                    // 옵션 값이 입력 창인 상태이거나 옵션 명이 입력되지 않은 경우 경고 메세지 출력
                                    if(optionValue.tagName !== 'B' || optionValue.innerText === ''){
                                        this.snackbar_text = 'まだ書かなかったオプションがあります。';
                                        this.snackbar = true;
                                        return null;  
                                    }
                                    // 이상 없으면 폼 데이터에 담기
                                    formData.append('option['+ i +'][value' + j+']', optionValue.innerText);
                                }
                            }
                            else {
                                this.snackbar_text = '2つ以上のオプションの内容を作ってください。';
                                this.snackbar = true;
                                return null; 
                            }                                                                                                            
                        }
                    }                  
                    // 콘솔창에 띄우기
                    for(var pair of formData.entries()) {
                        console.log(pair[0]+ ', '+ pair[1]); 
                    }
                    
                    // 데이터 보내기.
                    axios.post('/owner/createMenu',formData)
                    .then( (response) => {                        
                        this.snackbar_text = response.data.content;
                        this.snackbar = true;
                        location.reload(); 
                    })
                    .catch((ex)=>{
                        console.log('failed',ex);
                    });                    
                }
                // 메뉴 정보가 모두 입력되지 않은 경우 경고 메세지 출력
                else {
                    this.snackbar_text = '情報を全部書いてください。';
                    this.snackbar = true;
                    return null; 
                } 
            }
            // 등록된 이미지가 없는 경우 경고 메세지 출력
            else {
                this.snackbar_text = 'イメージを登録してください。';
                this.snackbar = true;
                return null;  
            }            
        },

        // 이미지 업로드하기.
        img_upload: function(){
            let menu_img = document.getElementById("menu_img");  // 메뉴 이미지
            let reader   = new FileReader();

            // 이미지 미리보기 띄우기
            reader.onload = function() {
                menu_img.src = reader.result;
            };
            reader.readAsDataURL(event.target.files[0]);
        },

        // 옵션 만들기.
        create_option : function() {
            let options         = document.getElementById('options');       // 옵션들이 들어갈 div
            let options_box     = document.createElement('div');            // 옵션 명, 옵션 값을 담을 div
            let created_option  = document.createElement('div');            // 옵션 명이 될 div   
            let created_input   = document.createElement('input');          // 카테고리 명 입력 창 만들기.
            let created_btns    = [                                         // 옵션 명 div에 들어갈 버튼들.
                                    document.createElement('input'),       
                                    document.createElement('input'),        
                                    document.createElement('input')       
                                ] 
            created_input.classList.add('op_ipt');
            
            created_option.prepend(created_input);
            created_option.classList.add('optionName');                     // 옵션 명에 css 추가 

            // 들어갈 버튼들 값 설정, css 추가, 메소드 설정
            for (let i=0; i < created_btns.length; i++){                   
                created_btns[i].setAttribute('type','button');
                created_btns[i].classList.add('optionBtn');

                switch(i){
                    case 0:
                        created_btns[i].value   = '削除';　// 삭제 
                        created_btns[i].onclick = this.delete_option; break;
                    case 1:
                        created_btns[i].value   = '内容追加';   // 내용 추가
                        created_btns[i].onclick = this.add_opionValue; break;
                    case 2: 
                        created_btns[i].value   = '完了';    // 완료
                        created_btns[i].onclick = this.rename_complete; break;
                }
                created_option.appendChild(created_btns[i]);
            }
            // 옵션 목록 div에 옵션 삽입
            options_box.classList.add('optionBox');
            options_box.appendChild(created_option);
            options.appendChild (options_box);

        }, // end of create_option

        // 옵션 명 수정하기
        rename_option : function(event){
            let get_option      = event.target.parentNode;             // 해당 옵션 가져오기
            let get_rename_btn  = event.target                         // 수정 버튼 가져오기 
            let created_input   = document.createElement('input');     // 카테고리 명 입력 창 만들기.

            get_rename_btn.value   = "完了";                           // 수정 버튼을 완료 버튼으로 변경
            get_rename_btn.onclick = this.rename_complete;
 
            created_input.classList.add('op_ipt'); 

            get_option.removeChild(get_option.children[0]);             // 기본 카테고리명 지우기.
            get_option.prepend(created_input);                          // 첫번째 자식으로 넣음
        }, 

        // 이름 수정 완료
        rename_complete : function(){
            let get_option  = event.target.parentNode;          // 해당 카테고리 가져오기
            let get_cpt_btn = event.target;                     // 완료 버튼 가져오기.
            let get_iptText = get_option.children[0];           // input 태그 가져오기
            let created_b   = document.createElement('b');      // 카테고리 명 생성.
 
            if(get_iptText.value.replace(/ /gi, "") === ''){
                this.snackbar_text = '内容を書いてください。';
                this.snackbar = true;
                return null; 
            }
            created_b.innerText = get_iptText.value;                                                   
            created_b.classList.add('op_b');
            
            get_cpt_btn.value   = "修正";                       // 완료 버튼을 수정 버튼으로 변경시킴. 
            get_cpt_btn.onclick = this.rename_option;   
 
            get_option.removeChild(get_iptText);              // input 태그 삭제.
            get_option.prepend(created_b);                    // 첫번째 자식으로 넣음
        },

        // 옵션 값 생성
        add_opionValue : function(event){  
            let get_box         = event.target.parentNode.parentNode;      // 해당 카테고리 박스 가져오기
            let optionValue     = document.createElement('div');           // 하위 카테고리 div 생성
            let created_input   = document.createElement('input');          // 카테고리 명 입력 창 만들기.            
            let created_btns    = [
                                    document.createElement('input'), 
                                    document.createElement('input')
                                ];            
            // 들어갈 버튼들 값 설정, css 추가.
            for (let i=0; i < created_btns.length; i++){                   
                created_btns[i].setAttribute('type','button');
                created_btns[i].classList.add('optionBtn');

                switch(i){
                    case 0:
                        created_btns[i].value   = '削除';  
                        created_btns[i].onclick = this.delete_optionValue; break;
                    case 1:
                        created_btns[i].value   = '完了'; 
                        created_btns[i].onclick = this.rename_complete; break; 
                }
                optionValue.appendChild(created_btns[i]);
            }             
            created_input.classList.add('op_ipt');
            optionValue.classList.add('optionValue');
            optionValue.prepend(created_input);
            get_box.appendChild(optionValue);
        }, // end of add_opionValue

        // 클릭한 옵션 삭제 
        delete_option : function(){
            let option = event.target.parentNode;
            option.parentNode.parentNode.removeChild(option.parentNode);   
        },

        // 클릭한 옵션 값 삭제 
        delete_optionValue : function(){
            let op_value = event.target.parentNode;
            op_value.parentNode.removeChild(op_value);          
        },

    }, // end of method
}
</script>

<style>

/* v-card padding 값 통일 */
.My_card_style{
    width: 100%; 
    padding: 5%;
}
.My_card_title{
    float:left;  
}

/* input file 안보이게 하기 */
#img_upload_btn{
    width: 0%;
    font-size: 0px;
    opacity: 0;
    position: relative; 
}
/* 메뉴 이미지 업로드 라벨 */
.btn_label{
    font-size: 1.5rem;
    margin-right: 3%; 
    font-weight: bold;
    float:right;
    color: #6d4d35; 
    user-select:none;               /* 드래그 방지 */
    -ms-user-select: none; 
    -moz-user-select: -moz-none; 
    -webkit-user-select: none; 
    -khtml-user-select: none; 
}
/* 마우스 클릭하고있을때 */
.btn_label:active{
    color: #9d724b;
}
/* 마우스 한번클릭후 */
.btn_label:visited{
    color: #6d4d35;
} 
/* 메뉴 이미지 비율 고정용 */
.img_div {
    width: 100%;
    height: 0;
    padding-bottom: 70%; 
    border: 1px solid #9d724b;
    overflow: hidden;
    position: relative;
    margin: auto;
}
/* 메뉴 이미지 스타일 */
#menu_img { 
    top: 0;
    left: 0;
    width: 100%;
    height: 100%; 
    object-fit: cover; 
    position: absolute;
} 
/* 옵션들이 출력될 div 스타일 */
#options{
    width:100%;
    height: auto;
    overflow: hidden; 
    margin-bottom: 5%; 
}
/* 하나의 옵션명과 그 옵션 값들을 감싸는 div */
.optionBox {
    width: 100%;
    height: auto; 
    overflow:hidden; 
    margin-top: 10%;
}
/* 옵션명 */
.optionName {
    width: 100%; 
    margin-top: 1%;
    border-bottom: 1px solid;
    position: relative; 
    overflow:hidden; 
}
/* 옵션명의 버튼들 */
.optionBtn {
    min-width: 10%;
    margin: 1%;
    font-size: 1.2rem;
    float: right;
    position: relative;
    color: #6d4d35; 
    font-weight: bold;
}
/* 옵션의 값 */
.optionValue {
    width: 95%; 
    margin-top: 1%;
    border-bottom: 1px solid;
    position: relative;
    overflow:hidden; 
    float: right; 
}
/* 옵션 명을 출력할 b태그 */
.op_b {
    margin-left: 1%;
    font-size: 1.3rem;
    float: left; 
}
/* 옵션 수정에 사용할 input text 태그 */
.op_ipt {
    width: 40%; 
    margin-left: 1%; 
    background-color: #d2b07d;  
    font-size: 1.2rem;
    float: left; 
    font-weight: bold;
}
</style>