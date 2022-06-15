<script lang="ts" setup>
import { ref, computed } from "vue";

const steps: [tape: (number | undefined)[], indexOfHead: number][] = [
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
  [
    [
      undefined,
      undefined,
      undefined,
      undefined,
      1,
      0,
      1,
      0,
      1,
      1,
      1,
      1,
      0,
      1,
      0,
    ],
    0,
  ],
  [
    [
      undefined,
      undefined,
      undefined,
      undefined,
      undefined,
      1,
      0,
      1,
      0,
      1,
      1,
      1,
      1,
      0,
      1,
      0,
    ],
    0,
  ],
  [
    [
      undefined,
      undefined,
      undefined,
      undefined,
      undefined,
      undefined,
      1,
      0,
      1,
      0,
      1,
      1,
      1,
      1,
      0,
      1,
      0,
    ],
    0,
  ],
];

const keysReference = steps[steps.length - 1][0].map((_, index) => index);

type StepWithKey = [
  tape: { key: string; value: number | undefined }[],
  indexOfHead: number
];

const tapeListWithKey: StepWithKey[] = steps
  .reverse()
  .map(([tape, indexOfHead]) => {
    if (tape.length !== keysReference.length) {
      const tapeIsReducedOnLeft = indexOfHead === 0;

      if (tapeIsReducedOnLeft === true) {
        keysReference.shift();
      } else {
        keysReference.pop();
      }
    }

    return [
      tape.map((value, index) => ({
        key: `square:${keysReference[index]}`,
        value,
      })),
      indexOfHead,
    ] as StepWithKey;
  })
  .reverse();

const indexOnStepList = ref(0);

const tape = computed(() => tapeListWithKey[indexOnStepList.value][0]);
const headIndexOnTape = computed(
  () => tapeListWithKey[indexOnStepList.value][1]
);

const displayedTapeLength = 11;
const emptyTape = Array.from({ length: displayedTapeLength }, (_, index) => ({
  key: `blank:${index}`,
  value: undefined,
}));

const displayedTape = computed(() => {
  const inferiorBoundToDisplay = Math.max(0, headIndexOnTape.value - 5);
  const superiorBoundToDisplay = Math.min(
    tape.value.length,
    headIndexOnTape.value + 6
  );
  const squaresRemaingOnRightOfTape = tape.value.length - headIndexOnTape.value;

  return [
    ...(headIndexOnTape.value < 6
      ? emptyTape.slice(0, 5 - headIndexOnTape.value)
      : []),
    ...tape.value.slice(inferiorBoundToDisplay, superiorBoundToDisplay),
    ...(squaresRemaingOnRightOfTape < 6
      ? emptyTape.slice(5 + squaresRemaingOnRightOfTape)
      : []),
  ];
});

function goToPreviousIteration() {
  if (indexOnStepList.value > 0) {
    indexOnStepList.value--;
  }
}

function goToNextIteration() {
  if (indexOnStepList.value < steps.length - 1) {
    indexOnStepList.value++;
  }
}
</script>

<template>
  <div>
    <p>Index on tape: {{ headIndexOnTape }}</p>
    <p>Step: {{ indexOnStepList }}</p>

    <div>
      <div>
        <div class="relative inline-flex">
          <TransitionGroup
            enter-active-class="transition-all duration-200"
            move-class="transition-all duration-200"
            leave-active-class="absolute transition-all duration-200"
            enter-from-class="opacity-0"
            leave-from-class="opacity-50"
            leave-to-class="opacity-0"
          >
            <div
              v-for="{ key, value } in displayedTape"
              :key="key"
              class="flex items-center justify-center w-10 h-10 text-center border border-gray-100"
            >
              <p>{{ value }}</p>
            </div>
          </TransitionGroup>

          <div class="absolute inset-x-0 flex justify-center">
            <div class="w-10 h-10 bg-yellow-100 opacity-50" />
          </div>
        </div>
      </div>
    </div>

    <div class="flex space-x-2">
      <button @click="goToPreviousIteration">Go left</button>
      <button @click="goToNextIteration">Go right</button>
    </div>
  </div>
</template>
