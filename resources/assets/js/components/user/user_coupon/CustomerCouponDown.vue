<template>
    <v-app>
        <div>
        <v-btn color="error" dark @click.stop="dialog2 = true">쿠폰 다운 받기</v-btn>
        <v-dialog v-model="dialog2" max-width="700px">
            <v-card>
                <v-card-title>
                    <h3><B>쿠폰 다운 받기</B></h3>
                    <br>
                </v-card-title>
                <v-card-text>
                    <v-data-table
                        :headers="headers"
                        :items="items"
                        hide-actions
                        class="elevation-1"
                    >
                        <template slot="items" slot-scope="props">
                            <td>{{ props.item.CouponName }}</td>
                            <td class="text-xs-left">{{ props.item.CouponType }}</td>
                            <td class="text-xs-left">{{ props.item.Condition }}</td>
                            <td class="text-xs-left">{{ props.item.Discount }}</td>
                            <td class="text-xs-left">{{ props.item.addproduct }}</td>
                            <td class="justify-center layout px-0">
                            <v-btn flat icon color="red lighten-2"   @click.stop="DownDialog = true">
                                <v-icon dark right>file_download</v-icon>
                            </v-btn>
                            </td>
                            <!-- Download 아이콘을 눌렀으때 -->
                            <v-dialog v-model="DownDialog" max-width="500px">
                                <v-card>
                                <v-card-title>
                                    {{ props.item.CouponName}} 을 다운 받으시겠습니까?
                                </v-card-title>
                                <v-card-actions>
                                    <v-btn color="primary" flat @click.stop="DownDialog=false, Download(props),Okey()">확인</v-btn>
                                    <v-btn color="primary" flat @click.stop="DownDialog=false">취소</v-btn>
                                </v-card-actions>
                                </v-card>
                            </v-dialog>
                        </template>
                    </v-data-table>
                </v-card-text>
            </v-card>
        </v-dialog>
        </div>
    </v-app>
</template>
<script>
import VueAxios         from 'vue-axios';
import axios            from 'axios';

export default {
    data() {
        return {
            DownDialog : false,
            dialog2: false,

            /* table */
            headers: [
                { text: '쿠폰 이름', value: 'CouponName' },
                { text: '쿠폰 종류', value: 'CouponType' },
                { text: '할인 정보', value: 'Discount' },
                { text: '제공 정보', value: 'addproduct' },
                { text: 'Actions', value: 'name', sortable: false }
            ],

            items: [],
            CouponItem: {
                CouponName: '',
                CouponType: '',
                Discount: null,
                addproduct: null
            }
        }
    },
    mounted : {
        /* CounponData 받기 */
        userCouponList() {
            axios.post('/userCoupon');
            this.axios.then((response) => {
                /* DB Coupon Data */
                var Index = 0;
                var Coupondata = Object.values(response.data.Coupon);
              
                console.log(response.Coupondata);

                while (Coupondata[Index] != null) {
                    this.CouponItem.CouponName   = Coupondata[Index].name;
                    this.CouponItem.CouponType   = Coupondata[Index].category;
                    this.CouponItem.Discount     = Coupondata[Index].discount;
                    this.CouponItem.addproduct   = Coupondata[Index].add_product;
                    this.CouponItem.Condition    = Coupondata[Index].price_condition;

                    this.items[Index].push(this.CouponItem)
                    Object.assign(this.items[this.Index], this.CouponItem)
                }   
            })
        },
    },
    methods : {
        Download(props) {
            /* Data 송신 -> 자신의 쿠폰함으로 담긴다*/
            axios.post('/CouponDown', {
                CouponName    : props.item.CouponName,
                CouponType    : props.item.CouponType,
                Discount      : props.item.Discount,
                addproduct    : props.item.addproduct, 
                Condition     : props.item.Condition,
            }).then(console.log('success')).catch(console.log('test '));     
        },
        Okey() {
          confirm('쿠폰함에 쿠폰이 다운되었습니다.')
        },
    }
}
</script>
<style>

</style>
