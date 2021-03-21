<template>
    <Teleport to="body">
  <div class="h-screen w-full fixed left-0 top-0 flex justify-center items-center bg-black bg-opacity-50">
    <!-- modal -->
    <div class="bg-white rounded shadow-lg">
      <!-- modal header -->
      <div class="border-b px-4 py-2 flex justify-between items-center">
        <h3 class="font-semibold text-lg">{{ title }}</h3>
        <button @click="$emit('update:active', false)" class="text-black close-modal">&cross;</button>
      </div>
      <!-- modal body -->
      <slot class="p-6">
      </slot>
    </div>
  </div>
    </Teleport>
</template>
<script>
export default {
    props: ['title', 'active'],
    emits: ['update:active'],
}
</script>