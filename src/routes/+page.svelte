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

    afterUpdate(() => {
        setProjects();
    });

    function getDefaultCost() {
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

    function newCost() {
        costs.update(n => n = [...$costs, getDefaultCost()]);
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

    function newProject(name = null) {
        if (name === null) name = prompt('프로젝트 이름', '새 프로젝트');
        if (name === '') {
            alert('무엇이든 입력해야 해');
            return;
        }

        if (name !== null) {
            const project = {
                name,
                costs: [getDefaultCost()]
            }
            projects = [...projects, project];
            setProject(projects.length - 1);
        }
    }

    function setProjectName(projectIdx) {
        const name = prompt('프로젝트 이름', projects[projectIdx].name);
        if (name !== null) projects[projectIdx].name = name;
        if (name === '') alert('무엇이든 입력해야 해');
    }

    function selProject(projectIdx) {
        if (selProjectIdx === projectIdx) {
            setProjectName(projectIdx);
        } else {
            setProject(projectIdx);
        }
    }    
    
    function setProject(projectIdx) {
        let projectCosts = [];
        selProjectIdx = projectIdx;
        if (selProjectIdx !== null) projectCosts = projects[selProjectIdx].costs;
        costs.update(n => n = projectCosts);
        selectedIndex = null;
    }

    function delProject(projectIdx) {
        if (confirm('삭제할 거야?')) {
            projects.splice(projectIdx, 1);
            projects = projects;
            let index = projects.length === 0 ? null : projects.length - 1;
            setProject(index);
        }
    }

    function setProjects() {
        if (selProjectIdx !== null) projects[selProjectIdx].costs = $costs;
        localStorage.setItem('projects', JSON.stringify(projects));
    }

    function getPDF() {
        let calculators = document.getElementById('calculators');
        let btns = document.getElementsByClassName('pdf-hide');
        Array.from(btns).forEach((element) => {
            element.classList.toggle('hidden');
        });
        calculators.classList.toggle('fixed');
        html2canvas(calculators).then(function(canvas) {
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
            doc.addImage(imgData, "PNG", 0, 0, canvas.width, canvas.height);
            doc.save(`${projects[selProjectIdx].name}_${new Date().toISOString().slice(0,10).replaceAll('-','')}.pdf`);
        });
    }

</script>

<div class="h-screen">
    <div class="h-9 px-1 border-b flex justify-between items-center">
        <div class="flex items-center gap-1 pl-1">
            <button class="px-2 py-0.5 rounded-lg
                text-gray-600 hover:bg-gray-100 active:bg-gray-200"
                on:click={() => newProject()}>
                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="w-6 h-6">
                    <path fill-rule="evenodd" d="M5.625 1.5H9a3.75 3.75 0 013.75 3.75v1.875c0 1.036.84 1.875 1.875 1.875H16.5a3.75 3.75 0 013.75 3.75v7.875c0 1.035-.84 1.875-1.875 1.875H5.625a1.875 1.875 0 01-1.875-1.875V3.375c0-1.036.84-1.875 1.875-1.875zM12.75 12a.75.75 0 00-1.5 0v2.25H9a.75.75 0 000 1.5h2.25V18a.75.75 0 001.5 0v-2.25H15a.75.75 0 000-1.5h-2.25V12z" clip-rule="evenodd" />
                    <path d="M14.25 5.25a5.23 5.23 0 00-1.279-3.434 9.768 9.768 0 016.963 6.963A5.23 5.23 0 0016.5 7.5h-1.875a.375.375 0 01-.375-.375V5.25z" />
                </svg>  
            </button>
            {#each projects as project, i}
                <div class="flex items-center rounded-lg text-sm text-gray-700 bg-gray-100 hover:bg-gray-200 active:bg-gray-300"
                    class:bg-gray-300={selProjectIdx === i}>
                    <button class="px-2 py-0.5 before:text-sm"
                        on:click={() => selProject(i)}>
                        {project.name}
                    </button>
                    <button class="px-1 py-0.5 hover:text-cyan-600"
                        on:click={() => delProject(i)}>
                        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" class="w-5 h-5">
                            <path d="M6.28 5.22a.75.75 0 00-1.06 1.06L8.94 10l-3.72 3.72a.75.75 0 101.06 1.06L10 11.06l3.72 3.72a.75.75 0 101.06-1.06L11.06 10l3.72-3.72a.75.75 0 00-1.06-1.06L10 8.94 6.28 5.22z" />
                        </svg>
                    </button>
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
                text-red-500 hover:bg-gray-100 active:bg-gray-200"
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
                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="w-6 h-6">
                    <path fill-rule="evenodd" d="M11.078 2.25c-.917 0-1.699.663-1.85 1.567L9.05 4.889c-.02.12-.115.26-.297.348a7.493 7.493 0 00-.986.57c-.166.115-.334.126-.45.083L6.3 5.508a1.875 1.875 0 00-2.282.819l-.922 1.597a1.875 1.875 0 00.432 2.385l.84.692c.095.078.17.229.154.43a7.598 7.598 0 000 1.139c.015.2-.059.352-.153.43l-.841.692a1.875 1.875 0 00-.432 2.385l.922 1.597a1.875 1.875 0 002.282.818l1.019-.382c.115-.043.283-.031.45.082.312.214.641.405.985.57.182.088.277.228.297.35l.178 1.071c.151.904.933 1.567 1.85 1.567h1.844c.916 0 1.699-.663 1.85-1.567l.178-1.072c.02-.12.114-.26.297-.349.344-.165.673-.356.985-.57.167-.114.335-.125.45-.082l1.02.382a1.875 1.875 0 002.28-.819l.923-1.597a1.875 1.875 0 00-.432-2.385l-.84-.692c-.095-.078-.17-.229-.154-.43a7.614 7.614 0 000-1.139c-.016-.2.059-.352.153-.43l.84-.692c.708-.582.891-1.59.433-2.385l-.922-1.597a1.875 1.875 0 00-2.282-.818l-1.02.382c-.114.043-.282.031-.449-.083a7.49 7.49 0 00-.985-.57c-.183-.087-.277-.227-.297-.348l-.179-1.072a1.875 1.875 0 00-1.85-1.567h-1.843zM12 15.75a3.75 3.75 0 100-7.5 3.75 3.75 0 000 7.5z" clip-rule="evenodd" />
                </svg>
            </button> 
        </div>
    </div>
    <div class="flex h-[calc(100vh-2.25rem)]">
        <div class="grow overflow-auto flex" id="calculators">
            {#each $costs as cost, index (index)}
                <Calculator {index}
                    {selectedIndex}
                    on:pleasesetProjects={setProjects}
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