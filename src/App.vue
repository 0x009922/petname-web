<script setup lang="ts">
import Generator from "./Generator.vue";
</script>

<template>
  <main>
    <h1>Petname Generator</h1>

    <p class="subtitle">Generate human-readable, random names for objects (e.g. hostnames, containers, blobs)</p>

    <Generator />

    <h2>
      About
    </h2>

    <p>
      This is a simple Web petname generator.
    </p>

    <blockquote>
      <p>
        <b>petname</b> − an <a href="https://tools.ietf.org/html/rfc1178" target="_blank">RFC1178</a> implementation to
        generate pronounceable, sometimes even memorable, "pet names", consisting of a random combination of adverbs, an
        adjective, and an animal name
      </p>

      <footer>
        <a href="https://github.com/dustinkirkland/python-petname" target="_blank">
          python-petname
        </a>
      </footer>
    </blockquote>

    <p>
      Links:
    </p>

    <ul>
      <li><a href="https://github.com/0x009922/petname-web" target="_blank">Source code</a></li>
      <li><a href="https://jsr.io/@quacumque/petname" target="_blank">JSR Package</a></li>
      <li><a href="https://quacumque.net" target="_blank">quacumque.net</a> (root)</li>
    </ul>
  </main>
</template>
