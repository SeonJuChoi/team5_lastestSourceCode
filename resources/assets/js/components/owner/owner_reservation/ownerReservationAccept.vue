<template>
    <v-app>
        <div class="reservation_Accept">
            <br>
            <h2 class="Main_title"><B>予約受諾</B></h2>
            <span class="sub_title">お客様の予約を受諾できます。</span>
            <hr><br>
            <div class="coupon_table">
                <v-data-table
                        :headers="headers"
                        :items="items"
                        hide-actions
                        class="elevation-1"
                >
                    <template slot="items" slot-scope="props">
                        <td>{{ props.item.user_name }}</td>
                        <td class="text-xs-left">{{ props.item.reservation_date }}</td>
                        <td class="text-xs-left">{{ props.item.person }}</td>
                        <td class="text-xs-left">{{ props.item.child }}</td>
                        <td class="text-xs-left">{{ props.item.refused_message }}</td>
                        <td class="justify-left"  v-if="props.item.order_num != null">
                            <!-- 메뉴 보기 버튼 -->
                            <v-btn flat small color="teal lighten-1" @click="menu(props.item), menuLoad = true">メーニュー</v-btn>
                            <v-dialog v-model="menuLoad" max-width="500px">
                                <v-toolbar color="teal lighten-1">
                                    <h3 style="color:white; margin:auto" > メーニュー </h3>
                                </v-toolbar>
                                <v-card>
                                    <v-card-text style="color : black">
                                        <div
                                                v-for="(card, index) in orderMenu"
                                                :key="index"
                                        >
                                            <B> {{orderMenu[index]}} </B>
                                        </div>
                                    </v-card-text>
                                    <v-card-actions>
                                        <v-btn color="teal lighten-1" style="color:white" @click.stop="menuLoad = false">
                                            確認
                                        </v-btn>
                                    </v-card-actions>
                                </v-card>
                            </v-dialog>
                        </td>
                        <td v-else>
                            無し
                        </td>
                        <td class="justify-left layout px-0">

                            <!-- 수락 버튼 -->
                            <v-btn  small 
                                    style="color:white; background-color:#ff9a55" 
                                    dark 
                                    @click.stop="Acceptdialog = true"
                                    @click="AcceptCustomer = props.item.user_name, AcceptItem(props.item)"
                            >
                               <v-icon small dark left>check_circle</v-icon> 受諾
                            </v-btn>
                            <!-- 수락 Dislog -->
                            <v-dialog v-model="Acceptdialog" max-width="400px">
                                <v-card>
                                    <v-card-title>
                                        <B> 予約受諾 </B>
                                    </v-card-title>
                                    <v-card-text>
                                        {{AcceptCustomer}}様の予約が受諾されました。
                                    </v-card-text>
                                    <v-btn color="primary" @click.stop="Acceptdialog = false">
                                        確認
                                    </v-btn>
                                </v-card>
                            </v-dialog>
                            <!-- 거절 버튼 -->
                            <v-btn small color="error" @click.stop="Canceldialog = true">
                                <v-icon small dark left>remove_circle</v-icon>拒絶
                            </v-btn>
                            <v-dialog v-model="Canceldialog" max-width="400px">
                                <v-card>
                                    <v-card-text>
                                        <v-card-title>
                                            <B> 予約拒絶 </B>
                                        </v-card-title>
                                        <v-card-text>
                                           　予約を拒絶する理由を記入してください。 <br>
                                            <v-text-field
                                                    v-model="WhyCancel"
                                                    label="予約拒絶理由"
                                                    class="input-group--focused"
                                                    color="error"
                                            ></v-text-field>
                                        </v-card-text>
                                    </v-card-text>
                                    <v-btn color="error" @click.stop="CancelItem(props.item), Canceldialog = false">
                                        確認
                                    </v-btn>
                                </v-card>
                            </v-dialog>
                        </td>
                    </template>
                </v-data-table>
            </div>
        </div>
    </v-app>
</template>

<script>
    import VueAxios         from 'vue-axios';
    import axios            from 'axios';
    export default {
        data() {
            return {
                dialog       : false,
                Acceptdialog : false,
                Canceldialog : false,
                menuLoad     : false,
                /* 수락한손님 */
                AcceptCustomer : null,
                /* 취소한 손님 */
                CancelCustomer  : null,
                WhyCancel       : '',
                headers: [
                    { text: '予約者名',    value: 'user_name' },
                    { text: '日付',    value: 'reservation_date' },
                    { text: '大人数',    value: 'person' },
                    { text: '子供数',    value: 'child' },
                    { text: '要求事項',    value: 'refused_message'},
                    { text: 'メーニュー',         value: 'menu_select' },
                    { text: '受諾・拒絶',  value: 'Accept', sortable: false },
                ],
                /* 저장 & 편집 & 삭제 */
                items: [ ],
                orderMenu : [],
            }
        },
        /* DB에서 데이터 값 받기 */
        created: function () {
            axios.post('/getReservationRequestList', {
                'shop_id' : this.$route.params.shop_id
            }).then((response) => {
                var reservationAccept = response.data.resData;
                this.items = reservationAccept;
            })
        },
        // 예약 수락, 거절 할 시 둘다 table delete!!!!!!!!!! 시켜주기
        methods: {
            menu(item) {
                const index         = this.items.indexOf(item)
                var reservation_id  = this.items[index].id;
                axios.post('/getReservationMenuList', {
                    id : reservation_id
                }).then((response) => {
                    var MenuorderData = response.data.currentOrder;
                    // 임시
                    var MenuArray = '';
                    // 1. 주문 메뉴 옵션 합치기s
                    for(var i = 0 ; i < MenuorderData[0].menuNum; i++)
                    {
                        MenuArray = (i+1)+'番 :' + MenuorderData[0]['menu_name' + (i+1)]
                            + ' 値段:' + MenuorderData[0]['menu_price' + (i+1)];
                        // 옵션 개수...도..
                        var OptionCount = MenuorderData[0]['optionNum'+(i+1)];
                        // 메뉴
                        for(var j = 0; j < OptionCount; j++)
                        {
                            MenuArray += ' ' + MenuorderData[0]['menu'+(i+1)+'-'+'option'+(j+1)]
                                + ':' + MenuorderData[0]['menu'+(i+1)+'-'+'subOption'+(j+1)];
                        }
                        this.orderMenu[i] = MenuArray;
                    }
                });
            },
            AcceptItem (item) {
                const index = this.items.indexOf(item);
                var id      = this.items[index].id;
                axios.post('/acceptReservation', {
                    // 수락한 유저 이름
                    id : id,
                    accept   : true,
                }).then((response) => {
                   location.reload();
                }).catch(console.log('test '));
            },
            CancelItem (item) {
                const index = this.items.indexOf(item);
                var id      = this.items[index].id;
                axios.post('/acceptReservation', {
                    // 거절한 유저 이름
                    id : id,
                    WhyCancel : this.WhyCancel,
                    accept    : false,
                }).then((response) => {
                    location.reload();
                }).catch(console.log('test '));
            }
        }
    }
</script>
<style>
    .Main_title {
        color: #6d4d35;
    }

    .sub_title {
        color : #9d724b;
    }
    .reservation_Accept {
         padding-left: 5%;
        padding-right: 5%;
    }
</style>