<script setup>
import { Copy } from 'lucide-vue-next'
import { ref, computed } from 'vue'

const props = defineProps({
  height: {
    type: Number,
    default: 5,
  },
  width: {
    type: Number,
    default: 15,
  },
})

const copyMessage = ref('Copy')
const objective = ref('')
const context = ref('')
const output = ref('')

const copyToClipboard = (text) => {
  navigator.clipboard.writeText(text).then(() => {
    copyMessage.value = 'Copied!'
    setTimeout(() => {
      copyMessage.value = 'Copy'
    }, 2000)
  })
}

const outputType = [
  'Step-by-step explanation',
  'Code snippet',
  'Comparison table',
  'SWOT analysis',
  'Business plan outline',
  'System architecture diagram (description)',
  'List of ideas',
  'Critique and suggestions',
  'Research summary',
  'Marketing strategy',
  'Algorithm breakdown',
  'Technical explanation (detailed)',
]

const finalPrompt = ref('')
const generatedPrompt = ref('')
const loading = ref(false)

const generatePrompt = async () => {
  if (objective.value && output.value && context.value) {
    finalPrompt.value = `Act as a prompt engineer.
Design a prompt that will achieve this goal
${objective.value.trim()}

I want the answer in this format or structure:
${output.value}

Relevant context:
${context.value.trim()}

`

    loading.value = true
    try {
      const res = await fetch('https://promptrefiner-back.onrender.com', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ prompt: finalPrompt.value }),
      })
      const data = await res.json()
      generatedPrompt.value = data.generatedPrompt
    } catch (error) {
      generatedPrompt.value = 'Error while generating prompt. Please try again.'
      console.error(error)
    } finally {
      loading.value = false
    }
  } else {
    finalPrompt.value = ''
    generatedPrompt.value = ''
  }
}
</script>

<template>
  <div class="px-4 w-[50%]">
    <form class="flex flex-col gap-4" @submit.prevent>
      <label for="objective">Prompt objectives</label>
      <textarea v-model="objective" name="objective" placeholder="What do you want to achieve?" id="objective"
        class="resize-none border-1 border-gray-300 rounded-md focus:border-purple-500 focus:ring-purple-500 p-2 focus:border-2 outline-none placeholder:text-gray-400"
        :cols="props.width" :rows="props.height"></textarea>

      <label for="output">Type of output</label>
      <select v-model="output" name="output-type" id="output"
        class="border-1 border-gray-300 focus-within:border-purple-500 p-2 rounded-md focus:border-2 focus:ring-purple-500 outline-none">
        <option value="" disabled class="text-gray-400">Select an output type</option>
        <option v-for="i in outputType" :value="i" :key="i">{{ i }}</option>
      </select>

      <label for="context">Context</label>
      <textarea v-model="context" name="answer context" id="context"
        placeholder="Provide any context for a better response."
        class="resize-none border-1 border-gray-300 rounded-md focus:border-purple-500 focus:border-2 focus:ring-purple-500 p-2 outline-none placeholder:text-gray-400"
        :cols="props.width" :rows="props.height"></textarea>
    </form>

    <div class="mt-6">
      <label class="font-semibold text-sm">Generated Prompt</label>
      <div class="group bg-gray-100 p-3 rounded-md flex items-center justify-between">
        <pre class="w-full h-full whitespace-pre-wrap break-words text-sm">{{ generatedPrompt }}</pre>
        <span @click="copyToClipboard(generatedPrompt)"
          class="opacity-0 cursor-pointer border-1 border-gray-950 p-1 rounded-md flex items-center flex-col group-hover:opacity-100 self-start">
          <Copy /> {{ copyMessage }}
        </span>
      </div>
    </div>
    <button class="outline-none w-full py-2 my-3 rounded-md text-white font-bold transition-colors"
      @click="generatePrompt" :disabled="!objective || !output || !context || loading" :class="[
        !objective || !output || !context || loading
          ? 'bg-purple-500 cursor-not-allowed opacity-50'
          : 'bg-purple-700 cursor-pointer hover:bg-purple-800',
      ]">
      {{ loading ? 'Generating...' : 'Generate' }}
    </button>
  </div>
</template>
