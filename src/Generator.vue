<script setup lang="ts">
import * as petname from "@quacumque/petname";
import { assert } from "@std/assert";
import { useClipboard, useLocalStorage } from "@vueuse/core";
import { reactive, shallowRef, watch } from "vue";

const opts: petname.GenerateOpts = reactive({
  letters: useLocalStorage("opts-letters", 6),
  words: useLocalStorage("opts-words", 2),
  separator: useLocalStorage("opts-sep", "-"),
});

const GENERATE_COUNT = 7;

function generate() {
  return Array.from({ length: GENERATE_COUNT }, () => petname.generate(opts));
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
  <div class="inputs">
    <label for="words">Words: {{ opts.words }}</label>
    <input id="words" type="range" v-model.number="opts.words" min="1" max="9">

    <label for="letters">Maximum Letters: {{ opts.letters }}</label>
    <input id="letters" type="range" v-model.number="opts.letters" min="3" max="15">

    <label for="separator">Separator:</label>
    <select id="separator" v-model="opts.separator">
      <option value="-">Hyphen (-)</option>
      <option value="_">Underscore (_)</option>
      <option value=" ">Space</option>
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
</template>

<style scoped>
.inputs {
  display: flex;
  flex-direction: column;
  align-items: start;
}

label[for="separator"] {
  margin-bottom: 0.25rem;
}

.inputs input + label {
  margin-top: 0.5rem;
}

ul.results {
  padding-inline-start: 0;
}

ul.results li {
  font-size: 0.8em;
  line-height: 1.3em;
  font-family: monospace;
  max-width: max-content;
  user-select: all;
}

.actions {
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.actions span {
  font-size: 0.8em;
  line-height: 1em;
}
</style>
