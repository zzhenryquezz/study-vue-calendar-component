<script setup lang="ts">
import moment from "moment";
import { computed, ref, watch } from "vue";

const weekdays = [
  "Sunday",
  "Monday",
  "Tuesday",
  "Wednesday",
  "Thursday",
  "Friday",
  "Saturday",
];

const current = ref(new Date());
const blocks = ref<any[]>([]);

// const firstDay = computed(() => moment(current.value).startOf("month"));
// const paddingDays = computed(() => firstDay.value.get("day"));

function getMonthData(date: Date) {
  const firstDay = moment(date).startOf("month");
  const paddingDays = firstDay.get("day");

  const blocks = Array.from({ length: 42 }).map((_, i) => {
    const date = moment(firstDay).add(i - paddingDays, "day");
    const classes = ["border p-2 h-16"];

    if (date.isSame(moment(), "day")) {
      classes.push("text-teal-500 font-bold");
    }

    if (i < paddingDays || !date.isSame(firstDay, "month")) {
      classes.push("bg-gray-100");
    }

    return {
      label: date.format("DD"),
      classes,
    };
  });

  return {
    blocks,
  };
}

function load() {
  const month = getMonthData(current.value);

  blocks.value = month.blocks;
}

watch(current, load, { immediate: true });
</script>

<template>
  <div class="h-screen w-screen flex flex-wrap items-center justify-center">
    <div class="w-full max-w-[800px] flex flex-wrap justify-center">
      <div class="w-full flex items-center mb-4">
        <input
          id="date"
          class="border p-2 mr-4"
          :value="moment(current).format('YYYY-MM-DD')"
          @change="e => current = moment((e.target as any).value).toDate()"
        />

        <label for="date" class="block font-bold text-xl mr-4">
          {{ moment(current).format("DD MMMM YYYY") }}
        </label>

        <button
          class="border border-teal-500 text-teal-500 px-4 py-2 rounded ml-auto"
          @click="current = new Date()"
        >
          Today
        </button>
      </div>

      <div class="grid grid-cols-7 w-full">
        <div
          v-for="weekday in weekdays"
          :key="weekday"
          class="h-12 bg-teal-500 text-white flex items-center justify-center"
        >
          {{ weekday }}
        </div>

        <div
          v-for="(block, index) in blocks"
          :key="index"
          :class="block.classes"
        >
          {{ block.label }}
        </div>
      </div>
    </div>
  </div>
</template>
