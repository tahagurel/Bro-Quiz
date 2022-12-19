<template>
    <div class="container py-4">
    <header class="pb-1 mb-4 border-bottom d-flex justify-content-between align-items-end">
      <router-link to="/" class="text-dark text-decoration-none">
        <span class="fs-4 fw-bold">
          <i class="bi bi-star-fill text-warning"/>
          BroQuiz
        </span>
      </router-link>
    </header>
    <slot/>
  </div>
</template>