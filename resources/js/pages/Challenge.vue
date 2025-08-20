<script setup lang="ts">
import { Head, Link } from '@inertiajs/vue3';
import { onMounted, ref } from 'vue';

// Reactive data
const reportData = ref<any>(null);
const loading = ref(true);
const error = ref<string | null>(null);

// AI Commentary state
const aiPrompt = ref('');
const aiResponse = ref('');
const aiLoading = ref(false);
const aiError = ref<string | null>(null);

// Fetch financial report data
const fetchFinancialData = async () => {
    try {
        loading.value = true;
        const response = await fetch('/api/financial-report');
        if (!response.ok) {
            throw new Error('Failed to fetch financial data');
        }
        const data = await response.json();
        reportData.value = data;
    } catch (err) {
        error.value = err instanceof Error ? err.message : 'An error occurred';
    } finally {
        loading.value = false;
    }
};

// Generate AI commentary
const generateCommentary = async () => {
    if (!aiPrompt.value.trim()) return;

    try {
        aiLoading.value = true;
        aiError.value = null;

        const response = await fetch('/api/generate-commentary', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                'X-CSRF-TOKEN': document.querySelector('meta[name="csrf-token"]')?.getAttribute('content') || '',
            },
            body: JSON.stringify({
                prompt: aiPrompt.value,
            }),
        });

        if (!response.ok) {
            throw new Error('Failed to generate commentary');
        }

        const data = await response.json();
        aiResponse.value = data.response || 'No response received';
    } catch (err) {
        aiError.value = err instanceof Error ? err.message : 'An error occurred';
    } finally {
        aiLoading.value = false;
    }
};

onMounted(() => {
    fetchFinancialData();
});
</script>

<template>
    <Head title="Financial Report Challenge" />

    <div class="min-h-screen bg-gradient-to-br from-green-50 via-emerald-50 to-lime-100">
        <!-- Floating farm elements -->
        <div class="pointer-events-none fixed inset-0 overflow-hidden">
            <div class="absolute top-20 left-10 animate-bounce text-4xl" style="animation-delay: 0s">🌾</div>
            <div class="absolute top-32 right-20 animate-bounce text-3xl" style="animation-delay: 1s">🐄</div>
            <div class="absolute bottom-20 left-20 animate-bounce text-2xl" style="animation-delay: 2s">🌱</div>
            <div class="absolute top-1/2 right-10 animate-bounce text-3xl" style="animation-delay: 3s">🚜</div>
            <div class="absolute right-32 bottom-32 animate-bounce text-2xl" style="animation-delay: 4s">🌽</div>
        </div>

        <div class="relative z-10 mx-auto max-w-7xl px-4 py-8">
            <!-- Header -->
            <div class="mb-12">
                <Link
                    href="/"
                    class="group mb-6 inline-flex items-center text-sm font-medium text-green-700 transition-all duration-200 hover:text-emerald-600"
                >
                    <svg class="mr-2 h-4 w-4 transition-transform group-hover:-translate-x-1" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7"></path>
                    </svg>
                    Back to Home
                </Link>

                <div class="text-center">
                    <h1 class="mb-4 bg-gradient-to-r from-green-600 via-emerald-600 to-lime-600 bg-clip-text text-5xl font-black text-transparent">
                        🐄 Figured Farm Financial Report 🌾
                    </h1>
                    <p class="mx-auto max-w-2xl text-lg text-green-700">
                        Fresh from the farm! Transform this basic data display into a beautiful, interactive financial report that brings your
                        agricultural data to life!
                    </p>
                </div>
            </div>

            <!-- Loading State -->
            <div v-if="loading" class="flex items-center justify-center py-20">
                <div class="text-center">
                    <div class="relative mb-6">
                        <div class="mx-auto h-16 w-16 animate-spin rounded-full border-4 border-green-200 border-t-green-600"></div>
                        <div class="absolute inset-0 mx-auto h-16 w-16 animate-pulse rounded-full border-4 border-emerald-100"></div>
                        <div class="absolute inset-0 flex items-center justify-center">
                            <span class="animate-bounce text-2xl">🐄</span>
                        </div>
                    </div>
                    <p class="text-lg font-medium text-green-800">Loading farm financial data...</p>
                    <p class="text-sm text-green-600">Milking the numbers... 🥛</p>
                </div>
            </div>

            <!-- Error State -->
            <div v-else-if="error" class="mx-auto max-w-md">
                <div class="rounded-xl border border-red-200 bg-gradient-to-br from-red-50 to-red-100 p-8 shadow-lg">
                    <div class="text-center">
                        <div class="mx-auto mb-4 flex h-16 w-16 items-center justify-center rounded-full bg-red-100">
                            <span class="text-3xl">🐄💔</span>
                        </div>
                        <h2 class="mb-2 text-xl font-bold text-red-800">Oops! The cow got loose!</h2>
                        <p class="mb-6 text-red-700">{{ error }}</p>
                        <button
                            @click="fetchFinancialData"
                            class="rounded-lg bg-red-600 px-6 py-3 font-medium text-white shadow-lg transition-all duration-200 hover:bg-red-700 hover:shadow-xl"
                        >
                            🔄 Wrangle the Data Back
                        </button>
                    </div>
                </div>
            </div>

            <!-- Basic Report Display -->
            <div v-else-if="reportData" class="space-y-8">
                <!-- Financial Report Table -->
                <div
                    class="group relative overflow-hidden rounded-2xl border border-green-200 bg-white/90 shadow-xl backdrop-blur-sm transition-all duration-300 hover:shadow-2xl"
                >
                    <!-- Decorative gradient overlay -->
                    <div
                        class="absolute inset-0 bg-gradient-to-br from-green-500/5 via-emerald-500/5 to-lime-500/5 opacity-0 transition-opacity duration-300 group-hover:opacity-100"
                    ></div>

                    <!-- Farm pattern background -->
                    <div class="absolute inset-0 opacity-5">
                        <svg width="100%" height="100%" xmlns="http://www.w3.org/2000/svg">
                            <defs>
                                <pattern id="farm-pattern" x="0" y="0" width="60" height="60" patternUnits="userSpaceOnUse">
                                    <text x="10" y="20" font-size="20" fill="#16a34a">🌾</text>
                                    <text x="40" y="50" font-size="16" fill="#059669">🐄</text>
                                </pattern>
                            </defs>
                            <rect width="100%" height="100%" fill="url(#farm-pattern)" />
                        </svg>
                    </div>

                    <div class="relative p-8">
                        <div class="mb-6 flex items-center space-x-3">
                            <div
                                class="flex h-12 w-12 items-center justify-center rounded-xl bg-gradient-to-br from-green-500 to-emerald-600 shadow-lg"
                            >
                                <span class="text-xl">🚜</span>
                            </div>
                            <div>
                                <h3 class="bg-gradient-to-r from-green-800 to-emerald-600 bg-clip-text text-2xl font-bold text-transparent">
                                    Farm Financial Report
                                </h3>
                                <p class="text-sm text-green-600">Profit & Loss Statement 🌱</p>
                            </div>
                        </div>

                        <div class="overflow-x-auto rounded-xl border border-slate-200">
                            <table class="min-w-full text-left text-sm">
                                <thead class="border-b border-slate-200 bg-gradient-to-r from-green-50 to-emerald-50">
                                    <tr>
                                        <th class="border-r border-slate-200 px-6 py-4 font-semibold text-green-800">
                                            <div class="flex items-center space-x-2">
                                                <span>🏪</span>
                                                <span>Line Item</span>
                                            </div>
                                        </th>
                                        <th
                                            v-for="(col, idx) in reportData.columns"
                                            :key="'col-' + idx"
                                            class="border-r border-slate-200 px-6 py-4 text-center font-semibold text-green-800 last:border-r-0"
                                        >
                                            <div class="flex items-center justify-center space-x-1">
                                                <span>📅</span>
                                                <span>{{ col.month }}</span>
                                            </div>
                                        </th>
                                    </tr>
                                </thead>

                                <tbody class="divide-y divide-slate-100">
                                    <!-- Iterate over sections -->
                                    <template v-for="(section, sIdx) in reportData.sections" :key="'section-' + sIdx">
                                        <tr
                                            class="bg-gradient-to-r from-green-50 to-emerald-50 transition-all duration-200 hover:from-green-100 hover:to-emerald-100"
                                        >
                                            <td
                                                class="border-r border-slate-200 px-6 py-4 font-bold text-green-800"
                                                :colspan="reportData.columns.length + 1"
                                            >
                                                <div class="flex items-center space-x-2">
                                                    <div class="h-2 w-2 rounded-full bg-green-500"></div>
                                                    <span>{{ section.name }}</span>
                                                </div>
                                            </td>
                                        </tr>

                                        <!-- Iterate over subsections -->
                                        <template v-for="(subsection, ssIdx) in section.subsections" :key="'subsection-' + ssIdx">
                                            <tr
                                                class="bg-gradient-to-r from-slate-50 to-green-50/30 transition-all duration-200 hover:from-slate-100 hover:to-green-100/50"
                                            >
                                                <td
                                                    class="border-r border-slate-200 px-6 py-3 font-semibold text-green-700"
                                                    :colspan="reportData.columns.length + 1"
                                                >
                                                    <div class="flex items-center space-x-2 pl-4">
                                                        <span class="text-green-500">🌾</span>
                                                        <span>{{ subsection.name }}</span>
                                                    </div>
                                                </td>
                                            </tr>

                                            <!-- Sub-subsections -->
                                            <template v-if="subsection.subsections">
                                                <template v-for="(subsub, sssIdx) in subsection.subsections" :key="'subsub-' + sssIdx">
                                                    <tr
                                                        class="from-slate-25 to-green-25 bg-gradient-to-r transition-all duration-200 hover:from-slate-50 hover:to-green-50"
                                                    >
                                                        <td
                                                            class="border-r border-slate-200 px-6 py-3 font-medium text-green-600 italic"
                                                            :colspan="reportData.columns.length + 1"
                                                        >
                                                            <div class="flex items-center space-x-2 pl-8">
                                                                <span class="text-emerald-400">🌱</span>
                                                                <span>{{ subsub.name }}</span>
                                                            </div>
                                                        </td>
                                                    </tr>

                                                    <!-- Line Items -->
                                                    <template v-for="(item, iIdx) in subsub.line_items" :key="'item-' + iIdx">
                                                        <tr class="group/row transition-all duration-200 hover:bg-green-50/30">
                                                            <td class="border-r border-slate-200 px-6 py-3 pl-12 text-green-700">
                                                                <div class="flex items-center space-x-2">
                                                                    <span
                                                                        class="text-green-400 transition-colors duration-200 group-hover/row:text-green-600"
                                                                        >🐄</span
                                                                    >
                                                                    <span>{{ item.name }}</span>
                                                                </div>
                                                            </td>
                                                            <td
                                                                v-for="(val, vIdx) in item.values"
                                                                :key="'val-' + vIdx"
                                                                class="border-r border-slate-200 px-6 py-3 text-right font-medium last:border-r-0"
                                                            >
                                                                <span
                                                                    class="inline-flex items-center rounded-lg bg-green-100 px-3 py-1 font-mono text-sm text-green-700 transition-all duration-200 group-hover/row:bg-green-200 group-hover/row:text-green-800"
                                                                >
                                                                    {{ val.toLocaleString() }}
                                                                </span>
                                                            </td>
                                                        </tr>
                                                    </template>
                                                </template>
                                            </template>

                                            <!-- Line Items directly under subsection -->
                                            <template v-if="subsection.line_items">
                                                <template v-for="(item, iIdx) in subsection.line_items" :key="'item-' + iIdx">
                                                    <tr class="group/row transition-all duration-200 hover:bg-green-50/30">
                                                        <td class="border-r border-slate-200 px-6 py-3 pl-8 text-green-700">
                                                            <div class="flex items-center space-x-2">
                                                                <span
                                                                    class="text-green-400 transition-colors duration-200 group-hover/row:text-green-600"
                                                                    >🌽</span
                                                                >
                                                                <span>{{ item.name }}</span>
                                                            </div>
                                                        </td>
                                                        <td
                                                            v-for="(val, vIdx) in item.values"
                                                            :key="'val-' + vIdx"
                                                            class="border-r border-slate-200 px-6 py-3 text-right font-medium last:border-r-0"
                                                        >
                                                            <span
                                                                class="inline-flex items-center rounded-lg bg-green-100 px-3 py-1 font-mono text-sm text-green-700 transition-all duration-200 group-hover/row:bg-green-200 group-hover/row:text-green-800"
                                                            >
                                                                {{ val.toLocaleString() }}
                                                            </span>
                                                        </td>
                                                    </tr>
                                                </template>
                                            </template>
                                        </template>
                                    </template>
                                </tbody>
                            </table>
                        </div>
                    </div>
                </div>

                <!-- Challenge Instructions -->
                <div
                    class="group relative overflow-hidden rounded-2xl border border-amber-200 bg-gradient-to-br from-amber-50 via-orange-50 to-yellow-50 p-8 shadow-lg transition-all duration-300 hover:shadow-xl"
                >
                    <!-- Decorative pattern -->
                    <div class="absolute inset-0 opacity-5">
                        <svg width="100%" height="100%" xmlns="http://www.w3.org/2000/svg">
                            <defs>
                                <pattern id="target-pattern" x="0" y="0" width="40" height="40" patternUnits="userSpaceOnUse">
                                    <text x="5" y="15" font-size="16" fill="#f59e0b">🎯</text>
                                    <text x="25" y="35" font-size="14" fill="#d97706">🌾</text>
                                </pattern>
                            </defs>
                            <rect width="100%" height="100%" fill="url(#target-pattern)" />
                        </svg>
                    </div>

                    <div class="relative">
                        <div class="mb-4 flex items-center space-x-3">
                            <div
                                class="flex h-10 w-10 items-center justify-center rounded-full bg-gradient-to-r from-amber-400 to-orange-500 text-xl shadow-lg"
                            >
                                🎯
                            </div>
                            <h3 class="bg-gradient-to-r from-amber-800 to-orange-700 bg-clip-text text-xl font-bold text-transparent">
                                Your Farm Challenge
                            </h3>
                        </div>
                        <div class="space-y-3 text-amber-800">
                            <p class="text-base leading-relaxed">
                                Build a complete, interactive farm Profit & Loss report using the provided agricultural financial data!
                            </p>
                            <p class="text-base leading-relaxed">
                                The data is available in
                                <code class="rounded-lg bg-amber-100 px-2 py-1 font-mono text-sm text-amber-900">reportData</code> - explore it and
                                create a professional farm financial report interface that would make any farmer proud! 🌱
                            </p>
                            <div class="mt-4 flex items-center space-x-2 text-sm text-amber-700">
                                <span class="text-lg">🐄</span>
                                <span>Make it interactive, beautiful, and farm-friendly!</span>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- AI Commentary Demo -->
                <div
                    class="group relative overflow-hidden rounded-2xl border border-emerald-200 bg-gradient-to-br from-emerald-50 via-teal-50 to-green-50 p-8 shadow-lg transition-all duration-300 hover:shadow-xl"
                >
                    <!-- Decorative pattern -->
                    <div class="absolute inset-0 opacity-5">
                        <svg width="100%" height="100%" xmlns="http://www.w3.org/2000/svg">
                            <defs>
                                <pattern id="ai-pattern" x="0" y="0" width="30" height="30" patternUnits="userSpaceOnUse">
                                    <path d="M15 5l5 5-5 5M5 25l5-5-5-5" stroke="#10b981" stroke-width="1" fill="none" />
                                </pattern>
                            </defs>
                            <rect width="100%" height="100%" fill="url(#ai-pattern)" />
                        </svg>
                    </div>

                    <div class="relative">
                        <div class="mb-6 flex items-center space-x-3">
                            <div
                                class="flex h-10 w-10 items-center justify-center rounded-full bg-gradient-to-r from-emerald-400 to-teal-500 text-xl shadow-lg"
                            >
                                🤖
                            </div>
                            <h3 class="bg-gradient-to-r from-emerald-800 to-teal-700 bg-clip-text text-xl font-bold text-transparent">
                                AI Commentary (Prism Demo)
                            </h3>
                        </div>

                        <div class="space-y-6">
                            <div>
                                <label for="ai-prompt" class="mb-3 block text-sm font-semibold text-emerald-800"> Ask AI a question: </label>
                                <div class="relative">
                                    <input
                                        id="ai-prompt"
                                        v-model="aiPrompt"
                                        type="text"
                                        placeholder="e.g., What are some general business insights?"
                                        class="w-full rounded-xl border-2 border-emerald-200 bg-white/80 p-4 pr-12 backdrop-blur-sm transition-all duration-200 focus:border-emerald-400 focus:ring-4 focus:ring-emerald-100 focus:outline-none"
                                        :disabled="aiLoading"
                                    />
                                    <div class="absolute inset-y-0 right-0 flex items-center pr-4">
                                        <svg class="h-5 w-5 text-emerald-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                            <path
                                                stroke-linecap="round"
                                                stroke-linejoin="round"
                                                stroke-width="2"
                                                d="M8 12h.01M12 12h.01M16 12h.01M21 12c0 4.418-4.03 8-9 8a9.863 9.863 0 01-4.255-.949L3 20l1.395-3.72C3.512 15.042 3 13.574 3 12c0-4.418 4.03-8 9-8s9 3.582 9 8z"
                                            ></path>
                                        </svg>
                                    </div>
                                </div>
                            </div>

                            <button
                                @click="generateCommentary"
                                :disabled="!aiPrompt.trim() || aiLoading"
                                class="group relative overflow-hidden rounded-xl bg-gradient-to-r from-emerald-500 to-teal-600 px-6 py-3 font-semibold text-white shadow-lg transition-all duration-200 hover:from-emerald-600 hover:to-teal-700 hover:shadow-xl disabled:cursor-not-allowed disabled:opacity-50"
                            >
                                <div class="flex items-center space-x-2">
                                    <svg v-if="aiLoading" class="h-5 w-5 animate-spin" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                        <path
                                            stroke-linecap="round"
                                            stroke-linejoin="round"
                                            stroke-width="2"
                                            d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"
                                        ></path>
                                    </svg>
                                    <svg v-else class="h-5 w-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 10V3L4 14h7v7l9-11h-7z"></path>
                                    </svg>
                                    <span>{{ aiLoading ? 'Generating...' : 'Generate Commentary' }}</span>
                                </div>
                            </button>

                            <div v-if="aiError" class="rounded-xl border border-red-200 bg-gradient-to-r from-red-50 to-pink-50 p-4 shadow-sm">
                                <div class="flex items-center space-x-2">
                                    <svg class="h-5 w-5 text-red-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                        <path
                                            stroke-linecap="round"
                                            stroke-linejoin="round"
                                            stroke-width="2"
                                            d="M12 8v4m0 4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"
                                        ></path>
                                    </svg>
                                    <p class="text-sm font-medium text-red-800">{{ aiError }}</p>
                                </div>
                            </div>

                            <div v-if="aiResponse" class="rounded-xl border border-emerald-200 bg-white/80 p-6 shadow-sm backdrop-blur-sm">
                                <div class="mb-3 flex items-center space-x-2">
                                    <svg class="h-5 w-5 text-emerald-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                        <path
                                            stroke-linecap="round"
                                            stroke-linejoin="round"
                                            stroke-width="2"
                                            d="M9.663 17h4.673M12 3v1m6.364 1.636l-.707.707M21 12h-1M4 12H3m3.343-5.657l-.707-.707m2.828 9.9a5 5 0 117.072 0l-.548.547A3.374 3.374 0 0014 18.469V19a2 2 0 11-4 0v-.531c0-.895-.356-1.754-.988-2.386l-.548-.547z"
                                        ></path>
                                    </svg>
                                    <h4 class="font-semibold text-emerald-800">AI Response:</h4>
                                </div>
                                <div class="prose prose-sm prose-emerald max-w-none">
                                    <p class="leading-relaxed whitespace-pre-wrap text-slate-700">{{ aiResponse }}</p>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Challenge Links -->
                <div
                    class="relative overflow-hidden rounded-2xl border border-green-200 bg-gradient-to-br from-green-50 to-emerald-50 py-12 text-center shadow-lg"
                >
                    <!-- Decorative elements -->
                    <div class="absolute inset-0 opacity-10">
                        <svg width="100%" height="100%" xmlns="http://www.w3.org/2000/svg">
                            <defs>
                                <pattern id="grid-pattern" x="0" y="0" width="40" height="40" patternUnits="userSpaceOnUse">
                                    <path d="M 40 0 L 0 0 0 40" fill="none" stroke="#16a34a" stroke-width="1" />
                                </pattern>
                            </defs>
                            <rect width="100%" height="100%" fill="url(#grid-pattern)" />
                        </svg>
                    </div>

                    <div class="relative">
                        <h3 class="mb-6 bg-gradient-to-r from-green-800 to-emerald-600 bg-clip-text text-xl font-bold text-transparent">
                            Ready to harvest some insights? 🌾
                        </h3>
                        <div class="flex flex-col items-center justify-center gap-4 sm:flex-row">
                            <a
                                href="/CHALLENGE.md"
                                target="_blank"
                                class="group inline-flex items-center rounded-xl bg-gradient-to-r from-green-600 to-emerald-600 px-8 py-4 font-semibold text-white shadow-lg transition-all duration-200 hover:-translate-y-1 hover:from-green-700 hover:to-emerald-700 hover:shadow-xl"
                            >
                                <span class="mr-3 text-lg">📋</span>
                                Farm Challenge Requirements
                                <svg
                                    class="ml-2 h-4 w-4 transition-transform group-hover:translate-x-1"
                                    fill="none"
                                    stroke="currentColor"
                                    viewBox="0 0 24 24"
                                >
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7"></path>
                                </svg>
                            </a>
                            <a
                                href="/api/financial-report"
                                target="_blank"
                                class="group inline-flex items-center rounded-xl bg-gradient-to-r from-lime-600 to-green-600 px-8 py-4 font-semibold text-white shadow-lg transition-all duration-200 hover:-translate-y-1 hover:from-lime-700 hover:to-green-700 hover:shadow-xl"
                            >
                                <span class="mr-3 text-lg">�</span>
                                Farm Data API
                                <svg
                                    class="ml-2 h-4 w-4 transition-transform group-hover:translate-x-1"
                                    fill="none"
                                    stroke="currentColor"
                                    viewBox="0 0 24 24"
                                >
                                    <path
                                        stroke-linecap="round"
                                        stroke-linejoin="round"
                                        stroke-width="2"
                                        d="M10 6H6a2 2 0 00-2 2v10a2 2 0 002 2h10a2 2 0 002-2v-4M14 4h6m0 0v6m0-6L10 14"
                                    ></path>
                                </svg>
                            </a>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
