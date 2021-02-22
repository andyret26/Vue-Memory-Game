<template>
    <div>
       <div id="container">

           <div id="player1" class="player">
               {{playerList[0].nickname}}
           </div>

           <div id="scoreP1" class="score">
               {{playerList[0].score}}
               </div>
           <p>-</p>

           <div id="scoreP2" class="score">
               {{playerList[1].score}}
            </div>

           <div id="player2" class="player">
                {{playerList[1].nickname}}
           </div>
           
       </div>
    </div>
</template>



<script>
export default {
    props: ["playerList"]
}
</script>


<style scoped>
#container{
    position: relative;
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;

    border-bottom: solid lightgray 2px;
    padding: 5px;
    margin-bottom: 10px;
    height: 50px;
}

.score{
    padding: 5px;
    border:solid black ;
    margin-right: 10px;
    margin-left: 10px;
    font-weight: bold;
}

p{
    margin-top: 0;
    margin-bottom: 0;
    margin-left: 5px;
    margin-right: 5px;
}

</style>