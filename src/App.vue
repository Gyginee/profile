<template>
  <div class="container">
    <div class="header"><Header /></div>
    <div class="section-first">
      <div class="sfContainer">
        <SectionFirst />
      </div>
      <div class="model-container">
        <Model />
      </div>
    </div>
    <div class="section-second pb-40">
      <div class="sfContainer">
        <SectionSecond />
      </div>
    </div>
    <div class="section-experience pb-40">
      <div class="seContainer">
        <SectionExperience />
      </div>
    </div>
  </div>
</template>

<script setup>
import Header from './components/Header.vue';
import SectionFirst from './components/SectionFirst.vue';
import SectionSecond from './components/SectionSecond.vue';
import SectionExperience from './components/SectionExperience.vue';
import Model from './components/Model.vue';
</script>

<style>
@import './style.css';
</style>