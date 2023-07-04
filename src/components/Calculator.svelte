<script>
    import { afterUpdate } from 'svelte';
	import { createEventDispatcher } from 'svelte';
    import { costs } from '../stores.js';

	const dispatch = createEventDispatcher();
    export let index;
    export let selectedIndex;
    let type = '';
    let cnstrOption = '';
    let support = 0;
    let building = 0;
    let piloti = 0;
    let site = 0;
    let siteDemolition = 0;
    let requiredContract = 0;
    let risk = 0;
    let contractTotal = 0;
    let constructionTotal = 0;
    let riskTotal = 0;
    let max = 0;

    if ($costs[index].unitPrice === undefined) $costs[index].unitPrice = JSON.parse(localStorage.getItem('defaultUnitPrice'));
    setOption();

    afterUpdate(() => {
        setOption();
        if ($costs[index].plan.buildingArea || $costs[index].plan.siteArea || $costs[index].plan.weeks) calculators();
    });

    function setOption() {
        type = $costs[index].plan.type;
        cnstrOption = $costs[index].plan.allContract ? 'contract' : 'ldc';
        if ($costs[index].unitPrice[type][cnstrOption].piloti === undefined) $costs[index].plan.piloti = false;
        if ($costs[index].unitPrice[type][cnstrOption].siteDemolition === undefined) $costs[index].plan.rebuild = false;
    }

    function calculators() {
        support = Math.floor($costs[index].plan.weeks * $costs[index].unitPrice.support[cnstrOption]);
        building = Math.floor($costs[index].plan.buildingArea * $costs[index].unitPrice[type][cnstrOption].building);
        site = $costs[index].unitPrice[type][cnstrOption].site !== undefined
            ? Math.floor($costs[index].plan.siteArea * $costs[index].unitPrice[type][cnstrOption].site)
            : 0;
        piloti = $costs[index].plan.piloti
            ? Math.floor($costs[index].plan.floorArea * $costs[index].unitPrice[type][cnstrOption].piloti)
            : 0;
        siteDemolition = $costs[index].plan.rebuild
            ? Math.floor($costs[index].plan.siteArea * $costs[index].unitPrice[type][cnstrOption].siteDemolition)
            : 0;
        requiredContract = Math.floor($costs[index].plan.buildingArea * $costs[index].unitPrice[type].requiredContract);

        $costs[index].risk.customs.forEach(risk => {
            risk.riskAllowance = Math.round( ((risk.min + risk.max) / 2) * ((risk.probability * risk.impact) / 10) );
        });

        constructionTotal = building + site + piloti + siteDemolition;
        contractTotal = requiredContract;
        risk = Math.floor( ($costs[index].plan.risk / 100) * (constructionTotal + contractTotal) );
        riskTotal = risk + $costs[index].risk.customs.reduce((a, b) => a + b.riskAllowance, 0);

        max = support + constructionTotal + contractTotal + riskTotal;
    }


    // function calculators() {
    //     defaultUnitPrice = JSON.parse(localStorage.getItem('defaultUnitPrice'));
    //     if ($costs[index].$costs[index].unitPrice.support === undefined) $costs[index].$costs[index].unitPrice.support = default$costs[index].unitPrice.support;
    //     if ($costs[index].$costs[index].unitPrice.contract === undefined) $costs[index].$costs[index].unitPrice.contract = default$costs[index].unitPrice.contract;
    //     if ($costs[index].$costs[index].unitPrice.construction === undefined) $costs[index].$costs[index].unitPrice.construction = defaultUnitPrice[$costs[index].plan.type];

    //     $costs[index].$costs[index].unitPrice.construction = defaultUnitPrice[$costs[index].plan.type];
    //     $costs[index].support.min = 0;
    //     $costs[index].support.max = 0;
    //     if ($costs[index].plan.type !== 'lease') {
    //         $costs[index].support.min = $costs[index].plan.allContract 
    //             ? Math.round($costs[index].plan.weeks * $costs[index].$costs[index].unitPrice.support.contract)
    //             : Math.round($costs[index].plan.weeks * $costs[index].$costs[index].unitPrice.support.ldc);
    //         $costs[index].support.max = Math.round($costs[index].support.min * ($costs[index].$costs[index].unitPrice.support.maxRate / 100));
    //     }

    //     $costs[index].construction.building = 0;
    //     $costs[index].construction.site = 0;
    //     if (!$costs[index].plan.allContract) {
    //         $costs[index].construction.building = Math.round($costs[index].plan.buildingArea * $costs[index].$costs[index].unitPrice.construction.building);
    //         $costs[index].construction.site = Math.round($costs[index].plan.siteArea * $costs[index].$costs[index].unitPrice.construction.site);
    //     }

    //     $costs[index].contract.defaults = [];
    //     if ($costs[index].plan.type === 'lease') {
    //         $costs[index].contract.defaults.push({
    //             name: '상가 내부 인테리어',
    //             min: Math.round($costs[index].$costs[index].unitPrice.construction.min * $costs[index].plan.buildingArea),
    //             max: Math.round($costs[index].$costs[index].unitPrice.construction.max * $costs[index].plan.buildingArea)
    //         });
    //     } else {
    //         $costs[index].contract.defaults.push({
    //             name: '전기 통신 소방',
    //             min: Math.round($costs[index].plan.buildingArea * $costs[index].$costs[index].unitPrice.construction.elect),
    //             max: Math.round($costs[index].plan.buildingArea * $costs[index].$costs[index].unitPrice.construction.elect * ($costs[index].$costs[index].unitPrice.contract.electMaxRate / 100))
    //         });
    //         if ($costs[index].plan.allContract) {
    //             let constructionTotal = Math.round($costs[index].plan.buildingArea * $costs[index].$costs[index].unitPrice.construction.building) + Math.round($costs[index].plan.siteArea * $costs[index].$costs[index].unitPrice.construction.site);
    //             $costs[index].contract.defaults.push({
    //                 name: '건축',
    //                 min: Math.round(constructionTotal * ($costs[index].$costs[index].unitPrice.contract.minRate / 100)),
    //                 max: Math.round(constructionTotal * ($costs[index].$costs[index].unitPrice.contract.maxRate / 100))
    //             });
    //         }
    //     }
    //     let costContractMinTotal = $costs[index].contract.defaults.reduce((a, b) => a + b.min, 0) + $costs[index].contract.customs.reduce((a, b) => a + b.min, 0);
    //     let costContractMaxTotal = $costs[index].contract.defaults.reduce((a, b) => a + b.max, 0) + $costs[index].contract.customs.reduce((a, b) => a + b.max, 0);

    //     if ($costs[index].plan.type === 'lease') $costs[index].risk.contract = {};
    //     if ($costs[index].plan.type !== 'lease' && Object.keys($costs[index].risk.contract).length === 0) {
    //         $costs[index].risk.contract = {
    //             id: 'contract',
    //             desc: '도급 계약 관리',
    //             probability: 3,
    //             impact: 3,
    //             min: Math.round(costContractMinTotal * 0.1),
    //             max: Math.round(costContractMaxTotal * 0.1),
    //             riskAllowance: Math.round( ((Math.round(costContractMinTotal * 0.1) + Math.round(costContractMaxTotal * 0.1)) / 2) * ((9) / 10) )
    //         };
    //     }
    //     if (Object.keys($costs[index].risk.contract).length !== 0) {
    //         $costs[index].risk.contract.min = Math.round(costContractMinTotal * 0.1);
    //         $costs[index].risk.contract.max = Math.round(costContractMaxTotal * 0.1);
    //         $costs[index].risk.contract.riskAllowance = Math.round(
    //             (($costs[index].risk.contract.min + $costs[index].risk.contract.max) / 2) * (($costs[index].risk.contract.probability * $costs[index].risk.contract.impact) / 10) );
    //     }
    //     $costs[index].risk.customs.forEach(risk => {
    //         risk.riskAllowance = Math.round( ((risk.min + risk.max) / 2) * ((risk.probability * risk.impact) / 10) );
    //     });

    //     $costs[index].minTotal = 0;
    //     $costs[index].maxTotal = 0;
    //     $costs[index].minTotal = $costs[index].support.min + costContractMinTotal;
    //     $costs[index].maxTotal = $costs[index].support.max + costContractMaxTotal + getRiskTotal($costs[index].risk);
    //     if (!$costs[index].plan.allContract) {
    //         $costs[index].minTotal = $costs[index].minTotal + $costs[index].construction.building + $costs[index].construction.site;
    //         $costs[index].maxTotal = $costs[index].maxTotal + $costs[index].construction.building + $costs[index].construction.site;
    //     }
    //     pleaseSetProjects();
    // }

    function getRiskTotal(risk) {
        let riskTotal = Object.keys($costs[index].risk.contract).length !== 0 ? risk.contract.riskAllowance : 0;
        return riskTotal + risk.customs.reduce((a, b) => a + b.riskAllowance, 0);
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

    function pleaseSetProjects() {
        dispatch('pleaseSetProjects');
    }

</script>

<div class="shrink-0 w-80 border-r overflow-auto">
    <div class="p-4 relative h-64">
        <button class="cursor-pointer absolute top-2 right-2 p-1 text-gray-700 hover:bg-gray-100 active:bg-gray-200 rounded-full pdf-hide"
            on:click={pleaseDeleteMe}>
            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="w-6 h-6">
                <path fill-rule="evenodd" d="M5.47 5.47a.75.75 0 011.06 0L12 10.94l5.47-5.47a.75.75 0 111.06 1.06L13.06 12l5.47 5.47a.75.75 0 11-1.06 1.06L12 13.06l-5.47 5.47a.75.75 0 01-1.06-1.06L10.94 12 5.47 6.53a.75.75 0 010-1.06z" clip-rule="evenodd" />
            </svg>              
        </button>
        <div class="text-center text-2xl font-semibold">
            <select class="appearance-none text-2xl font-semibold"
                name="type"
                bind:value={$costs[index].plan.type}>
                <option value="new">신축</option>
                <option value="major">개축</option>
                <option value="store">상가</option>
                <!-- <option value="lease">임대</option> -->
            </select>
        </div>

        <div class="flex flex-col text-sm font-semibold">
            <div class="py-4 flex items-center justify-between flex-row-reverse gap-x-6">
                <div class="flex items-center">
                    <input type="checkbox" id="all-contract-{index}" class="w-4 h-4"
                        bind:checked={$costs[index].plan.allContract}> 
                    <label for="all-contract-{index}" class="ml-1">전체도급</label>
                </div>
                {#if $costs[index].unitPrice[type][cnstrOption].piloti !== undefined}
                <div class="flex items-center">
                    <input type="checkbox" id="piloti-{index}" class="w-4 h-4"
                        bind:checked={$costs[index].plan.piloti}> 
                    <label for="piloti-{index}" class="ml-1">필로티</label>
                </div>
                {/if}
                {#if $costs[index].unitPrice[type][cnstrOption].siteDemolition !== undefined}
                <div class="flex items-center">
                    <input type="checkbox" id="rebuild-{index}" class="w-4 h-4"
                        bind:checked={$costs[index].plan.rebuild}> 
                    <label for="rebuild-{index}" class="ml-1">재건축</label>
                </div>
                {/if}
            </div>
            <!-- <div class="flex justify-between items-end">
                <label for="comment-{index}">비고</label>
                <input type="text" id="comment-{index}"
                    bind:value={$costs[index].plan.comment}
                    class="px-2 text-right                     
                        text-cyan-500 text-lg
                        border-b focus:outline-none focus:border-b focus:border-gray-400">
            </div> -->
            <div class="flex justify-between items-end">
                <label for="building-area-{index}">건축 면적</label>
                <input type="number" id="building-area-{index}"
                    bind:value={$costs[index].plan.buildingArea}
                    placeholder="제곱미터"
                    class="w-28 px-2 text-right text-base
                        border-b focus:outline-none focus:border-b focus:border-gray-400">
            </div>
            {#if $costs[index].unitPrice[type][cnstrOption].site !== undefined}
            <div class="flex justify-between items-end">
                <label for="site-area-{index}">부지 면적</label>
                <input type="number" id="site-area-{index}"
                    bind:value={$costs[index].plan.siteArea}
                    placeholder="제곱미터"
                    class="w-28 px-2 text-right text-base
                        border-b focus:outline-none focus:border-b focus:border-gray-400">
            </div>
            {/if}
            {#if $costs[index].plan.piloti}
            <div class="flex justify-between items-end">
                <label for="floor-area-{index}">단층 면적</label>
                <input type="number" id="floor-area-{index}"
                    bind:value={$costs[index].plan.floorArea}
                    placeholder="제곱미터"
                    class="w-28 px-2 text-right text-base
                        border-b focus:outline-none focus:border-b focus:border-gray-400">
            </div>
            {/if}
            <div class="flex justify-between items-end">
                <label for="weeks-{index}">공사 기간(주)</label>
                <input type="number" id="weeks-{index}"
                    bind:value={$costs[index].plan.weeks}
                    placeholder="주"
                    class="w-28 px-2 text-right text-base
                        border-b focus:outline-none focus:border-b focus:border-gray-400">
            </div>
            <div class="flex justify-between items-end">
                <label for="risk-{index}">리스크(%)</label>
                <input type="number" id="risk-{index}"
                    bind:value={$costs[index].plan.risk}
                    placeholder="%"
                    class="w-28 px-2 text-right text-base
                        border-b focus:outline-none focus:border-b focus:border-gray-400">
            </div>
        </div>
    </div>
    {#if $costs[index].plan.buildingArea || $costs[index].plan.siteArea || $costs[index].plan.weeks}
        <div class="p-4">
            <div class="flex flex-col gap-y-2 text-sm">
                <div class="font-semibold">
                    <div class="flex justify-between text-cyan-500 text-lg">
                        <div>MAX</div>
                        <div>{max.toLocaleString()}</div>
                    </div>
                </div>
                <div>
                    <div class="flex justify-between font-semibold">
                        <div>Support</div>
                        <div>{support.toLocaleString()}</div>
                    </div>
                </div>
                <div>
                    <div class="flex justify-between font-semibold">
                        <div>Construction</div>
                        <div>{constructionTotal.toLocaleString()}</div>
                    </div>
                    <div class="flex justify-between text-gray-500">
                        <div>건물 공사
                            {#if $costs[index].plan.allContract}
                                <span class="text-purple-500">(도급)</span>
                            {/if}
                        </div>
                        <div>{building.toLocaleString()}</div>
                    </div>
                    {#if piloti}
                    <div class="flex justify-between text-gray-500">
                        <div>필로티
                            {#if $costs[index].plan.allContract}
                                <span class="text-purple-500">(도급)</span>
                            {/if}
                        </div>
                        <div>{piloti.toLocaleString()}</div>
                    </div>
                    {/if}
                    <div class="flex justify-between text-gray-500">
                        <div>부지 공사
                            {#if $costs[index].plan.allContract}
                                <span class="text-purple-500">(도급)</span>
                            {/if}
                        </div>
                        <div>{site.toLocaleString()}</div>
                    </div>
                    {#if siteDemolition}
                    <div class="flex justify-between text-gray-500">
                        <div>부지 철거
                            {#if $costs[index].plan.allContract}
                                <span class="text-purple-500">(도급)</span>
                            {/if}
                        </div>
                        <div>{siteDemolition.toLocaleString()}</div>
                    </div>
                    {/if}
                    <div class="flex justify-between text-gray-500">
                        <div>전통소 
                            <span class="text-purple-500">(도급)</span>
                        </div>
                        <div>{requiredContract.toLocaleString()}</div>
                    </div>
                </div>
                <div>
                    <div class="flex justify-between font-semibold text-red-500">
                        <div>Risk</div>
                        <div>{riskTotal.toLocaleString()}</div>
                    </div>
                    {#if $costs[index].plan.risk}
                    <div class="flex justify-between text-gray-500">
                        <div>건축비용{$costs[index].plan.risk}%</div>
                        <div>{risk.toLocaleString()}</div>
                    </div>
                    {/if}
                    {#each $costs[index].risk.customs as risk}
                        <div class="flex justify-between text-gray-500">
                            <div>{risk.desc}</div>
                            <div>{risk.riskAllowance.toLocaleString()}</div>
                        </div>
                    {/each}
                </div>
                <!-- <div>
                    {#each $costs[index].contract.defaults as contract}
                        <div class="flex justify-between text-gray-500">
                            <div>{contract.name}</div>
                            <div></div>
                        </div>
                    {/each}
                    {#each $costs[index].contract.customs as contract}
                        <div class="flex justify-between text-gray-500">
                            <div>{contract.name}</div>
                            <div>{contract.max === null ? '-' : `₩ ${contract.max.toLocaleString()}`}</div>
                        </div>
                    {/each}
                </div> -->
                <div class="flex justify-end mt-4">
                    <button class="p-1 rounded-sm text-gray-700 hover:bg-gray-100 active:bg-gray-200 cursor-pointer pdf-hide"
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