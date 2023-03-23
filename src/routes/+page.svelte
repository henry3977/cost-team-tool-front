<script>
    import { onMount } from 'svelte';
    import { costs } from '../stores.js';
    import Calculator from '../components/Calculator.svelte';
    import EditCost from '../components/EditCost.svelte';

    let selectedIndex= null;
    // let projects= [{
    //     name: '새 프로젝트',
    //     costs: []
    // }];
    onMount(() => {
		newCalculator();
	});

    function newCalculator() {
        costs.update(n => n = [...$costs, {
            id: $costs.length + 1,
            unitPrice: {},
            plan: {
                buildingArea: null,
                siteArea: null,
                weeks: null,
                allContract: false,
                type:'new'
            },
            support: {
                min: 0,
                max: 0
            },
            construction: {
                building: 0,
                site: 0
            },
            contract: {
                defaults: [],
                customs: []
            },
            risks: [],
            minTotal: 0,
            maxTotal: 0
        }]);
    }

    function deleteCosts(event) {
        if (confirm('삭제할거야?')) {
            $costs.splice(event.detail.index, 1);
            $costs = $costs;
        }
    }

    function editCost(event) {
        if (selectedIndex === event.detail.index) {
            selectedIndex = null;
        } else {
            selectedIndex = event.detail.index;
        }
    }

    function closeEditCost() {
        selectedIndex = null;
    }

</script>

<div class="h-screen">
    <div class="bg-stone-300 ring-[0.5px] ring-stone-400 h-10 px-2 flex justify-start items-center gap-1">

        <!-- <button class="align-middle px-4 m-1 text-gray-700 hover:bg-stone-200 active:bg-stone-100 rounded-lg"
            on:click={newCalculator}>
            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="w-6 h-6">
                <path fill-rule="evenodd" d="M6.32 1.827a49.255 49.255 0 0111.36 0c1.497.174 2.57 1.46 2.57 2.93V19.5a3 3 0 01-3 3H6.75a3 3 0 01-3-3V4.757c0-1.47 1.073-2.756 2.57-2.93zM7.5 11.25a.75.75 0 01.75-.75h.008a.75.75 0 01.75.75v.008a.75.75 0 01-.75.75H8.25a.75.75 0 01-.75-.75v-.008zm.75 1.5a.75.75 0 00-.75.75v.008c0 .414.336.75.75.75h.008a.75.75 0 00.75-.75V13.5a.75.75 0 00-.75-.75H8.25zm-.75 3a.75.75 0 01.75-.75h.008a.75.75 0 01.75.75v.008a.75.75 0 01-.75.75H8.25a.75.75 0 01-.75-.75v-.008zm.75 1.5a.75.75 0 00-.75.75v.008c0 .414.336.75.75.75h.008a.75.75 0 00.75-.75V18a.75.75 0 00-.75-.75H8.25zm1.748-6a.75.75 0 01.75-.75h.007a.75.75 0 01.75.75v.008a.75.75 0 01-.75.75h-.007a.75.75 0 01-.75-.75v-.008zm.75 1.5a.75.75 0 00-.75.75v.008c0 .414.335.75.75.75h.007a.75.75 0 00.75-.75V13.5a.75.75 0 00-.75-.75h-.007zm-.75 3a.75.75 0 01.75-.75h.007a.75.75 0 01.75.75v.008a.75.75 0 01-.75.75h-.007a.75.75 0 01-.75-.75v-.008zm.75 1.5a.75.75 0 00-.75.75v.008c0 .414.335.75.75.75h.007a.75.75 0 00.75-.75V18a.75.75 0 00-.75-.75h-.007zm1.754-6a.75.75 0 01.75-.75h.008a.75.75 0 01.75.75v.008a.75.75 0 01-.75.75h-.008a.75.75 0 01-.75-.75v-.008zm.75 1.5a.75.75 0 00-.75.75v.008c0 .414.336.75.75.75h.008a.75.75 0 00.75-.75V13.5a.75.75 0 00-.75-.75h-.008zm-.75 3a.75.75 0 01.75-.75h.008a.75.75 0 01.75.75v.008a.75.75 0 01-.75.75h-.008a.75.75 0 01-.75-.75v-.008zm.75 1.5a.75.75 0 00-.75.75v.008c0 .414.336.75.75.75h.008a.75.75 0 00.75-.75V18a.75.75 0 00-.75-.75h-.008zm1.748-6a.75.75 0 01.75-.75h.008a.75.75 0 01.75.75v.008a.75.75 0 01-.75.75h-.008a.75.75 0 01-.75-.75v-.008zm.75 1.5a.75.75 0 00-.75.75v.008c0 .414.336.75.75.75h.008a.75.75 0 00.75-.75V13.5a.75.75 0 00-.75-.75h-.008zm-8.25-6A.75.75 0 018.25 6h7.5a.75.75 0 01.75.75v.75a.75.75 0 01-.75.75h-7.5a.75.75 0 01-.75-.75v-.75zm9 9a.75.75 0 00-1.5 0V18a.75.75 0 001.5 0v-2.25z" clip-rule="evenodd" />
            </svg>
        </button> -->

        <!-- {#each projects as project }
            <div class="px-4 py-1 rounded-lg cursor-pointer text-sm bg-stone-100">
                {project.name}
            </div>
        {/each} -->
        <button class="px-4 py-1 rounded-lg cursor-pointer 
            bg-stone-200 hover:bg-slate-100 active:bg-slate-50"
            on:click={newCalculator}>+</button>

    </div>
    <div class="flex h-[calc(100vh-2.5rem)]">
        <div class="grow overflow-x-scroll flex">
            {#each $costs as cost, index (cost.id)}
                <Calculator {index}
                    {selectedIndex}
                    on:pleaseDeleteMe={deleteCosts}
                    on:pleaseEditCost={editCost}/>
            {/each}
        </div>
        {#if selectedIndex !== null}
            <EditCost index={selectedIndex}
                on:pleaseCloseEditCost={closeEditCost}/>
        {/if}
    </div>
</div>