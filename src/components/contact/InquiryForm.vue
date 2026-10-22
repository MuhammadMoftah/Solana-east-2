<script setup lang="ts">
import { computed, nextTick, reactive, ref } from 'vue';

interface Field {
  name: string;
  label: string;
  type: 'text' | 'tel' | 'select' | 'textarea';
  required: boolean;
}

const props = defineProps<{
  fields: Field[];
  unitTypes: string[];
  whatsappNumber: string;
  projectName: string;
  disclaimer: string;
}>();

const NO_PREFERENCE = 'No preference';
const values = reactive<Record<string, string>>(Object.fromEntries(props.fields.map((field) => [field.name, ''])));
const errors = reactive<Record<string, string>>({});
const touched = reactive<Record<string, boolean>>({});
const status = ref<'idle' | 'sent'>('idle');
const whatsappUrl = ref('');

const inputFields = computed(() => props.fields.filter((f) => f.type === 'text' || f.type === 'tel'));
const choiceFields = computed(() => props.fields.filter((f) => f.type === 'select'));
const textFields = computed(() => props.fields.filter((f) => f.type === 'textarea'));

const autocomplete: Record<string, string> = { name: 'name', phone: 'tel' };
const placeholders: Record<string, string> = {
  areaRange: 'e.g. 150–200 m²',
  budget: 'e.g. EGP 15–20M',
  message: 'Anything you would like the sales team to know',
};

function validate(name: string) {
  const field = props.fields.find((f) => f.name === name)!;
  const value = values[name].trim();
  let message = '';
  if (field.required && !value) message = `Please enter your ${field.label.toLowerCase()}.`;
  else if (name === 'phone' && value && (value.replace(/\D/g, '').length < 8 || /[^\d\s()+-]/.test(value)))
    message = 'Please enter a valid phone number, including the country code if outside Egypt.';
  else if (name === 'name' && value && value.length < 2) message = 'Please enter your full name.';
  errors[name] = message;
  return !message;
}

function onBlur(name: string) {
  touched[name] = true;
  validate(name);
}

function onInput(name: string) {
  if (touched[name]) validate(name);
}

const messageText = computed(() => {
  const lines = [`Hello, I'd like information about ${props.projectName}.`, ''];
  for (const field of props.fields) {
    const value = values[field.name].trim();
    if (value) lines.push(`${field.label}: ${value}`);
  }
  return lines.join('\n');
});

const formEl = ref<HTMLFormElement | null>(null);
const statusEl = ref<HTMLElement | null>(null);

async function onSubmit() {
  const results = props.fields.map((field) => {
    touched[field.name] = true;
    return validate(field.name);
  });
  if (results.includes(false)) {
    const firstInvalid = props.fields.find((field) => errors[field.name]);
    if (firstInvalid) formEl.value?.querySelector<HTMLElement>(`[name="${firstInvalid.name}"]`)?.focus();
    return;
  }
  whatsappUrl.value = `https://wa.me/${props.whatsappNumber}?text=${encodeURIComponent(messageText.value)}`;
  window.open(whatsappUrl.value, '_blank', 'noopener');
  status.value = 'sent';
  await nextTick();
  statusEl.value?.focus();
}

async function reset() {
  for (const field of props.fields) {
    values[field.name] = '';
    errors[field.name] = '';
    touched[field.name] = false;
  }
  status.value = 'idle';
  await nextTick();
  formEl.value?.querySelector<HTMLElement>('input, textarea')?.focus();
}

const inputClass =
  'block w-full border-0 border-b bg-transparent px-0 pt-3 pb-3 text-base text-champagne-50 placeholder:text-mist-400/70 transition-colors duration-300 focus:outline-none';
const labelClass = 'block text-[0.6875rem] font-semibold tracking-[0.2em] text-mist-300 uppercase';
</script>

<template>
  <div data-requires-js>
    <div
      v-if="status === 'sent'"
      ref="statusEl"
      tabindex="-1"
      class="rounded-[2rem] border border-champagne-300/25 p-8 outline-none sm:p-12"
      role="status"
    >
      <p class="eyebrow">Almost done</p>
      <p class="mt-5 font-display text-3xl text-champagne-300">Your message is ready in WhatsApp.</p>
      <p class="mt-4 text-mist-300">
        Press send in WhatsApp to deliver your enquiry. If it didn't open,
        <a :href="whatsappUrl" target="_blank" rel="noopener" class="text-link">open WhatsApp here</a>.
      </p>
      <button type="button" class="btn mt-8" @click="reset">Start a new enquiry</button>
    </div>

    <form v-else ref="formEl" novalidate @submit.prevent="onSubmit">
      <div class="grid gap-x-16 gap-y-12 lg:grid-cols-2">
        <div class="space-y-9">
          <div v-for="field in inputFields" :key="field.name">
            <label :for="`inquiry-${field.name}`" :class="labelClass">
              {{ field.label }}<span v-if="field.required" class="text-champagne-500" aria-hidden="true"> *</span>
              <span v-else class="font-normal tracking-normal text-mist-400 normal-case"> (optional)</span>
            </label>
            <input
              :id="`inquiry-${field.name}`"
              v-model="values[field.name]"
              :name="field.name"
              :type="field.type"
              :required="field.required"
              :autocomplete="autocomplete[field.name] ?? 'off'"
              :inputmode="field.type === 'tel' ? 'tel' : undefined"
              :placeholder="placeholders[field.name]"
              :aria-invalid="errors[field.name] ? 'true' : undefined"
              :aria-describedby="errors[field.name] ? `inquiry-${field.name}-error` : undefined"
              :class="[inputClass, errors[field.name] ? 'border-error' : 'border-champagne-300/30 focus:border-champagne-300']"
              @blur="onBlur(field.name)"
              @input="onInput(field.name)"
            />
            <p v-if="errors[field.name]" :id="`inquiry-${field.name}-error`" class="mt-2 text-sm text-error">
              {{ errors[field.name] }}
            </p>
          </div>
        </div>

        <div class="space-y-10">
          <fieldset v-for="field in choiceFields" :key="field.name">
            <legend :class="labelClass">
              {{ field.label }}<span class="font-normal tracking-normal text-mist-400 normal-case"> (optional)</span>
            </legend>
            <div class="mt-5 flex flex-wrap gap-2.5">
              <label
                v-for="option in [NO_PREFERENCE, ...unitTypes]"
                :key="option"
                class="pill relative inline-flex min-h-11 cursor-pointer items-center rounded-full border px-5 text-sm transition-colors duration-300"
                :class="
                  (values[field.name] || NO_PREFERENCE) === option
                    ? 'border-champagne-300 bg-champagne-300 text-night-900'
                    : 'border-champagne-300/25 text-mist-200 hover:border-champagne-300/60 hover:text-champagne-100'
                "
              >
                <input
                  type="radio"
                  class="peer sr-only"
                  :name="field.name"
                  :value="option === NO_PREFERENCE ? '' : option"
                  :checked="(values[field.name] || NO_PREFERENCE) === option"
                  @change="values[field.name] = option === NO_PREFERENCE ? '' : option"
                />
                {{ option }}
              </label>
            </div>
          </fieldset>

          <div v-for="field in textFields" :key="field.name">
            <label :for="`inquiry-${field.name}`" :class="labelClass">
              {{ field.label }}<span class="font-normal tracking-normal text-mist-400 normal-case"> (optional)</span>
            </label>
            <textarea
              :id="`inquiry-${field.name}`"
              v-model="values[field.name]"
              :name="field.name"
              rows="4"
              :placeholder="placeholders[field.name]"
              :class="[inputClass, 'resize-y border-champagne-300/30 focus:border-champagne-300']"
              @blur="onBlur(field.name)"
            />
          </div>
        </div>
      </div>

      <div class="hairline mt-14 flex flex-col gap-6 border-t pt-8 md:flex-row md:items-center md:justify-between">
        <div class="max-w-xl space-y-2">
          <p class="text-sm text-mist-300">Opens WhatsApp with your details pre-filled — nothing is sent until you press send.</p>
          <p class="source-note">{{ disclaimer }}</p>
        </div>
        <button type="submit" class="btn shrink-0">
          Send via WhatsApp
          <svg class="btn-arrow" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.3" aria-hidden="true">
            <path d="M4 12h15M13 6l6 6-6 6" />
          </svg>
        </button>
      </div>
    </form>
  </div>
</template>

<style scoped>
.pill:has(input:focus-visible) {
  outline: 1.5px solid var(--color-champagne-300);
  outline-offset: 3px;
}
</style>
