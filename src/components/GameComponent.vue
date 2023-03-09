<template>
  <div class="game-container">
    <v-card class="mx-auto pa-5">
      <v-card-title>
        <span class="headline">Game Page</span>
      </v-card-title>
      <v-card-text>
        <v-row>
          <v-col cols="12" sm="6">
            <v-text-field
              v-model.number="sizeX"
              :error-messages="v$.sizeX.$errors.map(e => e.$message)"
              label="Size X"
              type="number"
              @input="handleSizeInput('sizeX', $event)"
              @blur="v$.sizeX.$touch"
              max="64"
            ></v-text-field>
          </v-col>
          <v-col cols="12" sm="6">
            <v-text-field
              v-model.number="sizeY"
              :error-messages="v$.sizeY.$errors.map(e => e.$message)"
              label="Size Y"
              type="number"
              @input="handleSizeInput('sizeY', $event)"
              @blur="v$.sizeY.$touch"
              max="64"
            ></v-text-field>
          </v-col>
        </v-row>
        <div class="grid" v-if="grid.length">
          <div
            v-for="(row, y) in grid"
            :key="y"
            class="row"
          >
            <div
              v-for="(cell, x) in row"
              :key="`${x}-${y}`"
              class="cell"
              :class="{ 'blue': cell }"
              @mouseover="toggleCell(x, y)"
            ></div>
          </div>
        </div>
      </v-card-text>
    </v-card>
  </div>
</template>

<script>
import {reactive, toRefs, watchEffect} from 'vue'
import {maxLength, required} from "@vuelidate/validators";
import {useVuelidate} from "@vuelidate/core";

export default {
  setup() {

    const initialState = {
      sizeX: 10,
      sizeY: 10,
      grid: [],
    }

    const state = reactive({
      ...initialState,
    })

    const rules = {
      sizeX: [required, maxLength(64)],
      sizeY: [required, maxLength(64)],
    }

    const v$ = useVuelidate(rules, state)

    const initGrid = () => {
      state.grid = new Array(state.sizeY).fill().map(() => new Array(state.sizeX).fill(false))
    }

    const toggleCell = (x, y) => {
      state.grid[y][x] = !state.grid[y][x]
    }

    const handleSizeInput = (size, event) => {
      const maxSize = 64;
      const enteredValue = parseInt(event.target.value, 10);
      if (enteredValue > maxSize) {
        event.preventDefault();
        event.target.value = maxSize;
        state[size] = maxSize;
      } else {
        state[size] = enteredValue;
      }
    }

    const watchSize = () => {
      initGrid()
    }

    watchEffect(() => {
      initGrid()
    })

    return {
      ...toRefs(state),
      v$,
      toggleCell,
      handleSizeInput
    }
  }
}
</script>

<style>
.grid {
  display: flex;
  flex-direction: column;
  background-color: #333;
  margin-top: 24px;
  padding: 20px;
}

.row {
  display: flex;
  justify-content: center;
}

.cell {
  width: 36px;
  height: 36px;
  border: 1px solid black;
  margin-right: 2px;
  background-color: white;
}

.blue {
  background-color: blue;
}

.white {
  background-color: white;
}
</style>
