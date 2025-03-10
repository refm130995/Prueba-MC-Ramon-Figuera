<template>
  <div class="container">
    <div class="box">
      <h3>Available Options</h3>
      <div class="list">
        <div
          v-for="option in availableOptions"
          :key="option.id"
          class="item"
          @click="toggleOption(option, 'available')"
        >
          {{ option.label }}
        </div>
      </div>
    </div>

    <div class="box">
      <h3>Disabled Options</h3>
      <div class="list">
        <div
          v-for="option in disabledOptions"
          :key="option.id"
          class="item"
          @click="toggleOption(option, 'disabled')"
        >
          {{ option.label }}
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { defineProps, defineEmits, ref, watch } from "vue";
import fieldMixin from "../FieldMixin";

const props = defineProps({
  field: {
    type: Object,
    required: true,
  },
  modelValue: {
    type: Array,
    default: () => [],
  },
});

const emit = defineEmits(["update"]);

const { handleChange, debounce } = fieldMixin.setup(props, { emit });

const availableOptions = ref([...props.field.options]);
const disabledOptions = ref([]);

watch(
  () => props.modelValue,
  (newVal) => {
    availableOptions.value = [...newVal];
  }
);

const toggleOption = (option, from) => {
  debounce(() => {
    if (from === "available") {
      availableOptions.value = availableOptions.value.filter(
        (o) => o !== option
      );
      disabledOptions.value.push(option);
    } else {
      disabledOptions.value = disabledOptions.value.filter((o) => o !== option);
      availableOptions.value.push(option);
    }
    emit("update", {
      value: disabledOptions.value,
      id: props.field.id,
    });
  }, 50)();
};
</script>

<style scoped>
.container {
  display: flex;
  justify-content: center;
  gap: 20px;
  padding: 20px;
}

.box {
  padding: 10px;
  border-radius: 8px;
  width: 200px;
  text-align: center;
  color: white;
}

.list {
  border: 1px solid white;
  min-height: 150px;
  padding: 5px;
}

.item {
  padding: 5px;
  cursor: pointer;
  transition: background 0.2s;
}

.item:hover {
  background: #333;
}
</style>
