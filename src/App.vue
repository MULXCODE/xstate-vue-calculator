<template>
  <div id="app">
    <div class="container">
      <input class="readout" :value="state.context.display" disabled>
      <div class="button-grid">
        <button
          v-for="button in buttons"
          :key="button"
          @click="handleButtonClick(button)"
          class="calc-button"
          :class="{'two-span': button === 'C'}"
          :title="buttonDescription(button)"
        >{{ button }}</button>
      </div>
      <div class="state">
        <p>State</p>
        <pre><code>{{ state.value }}</code></pre>
        <p>Context:</p>
        <pre><code>{{ state.context }}</code></pre>
      </div>
    </div>
  </div>
</template>

<script>
import calcMachine from "./calculatorStategraph";
import { useMachine } from "@xstate/vue";

const buttons = [
  "C",
  "CE",
  "/",
  "7",
  "8",
  "9",
  "x",
  "4",
  "5",
  "6",
  "-",
  "1",
  "2",
  "3",
  "+",
  "0",
  ".",
  "=",
  "%"
];

function isOperator(text) {
  return "+-x/".indexOf(text) > -1;
}

export default {
  name: "Calculator",
  setup() {
    const { state, send: machineSend } = useMachine(calcMachine, {});

    function send(event, payload) {
      console.log(event, payload);
      machineSend(event, payload);
    }

    function handleButtonClick(item) {
      console.log("handleButtonClick", item);
      if (Number.isInteger(+item)) {
        send("NUMBER", { key: +item });
      } else if (isOperator(item)) {
        send("OPERATOR", { operator: item });
      } else if (item === "C") {
        send("CLEAR_EVERYTHING");
      } else if (item === ".") {
        send("DECIMAL_POINT");
      } else if (item === "%") {
        send("PERCENTAGE");
      } else if (item === "CE") {
        send("CLEAR_ENTRY");
      } else {
        send("EQUALS");
      }
    }

    function buttonDescription(button) {
      if (Number.isInteger(+button)) return `NUMBER ${button}`;
      if (isOperator(button)) return `OPERATOR ${button}`;
      if (button === "C") return "CLEAR_EVERYTHING";
      if (button === "CE") return "CLEAR_ENTRY";
      if (button === ".") return "DECIMAL_POINT";
      if (button === "%") return "PERCENTAGE";
      if (button === "=") return "EQUALS";
    }

    return {
      state,
      send,
      buttons,
      handleButtonClick,
      buttonDescription
    };
  }
};
</script>

<style>
body {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
.container {
  max-width: 300px;
  margin: 0 auto;
  border: 2px solid gray;
  border-radius: 4px;
  box-sizing: border-box;
}

.readout {
  font-size: 32px;
  color: #333;
  text-align: right;
  padding: 5px 13px;
  width: 100%;
  border: none;
  border-bottom: 1px solid gray;
  box-sizing: border-box;
}

.button-grid {
  display: grid;
  padding: 20px;
  grid-template-columns: repeat(4, 1fr);
  grid-gap: 15px;
}

.state {
  padding: 20px;
  padding-top: 0;
}

.calc-button {
  padding: 10px;
  font-size: 22px;
  color: #eee;
  background: rgba(0, 0, 0, 0.5);
  cursor: pointer;
  border-radius: 2px;
  border: 0;
  outline: none;
  opacity: 0.8;
  transition: opacity 0.2s ease-in-out;
}

.calc-button:hover {
  opacity: 1;
}

.calc-button:active {
  background: #999;
  box-shadow: inset 0 1px 4px rgba(0, 0, 0, 0.6);
}

.two-span {
  grid-column: span 2;
  background-color: #3572db;
}

p,
pre,
code {
  text-align: left;
  margin: 0;
  padding: 0;
}

p {
  margin-top: 1em;
}
</style>
