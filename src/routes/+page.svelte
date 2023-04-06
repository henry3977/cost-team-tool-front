<script>
    import { onMount } from 'svelte';
    import { costs } from '../stores.js';
    import Calculator from '../components/Calculator.svelte';
    import EditCost from '../components/EditCost.svelte';
    import EditDefaultUnitPrice from '../components/EditDefaultUnitPrice.svelte';

    let setDefaultUnitPrice = false;
    let selectedIndex = null;
    let defaultUnitPrice = {
        support: {
            ldc: 8000000,
            contract: 3540000,
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

    onMount(() => {
        if (localStorage.getItem('defaultUnitPrice') === null) localStorage.setItem('defaultUnitPrice', JSON.stringify(defaultUnitPrice));
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
            risk: {
                contract: {},
                customs: []
            },

            minTotal: 0,
            maxTotal: 0
        }]);
    }

    function deleteCosts(event) {
        let result = true;
        if ($costs[event.detail.index].plan.buildingArea 
            || $costs[event.detail.index].plan.siteArea 
            || $costs[event.detail.index].plan.weeks) {
            result = confirm('삭제할거야?');
        } 
        if (result) {
            $costs.splice(event.detail.index, 1);
            $costs = $costs;
        }
        selectedIndex = null;
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
    <div class="h-10 px-2 flex justify-end items-center gap-1 border-b">

        <button class="px-4 py-1 rounded-lg
            text-gray-700 hover:bg-gray-100 active:bg-gray-200"
            on:click={newCalculator}>
            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
                <path stroke-linecap="round" stroke-linejoin="round" d="M12 6v12m6-6H6" />
            </svg>
        </button>
        
        <button class="px-4 py-1 rounded-lg cursor-pointer 
            text-gray-700 hover:bg-gray-100 active:bg-gray-200"
            class:bg-gray-200={setDefaultUnitPrice}
            on:click={() => setDefaultUnitPrice = !setDefaultUnitPrice }>
            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
                <path stroke-linecap="round" stroke-linejoin="round" d="M9.594 3.94c.09-.542.56-.94 1.11-.94h2.593c.55 0 1.02.398 1.11.94l.213 1.281c.063.374.313.686.645.87.074.04.147.083.22.127.324.196.72.257 1.075.124l1.217-.456a1.125 1.125 0 011.37.49l1.296 2.247a1.125 1.125 0 01-.26 1.431l-1.003.827c-.293.24-.438.613-.431.992a6.759 6.759 0 010 .255c-.007.378.138.75.43.99l1.005.828c.424.35.534.954.26 1.43l-1.298 2.247a1.125 1.125 0 01-1.369.491l-1.217-.456c-.355-.133-.75-.072-1.076.124a6.57 6.57 0 01-.22.128c-.331.183-.581.495-.644.869l-.213 1.28c-.09.543-.56.941-1.11.941h-2.594c-.55 0-1.02-.398-1.11-.94l-.213-1.281c-.062-.374-.312-.686-.644-.87a6.52 6.52 0 01-.22-.127c-.325-.196-.72-.257-1.076-.124l-1.217.456a1.125 1.125 0 01-1.369-.49l-1.297-2.247a1.125 1.125 0 01.26-1.431l1.004-.827c.292-.24.437-.613.43-.992a6.932 6.932 0 010-.255c.007-.378-.138-.75-.43-.99l-1.004-.828a1.125 1.125 0 01-.26-1.43l1.297-2.247a1.125 1.125 0 011.37-.491l1.216.456c.356.133.751.072 1.076-.124.072-.044.146-.087.22-.128.332-.183.582-.495.644-.869l.214-1.281z" />
                <path stroke-linecap="round" stroke-linejoin="round" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" />
            </svg>
        </button>
            

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
        {#if setDefaultUnitPrice}
            <EditDefaultUnitPrice 
                on:closeEditDefaultUnitPrice={() => {setDefaultUnitPrice = false }}/>
        {/if}
    </div>
</div>