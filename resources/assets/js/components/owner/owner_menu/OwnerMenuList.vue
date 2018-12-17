<template>
<div class="container" style="width:100%; background-color:white;">
<v-app>
<!-- 스낵바 : 경고 창 출력 -->
<v-snackbar 
    v-model="snackbar"
    :timeout="timeout" 
    :top="'top'" 
>
    {{ snackbar_text }}
    <v-btn 
        @click.native="snackbar = false"
        color="pink" icon flat 
    >
        <v-icon large> close </v-icon>
    </v-btn>
</v-snackbar>    

<v-container id="menus">
    <!-- 카테고리 영역 -->
    <v-layout                    
        v-for="a in range(0, categorysRow-1)" 
        :key="'categoryRow' + a"
    >
        <v-flex xs2 lg2 mr-1                
            v-for="b in range((a*6), (a*6)+5)" 
            :key="'category' + b"  
        >
            <v-btn 
                v-if="categorys[b] !== undefined"
                @click="click_category"
                style="background-color: #ff9a55; color: white; font-size:1.5rem;"
                :value="categorys[b]"
                round block
            >
                {{categorys[b]}}
            </v-btn>
        </v-flex>   
    </v-layout> 

    <!-- 메뉴들 -->
    <v-layout ma-3 mb-3 v-for="n in range(0, menu_num-1)" :key="'menuRow' + n">   
        <!-- 메뉴 영역 --> 
        <v-flex xs4 mr-3 
            v-for="m in range((n*3), (n*3)+2)" 
            :key="'menu' + m"  
        >                
            <v-layout                 
                class="pa-2"
                v-if="get_menus[m] !== undefined"
                style="background-color: #d2b07d;"
                :id="'menu' + (m+1)"
            >   
                <!-- 메뉴 이미지 -->             
                <v-flex xs3 class="menuOuter">
                    <!-- <img src="./ex.jpg" class="menuImg"> -->
                    <img class="menuImg" :src="get_menus[n].path + get_menus[n].filename">
                </v-flex>  

                <!-- 메뉴 이름 --> 
                <v-flex xs6 
                    class="pa-1"
                    style="background-color: white;"
                >
                    {{get_menus[m].name}} 
                </v-flex>  

                <!-- 메뉴 수정/삭제 버튼 --> 
                <v-flex xs3>
                    <div style="width:100%; height:50%;">                        
                        <button
                            @click="modifyMenu" 
                            style=" width:100%; 
                                    height:100%;  
                                    color:white; 
                                    background-color:#9d724b;"
                            :value="m+1"   
                        >
                            修正
                        </button>
                    </div>
                    <div style="width:100%; height:50%;">
                        <button                       
                            @click.stop="DeleteDialog = true; clickedMenu = m+1"
                            style=" width:100%; 
                                    height:100%;  
                                    color:white; 
                                    background-color:#6d4d35;"
                            :value="m+1"    
                        >
                            削除
                        </button>
                    </div>                   
                </v-flex>
            </v-layout>
        </v-flex>
    </v-layout>  

    <!-- 메뉴 삭제 -->
    <v-dialog v-model="DeleteDialog" width="30%" persistent>
        <v-card style="background-color:#f6e2a1; padding:3%;">                                    
            <div 
                style= "width:100%; 
                        text-align:center; 
                        margin-bottom:3%;"
            >
                <b style="font-size:1.5rem;"> 
                    メニューを削除しましょうか。 
                </b> 
            </div> 

            <v-layout>
                <v-flex xs6>
                    <v-btn 
                        @click="deleteMenu" 
                        @click.stop="DeleteDialog = false"
                        style="font-weight:bold; background-color:#9d724b;"   
                        block 
                    >
                        はい
                    </v-btn>
                </v-flex>

                <v-flex xs6>
                    <v-btn 
                        @click.stop="DeleteDialog = false"
                        style="font-weight:bold; background-color:#d2b07d;"
                        block 
                    >
                        いいえ
                    </v-btn>  
                </v-flex>
            </v-layout>                                                                     
        </v-card>
    </v-dialog>
</v-container>

<!-- 메뉴 수정하기. -->
<div style="width:100%" v-if="editMenu !== null" > 
    <!-- 타이틀 -->     
    <v-card class="pa-3" style="width:100%;"> 
        <v-layout class="mb-3"> 
            <v-flex xs8>
                <b style="font-size:3rem; "> メニューを修正 </b>
            </v-flex>       
            <v-spacer></v-spacer>

            <v-flex xs2>
                <v-btn 
                    @click="modifySave"  
                    style=" height: 100%;
                            margin: 0;
                            color: white;
                            background-color: #ff9a55;
                            font-size: 2rem; 
                            font-weight:bold;" 
                    block 
                >
                    修正
                </v-btn> 
            </v-flex>        
            <v-flex xs2 ml-3 mr-3> 
                <v-btn  
                    @click="modifyCancel"
                    style=" height: 100%;
                            margin: 0;
                            color: white;
                            background-color: #9d724b; 
                            font-size: 2rem; 
                            font-weight:bold;"
                    block 
                >
                    キャンセル
                </v-btn>
            </v-flex>   
        </v-layout>
    </v-card>    

    <!-- 메뉴 수정 영역 -->
    <v-layout>
        <v-flex xs8>
            <v-card class="pa-3">
                <!-- 이미지 영역 -->
                <v-layout class="pt-3 pb-3">
                    <v-flex xs6 class="edit_card_title"> 
                        メニューのイメージ
                    </v-flex>
                    <input 
                        type="file" 
                        id="img_upload_btn" 
                        class="ipt_btn"
                        accept=".png, .jpg, .jpeg" 
                        @change="menuImgEdit"
                    >  

                    <v-flex xs6>
                        <label for="img_upload_btn" class="label_style">
                            <b> イメージ登録 </b>
                        </label>     
                    </v-flex>
                </v-layout>                                      

                <v-layout>
                    <div xs12 class="img_div">                            
                        <!-- <img id="editImg" class="menuImg" src="./ex.jpg"> -->
                        <img id="editImg" class="menuImg" :src="editMenu.path + editMenu.filename">
                    </div>
                </v-layout>
                
                <!-- 정보 영역 -->
                <v-layout class="mt-3">
                    <v-flex xs6>
                        <v-flex xs12 mb-4>
                            <div class="editColumn"> メニューの名前 </div> 
                            <input type="text" id="ed_name" class="editInput" :value="editMenu.name">
                        </v-flex>

                        <v-flex xs12>                                
                            <div class="editColumn"> メニューの価格 </div> 
                            <input type="text" id="ed_price" class="editInput" :value="editMenu.price">
                        </v-flex>
                    </v-flex>

                    <v-flex xs6>                            
                        <v-flex xs12 mb-4> 
                            <div class="editColumn">
                                カテゴリー(選択)
                            </div> 
                            <select id="ed_category" class="editInput">
                                <option 
                                    v-for="j in range(0, states.length-1)" 
                                    :key="j"
                                >
                                    <h2 v-if="editMenu.category == states[j].name" selected>
                                        {{states[j].name}}
                                    </h2>

                                    <h2 v-else>
                                        {{states[j].name}}
                                    </h2>
                                </option>                                
                            </select>
                        </v-flex>
                                                
                        <v-flex xs12> 
                            <div class="editColumn">
                                ランチ / ディナ (選択)
                            </div> 

                            <select id="ed_remark" class="editInput">
                                <option 
                                    v-for="j in range(0, remarkList.length-1)" 
                                    :key="j"
                                >
                                    <h2 v-if="editMenu.remark == remarkList[j].text" selected>
                                        {{remarkList[j].text}}
                                    </h2>

                                    <h2 v-else>
                                        {{remarkList[j].text}}
                                    </h2>                                                            
                                </option>
                            </select>
                        </v-flex>                            
                    </v-flex>
                </v-layout>
                
                <v-layout class="mt-4">
                    <div class="editColumn" style="margin-bottom:0;">
                        メニューの説明
                    </div> 
                </v-layout>
                <v-layout> 
                    <textarea id="ed_expl" :value="editMenu.explanation">
                    </textarea> 
                </v-layout>
                
                <v-layout class="pa-3"> 
                </v-layout>
            </v-card>
        </v-flex>

        <!-- 옵션 영역 -->
        <v-flex xs4>
            <v-card style="height: 100%;" class="pa-3">
                <v-flex xs12 class="mt-3"> 
                    <b class="edit_card_title"> 
                        オプション登録 
                    </b> 
                    
                    <label 
                        for="create_option" 
                        id="create_option_label"
                        class="label_style"
                    > 
                        オプション追加
                    </label>
                    
                    <input 
                        type="button" 
                        id="create_option" 
                        @click="createOption" 
                        class="ipt_btn"
                    >                    
                </v-flex>                    
                <div id="editOptions"></div>
            </v-card>
        </v-flex>
    </v-layout>                           
</div>
</v-app>  
</div>
</template>
        
<script type="text/javascript"> 
import VueAxios from 'vue-axios';
import axios from 'axios'; 
import menuOperate from './OwnerMenuOperate.vue';
import { jSXOpeningElement, labeledStatement } from 'babel-types';
  
export default {
    created() {
        let url = '/menu/getCategory';
        let get_categorys = null;
        this.shop_id = this.$route.params.shop_id;      // 샵 아이디 
 
        // 카테고리 요청하기.
        axios.post(url, {
          'shop_id' : this.shop_id
        })
        .then( (response) => {
            get_categorys = response.data.category;
            this.categorys = this.unique(get_categorys); // 카테고리 중복 값 제거.         

            if(this.categorys.length % 6 !== 0){            
                this.categorysRow = Math.floor(this.categorys.length /6) +1;  
            }
            else {            
                this.categorysRow = Math.floor(this.categorys.length /6); 
            }

            this.click_category(this.categorys[0]);
        })
        .catch((ex)=>{ });            
        
    },
 
    data() {
        return {        
            // 스낵바 설정값
            timeout         : 2000,
            snackbar_text   : '',
            snackbar        : false,
            
            shop_id         : null,           // 가게 아이디
            e2              : null,           // 카테고리 클릭 값

            categorys       : null,           // 카테고리  
            categorysRow    : 0,              // 카테고리 줄 갯수
            clickedCategory : null,           // 클릭한 카테고리

            get_menus       : null,           // 서버에서 보내준 메뉴들 
            menu_num        : 0,              // 메뉴 갯수
            OptionDialog    : false,          // 옵션 보기 다이얼로그 
            DeleteDialog    : false,          // 삭제하기 다이얼로그
            clickedMenu     : null,           // 클릭한 메뉴
            editMenu        : null,           // 수정할 메뉴  

            // 카테고리
            states: [
                { name: '特選'}, { name: '推薦メニュー'}, { name: '丼'},
                { name: '御汁'}, { name: '蒸かし'}, { name: '肴'},
                { name: '酒'}, { name: '飲み物'}
            ],

            // 점심, 저녁 메뉴 구분
            remarkList : [{ text: '関係ない'},{ text: 'ランチ'},{ text: 'ディナー'}],
        }
    }, 

    // 메뉴 수정하기 클릭 시 메뉴 옵션 로드 메소드 실행함
    updated(){
        if(this.editMenu !== null && this.editMenu.opNum !== undefined){
            this.loadOptions(); 
        } 
    },

    methods : {        
        // 배열 중복 값 제거, 인자는 서버에 받은 카테고리 목록
        unique : function(argArr) {
            let array = [];
 
            for(let i=0; i < argArr.length; i++){
                // array에 없는 값이면 push
                if( !array.includes(argArr[i]['category']) ) { 
                    array.push(argArr[i]['category']);
                }
            }
            return array;
        },

        // v-for 용 함수, start에서 시작해서 end까지 1식 반환
        range : function (start, end) {
            return Array(end - start + 1).fill().map((_, idx) => start + idx)
        },

        // 메뉴 카테고리 클릭
        click_category : function(argMenu) { 
            let url = "";                           // 서버에 요청할 주소 
            
            // 클릭으로 호출
            if(argMenu === event){
                this.clickedCategory = event.target; 
                // 클릭한 값 검사
                if(this.clickedCategory.value === undefined)  
                    this.clickedCategory = this.clickedCategory.parentNode.value;
                else  
                    this.clickedCategory = this.clickedCategory.value;                        
            }
            // 최초 호출 
            else {
                this.clickedCategory = argMenu;
            }  

            url = '/menu/getMenu/' + this.shop_id + '/' + this.clickedCategory;
 
            // 클릭한 카테고리의 메뉴 호출
            axios.get(url)
            .then( (response) => {
                this.get_menus = response.data.menu;       
                    
                if(this.get_menus.length % 3 !== 0){            
                    this.menu_num = Math.floor(this.get_menus.length /3) +1;  
                }
                else {            
                    this.menu_num = Math.floor(this.get_menus.length /3); 
                }                    
            })
            .catch((ex)=>{
            });         
        },   
        
        // 수정하기
        modifyMenu : function(){ 
            let index = event.target.value;                   // 클릭한 button value
            let menus = document.getElementById('menus');       // 메뉴 출력 창
            
            this.editMenu = null;
            this.editMenu = this.get_menus[index -1];           // 클릭한 메뉴 
            menus.style.display = 'none';  
        },

        // 수정하기 - 수정 완료 
        modifySave : function(){ 
            let formData    = new FormData(); 
            let menu_img    = document.getElementById('img_upload_btn');       // 등록한 이미지 
            let ed_name     = document.getElementById('ed_name').value;              
            let ed_price    = document.getElementById('ed_price').value;
            let ed_category = document.getElementById('ed_category').value;
            let ed_remark   = document.getElementById('ed_remark').value;
            let ed_expl     = document.getElementById('ed_expl').value;
            let editOptions = document.getElementById('editOptions');          

            formData.append('shop_id', this.shop_id);  
             
            // 업로드한 이미지가 있는지 체크 후 진행함
            if(menu_img.files[0] !== undefined){   
                formData.append('menu_img',menu_img.files[0]);                              
            }
            // 등록된 이미지가 없는 경우 기존 이미지 유지
            else {
                formData.append('menu_img', 1);
            }    
            
            // 메뉴 정보 빈 값 없는지 체크
            if(ed_name!=='' && ed_price!=='' && ed_category!=='' && ed_remark!=='' && ed_expl!=='') {                    
                formData.append('name', ed_name);
                formData.append('price', ed_price);
                formData.append('category', ed_category);
                formData.append('remark', ed_remark);
                formData.append('explanation', ed_expl);  
                formData.append('menu_id', this.editMenu.id);

                // 옵션 담기. 없으면 실행 안함.
                if(editOptions.children.length !== 0){
                    for(let i=0; i < editOptions.children.length; i++){
                        let optionBox = editOptions.children[i];        
                        let optionName = optionBox.children[0].children[0];
                        
                        if(optionName.tagName !== 'B' || optionName.innerText === ''){
                            this.snackbar_text = 'まだ入力が完了していないオプションがあります。';
                            this.snackbar      = true;
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
                                    this.snackbar_text = 'まだ入力が完了していないオプションがあります。';
                                    this.snackbar      = true; 
                                    return null; 
                                }
                                formData.append('option['+ i +'][value' + j+']',optionValue.innerText);
                            }
                        } else {
                            this.snackbar_text = 'オプションの選択肢の値を2つ以上生成してください!';
                            this.snackbar      = true;
                            return null; 
                        }
                        
                    }
                }                                 
                // 콘솔창에 띄우기
                // for(var pair of formData.entries()) {
                //     console.log(pair[0]+ ', '+ pair[1]); 
                // }

                /* 선주야 이거 해줘 */   
                axios.post('/updateMenu',formData)
                .then( (response) => { 
                    this.snackbar_text = '修正完了';
                    this.snackbar      = true;                
                    this.modifyCancel();
                    return null;
                })
                .catch((ex)=>{});                
            }
            // 메뉴 정보가 모두 입력되지 않은 경우 경고 메세지 출력
            else { 
                this.snackbar_text = '情報を全部書いてください。';
                this.snackbar      = true;                 
                return null;  
            } 
        },

        // 수정하기 - 수정 취소 
        modifyCancel : function(){
            let menus = document.getElementById('menus');       // 메뉴 출력 창            
            menus.style.display = 'block';
            this.editMenu = null;
            this.click_category(this.clickedCategory);                              // 리스트 업
        },

        // 수정하기 - 옵션 불러오기
        loadOptions : function() { 
            let editOptions = document.getElementById('editOptions');         // 옵션들이 들어갈 div
           
            for(let i=1; i <= this.editMenu.opNum; i++){
                let options_box     = document.createElement('div');            // 옵션 명, 옵션 값을 담을 div
                let created_option  = document.createElement('div');            // 옵션 명이 될 div  
                let created_b       = document.createElement('b');              // 옵션 명
                let created_btns    = [                                         // 옵션 명 div에 들어갈 버튼들.
                                        document.createElement('input'),       
                                        document.createElement('input'),        
                                        document.createElement('input')       
                                    ];
                created_b.innerText = this.editMenu['optionName' +i];           // 옵션 초기 이름 값 설정.
                created_b.classList.add('ed_op_b');  

                created_option.classList.add('ed_op_div');                      // 옵션 명에 css 추가
                created_option.prepend(created_b);                               

                // 들어갈 버튼들 값 설정, css 추가, 메소드 설정
                for (let a=0; a < created_btns.length; a++){                   
                    created_btns[a].setAttribute('type','button');
                    created_btns[a].classList.add('ed_op_btn');

                    switch(a){
                        case 0:
                            created_btns[a].value   = '削除';             
                            created_btns[a].onclick = this.delete_option; break;
                        case 1:
                            created_btns[a].value   = '内容追加';
                            created_btns[a].onclick = this.add_opionValue; break;
                        case 2: 
                            created_btns[a].value   = '修正';                
                            created_btns[a].onclick = this.rename_option; break;
                    }                    
                    created_option.appendChild(created_btns[a]);
                }                              

                // 옵션 목록 div에 옵션 삽입   
                options_box.classList.add('ed_op_box');
                options_box.appendChild(created_option);
                editOptions.appendChild(options_box);                

                for(let j=1; j <= this.editMenu['subOpNum' +i]; j++){ 
                    let optionValue   = document.createElement('div');           // 하위 카테고리 div 생성
                    let Value_b       = document.createElement('b');             // 카테고리 명 입력할 b 태그
                    let Value_btns    = [
                                            document.createElement('input'), 
                                            document.createElement('input')
                                        ];  

                    // 들어갈 버튼들 값 설정, css 추가.
                    for (let b=0; b < Value_btns.length; b++){                   
                        Value_btns[b].setAttribute('type','button');
                        Value_btns[b].classList.add('ed_op_btn');

                        switch(b){
                            case 0:
                                Value_btns[b].value   = '削除';
                                Value_btns[b].onclick = this.delete_optionValue; break;
                            case 1:
                                Value_btns[b].value   = '修正';          
                                Value_btns[b].onclick = this.rename_option; break; 
                        }                        
                        optionValue.appendChild(Value_btns[b]);
                    }      

                    Value_b.innerText = this.editMenu[i+ 'optionValue' +j];  
                    Value_b.classList.add('ed_op_b');
 
                    optionValue.prepend(Value_b);
                    optionValue.classList.add('ed_op_value');

                    options_box.appendChild(optionValue);
                }
            }           
           
        }, // end of loadOptions
        
        // 수정하기 - 이미지 업로드하기.
        menuImgEdit: function(){ 
            let editImg = document.getElementById("editImg");  // 메뉴 이미지
            let reader  = new FileReader();

            // 이미지 미리보기 띄우기
            reader.onload = function() {
                editImg.src = reader.result;  
            }; 
            reader.readAsDataURL(event.target.files[0]); 
        },

        // 수정하기 - 옵션 만들기.
        createOption : function() { 
            let editOptions     = document.getElementById('editOptions');   // 옵션들이 들어갈 div
            let options_box     = document.createElement('div');            // 옵션 명, 옵션 값을 담을 div
            let created_option  = document.createElement('div');            // 옵션 명이 될 div  
            let created_input   = document.createElement('input');          // 카테고리 명 입력 창 만들기.
            let created_btns    = [                                         // 옵션 명 div에 들어갈 버튼들.
                                    document.createElement('input'),       
                                    document.createElement('input'),        
                                    document.createElement('input')       
                                ]; 
            created_input.classList.add('ed_op_ipt');
            created_option.classList.add('ed_op_div');                     // 옵션 명에 css 추가
            created_option.prepend(created_input);                               

            // 들어갈 버튼들 값 설정, css 추가, 메소드 설정
            for (let i=0; i < created_btns.length; i++){                   
                created_btns[i].setAttribute('type','button');
                created_btns[i].classList.add('ed_op_btn');

                switch(i){
                    case 0:
                        created_btns[i].value   = '削除'; 
                        created_btns[i].onclick = this.delete_option; break;
                    case 1:
                        created_btns[i].value   = '内容追加';
                        created_btns[i].onclick = this.add_opionValue; break;
                    case 2: 
                        created_btns[i].value   = '完了'; ;
                        created_btns[i].onclick = this.rename_complete; break;
                }
                created_option.appendChild(created_btns[i]);
            }
            // 옵션 목록 div에 옵션 삽입 
            options_box.classList.add('ed_op_box');
            options_box.appendChild(created_option);
            editOptions.appendChild(options_box);  
        }, // end of create_option

        // 수정하기 - 옵션 명 수정하기
        rename_option : function(event){
            let get_option      = event.target.parentNode;             // 해당 옵션 가져오기
            let get_rename_btn  = event.target                         // 수정 버튼 가져오기 
            let created_input   = document.createElement('input');     // 카테고리 명 입력 창 만들기.

            get_rename_btn.value   = "完了";                           // 수정 버튼을 완료 버튼으로 변경
            get_rename_btn.onclick = this.rename_complete;
 
            created_input.classList.add('ed_op_ipt'); 

            get_option.removeChild(get_option.children[0]);             // 기본 카테고리명 지우기.
            get_option.prepend(created_input);                          // 첫번째 자식으로 넣음
        }, 

        // 수정하기 - 이름 수정 완료
        rename_complete : function(){
            let get_option  = event.target.parentNode;          // 해당 카테고리 가져오기
            let get_cpt_btn = event.target;                     // 완료 버튼 가져오기.
            let get_iptText = get_option.children[0];           // input 태그 가져오기
            let created_b   = document.createElement('b');      // 카테고리 명 생성.
 
            if(get_iptText.value.replace(/ /gi, "") === ''){
                this.snackbar_text = '内容を書いてください。';
                this.snackbar      = true;                 
                return null; 
            }
            created_b.innerText = get_iptText.value;                                                   
            created_b.classList.add('ed_op_b');
            
            get_cpt_btn.value   = "修正";                       // 완료 버튼을 수정 버튼으로 변경시킴. 
            get_cpt_btn.onclick = this.rename_option;   
 
            get_option.removeChild(get_iptText);              // input 태그 삭제.
            get_option.prepend(created_b);                    // 첫번째 자식으로 넣음
        },

        // 수정하기 - 옵션 값 생성
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
                created_btns[i].classList.add('ed_op_btn');

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
            created_input.classList.add('ed_op_ipt');

            optionValue.classList.add('ed_op_value');
            optionValue.prepend(created_input);

            get_box.appendChild(optionValue);
        }, // end of add_opionValue

        // 수정하기 - 클릭한 옵션 삭제 
        delete_option : function(){
            let option = event.target.parentNode;
            option.parentNode.parentNode.removeChild(option.parentNode);   
        },

        // 수정하기 - 클릭한 옵션 값 삭제 
        delete_optionValue : function(){
            let op_value = event.target.parentNode;
            op_value.parentNode.removeChild(op_value);          
        },
        
        // 삭제하기
        deleteMenu : function(){ 
            let get_menu = document.getElementById('menu' + this.clickedMenu);
            let url      = '/deleteMenu';

            // 데이터베이스에서 삭제
            axios.post(url,{
                'shop_id' : this.shop_id,
                'menu_id' : this.clickedMenu
            })
            .then( (response) => {
                let result = response.data.result;      // 삭제 성공 / 실패 여부
                if(true){                    
                    get_menu.parentNode.removeChild(get_menu);      
                    this.snackbar_text = '削除しました。';
                    this.snackbar      = true;             
                    this.click_category(this.clickedCategory);     
                    return null; 
                }             
            })
            .catch((ex)=>{ });       
        } 
    }
}

</script>

<style>
.h3_style { margin-top: 1%; font-size: 1.2rem; padding: 1%;}  

#ed_expl {
    width: 95%; 
    height: 150px; 
    padding: 2%;      
    font-size: 1.4rem;                              
    border-bottom: 3px solid #6d4d35;
    margin: auto;
}

#ed_expl::-webkit-scrollbar {
    width: 0px;                     /* remove scrollbar space */
    background: transparent;        /* optional: just make scrollbar invisible */
} 
 
/* 메뉴 div 비율용 */
.menuOuter {    
    width: 100%;
    height: 0;
    padding-bottom: 23%;
    border: 1px solid;
    position: relative;
    overflow: hidden; 
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
/* 메뉴 이미지 */
.menuImg{
    top: 0;
    left: 0;
    width: 105%;
    height: 105%;    
    object-fit: cover;
    position: absolute;
}  
  
.edit_card_title{
    padding-left: 2%; 
    font-size: 2rem;
    font-weight: bold; 
    color: #46362c; 
    position: relative; 
}
/* input file 안보이게 하기 */
.ipt_btn{
    width: 0%;
    font-size: 0px;
    opacity: 0;
    position: relative; 
}
/* 메뉴 이미지 업로드 라벨 */
.label_style{
    font-size: 1.5rem;
    margin-right: 3%; 
    color: #6d4d35; 
    font-weight: bold;
    float:right;
    user-select:none;               /* 드래그 방지 */
    -ms-user-select: none; 
    -moz-user-select: -moz-none; 
    -webkit-user-select: none; 
    -khtml-user-select: none; 
} 
/* 마우스 클릭하고있을때 */
.label_style:active{
    color:  #9d724b;
}
/* 마우스 한번클릭후 */
.label_style:visited{
    color:  #6d4d35;
} 

.editColumn {
    font-size: 1.4rem;
    margin-left: 3%;
    margin-bottom: 3%;
    color:#6d4d35;  
    font-weight: bold;
}
.editInput {
    width: 90%;   
    font-size: 1.4rem;
    margin-left: 5%;
    padding-left: 1%; 
    border-bottom: 3px solid #6d4d35;
}

/* 옵션들이 출력될 div 스타일 */
#editOptions{
    width:100%;
    height: auto;
    overflow: hidden; 
    margin-bottom: 5%;   
}
/* 하나의 옵션명과 그 옵션 값들을 감싸는 div */
.ed_op_box {
    width: 100%;
    height: auto; 
    overflow:hidden; 
    margin-top: 5%;
    margin-bottom: 3%;
}
/* 옵션 */
.ed_op_div {
    width: 100%; 
    margin-top: 1%;
    border-bottom: 1px solid;
    position: relative; 
    overflow:hidden; 
}
/* 옵션명의 버튼들 */
.ed_op_btn {
    min-width: 10%;
    margin: 1%;
    float: right;
    position: relative;
    color: #6d4d35;  
    font-weight: bold;
}
/* 옵션의 값 */
.ed_op_value {
    width: 95%; 
    margin-top: 1%;
    border-bottom: 1px solid;
    position: relative;
    overflow:hidden; 
    float: right; 
}
/* 옵션 명을 출력할 b태그 */
.ed_op_b {
    font-size: 1.3rem;
    margin-left: 1%;
    float: left; 
}
/* 옵션 수정에 사용할 input text 태그 */
.ed_op_ipt {
    width: 40%; 
    margin-left: 1%; 
    font-size: 1.2rem;
    background-color: #9d724b;  
    float: left; 
    font-weight: bold;
}
</style>