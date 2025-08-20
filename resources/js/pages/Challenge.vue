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

    <div class="min-h-screen bg-gray-50 px-4 py-8">
        <div class="mx-auto max-w-7xl">
            <!-- Header -->
            <div class="mb-8">
                <Link href="/" class="mb-4 inline-flex items-center text-sm text-gray-600 hover:text-gray-900"> ← Back to Home </Link>

                <h1 class="mb-2 text-3xl font-bold text-gray-900">🌾 Figured Financial Report Challenge</h1>
                <p class="text-gray-600">Transform this basic data display into a beautiful, interactive financial report!</p>
            </div>

            <!-- Loading State -->
            <div v-if="loading" class="flex items-center justify-center py-12">
                <div class="h-12 w-12 animate-spin rounded-full border-b-2 border-blue-600"></div>
                <span class="ml-4 text-gray-600">Loading financial data...</span>
            </div>

            <!-- Error State -->
            <div v-else-if="error" class="rounded-lg border border-red-200 bg-red-50 p-6">
                <h2 class="mb-2 font-semibold text-red-800">Error Loading Data</h2>
                <p class="text-red-600">{{ error }}</p>
                <button @click="fetchFinancialData" class="mt-4 rounded bg-red-600 px-4 py-2 text-white hover:bg-red-700">Try Again</button>
            </div>

            <!-- Basic Report Display -->
            <div v-else-if="reportData" class="space-y-8">
                <!-- Financial Report Table -->
                <div class="overflow-x-auto rounded-lg border border-gray-200 bg-white p-6 shadow-sm">
                    <h3 class="mb-4 text-xl font-semibold text-gray-900">📊 Financial Report</h3>

                    <table class="min-w-full text-left text-sm text-gray-700">
                        <thead class="bg-gray-100 text-xs uppercase">
                            <tr>
                                <th class="px-4 py-2">Line Item</th>
                                <th v-for="(col, idx) in reportData.columns" :key="'col-' + idx" class="px-4 py-2">
                                    {{ col.month }}
                                </th>
                            </tr>
                        </thead>

                        <tbody>
                            <!-- Iterate over sections -->
                            <template v-for="(section, sIdx) in reportData.sections" :key="'section-' + sIdx">
                                <tr class="bg-blue-50 font-semibold">
                                    <td class="px-4 py-2" :colspan="reportData.columns.length + 1">
                                        {{ section.name }}
                                    </td>
                                </tr>

                                <!-- Iterate over subsections -->
                                <template v-for="(subsection, ssIdx) in section.subsections" :key="'subsection-' + ssIdx">
                                    <tr class="bg-gray-50 font-medium">
                                        <td class="px-4 py-2" :colspan="reportData.columns.length + 1">→ {{ subsection.name }}</td>
                                    </tr>

                                    <!-- Sub-subsections -->
                                    <template v-if="subsection.subsections">
                                        <template v-for="(subsub, sssIdx) in subsection.subsections" :key="'subsub-' + sssIdx">
                                            <tr class="bg-gray-100 text-gray-800 italic">
                                                <td class="px-4 py-2" :colspan="reportData.columns.length + 1">➤ {{ subsub.name }}</td>
                                            </tr>

                                            <!-- Line Items -->
                                            <template v-for="(item, iIdx) in subsub.line_items" :key="'item-' + iIdx">
                                                <tr>
                                                    <td class="px-4 py-2">{{ item.name }}</td>
                                                    <td v-for="(val, vIdx) in item.values" :key="'val-' + vIdx" class="px-4 py-2 text-right">
                                                        {{ val.toLocaleString() }}
                                                    </td>
                                                </tr>
                                            </template>
                                        </template>
                                    </template>

                                    <!-- Line Items directly under subsection -->
                                    <template v-if="subsection.line_items">
                                        <template v-for="(item, iIdx) in subsection.line_items" :key="'item-' + iIdx">
                                            <tr>
                                                <td class="px-4 py-2">{{ item.name }}</td>
                                                <td v-for="(val, vIdx) in item.values" :key="'val-' + vIdx" class="px-4 py-2 text-right">
                                                    {{ val.toLocaleString() }}
                                                </td>
                                            </tr>
                                        </template>
                                    </template>
                                </template>
                            </template>
                        </tbody>
                    </table>
                </div>

                <!-- Challenge Instructions -->
                <div class="rounded-lg border border-blue-200 bg-blue-50 p-6">
                    <h3 class="mb-3 text-lg font-semibold text-blue-900">🎯 Your Challenge</h3>
                    <div class="space-y-2 text-blue-800">
                        <p>Build a complete, interactive Profit & Loss report using the provided financial data.</p>
                        <p>
                            The data is available in <code class="rounded bg-blue-100 px-1">reportData</code> - explore it and create a professional
                            financial report interface.
                        </p>
                    </div>
                </div>

                <!-- AI Commentary Demo -->
                <div class="rounded-lg border border-green-200 bg-green-50 p-6">
                    <h3 class="mb-3 text-lg font-semibold text-green-900">🤖 AI Commentary (Prism Demo)</h3>

                    <div class="space-y-4">
                        <div>
                            <label for="ai-prompt" class="mb-2 block text-sm font-medium text-green-800"> Ask AI a question: </label>
                            <input
                                id="ai-prompt"
                                v-model="aiPrompt"
                                type="text"
                                placeholder="e.g., What are some general business insights?"
                                class="w-full rounded-lg border border-green-300 p-3 focus:border-transparent focus:ring-2 focus:ring-green-500"
                                :disabled="aiLoading"
                            />
                        </div>

                        <button
                            @click="generateCommentary"
                            :disabled="!aiPrompt.trim() || aiLoading"
                            class="rounded-lg bg-green-600 px-4 py-2 text-white hover:bg-green-700 disabled:cursor-not-allowed disabled:opacity-50"
                        >
                            {{ aiLoading ? 'Generating...' : 'Generate Commentary' }}
                        </button>

                        <div v-if="aiError" class="rounded-lg border border-red-200 bg-red-50 p-4">
                            <p class="text-sm text-red-800">{{ aiError }}</p>
                        </div>

                        <div v-if="aiResponse" class="rounded-lg border border-green-200 bg-white p-4">
                            <h4 class="mb-2 font-medium text-green-800">AI Response:</h4>
                            <p class="text-sm whitespace-pre-wrap text-gray-700">{{ aiResponse }}</p>
                        </div>
                    </div>
                </div>

                <!-- Challenge Links -->
                <div class="border-t border-gray-200 py-8 text-center">
                    <div class="space-x-4">
                        <a
                            href="/CHALLENGE.md"
                            target="_blank"
                            class="inline-flex items-center rounded-lg bg-blue-600 px-6 py-3 text-white hover:bg-blue-700"
                        >
                            📋 Challenge Requirements
                        </a>
                        <a
                            href="/api/financial-report"
                            target="_blank"
                            class="inline-flex items-center rounded-lg bg-green-600 px-6 py-3 text-white hover:bg-green-700"
                        >
                            🔗 API Data
                        </a>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
