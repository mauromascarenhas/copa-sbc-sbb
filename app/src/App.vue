<script setup lang="ts">
import EnrollmentForm from "./components/EnrollmentForm.vue";
import OutOfPeriodMessage from "./components/OutOfPeriodMessage.vue";

import { ref } from "vue";

const acceptsEnrollment = ref(
    (new Date()).getTime() <
     (new Date("2026-09-20T08:00:00.000Z")).getTime()
);
</script>

<template>
  <header>
    <nav class="navbar bg-primary">
      <div class="container-fluid">
        <a class="navbar-brand text-bg-primary mx-auto d-flex flex-row align-items-center" href="#">
          <img src="@/assets/logo_sbb.png" alt="Logotipo SBB"
            class="logo d-inline-block align-text-top me-3"
          >
          <div class="text-wrap">
            Copa São Bernardo de Badminton 2026
          </div>
        </a>
      </div>
    </nav>
  </header>

  <main>
    <EnrollmentForm v-if="acceptsEnrollment" />
    <OutOfPeriodMessage v-else />
  </main>

  <footer class="text-bg-primary text-center py-3">
    <div><b>SBB Clube</b></div>
    <div>Avenida Presidente Arthur Bernardes, 55, Rudge Ramos - São Bernardo do Campo - SP.</div>
  </footer>
</template>

<style lang="scss">
$primary: #263089;

@import "bootstrap";
@import "bootstrap-icons/font/bootstrap-icons.css";

.logo {
  width: 2rem;
  height: 2rem;
}

main {
  min-height: calc(100vh - 8.7rem);
}
</style>
