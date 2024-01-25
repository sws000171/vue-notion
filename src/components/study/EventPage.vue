<template>
    <div class="event-page">
        <h2>学習３：イベント</h2>
        <!--従来のJavaScript-->
        <h3><span class=badge badge-secondary>JavaScript</span></h3>
        <span id="countText">クリックした回数は0回です</span>
        <button id ="button" class="btn btn-sm btn-info">Click Me</button>
        <br/>
        <br/>
        <!--Vue.js-->
        <h3><span class=badge badge-secondary>Vue.js</span></h3>
        <span>クリックした回数は{{ count }}回です</span>
        <button id = "button" class="btn btn-sm btn-info" @click="onClickButton()">Click Me</button>
    </div>
</template>

<script>
//従来のJavaScript
let count = 0;
window.onload = ()=> {
    const button = document.getElementById('button');
    button.onclick = ()=> {
        count ++;
        //オブジェクトの変更はできないがプロパティの変更は可能なのでconst
        const countText = document.getElementById('countText');
        countText.innerHTML = 'クリックした回数は' + count + '回です'; 
    };
}
export default{
    data(){
        return{
            count : 0
        }
    },
    methods : {
        onClickButton() {
            this.count++;
        }
    }
}
</script>

<style scoped>
.event-page{
    padding: 20px;
}
</style>