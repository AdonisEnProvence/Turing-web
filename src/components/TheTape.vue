<script lang="ts" setup>
import { ref, computed } from 'vue';

const steps: [(number | undefined)[], number][] = [
    [[1, 0, 1], 0],
    [[1, 0, 1], 1],
    [[1, 0, 1], 2],
    [[1, 0, 1, 0], 3],
    [[1, 0, 1, 0, 1], 4],
    [[1, 0, 1, 0, 1], 3],
    [[1, 0, 1, 0, 1], 4],
    [[1, 0, 1, 0, 1, 1], 5],
    [[1, 0, 1, 0, 1, 1, 1], 6],
    [[1, 0, 1, 0, 1, 1, 1, 1], 7],
    [[1, 0, 1, 0, 1, 1, 1, 1, 0], 8],
    [[1, 0, 1, 0, 1, 1, 1, 1, 0, 1], 9],
    [[1, 0, 1, 0, 1, 1, 1, 1, 0, 1, 0], 10],
    [[1, 0, 1, 0, 1, 1, 1, 1, 0, 1, 0], 9],
    [[1, 0, 1, 0, 1, 1, 1, 1, 0, 1, 0], 8],
    [[1, 0, 1, 0, 1, 1, 1, 1, 0, 1, 0], 7],
    [[1, 0, 1, 0, 1, 1, 1, 1, 0, 1, 0], 6],
    [[1, 0, 1, 0, 1, 1, 1, 1, 0, 1, 0], 5],
    [[1, 0, 1, 0, 1, 1, 1, 1, 0, 1, 0], 4],
    [[1, 0, 1, 0, 1, 1, 1, 1, 0, 1, 0], 3],
    [[1, 0, 1, 0, 1, 1, 1, 1, 0, 1, 0], 2],
    [[1, 0, 1, 0, 1, 1, 1, 1, 0, 1, 0], 1],
    [[1, 0, 1, 0, 1, 1, 1, 1, 0, 1, 0], 0],
    [[undefined, 1, 0, 1, 0, 1, 1, 1, 1, 0, 1, 0], 0],
    [[undefined, undefined, 1, 0, 1, 0, 1, 1, 1, 1, 0, 1, 0], 0],
    [[undefined, undefined, undefined, 1, 0, 1, 0, 1, 1, 1, 1, 0, 1, 0], 0],
    [[undefined, undefined, undefined, undefined, 1, 0, 1, 0, 1, 1, 1, 1, 0, 1, 0], 0],
    [[undefined, undefined, undefined, undefined, undefined, 1, 0, 1, 0, 1, 1, 1, 1, 0, 1, 0], 0],
    [[undefined, undefined, undefined, undefined, undefined, undefined, 1, 0, 1, 0, 1, 1, 1, 1, 0, 1, 0], 0],
]

const indexOnStepList = ref(0)
const tape = computed(() => steps[indexOnStepList.value][0]);
const headIndexOnTape = computed(() => steps[indexOnStepList.value][1]);
const displayedTapeLength = 11
const emptyTape = Array.from({ length: displayedTapeLength }, () => undefined)
const headIndexInDisplayedTape = 5
const displayedTape = computed(() => {
    const inferiorBoundToDisplay = headIndexOnTape.value - 5
    const normalizedInferiorBoundToDisplay = Math.max(0, inferiorBoundToDisplay)
    const superiorBoundToDisplay = headIndexOnTape.value + 6
    const normalizedSuperiorBoundToDisplay = Math.min(tape.value.length, superiorBoundToDisplay)
    const squaresRemaingOnRightOfTape = tape.value.length - headIndexOnTape.value
    
    return [
        ...(headIndexOnTape.value < 6 ? emptyTape.slice(0, 5 - headIndexOnTape.value) : []),
        ...tape.value.slice(normalizedInferiorBoundToDisplay, normalizedSuperiorBoundToDisplay),
        ...(squaresRemaingOnRightOfTape < 6 ? emptyTape.slice(0, 6 - squaresRemaingOnRightOfTape) : [])
    ]
})

function goToPreviousIteration() {
    if (indexOnStepList.value > 0) {
        indexOnStepList.value--
    }
}

function goToNextIteration() {
    if (indexOnStepList.value < steps.length - 1) {
        indexOnStepList.value++
    }
}
</script>

<template>
  <div>
    <p>Index on tape: {{ headIndexOnTape }}</p>
    <p>Step: {{ indexOnStepList }}</p>
  
    <div class="relative">
      <div class="inset-0">
        <div class="flex">
          <div
            v-for="(value, index) in displayedTape"
            :key="index"
            :class="[
                'flex items-center justify-center w-10 h-10 text-center border border-gray-100',
                {
                    'bg-yellow-100': index === headIndexInDisplayedTape
                }
            ]"
          >
            <p>{{ value }}</p>
          </div>
        </div>
      </div>
    </div>

    <div class="flex space-x-2">
        <button @click="goToPreviousIteration">Go left</button>
        <button @click="goToNextIteration">Go right</button></div>
    </div>
</template>
