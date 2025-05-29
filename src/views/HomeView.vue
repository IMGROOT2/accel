<template>
  <main
    :class="[
      'flex flex-col items-center p-6 bg-gradient-to-tr from-purple-300 to-purple-600 min-h-screen',
      showInput ? 'justify-center' : 'space-y-6'
    ]"
  >
    <transition name="fade">
      <div
        v-if="showInput"
        class="w-full max-w-3xl *:flex flex-col items-center bg-slate-50 p-8 rounded-lg shadow-lg space-y-6"
        style="border: 2px solid #e5e7eb;"
      >

        <p class="text-center mx-auto justify-center text-xs inline text-purple-500 font-bold relative top-12">
          <a
            href="https://ruhangupta.com/"
            class="underline"
          >BY RUHAN GUPTA</a>
        </p>
        <img
          :src="AccelLogo"
          alt="Accel Logo"
          class="mx-auto -mb-2 w-64"
        >
        <h1 class="text-xl text-purple-500 mb-4 text-center justify-center">
          Your first step for AI optimization.
        </h1>
        <textarea
          v-model="operations"
          placeholder="Conduct short interviews with your team members. With the Speech-to-Text API from Google Cloud, ask them to describe the day-to-day operations of your business. We'll analyze your response and recommend AI optimizations."
          class="w-full h-56 p-4 border-2 border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-purple-500 resize-none"
        />
        <div class="w-full flex justify-center">
          <button
            class="p-6 py-5 bg-purple-500 rounded-full border-2 border-purple-300 hover:bg-purple-600 transition-colors flex items-center justify-center micbackground"
            @click="toggleSTT();"
          >
            <i
              class="fa-solid fa-microphone text-xl text-white micicon"
            />
          </button>
        </div>

        <div class="flex justify-center mt-4">
          <button
            :disabled="loading"
            class="px-6 py-2 font-semibold rounded-lg shadow bg-purple-500 text-white hover:bg-purple-600 disabled:opacity-50 flex items-center"
            @click="analyzeOperations"
          >
            <span
              v-if="loading && phase==='parse'"
              class="loader mr-2"
            />
            Analyze Operations
          </button>
        </div>
      </div>
    </transition>

    <transition name="fade">
      <div
        v-if="!showInput"
        class="w-full max-w-3xl bg-gray-50 p-8 rounded-lg shadow-lg space-y-6"
      >
        <div class="flex justify-between items-center mb-4">
          <div class="space-x-2">
            <span class="text-xs inline text-purple-500 font-bold relative top-2 ml-1"><a
              href="https://ruhangupta.com/"
              class="underline"
            >BY RUHAN GUPTA</a></span>
            <img
              :src="AccelLogo"
              alt="Accel Logo"
              class="w-32"
            >
            <span class="text-purple-500 font-bold text-lg">
              Recommendations & Insights
            </span>
          </div>
          <button
            class="text-white bg-purple-500 hover:bg-purple-600 px-4 py-2 rounded-lg shadow transition-all duration-200 flex items-center"
            @click="reset"
          >
            <i class="fas fa-undo mr-1" />
            Go Back
          </button>
        </div>

        <section class="mb-6">
          <div class="flex items-center justify-between mb-2 w-full">
            <h2 class="text-2xl font-semibold flex items-center">
              <button
                class="mr-2 focus:outline-none"
                @click="showSteps = !showSteps"
              >
                <i
                  class="fa-solid fa-chevron-down transform transition-transform duration-200"
                />
              </button>
              Key AI-Optimizable Steps
            </h2>
            <span class="bg-[#323232] text-white px-3 py-2 rounded-lg text-sm font-semibold">
              <img
                :src="Gemini"
                alt="Gemini Logo"
                class="inline-block w-18"
              >
            </span>
          </div>
          <div
            v-show="showSteps"
            v-html="stepsHtml"
          />
        </section>


        <section class="mb-6">
          <div class="flex items-center justify-between mb-2 w-full">
            <h2 class="text-2xl font-semibold flex items-center">
              <button
                class="mr-2 focus:outline-none"
                @click="showExamples = !showExamples"
              >
                <i
                  class="fa-solid fa-chevron-down transform transition-transform duration-200"
                />
              </button>
              How are other businesses using AI?
            </h2>
            <span class="bg-[#00684A] text-white px-3 py-2 rounded-lg text-sm font-semibold">
              <img
                :src="MongoDBLogo"
                alt="MongoDB Logo"
                class="inline-block w-24"
              >
            </span>
          </div>
          <div
            v-show="showExamples"
            v-html="examplesSummaryHtml"
          />
        </section>


        <div
          v-if="!automationAdviceHtml"
          class="flex justify-center mt-4"
        >
          <button
            :disabled="loading"
            class="px-6 py-2 font-semibold rounded-lg shadow bg-purple-500 text-white hover:bg-purple-600 disabled:opacity-50 flex items-center"
            @click="automateSteps"
          >
            <span
              v-if="loading && phase==='automate'"
              class="loader mr-2"
            />
            Recommend AI Workflows
          </button>
        </div>

        <section
          v-if="automationAdviceHtml"
          class="mt-6 space-y-4"
        >
          <h2 class="text-2xl font-semibold flex items-center mb-2">
            <button
              class="mr-2 focus:outline-none"
              @click="showAutomation = !showAutomation"
            >
              <i
                class="fa-solid fa-chevron-down transform transition-transform duration-200"
              />
            </button>
            AI Workflow Suggestions
          </h2>
          <div
            v-show="showAutomation"
            v-html="automationAdviceHtml"
          />
        </section>
      </div>
    </transition>
  </main>
</template>

<script setup>
import { ref, onUnmounted } from 'vue';
import axios from 'axios';
import { io } from 'socket.io-client';
import AccelLogo from '../assets/accel.svg';
import MongoDBLogo from '../assets/mongodb.svg';
import Gemini from '../assets/gemini.png';

const socket = io('https://accel-api-985126394507.us-central1.run.app');

const isRecording = ref(false);
let mediaRecorder;
let micStream;


let finalTranscript = '';


const operations = ref('');
const stepsHtml = ref('');
const examplesSummaryHtml = ref('');
const automationAdviceHtml = ref('');
const loading = ref(false);
const phase = ref('');
const showInput = ref(true);


const showSteps = ref(true);
const showExamples = ref(true);
const showAutomation = ref(true);

function reset() {
  operations.value = '';
  stepsHtml.value = '';
  examplesSummaryHtml.value = '';
  automationAdviceHtml.value = '';
  showInput.value = true;
  showSteps.value = true;
  showExamples.value = true;
  showAutomation.value = true;
  finalTranscript = '';
}


socket.on('transcription', ({ text, isFinal }) => {
  if (isFinal) {

    finalTranscript += text + ' ';
    operations.value = finalTranscript;
  } else {

    operations.value = finalTranscript + text;
  }
});

socket.on('stt-error', msg => {
  console.error('Speech-to-text stream error:', msg);
});

onUnmounted(() => {
  if (mediaRecorder && mediaRecorder.state !== 'inactive') {
    mediaRecorder.stop();
  }
  if (micStream) {
    micStream.getTracks().forEach(t => t.stop());
  }
  socket.disconnect();
});

async function toggleSTT() {
  if (!isRecording.value) {
    micStream = await navigator.mediaDevices.getUserMedia({ audio: true });
    mediaRecorder = new MediaRecorder(micStream, { mimeType: 'audio/webm; codecs=opus' });

    mediaRecorder.ondataavailable = e => {
      if (e.data.size > 0) {
        socket.emit('audio', e.data);
      }
    };

    isRecording.value = true;
    document.querySelector('.micbackground').classList.toggle('animate-pulse');
    console.log(isRecording.value);
    mediaRecorder.start(200);
    
  } else {
    document.querySelector('.micbackground').classList.toggle('animate-pulse');
    isRecording.value = false;
    console.log(isRecording.value);
    mediaRecorder.stop();
    micStream.getTracks().forEach(t => t.stop());
    
  }
}

async function analyzeOperations() {
  loading.value = true;
  phase.value = 'parse';
  try {
    const { data: p } = await axios.post('https://accel-api-985126394507.us-central1.run.app/api/parse', { text: operations.value });
    stepsHtml.value = p.stepsHtml;

    const parser = new DOMParser();
    const doc = parser.parseFromString(p.stepsHtml, 'text/html');
    const plainSteps = Array.from(doc.querySelectorAll('.step'))
      .map(el => el.textContent.trim().replace(/^\d+\.\s*/, ''));

    const { data: s } = await axios.post('https://accel-api-985126394507.us-central1.run.app/api/examples-summary', { steps: plainSteps });
    examplesSummaryHtml.value = s.summaryHtml;

    showInput.value = false;
  } catch (e) {
    console.error(e);
    alert('Analysis failed.');
  } finally {
    loading.value = false;
  }
}

async function automateSteps() {
  loading.value = true;
  phase.value = 'automate';
  try {
    showSteps.value = false;
    showExamples.value = false;

    const parser = new DOMParser();
    const doc = parser.parseFromString(stepsHtml.value, 'text/html');
    const plainSteps = Array.from(doc.querySelectorAll('.step'))
      .map(el => el.textContent.trim().replace(/^\d+\.\s*/, ''));

    const { data: a } = await axios.post('https://accel-api-985126394507.us-central1.run.app/api/automate', { steps: plainSteps });
    automationAdviceHtml.value = a.adviceHtml;
  } catch (e) {
    console.error(e);
    alert('Recommendation failed.');
  } finally {
    loading.value = false;
  }
}
</script>


<style scoped>
.loader {
  border: 2px solid transparent;
  border-top-color: white;
  border-radius: 50%;
  width: 1rem;
  height: 1rem;
  animation: spin 1s linear infinite;
}
@keyframes spin { to { transform: rotate(360deg); } }
.fade-enter-active, .fade-leave-active { transition: opacity 0.3s; }
.fade-enter-from, .fade-leave-to { opacity: 0; }
:deep(.step) {
  display: inline-block;
  padding: 0.25rem 0.5rem;
  background-color: #e5e7eb;
  border-radius: 0.25rem;
  margin-right: 0.5rem;
  margin-bottom: 0.5rem;
  font-weight: 500;
  color: #333;
  transition: background-color 0.2s;
  width: 100%;
}
:deep(.insight) { background-color: #f0fff7; border-left: 4px solid #026849; padding: 0.5rem; margin-bottom: 1rem; border-radius: 0.25rem; }
:deep(.insightTwo) { background-color: #f0f8ff; border-left: 4px solid #287eaf; padding: 0.5rem; margin-bottom: 1rem; border-radius: 0.25rem; }
:deep(.workflow) { background-color: #f9f9f9; border: 1px solid #ddd; padding: 1rem; border-radius: 0.5rem; margin-bottom: 1rem; }
:deep(.workflow-title) { font-weight: bold; margin-bottom: 0.5rem; }
:deep(.workflow-desc) { margin-bottom: 0.5rem; color: #555; }
:deep(.mongo-metric) { font-weight: 700; color: #026849; }
</style>
