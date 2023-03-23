<script>
    import { costs } from '../stores.js';
    import { createEventDispatcher } from 'svelte';

	const dispatch = createEventDispatcher();

    export let index;

    function pleaseCloseEditCost() {
        dispatch('pleaseCloseEditCost');
    }

    function addRisk() {
        $costs[index].risks = [...$costs[index].risks, {
            id: $costs[index].risks.length + 1, 
            desc: '',
            probability: 1,
            impact: 1,
            min: 0,
            max: 0,
            riskAllowance: 0
        }];
    }

    function addContract() {
        $costs[index].contract.customs = [...$costs[index].contract.customs, {
            name: '',
            min: 0,
            max: 0
        }];
    }

    function deleteRisk(i) {
        if ($costs[index].risks.length > 1) {
            $costs[index].risks.splice(i, 1);
            $costs[index].risks = $costs[index].risks;
        }
    }

    function deleteContract(i) {
        if ($costs[index].contract.customs.length > 0) {
            $costs[index].contract.customs.splice(i, 1);
            $costs[index].contract.customs = $costs[index].contract.customs;
        }
    }
</script>

<div class="basis-72 p-3 bg-stone-300 ring-[0.5px] ring-stone-400 relative overflow-y-scroll">
    <button class="cursor-pointer absolute top-2 right-2 text-stone-700 hover:bg-stone-100 active:bg-stone-200 rounded-full"
        on:click={pleaseCloseEditCost}>
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="w-6 h-6">
            <path fill-rule="evenodd" d="M5.47 5.47a.75.75 0 011.06 0L12 10.94l5.47-5.47a.75.75 0 111.06 1.06L13.06 12l5.47 5.47a.75.75 0 11-1.06 1.06L12 13.06l-5.47 5.47a.75.75 0 01-1.06-1.06L10.94 12 5.47 6.53a.75.75 0 010-1.06z" clip-rule="evenodd" />
        </svg>
    </button>
    <div class="divide-y-[0.5px] divide-stone-400">
        <div class="py-4">
            <h3 class="font-semibold">Support</h3>
            <div class="mt-1 flex justify-between items-end">
                <label class="ml-1 font-light text-xs" for="support-amount">LDC</label>
                <div>
                    <input type="number"
                        id="support-amount"
                        class="px-1 h-5 text-xs text-right ring-[0.5px] ring-stone-400 rounded-sm"
                        bind:value={$costs[index].unitPrice.support.ldc}>
                    <span class="font-light text-xs">₩</span>
                </div>
            </div>
            <div class="mt-1 flex justify-between items-end">
                <label class="ml-1 font-light text-xs" for="support-amount">All Contract</label>
                <div>
                    <input type="number"
                        id="support-amount"
                        class="px-1 h-5 text-xs text-right ring-[0.5px] ring-stone-400 rounded-sm"
                        bind:value={$costs[index].unitPrice.support.contract}>
                    <span class="font-light text-xs">₩</span>
                </div>
            </div>
            <div class="mt-1 flex justify-between items-end">
                <label class="ml-1 font-light text-xs" for="support-max-rate">Max Rate</label>
                <div>
                    <input type="number"
                        id="support-max-rate"
                        class="px-1 h-5 text-xs text-right ring-[0.5px] ring-stone-400 rounded-sm"
                        bind:value={$costs[index].unitPrice.support.maxRate}>
                    <span class="font-light text-xs">%</span>
                </div>
            </div>
        </div>
    
        <div class="py-4">
            <div class="flex items-center">
                <h3 class="font-semibold">Contract</h3>
                <button class="px-4 ml-2 bg-white ring-[0.5px] ring-stone-400 rounded-lg
                    hover:bg-stone-100 active:bg-stone-300
                    text-xs font-semibold"
                    on:click={addContract}>+</button>
            </div>
    
            <div class="mb-2">
                <input type="text" 
                    class="px-1 h-5 w-[249px] text-xs ring-[0.5px] ring-stone-400 rounded-sm read-only:bg-stone-200"
                    value="전기 통신 소방"
                    readonly>
                <div class="mt-1 flex justify-between items-end">
                    <label class="ml-1 font-light text-xs" for="elect-unit-price">Unit Price</label>
                    <div>
                        <input type="number" 
                            id="elect-unit-price"
                            bind:value={$costs[index].unitPrice.construction.elect}
                            class="px-1 h-5 text-xs text-right ring-[0.5px] ring-stone-400 rounded-sm">
                        <span class="font-light text-xs">₩</span>
                    </div>
                </div>
                <div class="mt-1 flex justify-between items-end">
                    <label class="ml-1 font-light text-xs" for="elect-max-rate">Max Rate</label>
                    <div>
                        <input type="number"
                            id="elect-max-rate"
                            bind:value={$costs[index].unitPrice.contract.electMaxRate}
                            class="px-1 h-5 text-xs text-right ring-[0.5px] ring-stone-400 rounded-sm">
                        <span class="font-light text-xs">%</span>
                    </div>
                </div>
            </div>
    
            <div class="mb-2">
                <input type="text" 
                    class="px-1 h-5 w-[249px] text-xs ring-[0.5px] ring-stone-400 rounded-sm read-only:bg-stone-200"
                    value="도급공사 증가율"
                    readonly>
                <div class="mt-1 flex justify-between items-end">
                    <label class="ml-1 font-light text-xs" for="rate-of-increase-min">Min</label>
                    <div>
                        <input type="number"
                            id="rate-of-increase-min"
                            bind:value={$costs[index].unitPrice.contract.minRate}
                            class="px-1 h-5 text-xs text-right ring-[0.5px] ring-stone-400 rounded-sm">
                        <span class="font-light text-xs">%</span>
                    </div>
                </div>
                <div class="mt-1 flex justify-between items-end">
                    <label class="ml-1 font-light text-xs" for="rate-of-increase-max">Max</label>
                    <div>
                        <input type="number"
                            id="rate-of-increase-max"
                            bind:value={$costs[index].unitPrice.contract.maxRate}
                            class="px-1 h-5 text-xs text-right ring-[0.5px] ring-stone-400 rounded-sm">
                        <span class="font-light text-xs">%</span>
                    </div>
                </div>
            </div>

            {#each $costs[index].contract.customs as contract, i}
                <div class="mb-2">
                    <div class="flex items-center">
                        <button class="text-stone-700 text-xl font-semibold px-2 hover:bg-stone-100 active:bg-stone-200 rounded-full"
                            on:click={() => { deleteContract(i) }}>-</button>
                    </div>
                    <input type="text" 
                        class="px-1 h-5 w-[249px] text-xs ring-[0.5px] ring-stone-400 rounded-sm read-only:bg-stone-200"
                        binx:value={contract.name}>
                    <div class="mt-1 flex justify-between items-end">
                        <label class="ml-1 font-light text-xs" for="rate-of-increase-min">Min</label>
                        <div>
                            <input type="number"
                                id="rate-of-increase-min"
                                bind:value={contract.min}
                                class="px-1 h-5 text-xs text-right ring-[0.5px] ring-stone-400 rounded-sm">
                            <span class="font-light text-xs">₩</span>
                        </div>
                    </div>
                    <div class="mt-1 flex justify-between items-end">
                        <label class="ml-1 font-light text-xs" for="rate-of-increase-max">Max</label>
                        <div>
                            <input type="number"
                                id="rate-of-increase-max"
                                bind:value={contract.max}
                                class="px-1 h-5 text-xs text-right ring-[0.5px] ring-stone-400 rounded-sm">
                            <span class="font-light text-xs">₩</span>
                        </div>
                    </div>
                </div>
            {/each}
        </div>
    
        <div class="py-4">
            {#if $costs[index].risks.length}
                <div class="flex items-center">
                    <h3 class="font-semibold">Risk</h3>
                    <button class="px-4 ml-2 bg-white ring-[0.5px] ring-stone-400 rounded-lg
                        hover:bg-stone-100 active:bg-stone-300
                        text-xs font-semibold"
                        on:click={addRisk}>+</button>
                </div>
            {/if}
            
            {#each $costs[index].risks as risk, i (risk.id)}
                <div class="mb-2">
                    {#if i > 0}
                        <div class="flex items-center">
                            <button class="text-stone-700 text-xl font-semibold px-2 hover:bg-stone-100 active:bg-stone-200 rounded-full"
                                on:click={() => { deleteRisk(i) }}>-</button>
                        </div>
                    {/if}
                    
                    <div class="mt-2 flex justify-between item-center">
                        <input type="text"
                            class="px-1 h-5 w-[249px] text-xs ring-[0.5px] ring-stone-400 rounded-sm read-only:bg-stone-200"
                            bind:value={risk.desc}
                            readonly={risk.id === 1}
                            placeholder="Description of Risk">
                    </div>
                  
                    <div class="mt-2 mr-[15px] flex justify-between items-center">
                        <span class="ml-1 font-light text-xs">Probability</span>
                        <div class="flex">
                            <input type="radio" name="probability-{risk.id}" id="probability-low-{risk.id}"
                                value={1}
                                bind:group={risk.probability}
                                class="peer/probability-low hidden"/>
                            <label for="probability-low-{risk.id}" 
                                class="px-4 bg-white text-xs
                                    peer-checked/probability-low:bg-lime-400
                                    ring-[0.5px] ring-stone-400 rounded-l-lg">1</label>
                            <input type="radio" name="probability-{risk.id}" id="probability-med-{risk.id}"
                                value={2}
                                bind:group={risk.probability}
                                class="peer/probability-med hidden"/>
                            <label for="probability-med-{risk.id}" 
                                class="px-4 bg-white text-xs
                                    peer-checked/probability-med:bg-orange-400
                                    ring-[0.5px] ring-stone-400">2</label>
                            <input type="radio" name="probability-{risk.id}" id="probability-high-{risk.id}"
                                value={3}
                                bind:group={risk.probability}
                                class="peer/probability-high hidden"/>
                            <label for="probability-high-{risk.id}" 
                                class="px-4 bg-white text-xs
                                    peer-checked/probability-high:bg-red-400
                                    ring-[0.5px] ring-stone-400 rounded-r-lg">3</label>
                        </div>
                    </div>
                    <div class="mt-2 mr-[15px] flex justify-between items-center">
                        <span class="ml-1 font-light text-xs">Impact</span>
                        <div class="flex">
                            <input type="radio" name="impact-{risk.id}" id="impact-low-{risk.id}"
                                value={1}
                                bind:group={risk.impact}
                                class="peer/impact-low hidden"/>
                            <label for="impact-low-{risk.id}" 
                                class="px-4 bg-white text-xs
                                    peer-checked/impact-low:bg-lime-400
                                    ring-[0.5px] ring-stone-400 rounded-l-lg">1</label>
                            <input type="radio" name="impact-{risk.id}" id="impact-med-{risk.id}"
                                value={2}
                                bind:group={risk.impact}
                                class="peer/impact-med hidden"/>
                            <label for="impact-med-{risk.id}" 
                                class="px-4 bg-white text-xs
                                    peer-checked/impact-med:bg-orange-400
                                    ring-[0.5px] ring-stone-400">2</label>
                            <input type="radio" name="impact-{risk.id}" id="impact-high-{risk.id}"
                                value={3}
                                bind:group={risk.impact}
                                class="peer/impact-high hidden"/>
                            <label for="impact-high-{risk.id}" 
                                class="px-4 bg-white text-xs
                                    peer-checked/impact-high:bg-red-400
                                    ring-[0.5px] ring-stone-400 rounded-r-lg">3</label>
                        </div>
                    </div>
                    <div class="mt-1 flex justify-between items-center">
                        <label class="ml-1 font-light text-xs" for="risk-min-{risk.id}">Min</label>
                        <div>
                            <input type="number" id="risk-min-{risk.id}"
                                bind:value={risk.min}
                                readonly={risk.id === 1}
                                class="px-1 h-5 text-xs text-right ring-[0.5px] ring-stone-400 rounded-sm read-only:bg-stone-200">
                            <span class="font-light text-xs">₩</span>
                        </div>
                    </div>
                    <div class="mt-1 flex justify-between items-center">
                        <label class="ml-1 font-light text-xs" for="risk-max-{risk.id}">Max</label>
                        <div>
                            <input type="number" id="risk-max-{risk.id}"
                                bind:value={risk.max}
                                readonly={risk.id === 1}
                                class="px-1 h-5 text-xs text-right ring-[0.5px] ring-stone-400 rounded-sm read-only:bg-stone-200">
                            <span class="font-light text-xs">₩</span>
                        </div>
                    </div>
                    <div class="mt-1 flex justify-between items-center">
                        <span class="ml-1 font-light text-xs">Risk Allowance</span>
                        <div>
                            <span class="text-xs">{risk.riskAllowance.toLocaleString()}</span>
                            <span class="font-light text-xs">₩</span>
                        </div>
                    </div>
                </div>
            {/each}
    
        </div>
    </div>

</div>