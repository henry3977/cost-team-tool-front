<script>
    import { costs } from '../stores.js';
    import { createEventDispatcher } from 'svelte';

	const dispatch = createEventDispatcher();
    export let index;

    function addRisk() {
        $costs[index].risk.customs = [...$costs[index].risk.customs, {
            id: $costs[index].risk.customs.length + 1, 
            desc: 'New Risk',
            probability: 1,
            impact: 1,
            min: null,
            max: null,
            riskAllowance: 0
        }];
    }

    function deleteRisk(i) {
        if ($costs[index].risk.customs.length > 0) {
            $costs[index].risk.customs.splice(i, 1);
            $costs[index].risk.customs = $costs[index].risk.customs;
        }
    }

    function pleaseCloseEditCost() {
        dispatch('pleaseCloseEditCost');
    }
</script>

<div class="shrink-0 w-72 p-3 border-l relative overflow-auto">
    <button class="cursor-pointer absolute top-2 right-2 text-gray-700 hover:bg-gray-100 active:bg-gray-200 rounded-full"
        on:click={pleaseCloseEditCost}>
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="w-6 h-6">
            <path fill-rule="evenodd" d="M5.47 5.47a.75.75 0 011.06 0L12 10.94l5.47-5.47a.75.75 0 111.06 1.06L13.06 12l5.47 5.47a.75.75 0 11-1.06 1.06L12 13.06l-5.47 5.47a.75.75 0 01-1.06-1.06L10.94 12 5.47 6.53a.75.75 0 010-1.06z" clip-rule="evenodd" />
        </svg>
    </button>
    <div class="divide-y-[0.5px] divide-gray-400">
        <div class="py-4">
            <div class="flex items-center">
                <h3 class="font-semibold">Risk</h3>
                <button class="px-2 ml-1 rounded-lg
                    text-gray-700 hover:bg-gray-100 active:bg-gray-200
                    text-xs font-semibold"
                    on:click={addRisk}>
                    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" class="w-5 h-5">
                        <path d="M10.75 4.75a.75.75 0 00-1.5 0v4.5h-4.5a.75.75 0 000 1.5h4.5v4.5a.75.75 0 001.5 0v-4.5h4.5a.75.75 0 000-1.5h-4.5v-4.5z" />
                    </svg>
                </button>
            </div>
            
            {#each $costs[index].risk.customs as risk, i (risk.id)}
                <div class="mb-2">
                    <div class="flex items-center">
                        <button class="px-2 rounded-lg
                            text-gray-700 hover:bg-gray-100 active:bg-gray-200"
                            on:click={() => { deleteRisk(i) }}>
                            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" class="w-5 h-5">
                                <path fill-rule="evenodd" d="M4 10a.75.75 0 01.75-.75h10.5a.75.75 0 010 1.5H4.75A.75.75 0 014 10z" clip-rule="evenodd" />
                            </svg>
                        </button>
                    </div>
                    <div class="mt-2 flex justify-between item-center">
                        <input type="text"
                            class="px-1 h-5 w-[249px] text-xs ring-[0.5px] ring-gray-400 rounded-sm read-only:bg-gray-200"
                            bind:value={risk.desc}
                            placeholder="Description of Risk">
                    </div>
                    <div class="mt-2 mr-[15px] flex justify-between items-center">
                        <span class="ml-1 text-xs">Probability</span>
                        <div class="flex">
                            <input type="radio" name="probability-{risk.id}" id="probability-low-{risk.id}"
                                value={1}
                                bind:group={risk.probability}
                                class="peer/probability-low hidden"/>
                            <label for="probability-low-{risk.id}" 
                                class="px-4 bg-white text-xs
                                    peer-checked/probability-low:bg-lime-400
                                    ring-[0.5px] ring-gray-400 rounded-l-lg">1</label>
                            <input type="radio" name="probability-{risk.id}" id="probability-med-{risk.id}"
                                value={2}
                                bind:group={risk.probability}
                                class="peer/probability-med hidden"/>
                            <label for="probability-med-{risk.id}" 
                                class="px-4 bg-white text-xs
                                    peer-checked/probability-med:bg-orange-400
                                    ring-[0.5px] ring-gray-400">2</label>
                            <input type="radio" name="probability-{risk.id}" id="probability-high-{risk.id}"
                                value={3}
                                bind:group={risk.probability}
                                class="peer/probability-high hidden"/>
                            <label for="probability-high-{risk.id}" 
                                class="px-4 bg-white text-xs
                                    peer-checked/probability-high:bg-red-400
                                    ring-[0.5px] ring-gray-400 rounded-r-lg">3</label>
                        </div>
                    </div>
                    <div class="mt-2 mr-[15px] flex justify-between items-center">
                        <span class="ml-1 text-xs">Impact</span>
                        <div class="flex">
                            <input type="radio" name="impact-{risk.id}" id="impact-low-{risk.id}"
                                value={1}
                                bind:group={risk.impact}
                                class="peer/impact-low hidden"/>
                            <label for="impact-low-{risk.id}" 
                                class="px-4 bg-white text-xs
                                    peer-checked/impact-low:bg-lime-400
                                    ring-[0.5px] ring-gray-400 rounded-l-lg">1</label>
                            <input type="radio" name="impact-{risk.id}" id="impact-med-{risk.id}"
                                value={2}
                                bind:group={risk.impact}
                                class="peer/impact-med hidden"/>
                            <label for="impact-med-{risk.id}" 
                                class="px-4 bg-white text-xs
                                    peer-checked/impact-med:bg-orange-400
                                    ring-[0.5px] ring-gray-400">2</label>
                            <input type="radio" name="impact-{risk.id}" id="impact-high-{risk.id}"
                                value={3}
                                bind:group={risk.impact}
                                class="peer/impact-high hidden"/>
                            <label for="impact-high-{risk.id}" 
                                class="px-4 bg-white text-xs
                                    peer-checked/impact-high:bg-red-400
                                    ring-[0.5px] ring-gray-400 rounded-r-lg">3</label>
                        </div>
                    </div>
                    <div class="mt-1 flex justify-between items-center">
                        <label class="ml-1 text-xs" for="risk-min-{risk.id}">Min</label>
                        <div>
                            <input type="number" id="risk-min-{risk.id}"
                                bind:value={risk.min}
                                class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm read-only:bg-gray-200">
                            <span class=" text-xs">₩</span>
                        </div>
                    </div>
                    <div class="mt-1 flex justify-between items-center">
                        <label class="ml-1 text-xs" for="risk-max-{risk.id}">Max</label>
                        <div>
                            <input type="number" id="risk-max-{risk.id}"
                                bind:value={risk.max}
                                class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm read-only:bg-gray-200">
                            <span class=" text-xs">₩</span>
                        </div>
                    </div>
                    <div class="mt-1 flex justify-between items-center">
                        <span class="ml-1 text-xs">Risk Allowance</span>
                        <div>
                            <span class="text-xs">{risk.riskAllowance.toLocaleString()}</span>
                            <span class="text-xs">₩</span>
                        </div>
                    </div>
                </div>
            {/each}
    
        </div>
    </div>
</div>