<template>
    <div class="bgRain h-[100vh] w-full flex justify-center ">
        <div class="container flex justify-between">
            <div class="hidden w-[70%] p-5 md:flex flex-col justify-between">
                <div class="w-full flex justify-end items-center">
                    <p class="text-white border-r-2 pr-4">
                        {{ formattedDate(weatherInpo[0]?.dt_txt,"DD MMMM YY") }}
                    </p>
                    <p class="text-white ml-4">
                        {{ minuteUpdate }}
                    </p>
                </div>
                <div class="flex w-full flex-col">
                    <div class="w-full border-b-[3px] py-9 mb-4 border-white/75">
                        <p class="text-8xl font-normal text-white/75 text-right w-full ">
                            {{ weatherInpo[0]?.weather[0]?.description}}
                        </p>
                    </div>
                    <div class="flex w w-full justify-between">
                        <div
                            v-for="(item, index) in weatherMany[0]"
                            :key="index"
                            class="w-[70px] backdrop-blur-xs bg-white/10  rounded-md p-3 flex flex-col items-center">
                            <p class="text-white/75 underline underline-offset-2 mb-2">
                                {{ formattedDate(item?.dt_txt,"HH:mm" ) }}
                            </p>
                            <div class="backdrop-blur-md bg-white/10 rounded-sm">
                                <img
                                    alt="icon"
                                    :src="'https://openweathermap.org/img/wn/'+item?.weather[0]?.icon+ '@2x.png'"/>
                            </div>
                            <p class="text-white/75 mt-2">
                                {{Math.ceil(item?.main?.temp - 273.15)}}&deg;C
                            </p>
                        </div>
                    </div>
                </div>
            </div>
            <div
                class="overflow-hidden w-[100vw] h-[100vh] md:w-[30%] backdrop-blur-md bg-white/10 flex flex-col items-center md:p-8 ">
                <div class="w-full flex justify-center mt-10 md:m-0">
                    <select
                        @change="handleOnSelect($event.target.value)"
                        id=""
                        class="bg-transparent border-[1px] rounded-md border-white/75 p-2 text-white/75 w-2/3 focus:border-none">
                        <option
                            v-for="(item, index) in cities"
                            :key="index"
                            class="text-black bg-gray-400 backdrop-blur-sm max-w-[80%]"
                            :value="item">
                            {{ item }}
                        </option>
                    </select>
                </div>
                <div class="p-10 border-b-[1px] border-white/60 flex flex-col items-center">
                    <p class="md:hidden text-lg font-semibold text-white text-center mb-2">
                        {{ weatherInpo[0]?.weather[0]?.description }}
                    </p>
                    <p class="text-7xl text-white">
                        {{ Math.ceil(weatherInpo[0]?.main?.temp - 273.15) }}
                        &deg;C
                    </p>
                    <p class="text-white/60 mt-5">
                        wind speed
                        {{ weatherInpo[0]?.wind?.speed }}
                    </p>
                </div>
                <div class="flex flex-col items-center w-full">
                    <p class="text-lg font-semibold text-white mt-5">
                        The Next Day Forest
                    </p>
                    <div class="flex w-full justify-center my-5">
                        <button
                            v-for="(item, index) in btnNumber"
                            :key="index"
                            class="text-white mx-5 backdrop-blur-sm bg-white/5 px-2 py-1 rounded-lg w-20">{{ item }}</button>
                    </div>
                    <div v-for="(item, index) in nextWeather" :key="index" class="flex items-center w-[80%] justify-between mt-5">
                        <div class="flex">
                            <div class="w-12 h-12 backdrop-blur-md bg-white/10 rounded-md mr-2">
                                <img alt="icon" :src="`https://openweathermap.org/img/wn/`+item?.weather[0]?.icon+`@2x.png`"/>
                            </div>
                            <div>
                                <p class="text-white">
                                    {{ formattedDate(item?.dt_txt,'dddd') }}
                                    {{ formattedDate(item?.dt_txt,'MMMM DD') }}
                                </p>
                                <p class="text-white/60 text-sm">
                                    {{item?.weather[0]?.description}}
                                </p>
                            </div>
                        </div>
                        <div class=" flex flex-col justify-center h-full  p-0">
                            <p class="text-white border-l-[1px] pl-4">
                                {{Math.ceil(item?.main?.temp - 273.15)}}&deg;
                            </p>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script >
    import {defineComponent} from 'vue';
    import axios from "axios"
    import moment from "moment"

    export default defineComponent({
        data() {
            return {
                cities: [
                    "Jakarta",
                    "Denpasar",
                    "Bandung kota",
                    "Surabaya",
                    "Yogyakarta",
                    "Padang",
                    "Makassar",
                    "Sumedang"
                ],
                weatherInpo: [],
                weatherMany: [],
                btnNumber: ["4", "14", "30"],
                weatherNexTDay:[]
            }
        },
        computed : {
            nextWeather() {
                let data = []
                data.push(this.weatherInpo[0])
                for (let i = 0; i < this.weatherInpo.length; i++) {
                    if (i % 8 == 0) {
                        data.push(this.weatherInpo[i])
                    }
                }
                return data
            },
            minuteUpdate(){
                return moment(new Date()).format("LT");
            }
        },


        
        methods: {
            handleOnSelect(e) {
                axios
                    .get(
                    `https://api.openweathermap.org/geo/1.0/direct?q=${e ? e : "jakarta"}&limit=5&appid=7e0e1623e448b646e2dd26321c4fd1c1`
                    )
                    .then((res) => {
                        // setCityCordinate();
                        this.getWeather(
                            res
                                ?.data[0]
                        );
                        // console.log(res);
                    })
                    .catch((err) => {
                        console.log(err);
                    });
            },
            getWeather(param) {
                axios
                        .get(
                        `https://api.openweathermap.org/data/2.5/forecast?lat=${param.lat}&lon=${param
                            ?.lon
                                ? param
                                    ?.lon
                                    : "106.827183"}&appid=7e0e1623e448b646e2dd26321c4fd1c1`
                    )
                        .then((res) => {
                            this.weatherInpo = res.data.list
                            this.weatherMany = [
                                ...this.weatherMany,
                                res
                                    .data
                                    .list
                                    .splice(0, 10)
                            ]
                            console.log(this.weatherNexTDay);
                        })
                        .catch((err) => {
                            console.log(err);
                        });
            },
            formattedDate(val, format) {
                return moment(val).format(format);
            }
        },
        mounted(){
            this.handleOnSelect()
        }
    })
</script>

<style scoped="scoped">
    .bgRain {
        background-image: url("./assets/backgound/bg-rain.jpg");
        background-size: cover;
    }
    .container {
        width: 100%;
        max-width: 1540px;
        /* padding: 0 32px; */
    }
</style>