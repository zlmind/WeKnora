<template>
  <a
    :href="url"
    target="_blank"
    rel="noopener noreferrer"
    class="official-link"
  >
    <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 12a9 9 0 01-9 9m9-9a9 9 0 00-9-9m9 9H3m9 9a9 9 0 01-9-9m9 9c1.657 0 3-4.03 3-9s-1.343-9-3-9m0 18c-1.657 0-3-4.03-3-9s1.343-9 3-9m-9 9a9 9 0 019-9" />
    </svg>
    {{ text }}
  </a>
</template>

<script setup lang="ts">
interface Props {
  url: string
  text: string
}

defineProps<Props>()
</script>

<style scoped lang="css">
.official-link {
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.5rem 1rem;
  background: rgba(255, 255, 255, 0.9);
  backdrop-filter: blur(4px);
  border: 1px solid rgb(229, 231, 235);
  border-radius: 0.5rem;
  font-size: 0.875rem;
  font-weight: 500;
  color: rgb(55, 65, 81);
  text-decoration: none;
  box-shadow: 0 1px 2px 0 rgba(0, 0, 0, 0.05);
  transition: all 0.2s;
}

.official-link:hover {
  background: white;
  border-color: rgb(147, 197, 253);
  color: rgb(37, 99, 235);
}

.official-link:focus {
  outline: 2px solid rgb(37, 99, 235);
  outline-offset: 2px;
}
</style>