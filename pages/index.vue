<template>
    <!-- FORM 1: Birth date -->
    <section class="section" id="form1" >
        <form @submit.prevent="fromf1tof2">
            <div class="birth-fields">
                <input type="number" v-model="day" placeholder="Day" min="1" max="31" required />
                <input type="number" v-model="month" placeholder="Month" min="1" max="12" required />
                <input type="number" v-model="year" placeholder="Year" min="1900" max="2024" required />
            </div>
            <button type="submit">Next</button>
        </form>
    </section>

    <!-- FORM 2: Gender -->
    <section class="section" id="form2">
        <form @submit.prevent="fromf2tof3">
            <div class="gender-options">
                <label>
                <input type="radio" name="gender" value="male"/>
                <span class="gender-label">Male</span>
                </label>
                <label>
                <input type="radio" name="gender" value="female" />
                <span class="gender-label">Female</span>
                </label>
                <label>
                <input type="radio" name="gender" value="other" checked />
                <span class="gender-label">Other / Prefer not to say</span>
                </label>
            </div>
            <button type="submit">Next</button>
        </form>
    </section>

    <!-- FORM 3: Region -->
    <section class="section" id="form3">
        <form @submit.prevent="fromf3tomento">
            <select required>
                <option value="europe" selected>Europe</option>
                <option value="north-america">North America</option>
            </select>
            <button type="submit">Show Calendar</button>
        </form>
    </section>

    <!-- Memento Mori -->
    <section id="memento-mori">
        <div id="grid-alive" class="grid"></div>
    </section>
</template>

<script setup>

//user defined values
let year = 2023
let month = 1
let day = 13

let livespan = 85;

//forms
function fromf1tof2(){
    console.log("form1 to form2");
    
    firstYearWeekCount.value = calculatefirstYearWeekCount(year, month, day);
    fullYearCount.value = calculateFullYearCount(year);
    lastYearWeekCount.value = calculateLastYearWeekCount();

    generateGridFromValue();

    smoothScrollTo("memento-mori");
}

function fromf2tof3(){
    console.log("form1 to form2");
    //handle values

    smoothScrollTo("form3");
}

function fromf3tomento(){
    console.log("form1 to form2");
    //handle values

    smoothScrollTo("memento-mori");
}

function smoothScrollTo(elementId) {
    const element = document.getElementById(elementId);
    if (element) {
        element.scrollIntoView({ behavior: 'smooth' });
    }
}

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
        noneBox.className = "week invisible";
        return noneBox;
    }

    const GENERATE_PASSEDBOX = function (){
        let passedBox = document.createElement("div");
        passedBox.className = "week past";
        return passedBox;
    }

    const GENERATE_FUTUREBOX = function (){
        let futureBox = document.createElement("div");
        futureBox.className = "week future";
        return futureBox;
    }

    let grid = document.getElementById("grid-alive");

    //add first year column
    let firstYear = document.createElement("div");
    firstYear.className = "year-column";
    let i = 0
    for(; i < firstYearWeekCount.value; i++){
        firstYear.prepend(GENERATE_NONEBOX());
    }
    let maxWeek = isYearLeap(year) ? 53 : 52;
    for(; i < maxWeek; i++){
        firstYear.prepend(GENERATE_PASSEDBOX());
    }
    if(maxWeek == 52){
        firstYear.prepend(GENERATE_NONEBOX());
    }
    grid.appendChild(firstYear);

    //add full year columns
    for(let i = 0; i < fullYearCount.value; i++){
        let yearColumn = document.createElement("div");
        yearColumn.className = "year-column";
        let maxWeek = isYearLeap(year + i + 1) ? 53 : 52;
        for(let j = 0; j < maxWeek; j++){
            yearColumn.prepend(GENERATE_PASSEDBOX());
        }
        if(maxWeek == 52){
            yearColumn.prepend(GENERATE_NONEBOX());
        }
        grid.appendChild(yearColumn);
    }

    //add last year column
    let lastYear = document.createElement("div");
    lastYear.className = "year-column";
    i = 0;
    for(; i < lastYearWeekCount.value; i++){
        lastYear.prepend(GENERATE_PASSEDBOX());
    }
    maxWeek = isYearLeap(year + fullYearCount.value + 1) ? 53 : 52;
    for(; i < maxWeek; i++){
        lastYear.prepend(GENERATE_FUTUREBOX());
    }
    if(maxWeek == 52){
        lastYear.prepend(GENERATE_NONEBOX());
    }
    grid.appendChild(lastYear);

    //add future year columns
    let curYearpassed = fullYearCount.value + 1;
    for(let i = curYearpassed; i < livespan; i++){
        let yearColumn = document.createElement("div");
        yearColumn.className = "year-column";
        let maxWeek = isYearLeap(year + i + 1) ? 53 : 52;
        for(let j = 0; j < maxWeek; j++){
            yearColumn.prepend(GENERATE_FUTUREBOX());
        }
        if(maxWeek == 52){
            yearColumn.prepend(GENERATE_NONEBOX());
        }
        grid.appendChild(yearColumn);
    }
}

onMounted(() => {
    
});

</script>

<style lang="css">
    @import url("~/assets/css/main.css");

    @import url("~/assets/css/memento_pc.css");
    @import url("~/assets/css/forms_pc.css");

    
</style>