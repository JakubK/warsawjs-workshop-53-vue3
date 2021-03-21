<template>
  <div class="max-w-screen-md mx-auto p-2">
    <template v-if="!meme.url">
      <div class="flex">
        <div class="px-1 flex-1">
          <form-select
              class="my-2"
              v-model="meme.backgroundUrl"
              label="Background"
              :choices="availableBackgrounds.map(bg => ({value: bg.url, name: bg.name}))"
          />
          <form-input v-model="meme.topCaption" class="my-2" label="Top caption" placeholder="Funny top caption...." />
          <form-input v-model="meme.bottomCaption" class="my-2" label="Bottom caption" placeholder="Funny bottom caption...." />
          <div class="mx-auto py-2 flex justify-center">
            <button
                class="p-2 m-2 bg-blue-500 text-white font-bold rounded"
                :class="{'bg-gray-300': !isValid, 'cursor-not-allowed': !isValid}"
                @click="generate"
                :disabled="!isValid">
              Generate
            </button>
            <button
                class="p-2 m-2 bg-red-300 rounded"
                @click="$emit('cancel')">
              Cancel
            </button>
          </div>
        </div>
        <div class="px-1 flex-1 flex justify-center items-center">
          <img v-if="meme.backgroundUrl" :src="meme.backgroundUrl"/>
        </div>
      </div>
    </template>
    <template v-else>
      <img class="mx-auto rounded-xl" :src="meme.url"/>
      <div class="flex justify-center">
        <button
            class="p-2 m-2 bg-blue-500 text-white font-bold rounded"
            @click="$emit('update:modelValue', meme)">
          Save
        </button>
        <button
            class="p-2 m-2 bg-red-300 rounded"
            @click="$emit('cancel')">
          Cancel
        </button>
      </div>
    </template>
  </div>
</template>


<script>
import FormInput from '@/components/FormInput.vue';
import FormSelect from '@/components/FormSelect.vue';
import { ref, computed } from 'vue';
export default {
  name: 'Meme',
  components: {FormSelect, FormInput},
  props: ['modelValue'],
  emits: ['update:modelValue', 'cancel'],
  setup(props) {
      const availableBackgrounds = ref([]);
      const meme = ref(props.modelValue ? {...props.modelValue} : {})
      meme.value.url = null;

      const selectedBackground = computed(() => {
          return availableBackgrounds.value.find(b => b.url === meme.value.backgroundUrl)
      });

      const isValid = computed(() => {
          return meme.value.topCaption && meme.value.bottomCaption && selectedBackground.value
      });

      const fetchBackgrounds = async() => {
        const response = await fetch('https://api.imgflip.com/get_memes')
        const data = await response.json()
        availableBackgrounds.value = data.data.memes
      }

      fetchBackgrounds();

      const generate = async() => {
        const formData = new FormData();
        formData.append('template_id',  selectedBackground.value.id)
        formData.append('text0', meme.value.topCaption)
        formData.append('text1', meme.value.bottomCaption)
        formData.append('username', import.meta.env.VITE_API_USERNAME)
        formData.append('password', import.meta.env.VITE_API_PASSWORD)
        const response = await fetch('https://api.imgflip.com/caption_image', {
            method: 'POST',
            body: formData
        })
        const data = await response.json()
        meme.value.url = data.data.url
      }

      return {
          availableBackgrounds,
          meme,

          isValid,
          selectedBackground,
          generate
      }
  },
}
</script>