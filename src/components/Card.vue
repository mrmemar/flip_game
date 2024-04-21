<script>
export default {
    props: ["card", "width", "index", "allowClick","refresh"],
    emits: ["check"],
    data() {
        return {
            showCard: false,
        };
    },
    methods: {
        findImage() {
            if (!this.showCard && this.allowClick) {
                this.showCard = true;
                this.$emit("check", this);
            }
        }

    },
    computed: ({
        style() {
            return `max-width:${this.width}px;height:${this.width}px`;
        }
    }),
    watch: {
        refresh(val) {
            if (val === true) {
                this.showCard = false;
            }
        }
    },

};
</script>

<template>
    <div @click="findImage()" :id="card.id" class="square " :style="style">
        <div class="index animate__animated animate__flipInY " :class="{ hide :showCard}">
            {{ index }}
        </div>
        <img :class="{show:showCard}" class="hide animate__animated  animate__flipInY" :src="card.src" />
    </div>
</template>

<style scoped>
div.square {
    cursor: pointer;
    position: relative;
    flex: 1 0 100px;
}
.square>img,
.square>div {
    border-radius: 5px;
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    align-content: center;
}
img {
    object-fit: cover;
}
.index {
    background: #000;
}
.hide {
    display: none;
}
.show {
    display: block;
}
</style>
