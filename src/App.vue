<script setup lang="ts">
import { ref } from "vue";

type Button = {
  type: string;
  value: string;
  id: string;
};

const buttons: Button[] = [
  { type: "action", value: "AC", id: "clear" },
  { type: "action", value: "&lArr;", id: "back" },
  { type: "operation", value: "%", id: "percent" },
  { type: "operation", value: "/", id: "divide" },
  { type: "number", value: "7", id: "seven" },
  { type: "number", value: "8", id: "eight" },
  { type: "number", value: "9", id: "nine" },
  { type: "operation", value: "*", id: "multiply" },
  { type: "number", value: "4", id: "four" },
  { type: "number", value: "5", id: "five" },
  { type: "number", value: "6", id: "six" },
  { type: "operation", value: "-", id: "subtract" },
  { type: "number", value: "1", id: "one" },
  { type: "number", value: "2", id: "two" },
  { type: "number", value: "3", id: "three" },
  { type: "operation", value: "+", id: "add" },
  { type: "number", value: "0", id: "zero" },
  { type: "decimal", value: ".", id: "decimal" },
  { type: "action", value: "=", id: "equals" },
];

const formula = ref("");
const output = ref("");
const evaluated = ref(false);
const currentOperand = ref("");

function normalizeFormula(formula: string): string {
  if (!formula.includes("%")) {
    return eval(formula);
  }

  const regex = /[\d\+\-\/\*\.]*%/g;
  const matches = formula.match(regex);
  // get percentage 20+40-30% => -30
  const percentage = matches![0].slice(0, -1);
  const percentageNumber = percentage.match(/[\d\.]+$/);

  // get operands before the % operand 20+40-30% => 20+40
  const operand = matches![0].replace(/[\/\*\-\+\.]*[\d]+%$/, "") || "1";

  // get operator 20+40-30% => -
  const operatorsArr = matches![0].match(/[\/\*\-\+]+/g);
  const [operator, isNegative] = operatorsArr
    ? operatorsArr[operatorsArr.length - 1]
    : "*";

  const percentageValue =
    (eval("/*".includes(operator) ? "1" : operand) / 100) *
    Number(percentageNumber![0]);

  const newFormula = formula.replace(
    matches![0],
    eval(operand + operator + `${isNegative ?? ""}${percentageValue}`),
  );
  return normalizeFormula(newFormula);
}

const handleClick = (button: Button) => {
  if (evaluated.value) {
    formula.value = output.value;
    currentOperand.value = "";
  }

  evaluated.value = false;

  // clear
  if (button.id === "clear") {
    formula.value = "";
    output.value = "";
    currentOperand.value = "";
  }

  // back
  if (button.id === "back") {
    formula.value = formula.value.slice(0, -1);
    currentOperand.value = currentOperand.value.slice(0, -1);
  }

  // evaluation
  if (button.id === "equals") {
    evaluated.value = true;

    // count % value

    const normalizedFormula = normalizeFormula(formula.value);
    output.value = `${
      Math.round(eval(normalizedFormula) * 10000) / 10000 || 0
    }`;
    currentOperand.value = "";
  }

  // input number / decimal
  if (button.type === "number" || button.type === "decimal") {
    const inputVal =
      !currentOperand.value.length && button.value === "."
        ? "0."
        : button.value;

    // check multiple 0
    if (formula.value === "0" && inputVal === "0") {
      return;
    }

    // check multiple .
    if (currentOperand.value.includes(".") && button.value === ".") {
      return;
    }

    currentOperand.value = `${currentOperand.value}${inputVal}`;
    formula.value = `${formula.value}${inputVal}`;
    output.value = `${currentOperand.value}`;
  }

  if (button.type === "operation") {
    currentOperand.value = "";

    if (!formula.value.length && button.value !== "-") {
      return;
    }

    let newFormula = `${formula.value}${button.value}`;
    if (
      (button.value === "-" &&
        "/*%".includes(formula.value[formula.value.length - 1])) ||
      (button.value === "+" &&
        "%".includes(formula.value[formula.value.length - 1]))
    ) {
      newFormula = `${formula.value}${button.value}`;
    } else {
      newFormula = newFormula.replace(/[\/\*\-\+\%]{2,3}$/, button.value);
    }

    formula.value = newFormula;
  }
};
</script>

<template>
  <div
    id="calculator"
    class="w-72 shrink-0 rounded-lg border-2 bg-black text-lg text-white"
  >
    <div
      id="screen"
      class="flex min-h-[126px] flex-col items-end justify-end rounded-lg bg-gray-500 p-2"
    >
      <div
        id="formula"
        :class="[!evaluated ? 'text-2xl text-white' : 'text-sm text-gray-300']"
      >
        {{ formula || "0" }}
      </div>
      <div
        id="display"
        :class="[evaluated ? 'text-2xl text-white' : 'text-sm text-gray-300']"
      >
        {{ output || 0 }}
      </div>
    </div>
    <div class="buttons grid grid-cols-4 gap-4 p-2">
      <button
        v-for="b in buttons"
        :key="b.value"
        :id="b.id"
        class="h-12 w-12 rounded-full"
        @click="handleClick(b)"
      >
        <p
          class="h-12 w-12 rounded-full bg-slate-300 bg-opacity-0 leading-[3rem] transition-colors duration-500 hover:bg-opacity-20"
          v-html="b.value"
        />
      </button>
    </div>
  </div>
</template>

<style scoped>
#formula {
  @apply break-all text-right;
}

#zero {
  @apply col-start-2;
}

#divide,
#multiply,
#subtract,
#add {
  @apply bg-gray-800;
}

#equals {
  @apply bg-red-500;
}
</style>
