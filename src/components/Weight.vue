<script setup>
import Chart from "chart.js/auto";
import { ref, shallowRef, computed, watch, nextTick } from "vue";

const weights = ref([]);
const weightInput = ref(0.0);
const weightChartEl = ref(null);
const weightChart = shallowRef(null);

const currentWeight = computed(() => {
  return weights.value.sort((a, b) => b.date - a.date)[0] || { weight: 0 };
});

const addWeight = () => {
  weights.value.push({
    weight: weightInput.value,
    date: new Date().getTime(),
  });
};

watch(
  weights,
  (newWeights) => {
    const ws = [...newWeights];
    if (weightChart.value) {
      weightChart.value.data.labels = ws
        .sort((a, b) => a.date - b.date)
        .map((w) => new Date(w.date).toLocaleDateString())
        .slice(-7);
      weightChart.value.data.datasets[0].data = ws
        .sort((a, b) => a.date - b.date)
        .map((w) => w.weight)
        .slice(-7);
      weightChart.value.update();
      return;
    }
    nextTick(() => {
      weightChart.value = new Chart(weightChartEl.value.getContext("2d"), {
        type: "line",
        data: {
          labels: ws
            .sort((a, b) => a.date - b.date)
            .map((w) => new Date(w.date).toLocaleDateString()),
          datasets: [
            {
              labels: "Weights",
              data: ws.sort((a, b) => a.date - b.date).map((w) => w.weight),
              backgroundColor: "rgba(160, 0, 255, .4)",
              borderColor: "rgb(160, 0, 255)",
              borderWidth: 1,
              fill: true,
            },
          ],
        },
        option: {
          responsive: true,
          maintainAspectRatio: false,
        },
      });
    });
  },
  { deep: true }
);
</script>

<template>
  <main class="main">
    <span class="current-weight">{{ currentWeight.weight }}</span>
    <form class="form" @submit.prevent="addWeight">
      <input class="input" v-model="weightInput" type="number" step="0.1" />
      <input class="input-submit" type="submit" value="Добавить" />
    </form>
    <div class="dash-board" v-if="weights && weights.length > 0">
      <h2>График изменения вашего веса</h2>
      <div class="canvas-box">
        <canvas ref="weightChartEl"></canvas>
      </div>
      <div class="weight-history">
        <h2>История вашего веса</h2>
        <ul class="list">
          <li class="list-item" v-for="weight in weights">
            <span>{{ weight.weight }} кг.</span>
            <span>{{ new Date(weight.date).toLocaleDateString() }}</span>
          </li>
        </ul>
      </div>
    </div>
  </main>
</template>

<style lang="scss" scoped>
@import "../assets/styles/main.scss";

.main {
  display: flex;
  align-items: center;
  flex-direction: column;
  & .current-weight {
    display: flex;
    align-items: center;
    justify-content: center;
    border: 7px solid $main-color;
    margin-bottom: 30px;
    border-radius: 50%;
    color: $text-color;
    font-weight: 400;
    font-size: 30px;
    width: 140px;
    height: 140px;
  }
  & .form {
    margin: 30px 0;
  }
  & .input {
    height: 64px;
    width: 300px;
    font-size: 26px;
    font-weight: 400;
    padding-left: 20px;
    color: $text-color;
    border: 3px solid $main-color;
    &:focus {
      outline: none;
    }
  }
  & .input-submit {
    margin: 0;
    width: 150px;
    height: 64px;
    font-size: 26px;
    cursor: pointer;
    font-weight: 400;
    color: $bg-color;
    border-radius: 0 25px 25px 0;
    border: 3px solid $main-color;
    background-color: $main-color;
  }
  & .dash-board {
    display: flex;
    align-items: center;
    flex-direction: column;
    & .weight-history {
      margin-bottom: 30px;
    }
    & .canvas-box {
      margin: 30px 0;
      padding: 30px;
      border-radius: 20px;
      background-color: $bg-color;
      -webkit-box-shadow: 0px 0px 10px 9px rgba(34, 60, 80, 0.2);
      -moz-box-shadow: 0px 0px 10px 9px rgba(34, 60, 80, 0.2);
      box-shadow: 0px 0px 10px 9px rgba(34, 60, 80, 0.2);
    }
    & .list {
      margin: 10px 0;
      list-style: none;
      & .list-item {
        display: flex;
        flex-direction: row;
        align-items: center;
        justify-content: space-between;
      }
    }
  }
}
</style>
