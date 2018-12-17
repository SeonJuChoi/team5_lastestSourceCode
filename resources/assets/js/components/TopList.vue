<template>
    <div>
        <div style="background-color: #ff9a55; height: 10px"></div>
            <v-layout row>
                <v-flex xs12 sm10 offset-sm1>
                    <v-card>
                        <v-alert v-if="$session.get('top_mode') == 'region'" @click="open_gps_dialog()" :value="true" outline color="success" icon="gps_fixed" style="cursor:pointer">
                            {{ $session.get('top_region') }}
                        </v-alert>
                        <GoogleMap v-if="$session.get('top_mode') == 'region'" googleMapMode="top"></GoogleMap>
                    </v-card>
                </v-flex>
            </v-layout>
        <br>
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
                        <strong v-if="$session.get('top_mode') == 'region'" class="bestBanner">{{ $session.get('top_show_region') }} Best {{ $session.get('top_listLimit') }}</strong>
                        <strong v-else class="bestBanner">{{ $session.get('top_show_type') }} Best {{ $session.get('top_listLimit') }}</strong>
                    </v-alert>
                    <v-container fluid grid-list-sm>
                        <v-layout row wrap>
                            <v-flex md4 xs12 v-for="(item, i) in $session.get('top_restaurantList')" :key="i">
                                <v-card>
                                    <v-card-media
                                            :src="`/images/${item.shop_id}/${item.shop_id}_titleImg.jpg`"
                                            height="250px"
                                            @click="moveRestaurant(item.shop_id)"
                                    ></v-card-media>
                                    <v-card-title class="headline">{{ item.shop_name }}<v-spacer></v-spacer><span class="orange--text">{{ item.totalRating }}</span></v-card-title>
                                    <v-card-actions>{{ item.shop_ddobuken }} - {{ item.shop_type }}</v-card-actions>
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
    import GoogleMap from "./GoogleMap";
    import axios from 'axios';

    export default {
        components: {
            GoogleMap
        },

        data(){
            return {

            }
        },

        created() {
            
        },

        methods: {
            open_gps_dialog() {
                this.$parent.$parent.$parent.gps_search = true;
            },

            moveRestaurant(shop_id) {
                this.$router.push('/restaurant/' + shop_id + '/info');
            },
        }
    }
</script>

<style>
    #google_map {
        height: 200pt;
    }
</style>