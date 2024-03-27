<template>
  <div class="card">
    <h1>{{ data.buyer }}</h1>
    <p>מכירה: {{ data.name }}</p>
    <p>יעד: {{ data.target + " " + data.currency }}</p>
    <p>התקדמות: {{ data.progress }}</p>
    <p>מוכר: {{ data.seller }}</p>
    <p>תאריך יעד: {{ endDateString }}</p>
    <p>כרגע אמור להיות ב:{{ Math.round(progress.currentTarget.value) }}</p>
    <div class="progress progress-striped" :class="{ active: !progress.done.value }">
      <div role="progressbar " :style="{ width: progress.width.value }"
        :class="`progress-bar progress-bar-${progress.color.value}`"><span>{{ progress.width.value }}</span></div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue';
import {
  formatJewishDateInHebrew,
  toJewishDate
} from "jewish-date";

const startingDate = new Date(2024, 2, 23);
const getPassedTimePercentage = () => {
  const now = new Date();
  const totalTime = props.data.end.getTime() - startingDate.getTime();
  const passedTime = now.getTime() - startingDate.getTime();
  return passedTime / totalTime;
};

const props = defineProps<{ data: SaleData }>();
const progress = {
  width: computed(() => `${Math.min((props.data.progress / props.data.target) * 100, 100).toFixed(1)}%`),
  currentTarget: computed(() => props.data.target * getPassedTimePercentage()),
  color: computed(() => {
    const x = (props.data.progress + 0.1 * props.data.target) / progress.currentTarget.value;
    if (x >= 1)
      return 'success';
    if (x >= 0.65)
      return 'info';
    if (x >= 0.3)
      return 'warning';
    return 'danger';
  }),
  done: computed(() => props.data.progress >= props.data.target)
};


const endDateString = computed(() => {
  const hebrewDate = formatJewishDateInHebrew(toJewishDate(props.data.end));
  const gregorianDate = props.data.end.toLocaleDateString('he-IL');
  return `${hebrewDate} (${gregorianDate} למניינם)`;
});
</script>


<style scoped>
.card {
  background-color: #f0f0f0;
  border-radius: 0.5em;
  padding: 1em;
  margin: 1em;
}



.progress {
  background-color: #f5f5f5;
  border-radius: 3px;
  box-shadow: none;
}

.progress.progress-xs {
  height: 5px;
  margin-top: 5px;
}

.progress.progress-sm {
  height: 10px;
  margin-top: 5px;
}

.progress.progress-lg {
  height: 25px;
}

.progress.vertical {
  position: relative;
  width: 20px;
  height: 200px;
  display: inline-block;
  margin-right: 10px;
}

.progress.vertical>.progress-bar {
  width: 100% !important;
  position: absolute;
  bottom: 0;
}

.progress.vertical.progress-xs {
  width: 5px;
  margin-top: 5px;
}

.progress.vertical.progress-sm {
  width: 10px;
  margin-top: 5px;
}

.progress.vertical.progress-lg {
  width: 30px;
}

.progress-bar {
  background-color: #2196f3;
  box-shadow: none;
}

.progress-bar.text-left {
  text-align: left;
}

.progress-bar.text-left span {
  margin-left: 10px;
}

.progress-bar.text-right {
  text-align: right;
}

.progress-bar.text-right span {
  margin-right: 10px;
}

@-webkit-keyframes progress-bar-stripes {
  from {
    background-position: 40px 0;
  }

  to {
    background-position: 0 0;
  }
}

@keyframes progress-bar-stripes {
  from {
    background-position: 40px 0;
  }

  to {
    background-position: 0 0;
  }
}

.progress.active .progress-bar,
.progress-bar.active {
  -webkit-animation: progress-bar-stripes 2s linear infinite;
  -o-animation: progress-bar-stripes 2s linear infinite;
  animation: progress-bar-stripes 2s linear infinite;
}

.progress-striped .progress-bar,
.progress-bar-striped {
  background-image: -webkit-linear-gradient(45deg, rgba(255, 255, 255, .15) 25%, transparent 25%, transparent 50%, rgba(255, 255, 255, .15) 50%, rgba(255, 255, 255, .15) 75%, transparent 75%, transparent);
  background-image: -o-linear-gradient(45deg, rgba(255, 255, 255, .15) 25%, transparent 25%, transparent 50%, rgba(255, 255, 255, .15) 50%, rgba(255, 255, 255, .15) 75%, transparent 75%, transparent);
  background-image: linear-gradient(45deg, rgba(255, 255, 255, .15) 25%, transparent 25%, transparent 50%, rgba(255, 255, 255, .15) 50%, rgba(255, 255, 255, .15) 75%, transparent 75%, transparent);
  background-size: 40px 40px;
}

.progress-bar-secondary {
  background-color: #323a45;
}

.progress-bar-default {
  background-color: #b0bec5;
}

.progress-bar-success {
  background-color: #64dd17;
}

.progress-bar-info {
  background-color: #29b6f6;
}

.progress-bar-warning {
  background-color: #ffd600;
}

.progress-bar-danger {
  background-color: #ef1c1c;
}
</style>
