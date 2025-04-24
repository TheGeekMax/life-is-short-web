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
                <option value="Europe" selected>Europe</option>
                <option value="North-America">North America</option>
                <option value="South-America">South America</option>
                <option value="Africa">Africa</option>
                <option value="Asia">Asia</option>
                <option value="Oceania">Oceania</option>
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

const MAX_LIFESPAN_BY_REGION_AND_GENRE = {
    "Africa": {
        "male": 63,
        "female": 67,
        "other": 65
    },
    "Asia": {
        "male": 72,
        "female": 76,
        "other": 74
    },
    "Europe": {
        "male": 77,
        "female": 83,
        "other": 80
    },
    "North-America": {
        "male": 77,
        "female": 82,
        "other": 80
    },
    "South-America": {
        "male": 73,
        "female": 79,
        "other": 76
    },
    "Oceania": {
        "male": 72,
        "female": 77,
        "other": 75
    }
}

const EVENT_LISTS = [
  {
    "type": "global",
    "weeks": 17,
    "tooltip": "Rolls over front to back and back to front"
  },
  {
    "type": "global",
    "weeks": 26,
    "tooltip": "Sits unsupported"
  },
  {
    "type": "global",
    "weeks": 39,
    "tooltip": "Begins crawling"
  },
  {
    "type": "global",
    "weeks": 52,
    "tooltip": "Takes first independent steps"
  },
  {
    "type": "global",
    "weeks": 52,
    "tooltip": "Says first meaningful word"
  },
  {
    "type": "family",
    "weeks": 140,
    "tooltip": "Welcomes birth of a younger sibling"
  },
  {
    "type": "family",
    "weeks": 416,
    "tooltip": "Experiences parental divorce or separation"
  },
  {
    "type": "global",
    "weeks": 312,
    "tooltip": "Loses first baby tooth"
  },
  {
    "type": "personal",
    "weeks": 340,
    "tooltip": "Loses first baby tooth (personal memory)"
  },
  {
    "type": "family",
    "weeks": 624,
    "tooltip": "Family moves to a new home"
  },
  {
    "type": "global",
    "weeks": 624,
    "tooltip": "Onset of puberty"
  },
  {
    "type": "family",
    "weeks": 780,
    "tooltip": "First sibling leaves home for college or work"
  },
  {
    "type": "personal",
    "weeks": 832,
    "tooltip": "Shares first kiss"
  },
  {
    "type": "personal",
    "weeks": 884,
    "tooltip": "Has first romantic relationship"
  },
  {
    "type": "global",
    "weeks": 936,
    "tooltip": "Reaches legal voting age"
  },
  {
    "type": "personal",
    "weeks": 1044,
    "tooltip": "Moves out of the parental home"
  },
  {
    "type": "global",
    "weeks": 1144,
    "tooltip": "Enters first full-time employment"
  },
  {
    "type": "personal",
    "weeks": 1144,
    "tooltip": "Completes highest level of formal education"
  },
  {
    "type": "family",
    "weeks": 1248,
    "tooltip": "First niece or nephew is born"
  },
  {
    "type": "personal",
    "weeks": 1248,
    "tooltip": "Starts first full-time job"
  },
  {
    "type": "family",
    "weeks": 1300,
    "tooltip": "First grandparent’s death"
  },
  {
    "type": "family",
    "weeks": 1560,
    "tooltip": "First child is born"
  },
  {
    "type": "personal",
    "weeks": 1560,
    "tooltip": "Marries"
  },
  {
    "type": "family",
    "weeks": 2016,
    "tooltip": "Family major health crisis of a parent"
  },
  {
    "type": "personal",
    "weeks": 1820,
    "tooltip": "Purchases first home"
  },
  {
    "type": "personal",
    "weeks": 2280,
    "tooltip": "Experiences mid-life career change"
  },
  {
    "type": "family",
    "weeks": 2340,
    "tooltip": "First parent’s death"
  },
  {
    "type": "global",
    "weeks": 3380,
    "tooltip": "Becomes eligible for standard retirement benefits"
  },
  {
    "type": "family",
    "weeks": 3380,
    "tooltip": "Parent reaches typical retirement age"
  },
  {
    "type": "personal",
    "weeks": 3380,
    "tooltip": "Retires from primary career"
  }
]

const COLOR_EVENT_CLASS = {
    "global": "stat-yellow",
    "family": "stat-blue",
    "personal": "stat-red"
}


//user defined values
let year = 2023
let month = 1
let day = 13

let livespan = 85;

//forms
function fromf1tof2(){
    console.log("form1 to form2");

    smoothScrollTo("form2");
}

function fromf2tof3(){
    console.log("form2 to form3");
    //handle values

    smoothScrollTo("form3");
}

function fromf3tomento(){
    console.log("form3 to memento");
    //handle values

    let region = document.querySelector("#form3 select").value
    let genre = document.querySelector("#form2 input[type='radio']:checked").value


    livespan = MAX_LIFESPAN_BY_REGION_AND_GENRE[region][genre];
    console.log("livespan: " + livespan);
    firstYearWeekCount.value = calculatefirstYearWeekCount(year, month, day);
    fullYearCount.value = calculateFullYearCount(year);
    lastYearWeekCount.value = calculateLastYearWeekCount();

    generateGridFromValue();

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
    let totalWeekCount = 0;

    const GET_EVENT_DATA = function (week){
        let event = EVENT_LISTS.find(event => event.weeks == week);
        if(event){
            return event;
        }
        return null;
    }


    const GENERATE_NONEBOX = function (){
        let noneBox = document.createElement("div");
        noneBox.className = "week invisible";
        
        return noneBox;
    }

    const GENERATE_PASSEDBOX = function (){
        totalWeekCount++;

        let passedBox = document.createElement("div");
        passedBox.className = "week past ";

        let ev = GET_EVENT_DATA(totalWeekCount);
        if(ev){
            passedBox.setAttribute("data-tooltip", ev.tooltip);
            passedBox.className += COLOR_EVENT_CLASS[ev.type];
        }
        return passedBox;
    }

    const GENERATE_FUTUREBOX = function (){
        totalWeekCount++;

        let futureBox = document.createElement("div");
        futureBox.className = "week future ";

        let ev = GET_EVENT_DATA(totalWeekCount);
        if(ev){
            futureBox.setAttribute("data-tooltip", ev.tooltip);
            futureBox.className += COLOR_EVENT_CLASS[ev.type];
        }
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

    @import url("~/assets/css/memento_stats.css");
</style>