<script>
    import { afterUpdate } from 'svelte';
	import { createEventDispatcher } from 'svelte';
    import { costs } from '../stores.js';

	const dispatch = createEventDispatcher();
    export let index;
    export let selectedIndex;
    export let defaultUnitPrice = {
        support: {
            contract: 3540000,
            ldc: 8000000,
            maxRate:104.3
        },
        contract: {
            minRate: 150,
            maxRate: 165,
            electMaxRate: 120
        },
        new: {
            building: 991968,
            site: 178787,
            elect: 580000
        },
        major: {
            building: 732758.87,
            site: 227171.45,
            elect: 300000
        },
        store: {
            building: 830734,
            site: 0,
            elect: 300000
        },
        lease: {
            building: 0,
            site: 0,
            elect: 0,
            min: 936944,
            max: 1516164
        }
    }

    afterUpdate(() => {
        if ($costs[index].plan.buildingArea || $costs[index].plan.siteArea || $costs[index].plan.weeks) calculators();
    });

    $: constructionTotal = $costs[index].construction.building + $costs[index].construction.site;
    $: contractMinTotal = $costs[index].contract.defaults.reduce((a, b) => a + b.min, 0);
    $: contractMaxTotal = $costs[index].contract.defaults.reduce((a, b) => a + b.max, 0);
    $: riskTotal = $costs[index].risks.reduce((a, b) => a + b.riskAllowance, 0);

    function calculators() {
        if ($costs[index].unitPrice.support === undefined) $costs[index].unitPrice.support = defaultUnitPrice.support;
        if ($costs[index].unitPrice.contract === undefined) $costs[index].unitPrice.contract = defaultUnitPrice.contract;
        if ($costs[index].unitPrice.construction === undefined) $costs[index].unitPrice.construction = defaultUnitPrice[$costs[index].plan.type];
        
        $costs[index].support.min = 0;
        $costs[index].support.max = 0;
        if ($costs[index].plan.type !== 'lease') {
            $costs[index].support.min = $costs[index].plan.allContract 
                ? Math.round($costs[index].plan.weeks * $costs[index].unitPrice.support.contract)
                : Math.round($costs[index].plan.weeks * $costs[index].unitPrice.support.ldc);
            $costs[index].support.max = Math.round($costs[index].support.min * ($costs[index].unitPrice.support.maxRate / 100));
        }

        $costs[index].construction.building = 0;
        $costs[index].construction.site = 0;
        if (!$costs[index].plan.allContract) {
            $costs[index].construction.building = Math.round($costs[index].plan.buildingArea * $costs[index].unitPrice.construction.building);
            $costs[index].construction.site = Math.round($costs[index].plan.siteArea * $costs[index].unitPrice.construction.site);
        }

        $costs[index].contract.defaults = [];
        if ($costs[index].plan.type === 'lease') {
            $costs[index].contract.defaults.push({
                name: '상가 내부 인테리어',
                min: Math.round($costs[index].unitPrice.construction.min * $costs[index].plan.buildingArea),
                max: Math.round($costs[index].unitPrice.construction.max * $costs[index].plan.buildingArea)
            });
        } else {
            $costs[index].contract.defaults.push({
                name: '전기 통신 소방',
                min: Math.round($costs[index].plan.buildingArea * $costs[index].unitPrice.construction.elect),
                max: Math.round($costs[index].plan.buildingArea * $costs[index].unitPrice.construction.elect * ($costs[index].unitPrice.contract.electMaxRate / 100))
            });
            if ($costs[index].plan.allContract) {
                let constructionTotal = Math.round($costs[index].plan.buildingArea * $costs[index].unitPrice.construction.building) + Math.round($costs[index].plan.siteArea * $costs[index].unitPrice.construction.site);
                $costs[index].contract.defaults.push({
                    name: '건축',
                    min: Math.round(constructionTotal * ($costs[index].unitPrice.contract.minRate / 100)),
                    max: Math.round(constructionTotal * ($costs[index].unitPrice.contract.maxRate / 100))
                });
            }
        }
        let costContractMinTotal = $costs[index].contract.defaults.reduce((a, b) => a + b.min, 0);
        let costContractMaxTotal = $costs[index].contract.defaults.reduce((a, b) => a + b.max, 0);

        if ($costs[index].plan.type === 'lease') $costs[index].risks = [];
        if ($costs[index].plan.type !== 'lease' && $costs[index].risks.length === 0) {
            pushRisk({
                desc: '도급 계약 관리',
                probability: 3,
                impact: 3,
                min: Math.round(costContractMinTotal * 0.1),
                max: Math.round(costContractMaxTotal * 0.1),
                riskAllowance: 0
            });
            pushRisk({
                desc: '날씨로 인한 지연',
                probability: 2,
                impact: 2,
                min: 3000000,
                max: 6000000,
                riskAllowance: 0
            });
        } 
        if ($costs[index].plan.type !== 'lease' && $costs[index].risks.length > 0) {
            $costs[index].risks[0].min = Math.round(costContractMinTotal * 0.1);
            $costs[index].risks[0].max = Math.round(costContractMaxTotal * 0.1);
        }
        $costs[index].risks.forEach(risk => {
            risk.riskAllowance = Math.round( ((risk.min + risk.max) / 2) * ((risk.probability * risk.impact) / 10) );
        });

        $costs[index].minTotal = 0;
        $costs[index].maxTotal = 0;
        $costs[index].minTotal = $costs[index].support.min + costContractMinTotal;
        $costs[index].maxTotal = $costs[index].support.max + costContractMaxTotal + $costs[index].risks.reduce((a, b) => a + b.riskAllowance, 0);
        if (!$costs[index].plan.allContract) {
            $costs[index].minTotal = $costs[index].minTotal + $costs[index].construction.building + $costs[index].construction.site;
            $costs[index].maxTotal = $costs[index].maxTotal + $costs[index].construction.building + $costs[index].construction.site;
        }
    }

    function pushRisk(risk) {
        risk.id = $costs[index].risks.length + 1;
        $costs[index].risks.push(risk);
    }

    function selectdType() {
        $costs[index].unitPrice.construction = defaultUnitPrice[$costs[index].plan.type];
    }

    function pleaseDeleteMe() {
        dispatch('pleaseDeleteMe', {
			index: index
		});
    }

    function pleaseEditCost() {
        dispatch('pleaseEditCost', {
			index: index
		});
    }

</script>

<div class="shrink-0 w-full lg:w-1/3 2xl:w-1/5 border-r hover:shadow-xl "
    class:bg-gray-50={selectedIndex == index}>
    <div class="p-4 relative">
        <button class="cursor-pointer absolute top-2 right-2 p-1 text-gray-700 hover:bg-gray-100 active:bg-gray-200 rounded-full"
            on:click={pleaseDeleteMe}>
            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="w-6 h-6">
                <path fill-rule="evenodd" d="M5.47 5.47a.75.75 0 011.06 0L12 10.94l5.47-5.47a.75.75 0 111.06 1.06L13.06 12l5.47 5.47a.75.75 0 11-1.06 1.06L12 13.06l-5.47 5.47a.75.75 0 01-1.06-1.06L10.94 12 5.47 6.53a.75.75 0 010-1.06z" clip-rule="evenodd" />
            </svg>
        </button>
        <div class="text-2xl font-semibold text-center">
            <select class="appearance-none"
                bind:value={$costs[index].plan.type}
                on:change={selectdType}>
                <option value="new">New</option>
                <option value="major">Major</option>
                <option value="store">Store</option>
                <option value="lease">Lease</option>
            </select>
        </div>
        <div class="flex flex-col gap-y-1 mt-4 text-sm font-semibold ">
            <div class="flex justify-between items-center">
                <label for="building-area-{index}">Building Area</label>
                <input type="number" id="building-area-{index}"
                    bind:value={$costs[index].plan.buildingArea}
                    class="rounded-md shadow-sm
                        w-32 px-2 py-1 mt-1 text-right
                        ring-1 ring-inset ring-gray-200
                        focus:outline-none focus:ring-1 focus:ring-gray-700">
            </div>
            <div class="flex justify-between items-center h-[32px]">
                <label for="site-area-{index}">Site Area</label>
                {#if $costs[index].plan.type === 'new' || $costs[index].plan.type === 'major'}
                    <input type="number" id="site-area-{index}"
                        bind:value={$costs[index].plan.siteArea}
                        class="rounded-md shadow-sm
                            w-32 px-2 py-1 mt-1 text-right
                            ring-1 ring-inset ring-gray-200 
                            focus:outline-none focus:ring-1 focus:ring-gray-700">
                {/if}
            </div>
            <div class="flex justify-between items-center h-[32px]">
                <label for="weeks-{index}">Weeks</label>
                {#if $costs[index].plan.type !== 'lease'}
                    <input type="number" id="weeks-{index}"
                        bind:value={$costs[index].plan.weeks}
                        class="rounded-md shadow-sm
                            w-32 px-2 py-1 mt-1 text-right
                            ring-1 ring-inset ring-gray-200 
                            focus:outline-none focus:ring-1 focus:ring-gray-700">
                {/if}
            </div>
            <div class="mt-1 flex items-center justify-end h-[20px]">
                {#if $costs[index].plan.type !== 'lease'}
                    <input type="checkbox" id="all-contract-{index}" class="w-4 h-4"
                        bind:checked={$costs[index].plan.allContract}> 
                    <label for="all-contract-{index}" class="ml-2">All Contract</label>
                {/if}
            </div>
        </div>
    </div>
    {#if $costs[index].plan.buildingArea || $costs[index].plan.siteArea || $costs[index].plan.weeks}
        <div class="p-4 mt-4">
            <div class="flex flex-col gap-y-3.5 text-sm h-[calc(100vh-2.5rem-260px)]">

                <div class="font-semibold">
                    <div class="flex justify-between text-cyan-500 text-lg">
                        <div>MIN</div>
                        <div>{$costs[index].minTotal === 0 ? '-' : `₩ ${$costs[index].minTotal.toLocaleString()}`}</div>
                    </div>
                    <div class="flex justify-between text-purple-500  text-lg">
                        <div>MAX</div>
                        <div>{$costs[index].maxTotal === 0 ? '-' : `₩ ${$costs[index].maxTotal.toLocaleString()}`}</div>
                    </div>
                </div>

                <div>
                    <div class="flex justify-between font-semibold">
                        <div>Support
                            <span class="rounded-md px-1 ml-1
                                text-xs text-cyan-500 ring-1 ring-cyan-500">MIN</span>
                        </div>
                        <div>{$costs[index].support.min === 0 ? '-' : `₩ ${$costs[index].support.min.toLocaleString()}`}</div>
                        
                    </div>
                    <div class="flex justify-between font-semibold">
                        <div>Support
                            <span class="rounded-md px-1 ml-1
                                text-xs text-purple-500 ring-1 ring-purple-500">MAX</span>
                        </div>
                        <div>{$costs[index].support.max === 0 ? '-' : `₩ ${$costs[index].support.max.toLocaleString()}`}</div>
                        
                    </div>
                </div>
                
                <div>
                    <div class="flex justify-between font-semibold">
                        <div>Construction</div>
                        <div>{constructionTotal === 0 ? '-' : `₩ ${constructionTotal.toLocaleString()}`}</div>
                    </div>
                    <div class="flex justify-between text-gray-500">
                        <div>건물 공사</div>
                        <div>{$costs[index].construction.building === 0 ? '-' : `₩ ${$costs[index].construction.building.toLocaleString()}`}</div>
                    </div>
                    <div class="flex justify-between text-gray-500">
                        <div>부지 공사</div>
                        <div>{$costs[index].construction.site === 0 ? '-' : `₩ ${$costs[index].construction.site.toLocaleString()}`}</div>
                    </div>
                </div>

                <div>
                    <div class="flex justify-between font-semibold">
                        <div>Contract
                            <span class="rounded-md px-1 ml-1
                                text-xs text-cyan-500 ring-1 ring-cyan-500">MIN</span>
                        </div>
                        <div>{contractMinTotal === 0 ? '-' : `₩ ${contractMinTotal.toLocaleString()}`}</div>
                    </div>
                    {#each $costs[index].contract.defaults as contract}
                        <div class="flex justify-between text-gray-500">
                            <div>{contract.name}</div>
                            <div>{contract.min === 0 ? '-' : `₩ ${contract.min.toLocaleString()}`}</div>
                        </div>
                    {/each}
                    {#each $costs[index].contract.customs as contract}
                        <div class="flex justify-between text-gray-500">
                            <div>{contract.name}</div>
                            <div>{contract.min === 0 ? '-' : `₩ ${contract.min.toLocaleString()}`}</div>
                        </div>
                    {/each}
                </div>

                <div>
                    <div class="flex justify-between font-semibold">
                        <div>Contract
                            <span class="rounded-md px-1 ml-1
                                text-xs text-purple-500 ring-1 ring-purple-500">MAX</span>
                        </div>
                        <div>{contractMaxTotal === 0 ? '-' : `₩ ${contractMaxTotal.toLocaleString()}`}</div>
                    </div>
                    {#each $costs[index].contract.defaults as contract}
                        <div class="flex justify-between text-gray-500">
                            <div>{contract.name}</div>
                            <div>{contract.max === 0 ? '-' : `₩ ${contract.max.toLocaleString()}`}</div>
                        </div>
                    {/each}
                    {#each $costs[index].contract.customs as contract}
                        <div class="flex justify-between text-gray-500">
                            <div>{contract.name}</div>
                            <div>{contract.max === 0 ? '-' : `₩ ${contract.max.toLocaleString()}`}</div>
                        </div>
                    {/each}
                </div>

                <div>
                    <div class="flex justify-between font-semibold">
                        <div>Risk
                            <span class="rounded-md px-1 ml-1
                                text-xs text-purple-500 ring-1 ring-purple-500">MAX</span>
                        </div>
                        <div>{riskTotal === 0 ? '-' : `₩ ${riskTotal.toLocaleString()}`}</div>
                    </div>
                    {#each $costs[index].risks as risk}
                        <div class="flex justify-between text-gray-500">
                            <div>{risk.desc}</div>
                            <div>{risk.riskAllowance === 0 ? '-' : `₩ ${risk.riskAllowance.toLocaleString()}`}</div>
                        </div>
                    {/each}
                </div>

                <div class="flex justify-end mt-4">
                    <button class="p-1 rounded-sm text-gray-700 hover:bg-gray-100 active:bg-gray-200 cursor-pointer"
                        class:bg-gray-200={selectedIndex == index}
                        on:click={pleaseEditCost}>
                        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="w-6 h-6">
                            <path d="M18.75 12.75h1.5a.75.75 0 000-1.5h-1.5a.75.75 0 000 1.5zM12 6a.75.75 0 01.75-.75h7.5a.75.75 0 010 1.5h-7.5A.75.75 0 0112 6zM12 18a.75.75 0 01.75-.75h7.5a.75.75 0 010 1.5h-7.5A.75.75 0 0112 18zM3.75 6.75h1.5a.75.75 0 100-1.5h-1.5a.75.75 0 000 1.5zM5.25 18.75h-1.5a.75.75 0 010-1.5h1.5a.75.75 0 010 1.5zM3 12a.75.75 0 01.75-.75h7.5a.75.75 0 010 1.5h-7.5A.75.75 0 013 12zM9 3.75a2.25 2.25 0 100 4.5 2.25 2.25 0 000-4.5zM12.75 12a2.25 2.25 0 114.5 0 2.25 2.25 0 01-4.5 0zM9 15.75a2.25 2.25 0 100 4.5 2.25 2.25 0 000-4.5z" />
                        </svg>          
                    </button>
                </div>

            </div>
        </div>
    {/if}
</div>