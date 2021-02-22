<template>
    <div class="card"  :class="flipAnimation">

        <div class="card-face card-back" @click.self="turnCard">
        </div>

        <div class="card-face card-front">
                       
            <img :src="`images/${matchId}.png`"
            :alt="matchId"
            />
            
        </div>

    </div>

</template>

<script>
export default {
    props: {
        value: {
            type: Number,
            required: true
        },

        cardHidden: {
            type: Boolean,
            default: true
        },

        matchId: {
            type: String,
            required: true
        },

        position: {
            type: Number,
            
        },

    },

    data: () => {
        return{
            msg: "hello",
        }
    },

    methods: {
        turnCard: function(){
            
            this.$emit("cardFliped", this.value, this.position);

        },

    },

    computed: {
        flipAnimation: function(){
            if(this.cardHidden === false){
                return "cardFliped"
            }
            else{
                return ""
            }
        }
    }


}
</script>


<style scoped>

.card{
    text-align: center;
    transition: 0.5s transform ease-in;
    position: relative;
    backface-visibility: hidden;
    transform-style: preserve-3d;
}

.card-face{
    height: 100%;
    width: 100%;
    border-radius: 10px;
    display: flex;
    align-items: center;
    justify-content: center;
    backface-visibility: hidden;
}

.card-front{
    background-color: white;
    transform: rotateY(180deg);
}


.card-back{
background-image: url('../../public/images/Europe.jpg');
background-repeat: no-repeat;
background-size: contain;
position: absolute;
top: 0;
}

img{
    border-radius: inherit;
    height: inherit;
    width: inherit;
}

.cardFliped{
    transform: rotateY(180deg);
}






</style>