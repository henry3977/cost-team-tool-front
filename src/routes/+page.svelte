<script>
    import { onMount, afterUpdate } from 'svelte';
    import { costs } from '../stores.js';
    import Calculator from '../components/Calculator.svelte';
    import EditCost from '../components/EditCost.svelte';
    import EditDefaultUnitPrice from '../components/EditDefaultUnitPrice.svelte';
    import html2canvas from 'html2canvas';
    import { jsPDF } from "jspdf";

    let setDefaultUnitPrice = false;
    let selectedIndex = null;
    let selProjectIdx = null;
    let projects = [];
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
        if (localStorage.getItem('projects') === null) localStorage.setItem('projects', JSON.stringify([]));
        if (localStorage.getItem('projects') === null) newProject('새 프로젝트');
		projects = JSON.parse(localStorage.getItem('projects'));
        if (projects.length === 0) newProject('새 프로젝트');
        selProjectIdx = projects.length - 1;
        costs.update(n => n = projects[selProjectIdx].costs);
	});

    function newCost() {
        costs.update(n => n = [...$costs, getDefaultCost()]);
        setProjectCosts();
    }

    function getDefaultCost() {
        // defaultUnitPrice = JSON.parse(localStorage.getItem('defaultUnitPrice'));
        // if ($costs[index].unitPrice.support === undefined) $costs[index].unitPrice.support = defaultUnitPrice.support;
        // if ($costs[index].unitPrice.contract === undefined) $costs[index].unitPrice.contract = defaultUnitPrice.contract;
        // if ($costs[index].unitPrice.construction === undefined) $costs[index].unitPrice.construction = defaultUnitPrice[$costs[index].plan.type];

        return {
            unitPrice: {},
            plan: {
                type:'new',
                comment: null,
                buildingArea: null,
                siteArea: null,
                weeks: null,
                allContract: false
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
        };
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

    function getPDF() {
        let calculators = document.getElementById('calculators');
        let btns = document.getElementsByClassName('pdf-hide');
        console.log(btns)
        Array.from(btns).forEach((element) => {
            element.classList.toggle('hidden');
        });
        // calculators.classList.toggle("overflow-x-scroll");
        calculators.classList.toggle('fixed');
        html2canvas(calculators).then(function(canvas) {
            // document.body.appendChild(canvas);
            // calculators.classList.toggle("overflow-x-scroll");
            calculators.classList.toggle('fixed');
            Array.from(btns).forEach((element) => {
                element.classList.toggle('hidden');
            });
            const imgData = canvas.toDataURL('image/png');
            const doc = new jsPDF({
                orientation: 'l', // landscape
                unit: 'pt', // points, pixels won't work properly
                format: [canvas.width, canvas.height] // set needed dimensions for any element
            });

            // doc.addImage(imgData, "PNG", 15, 40, 180, 180);
            // doc.setFontSize(45);
            // doc.text(projects[selProjectIdx].name, 35, 45);
            doc.addImage(imgData, "PNG", 0, 0, canvas.width, canvas.height);
            doc.save(`${projects[selProjectIdx].name}.pdf`); 

        });
        // const doc = new jsPDF();
        // doc.text("Hello world!", 10, 10);
        // doc.save("a4.pdf"); 
    }

    function newProject(name = null) {
        if (name === null) name = prompt('프로젝트 이름?');
        const project = {
            name,
            costs: [getDefaultCost()]
        }
        projects = [...projects, project];
        selProjectIdx = projects.length - 1;
        localStorage.setItem('projects', JSON.stringify(projects));
    }

    function setProjectName(projectIdx) {
        const name = prompt('프로젝트 이름?', projects[projectIdx].name);
        if (name) projects[projectIdx].name = name;
        if (name === '') alert('무엇이든 입력해야해');
    }

    function selProject(projectIdx) {
        if (selProjectIdx == projectIdx) {
            setProjectName(projectIdx);
        } else {
            selProjectIdx = projectIdx;
            costs.update(n => n = projects[selProjectIdx].costs);
        }
    }

    // function delProject(projectIdx) {
    //     if (confirm('삭제할거야?')) {
    //         projects.splice(projectIdx, 1);
    //         projects = projects;
    //         localStorage.setItem('projects', JSON.stringify(projects));
    //         selProjectIdx = projects.length - 1;
    //     }
    // }

    function setProjectCosts() {
        projects[selProjectIdx].costs = $costs;
        localStorage.setItem('projects', JSON.stringify(projects));
    }

    afterUpdate(() => {
        setProjectCosts();
    });
</script>

<div class="h-screen">
    <div class="h-9 px-1 border-b flex justify-between items-center">
        <div class="flex items-center gap-1 pl-1">
            <!-- <button class="px-2 py-0.5 rounded-lg
                text-gray-700 hover:bg-gray-100 active:bg-gray-200"
                on:click={() => newProject()}>
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M19.5 14.25v-2.625a3.375 3.375 0 00-3.375-3.375h-1.5A1.125 1.125 0 0113.5 7.125v-1.5a3.375 3.375 0 00-3.375-3.375H8.25m3.75 9v6m3-3H9m1.5-12H5.625c-.621 0-1.125.504-1.125 1.125v17.25c0 .621.504 1.125 1.125 1.125h12.75c.621 0 1.125-.504 1.125-1.125V11.25a9 9 0 00-9-9z" />
                  </svg>                  
            </button> -->
            {#each projects as project, i}
                <div class="flex items-center rounded-lg text-sm text-gray-700 bg-gray-100 hover:bg-gray-200 active:bg-gray-300"
                    class:bg-gray-300={selProjectIdx === i}>
                    <button class="px-2 py-0.5 before:text-sm"
                        on:click={() => selProject(i)}>
                        {project.name}
                    </button>
                    <!-- <button class="px-1 py-0.5 hover:text-cyan-600"
                        on:click={() => delProject(i)}>
                        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" class="w-5 h-5">
                            <path d="M6.28 5.22a.75.75 0 00-1.06 1.06L8.94 10l-3.72 3.72a.75.75 0 101.06 1.06L10 11.06l3.72 3.72a.75.75 0 101.06-1.06L11.06 10l3.72-3.72a.75.75 0 00-1.06-1.06L10 8.94 6.28 5.22z" />
                        </svg>
                    </button> -->
                </div>
            {/each}
        </div>
        <div class="flex items-center gap-2">
            <button class="px-2 py-0.5 rounded-lg
                text-gray-700 hover:bg-gray-100 active:bg-gray-200"
                on:click={newCost}>
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M12 6v12m6-6H6" />
                </svg>
            </button>
        
            <button class="px-2 py-0.5 rounded-lg
                text-gray-700 hover:bg-gray-100 active:bg-gray-200"
                on:click={getPDF}>
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M19.5 14.25v-2.625a3.375 3.375 0 00-3.375-3.375h-1.5A1.125 1.125 0 0113.5 7.125v-1.5a3.375 3.375 0 00-3.375-3.375H8.25m.75 12l3 3m0 0l3-3m-3 3v-6m-1.5-9H5.625c-.621 0-1.125.504-1.125 1.125v17.25c0 .621.504 1.125 1.125 1.125h12.75c.621 0 1.125-.504 1.125-1.125V11.25a9 9 0 00-9-9z" />
                </svg>              
            </button>
        </div>
        <div class="flex items-center gap-2">           
            <button class="px-2 py-0.5 rounded-lg cursor-pointer 
                text-gray-700 hover:bg-gray-100 active:bg-gray-200"
                class:bg-gray-200={setDefaultUnitPrice}
                on:click={() => setDefaultUnitPrice = !setDefaultUnitPrice }>
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M9.594 3.94c.09-.542.56-.94 1.11-.94h2.593c.55 0 1.02.398 1.11.94l.213 1.281c.063.374.313.686.645.87.074.04.147.083.22.127.324.196.72.257 1.075.124l1.217-.456a1.125 1.125 0 011.37.49l1.296 2.247a1.125 1.125 0 01-.26 1.431l-1.003.827c-.293.24-.438.613-.431.992a6.759 6.759 0 010 .255c-.007.378.138.75.43.99l1.005.828c.424.35.534.954.26 1.43l-1.298 2.247a1.125 1.125 0 01-1.369.491l-1.217-.456c-.355-.133-.75-.072-1.076.124a6.57 6.57 0 01-.22.128c-.331.183-.581.495-.644.869l-.213 1.28c-.09.543-.56.941-1.11.941h-2.594c-.55 0-1.02-.398-1.11-.94l-.213-1.281c-.062-.374-.312-.686-.644-.87a6.52 6.52 0 01-.22-.127c-.325-.196-.72-.257-1.076-.124l-1.217.456a1.125 1.125 0 01-1.369-.49l-1.297-2.247a1.125 1.125 0 01.26-1.431l1.004-.827c.292-.24.437-.613.43-.992a6.932 6.932 0 010-.255c.007-.378-.138-.75-.43-.99l-1.004-.828a1.125 1.125 0 01-.26-1.43l1.297-2.247a1.125 1.125 0 011.37-.491l1.216.456c.356.133.751.072 1.076-.124.072-.044.146-.087.22-.128.332-.183.582-.495.644-.869l.214-1.281z" />
                    <path stroke-linecap="round" stroke-linejoin="round" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" />
                </svg>
            </button> 
        </div>

    </div>
    <div class="flex h-[calc(100vh-2.25rem)]">
        <div class="grow overflow-auto flex" id="calculators">
            {#each $costs as cost, index (index)}
                <Calculator {index}
                    {selectedIndex}
                    on:pleaseSetProjectCosts={setProjectCosts}
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