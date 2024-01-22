<script setup>
import Levels from './Levels.vue';
import Error from './Error.vue';
import { reactive, ref, computed, onMounted } from 'vue';

const level = ref(1);
const err = reactive([]);
const nameOfLottery = ref();
const nameOfPerson = ref();
const lotteries = reactive({});
// const people = reactive([]);
const winner = ref();

onMounted(()=>{
  const storedLotteries = JSON.parse(localStorage.getItem('lotteries')) || {};
  Object.assign(lotteries, storedLotteries);
});

const addLottery = ()=>{
  if(!nameOfLottery.value){
    errHandler(['empty field not acceptable'], 5000);
  }else{
    (nameOfLottery.value in lotteries) ? lotteries[nameOfLottery.value] : lotteries[nameOfLottery.value] = [];
    localStorage.setItem("lotteries", JSON.stringify(lotteries));
    nextLevel();
  }
};

const editLottery = (key) =>{
  nameOfLottery.value = key;
  nextLevel();
}

const deleteLottery = (key, lotteries) =>{
  delete lotteries[key];
  localStorage.setItem("lotteries", JSON.stringify(lotteries));
}

const addPerson = () =>{
  if(!nameOfPerson.value){
    errHandler(['empty field not acceptable'], 5000);
  }else{
    lotteries[nameOfLottery.value].push(nameOfPerson.value);
    localStorage.setItem("lotteries", JSON.stringify(lotteries));
    nameOfPerson.value = null;
    err.pop();
  }
}

const editPerson = (index, array) =>{
  const reverseIndexCalculator = reverseIndexCalculator(index, array);
  array.splice(reverseIndexCalculator, 1);
  localStorage.setItem("lotteries", JSON.stringify(lotteries));
}

const deletePerson = (index, array) =>{
  const reverseIndexCalculator = parseInt(array.length-index-1);
  array.splice(reverseIndexCalculator, 1);
  localStorage.setItem("lotteries", JSON.stringify(lotteries));
}

const result = () => {
  if(people.length === 0){
    errHandler(['add someone to the lottery'], 5000);
  }else{
    nextLevel();
    let randNum = Math.floor(Math.random() * (lotteries[nameOfLottery.value].length));
    winner.value = lotteries[nameOfLottery.value][randNum];
  }
}

const reset = () =>{
  level.value = 1;
  nameOfLottery.value = null;
}

const nextLevel = ()=>{
  level.value++;
  err.pop();
}

const previousLevel = ()=>{
  level.value--;
  err.pop();
}

const errHandler = (errors, timer) =>{
  err.pop();
  errors.forEach(error => {
    err.push({msg: error});
  });
}
</script>

<template>
    <div class="wrapper">

    <!-- Level 1 -->
    <Levels v-if="level === 1">

        <template #header>Choose your options</template>

        <div v-if="Array.isArray(err) && err.length" v-for="error in err">
          <Error :msg="error.msg" />
        </div>

        <div>
            <label for="nameOfLottery">Name of Lottery:</label>
            <input id="nameOfLottery" autofocus @keyup.enter="addLottery" v-model="nameOfLottery" placeholder="Name of Lottery"/>
        </div>

        <!-- <div>
            <label>Color:</label>
            <select v-model="colorOfLottery">
                <option value="default">Default</option>
                <option value="blue">Blue</option>
                <option value="orange">Orange</option>
            </select>
        </div> -->

        <template #button>
          <button class="next" @click="addLottery">Save and Go Next</button>
        </template>

        <!--  -->
        <template #more v-if="Object.keys(lotteries).length !== 0">
            <label for="lottories">Recent Lotteries:</label>
            <ul id="lottories">
                <!-- lotteries.slice().reverse() -->
                <li v-for="(value, key, index) in lotteries" :key="index">
                  {{ key }} <div class="iBtns"><span @click="editLottery(key)"><font-awesome-icon :icon="['fas', 'pen-to-square']" /></span> <span @click="deleteLottery(key, lotteries)"><font-awesome-icon :icon="['fas', 'trash-can']" /></span></div>
                </li>
            </ul>
        </template>
    </Levels>

    <!-- Level 2 -->
    <Levels v-else-if="level === 2" >
        <template #header>{{ nameOfLottery }}</template>

        <div v-if="Array.isArray(err) && err.length" v-for="error in err">
          <Error :msg="error.msg" />
        </div>
        
        <div v-if="Object.keys(lotteries[nameOfLottery]).length !== 0">
          <label for="people">List of people:</label>
          <ul id="people">

              <li v-for="(person, index) in lotteries[nameOfLottery].slice().reverse()"> 
                {{ person }} <div class="iBtns"><span @click="editPerson(index, lotteries[nameOfLottery])"><font-awesome-icon :icon="['fas', 'pen-to-square']" /></span> <span @click="deletePerson(index, lotteries[nameOfLottery])"><font-awesome-icon :icon="['fas', 'trash-can']" /></span></div>
              </li>
          </ul>
        </div>
        <label>Name:</label>
        <input type="text" v-model="nameOfPerson" @keyup.enter="addPerson" placeholder="Name" />
        <template #button>
            <button class="add" @click="addPerson">Add to the List</button>
            <button class="next" @click="result">Save and Let find out the Winner</button>
            <button class="back" @click="previousLevel">Back to Settings</button>
        </template>
    </Levels>

    <Levels v-else-if="level === 3">
        <template #header>Final </template>

        <div class="winner">The lucky winner is... <strong>{{ winner }}</strong></div>

        <canvas id="winner"></canvas>

        <template #button>
          <button class="back" @click="reset">Reset</button>
        </template>
    </Levels>

</div>

</template>

<style scoped lang="scss">

canvas{display:block}

select, label{
  display: block;
  text-align: left;
}

button, input, select{
  width: 90%;
  box-sizing: content-box;
  padding: .9rem .7rem;
  border-width: .1rem;
  margin: 15px auto;
  border-radius: 10px;
  font-size: .9rem;
  &:focus{
    outline: none;
  }
}

button{
  margin: 5px 0px;
  border: none;
  color: #fff;
  height: 20px;
  transition: font-size .4s;
  &:hover{
    font-size: .95rem;
    cursor: pointer;
  }
}

label{
  // font-weight: bold;
  font-size: 1rem;
}

ul{
  height: 100px;
  overflow-y: scroll;
  margin: 10px 0px;
  li{
    position: relative;
    list-style: none;
    cursor: pointer;
    margin: 5px;
    display: inline-block;
    padding: .6rem 2.4rem;
    border: 1px solid #222;
    border-radius: 10px;
    overflow: hidden;
    .iBtns{
      position: absolute;
      width: 100%;
      height: 100%;
      left: 0;
      top: 0;
      display: none;
      color: #5C5D8D;
      span{
        margin-right: 5px;
        line-height: 35px;
        &:hover{
          color: #7a7cff;
        }
      }
    }
    &:hover .iBtns{
        background: #f0f0f0;
        opacity: .9;
        display: block;
      }
  }
}
.winner{
  margin-bottom: 20px;
  strong{
    display: block;
    margin-top: 10px;
    font-size: 2rem;
  }
}

</style>