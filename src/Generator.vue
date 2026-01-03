<script setup lang="ts">
import * as petname from "@quacumque/petname";
import { assert } from "@std/assert";
import { useClipboard, useLocalStorage } from "@vueuse/core";
import * as changeCase from "change-case";
import { reactive, shallowRef, watch } from "vue";

type Style = keyof typeof changeCase & `${string}Case`;

const STYLES = [
  "camelCase",
  "capitalCase",
  "constantCase",
  "dotCase",
  "kebabCase",
  "noCase",
  "pascalCase",
  "pascalSnakeCase",
  "pathCase",
  "sentenceCase",
  "snakeCase",
  "trainCase",
] satisfies Style[];

const somePet = petname.generate();
const STYLE_OPTIONS = STYLES.map((style) => ({
  value: style,
  label: `${changeCase.capitalCase(style)} (${changeCase[style](somePet)})`,
}));

const opts = reactive({
  letters: useLocalStorage("opts-letters", 6),
  words: useLocalStorage("opts-words", 2),
  style: useLocalStorage<Style>("opts-style", "kebabCase"),
});

const GENERATE_COUNT = 7;

function generate(): string[] {
  return Array.from({ length: GENERATE_COUNT }, () => {
    const { style, ...rest } = opts;
    const name = petname.generate(rest);
    const cased = changeCase[style](name);
    return cased;
  });
}

const generated = shallowRef<string[]>(generate());

function regen() {
  generated.value = generate();
}

watch(opts, () => regen());

const clip = useClipboard();

async function copyOne() {
  const one = generated.value[~~(Math.random() * generated.value.length)];
  assert(one, "should be at least one");
  await clip.copy(one);
}
</script>

<template>
  <div class="generator">
    <div class="inputs">
      <label for="words">Words: {{ opts.words }}</label>
      <input id="words" type="range" v-model.number="opts.words" min="1" max="9">

      <label for="letters">Maximum Letters: {{ opts.letters }}</label>
      <input id="letters" type="range" v-model.number="opts.letters" min="3" max="15">

      <label for="style">Style:</label>
      <select id="style" v-model="opts.style">
        <option v-for="({ value, label }) in STYLE_OPTIONS" :key="value" :value>
          {{ label }}
        </option>
      </select>
    </div>

    <ul class="results">
      <li v-for="x in generated">
        {{ x }}
      </li>
    </ul>

    <p class="actions">
      <button @click="regen()">Regenerate</button>
      <button v-if="clip.isSupported.value" @click="copyOne()">Copy one</button>
      <span v-if="clip.copied.value">Copied!</span>
    </p>
  </div>
</template>

<style scoped>
.generator {
  background: white;
  padding: 1rem;
  box-sizing: content-box;
  max-width: 480px;
  margin: 2rem 0;
  border-radius: 2px;
  box-shadow:
    rgba(0, 0, 0, 0.05) 0px 2px 4px 0px inset,
    rgba(0, 0, 0, 0) 0px 0px 0px 0px,
    rgba(0, 0, 0, 0) 0px 0px 0px 0px,
    oklab(0 0 0 / 0.05) 0px 0px 0px 1px,
    rgba(0, 0, 0, 0) 0px 0px 0px 0px;
}

.inputs {
  display: flex;
  flex-direction: column;
  align-items: start;
}

label[for="style"] {
  margin-bottom: 0.5rem;
}

.inputs input + label {
  margin-top: 0.5rem;
}

ul.results {
  padding-inline-start: 1.2rem;
}

ul.results li {
  font-size: 0.8em;
  line-height: 1.3em;
  font-family: monospace;
  max-width: max-content;
  user-select: all;
  word-break: break-all;
}

.actions {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  margin: 0;
}

.actions span {
  font-size: 0.8em;
  line-height: 1em;
}
</style>
