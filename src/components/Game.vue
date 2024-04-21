<template>
    <img class="win" v-if="isWin" src="../assets/images/win.gif"  alt="">
    <div class="head">
        <h3 class="chance">تعداد حرکت : {{ chance }}</h3>
        <h3 class="time">زمان : <span id="sec"></span> : <span id="min"></span></h3>
    </div>
    <div class="wrapper">
        <template v-for="(card, index) in gameCards" :key="index">
            <card @check="check($event)" :refresh="refresh" :allowClick="allowClick" :index="index + 1" :card="card"
                :width="width"></card>
        </template>
        <button v-if="!time || !chance || isWin" @click="reset()">شروع دوباره</button>
    </div>
</template>

<script>
import Card from './Card.vue';
import {
    toast
} from 'vue3-toastify';
export default {
    components: {
        "card": Card
    },
    mounted() {
        this.init();
    },
    data: () => {
        return {
            successMove: 0,
            refresh: false,
            card1: null,
            card2: null,
            firstClick: true,
            chance: 0,
            time: 0,
            runTimer: false,
            timer: null,
            width: 100,
            cards: [{
                    id: "1",
                    src: "./src/assets/images/product-1.jpg"
                },
                {
                    id: "2",
                    src: "./src/assets/images/product-2.jpg"
                },
                {
                    id: "3",
                    src: "./src/assets/images/product-3.jpg"
                },
                {
                    id: "4",
                    src: "./src/assets/images/product-4.jpg"
                },
                {
                    id: "5",
                    src: "./src/assets/images/product-5.jpg"
                },
                {
                    id: "6",
                    src: "./src/assets/images/product-6.jpg"
                },
                {
                    id: "7",
                    src: "./src/assets/images/product-7.jpg"
                },
                {
                    id: "8",
                    src: "./src/assets/images/product-8.jpg"
                },
            ],
            successMessages: [
                "بابا ایول",
                "دمت گرم!",
                "تو نابغه ای",
                "دسخوش نابغه",
                "بنازم به این هوش",
            ],
            errorMessages: [
                "دوباره تلاش کن",
                "ناامید نشو",
                "بعدی رو درست میزنی",
                "تو میتونی",
            ]
        }
    },
    methods: {
        init() {
            this.successMove = 0,
            this.card1 = null;
            this.card2 = null;
            this.firstClick = true;
            this.chance = 40;
            this.time = 90;
            this.runTimer = false;
            clearInterval(this.timer)
        },
        randomizeArray(array) {
            return array[Math.floor(Math.random() * array.length)]
        },
        duplicateArray(array) {
            return [...array, ...array];
        },
        shuffleArray(array) {
            let currentIndex = array.length;
            while (currentIndex != 0) {
                let randomIndex = Math.floor(Math.random() * currentIndex);
                currentIndex--;
                [array[currentIndex], array[randomIndex]] = [
                    array[randomIndex], array[currentIndex]
                ];
            }
            return array;
        },
        check(cardComponent) {
            if (this.allowClick) {
                this.chance += -1;
                if (!this.runTimer) this.runTimer = true;
                if (this.firstClick) {
                    this.card1 = null;
                    this.card2 = null;
                    this.firstClick = false;
                    this.card1 = cardComponent;
                } else {
                    this.card2 = cardComponent;
                    if (this.card1.card.id === this.card2.card.id) {
                        this.successMove++;
                        toast.success(this.randomizeArray(this.successMessages));
                        this.firstClick = true;
                    } else {
                        toast.error(this.randomizeArray(this.errorMessages));
                        setTimeout(() => {
                            this.card1.showCard = false;
                            this.card2.showCard = false;
                            this.firstClick = true;
                        }, 1000);

                    }
                }
            }
        },
        reset() {
            this.refresh = true;
            this.gameCards = this.cards;
            this.init();
            setTimeout(() => {
                this.refresh = false
            }, 0);
        }

    },
    computed: {
        gameCards: {
            get() {
                return this.shuffleArray(this.duplicateArray(this.cards));
            },
            set(array) {
                this.cards = this.shuffleArray(array);
            }
        },
        allowClick() {
            if (this.chance && this.time && ((this.card2 && !this.card2.showCard) || !this.card1 || !this.card2 || this.firstClick)) {
                return true;
            }
            return false;
        },
        isWin() {
            if (this.successMove == this.cards.length )
                return true;
            return false;
        }
    },
    watch: {
        runTimer(val, oldVal) {
            if (oldVal === false && val === true) {
                this.timer = setInterval(() => {
                    if (this.time > 0) {
                        this.time += -1;
                    }
                }, 1000);
            }

        },
        time(val) {
            const min = Math.floor(val / 60);
            const sec = Math.floor(val % 60);
            document.getElementById("min").innerHTML = (min < 10) ? `0${min}` : min;
            document.getElementById("sec").innerHTML = (sec < 10) ? `0${sec}` : sec;
        },
        isWin(val) {
            if (val === true) {
                clearInterval(this.timer);
            }
        },
        chance(val) {
            if (val === 0) {
                clearInterval(this.timer);
            }
        }

    },

}
</script>

<style scoped>
div.wrapper {
    width: 430px;
    margin: auto;
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
}

.head {
    direction: rtl;
    display: flex;
    justify-content: space-between
}
.win{
    position: absolute;
    bottom: 0;
    right: 0;
}
</style>
