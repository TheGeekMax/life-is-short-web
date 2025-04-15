<template>
    <p>nb de semaine entre l'année de naissance et votre naissance {{ firstYearWeekCount }}</p>
    <p>nb d'année complete entre votre naissance et aujourd'hui {{ fullYearCount }}</p>
    <p>nb de semaine passé depuis le debut de cette année {{ lastYearWeekCount }}</p>
    <div id="livegrid">

    </div>
</template>

<script setup>

//user defined values
let year = 2003
let month = 3
let day = 9

let livespan = 85;

//calculated values
let firstYearWeekCount = ref(0);
let fullYearCount = ref(0);
let lastYearWeekCount = ref(0);

function calculatefirstYearWeekCount(year, month, day) {
    let firstYear = year;
    let firstMonth = month;
    let firstDay = day;

    let firstDate = new Date(firstYear, firstMonth, firstDay);
    let firstWeekCount = Math.ceil((firstDate.getTime() - new Date(firstYear, 0, 1).getTime()) / (1000 * 60 * 60 * 24 * 7));
    return firstWeekCount;
}

function calculateFullYearCount(year) {
    let todayDate = new Date(Date.now());
    let todayYear = todayDate.getFullYear();

    let fullYear = todayYear - year - 1;
    return fullYear;
}

function calculateLastYearWeekCount() {
    //nombre de semaine passé depuis le debut de l'année actuel
    let todayDate = new Date(Date.now());
    let todayYear = todayDate.getFullYear();

    let weekCount = Math.ceil((todayDate.getTime() - new Date(todayYear, 0, 1).getTime()) / (1000 * 60 * 60 * 24 * 7));

    return weekCount
}

function isYearLeap(year) {
    return (year % 4 === 0 && year % 100 !== 0) || (year % 400 === 0);
}

function generateGridFromValue(value){
    const GENERATE_NONEBOX = function (){
        let noneBox = document.createElement("div");
        noneBox.className = "grid-box none";
        noneBox.style.backgroundColor = "white";
        noneBox.innerHTML="."
        return noneBox;
    }

    const GENERATE_PASSEDBOX = function (){
        let passedBox = document.createElement("div");
        passedBox.className = "grid-box passed";
        passedBox.style.backgroundColor = "green";
        passedBox.innerHTML="."
        return passedBox;
    }

    const GENERATE_FUTUREBOX = function (){
        let futureBox = document.createElement("div");
        futureBox.className = "grid-box future";
        futureBox.style.backgroundColor = "red";
        futureBox.innerHTML="."
        return futureBox;
    }

    let grid = document.getElementById("livegrid");

    //add first year column
    let firstYear = document.createElement("div");
    firstYear.className = "grid-column";
    let i = 0
    for(; i < firstYearWeekCount.value; i++){
        firstYear.appendChild(GENERATE_NONEBOX());
    }
    let maxWeek = isYearLeap(year) ? 53 : 52;
    for(; i < maxWeek; i++){
        firstYear.appendChild(GENERATE_PASSEDBOX());
    }
    if(maxWeek == 52){
        firstYear.appendChild(GENERATE_NONEBOX());
    }
    grid.appendChild(firstYear);

    //add full year columns
    for(let i = 0; i < fullYearCount.value; i++){
        let yearColumn = document.createElement("div");
        yearColumn.className = "grid-column";
        let maxWeek = isYearLeap(year + i + 1) ? 53 : 52;
        for(let j = 0; j < maxWeek; j++){
            yearColumn.appendChild(GENERATE_PASSEDBOX());
        }
        if(maxWeek == 52){
            yearColumn.appendChild(GENERATE_NONEBOX());
        }
        grid.appendChild(yearColumn);
    }

    //add last year column
    let lastYear = document.createElement("div");
    lastYear.className = "grid-column";
    i = 0;
    for(; i < lastYearWeekCount.value; i++){
        lastYear.appendChild(GENERATE_PASSEDBOX());
    }
    maxWeek = isYearLeap(year + fullYearCount.value + 1) ? 53 : 52;
    for(; i < maxWeek; i++){
        lastYear.appendChild(GENERATE_FUTUREBOX());
    }
    if(maxWeek == 52){
        lastYear.appendChild(GENERATE_NONEBOX());
    }
    grid.appendChild(lastYear);

    //add future year columns
    let curYearpassed = fullYearCount.value + 1;
    for(let i = curYearpassed; i < livespan; i++){
        let yearColumn = document.createElement("div");
        yearColumn.className = "grid-column";
        let maxWeek = isYearLeap(year + i) ? 53 : 52;
        for(let j = 0; j < maxWeek; j++){
            yearColumn.appendChild(GENERATE_FUTUREBOX());
        }
        if(maxWeek == 52){
            yearColumn.appendChild(GENERATE_NONEBOX());
        }
        grid.appendChild(yearColumn);
    }
}

onMounted(() => {
    firstYearWeekCount.value = calculatefirstYearWeekCount(year, month, day);
    fullYearCount.value = calculateFullYearCount(year);
    lastYearWeekCount.value = calculateLastYearWeekCount();

    generateGridFromValue();
});

</script>

<style lang="css" scoped>
:root {
  --cell-size: 15px; /* Change this value to adjust cell size */
  --cell-gap: 1px;
  --column-gap: 2px;
}

#livegrid {
  display: flex;
  flex-direction: column;
  gap: var(--column-gap);
  overflow-x: auto;
  padding: 20px 0;
  max-width: 100%;
  background-color: #f5f5f5;
  border-radius: 8px;
}

.grid-column {
  display: flex;
  flex-direction: column;
  gap: var(--cell-gap);
}

.grid-box {
  width: var(--cell-size);
  height: var(--cell-size);
  border-radius: 2px;
  box-sizing: border-box;
  border: 1px solid rgba(0, 0, 0, 0.1);
}

.grid-box:hover {
  transform: scale(1.2);
  z-index: 1;
  box-shadow: 0 0 5px rgba(0, 0, 0, 0.2);
}

.grid-box.passed {
  border-color: rgba(0, 128, 0, 0.3);
}

.grid-box.future {
  border-color: rgba(255, 0, 0, 0.3);
}

.grid-box.none {
  border: 1px dashed #ccc;
}
</style>