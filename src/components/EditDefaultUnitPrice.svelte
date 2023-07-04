<script>
    let defaultUnitPrice = JSON.parse(localStorage.getItem('defaultUnitPrice'));

    function saveDefaultUnitPrice() {
        localStorage.setItem('defaultUnitPrice', JSON.stringify(defaultUnitPrice));
    }
</script>

<div class="shrink-0 w-72 p-3 border-l bg-gray-100 overflow-y-scroll">
    <h2 class="text-lg font-bold">단가 설정</h2>
    <div class="divide-y-[0.5px] divide-gray-400">
        <div class="py-4">
            <h3 class="font-bold">Support</h3>
            <div class="flex justify-between items-end">
                <label class="ml-1 text-xs" for="support-amount">LDC 공사</label>
                <div>
                    <input type="number"
                        id="support-amount"
                        bind:value={defaultUnitPrice.support.ldc}
                        class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                    <span class=" text-xs">₩</span>
                </div>
            </div>
            <div class="flex justify-between items-end">
                <label class="ml-1 text-xs" for="support-amount">전체도급</label>
                <div>
                    <input type="number"
                        id="support-amount"
                        bind:value={defaultUnitPrice.support.contract}
                        class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                    <span class="text-xs">₩</span>
                </div>
            </div>
        </div>

        <div class="py-4">
            <h3 class="font-bold">신축</h3>
            <div class="flex justify-between items-end">
                <label class="ml-1 text-xs" for="support-max-rate">전통소</label>
                <div>
                    <input type="number"
                        id="support-max-rate"
                        bind:value={defaultUnitPrice.new.requiredContract}
                        class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                    <span class="text-xs">₩</span>
                </div>
            </div>
            <h4 class="mt-2 ml-1 font-bold text-xs">LDC 공사</h4>
            <div>
                <div class="flex justify-between items-end">
                    <label class="ml-1 text-xs" for="support-amount">건축</label>
                    <div>
                        <input type="number"
                            id="support-amount"
                            bind:value={defaultUnitPrice.new.ldc.building}
                            class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                        <span class="text-xs">₩</span>
                    </div>
                </div>
                <div class="flex justify-between items-end">
                    <label class="ml-1 text-xs" for="support-amount">부지</label>
                    <div>
                        <input type="number"
                            id="support-amount"
                            bind:value={defaultUnitPrice.new.ldc.site}
                            class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                        <span class=" text-xs">₩</span>
                    </div>
                </div>
                <div class="flex justify-between items-end">
                    <label class="ml-1 text-xs" for="support-amount">필로티</label>
                    <div>
                        <input type="number"
                            id="support-amount"
                            bind:value={defaultUnitPrice.new.ldc.piloti}
                            class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                        <span class="text-xs">₩</span>
                    </div>
                </div>
                <div class="flex justify-between items-end">
                    <label class="ml-1 text-xs" for="support-amount">부지철거</label>
                    <div>
                        <input type="number"
                            id="support-amount"
                            bind:value={defaultUnitPrice.new.ldc.siteDemolition}
                            class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                        <span class=" text-xs">₩</span>
                    </div>
                </div>
            </div>
            <h4 class="mt-2 ml-1 font-bold text-xs">전체도급</h4>
            <div>
                <div class="flex justify-between items-end">
                    <label class="ml-1 text-xs" for="support-amount">건축</label>
                    <div>
                        <input type="number"
                            id="support-amount"
                            bind:value={defaultUnitPrice.new.contract.building}
                            class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                        <span class="text-xs">₩</span>
                    </div>
                </div>
                <div class="flex justify-between items-end">
                    <label class="ml-1 text-xs" for="support-amount">부지</label>
                    <div>
                        <input type="number"
                            id="support-amount"
                            bind:value={defaultUnitPrice.new.contract.site}
                            class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                        <span class=" text-xs">₩</span>
                    </div>
                </div>
                <div class="flex justify-between items-end">
                    <label class="ml-1 text-xs" for="support-amount">필로티</label>
                    <div>
                        <input type="number"
                            id="support-amount"
                            bind:value={defaultUnitPrice.new.contract.piloti}
                            class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                        <span class="text-xs">₩</span>
                    </div>
                </div>
                <div class="flex justify-between items-end">
                    <label class="ml-1 text-xs" for="support-amount">부지철거</label>
                    <div>
                        <input type="number"
                            id="support-amount"
                            bind:value={defaultUnitPrice.new.contract.siteDemolition}
                            class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                        <span class=" text-xs">₩</span>
                    </div>
                </div>
            </div>
        </div>

        <div class="py-4">
            <h3 class="font-bold">개축</h3>
            <div class="flex justify-between items-end">
                <label class="ml-1 text-xs" for="support-max-rate">전통소</label>
                <div>
                    <input type="number"
                        id="support-max-rate"
                        bind:value={defaultUnitPrice.major.requiredContract}
                        class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                    <span class="text-xs">₩</span>
                </div>
            </div>
            <h4 class="mt-2 ml-1 font-bold text-xs">LDC 공사</h4>
            <div>
                <div class="flex justify-between items-end">
                    <label class="ml-1 text-xs" for="support-amount">건축</label>
                    <div>
                        <input type="number"
                            id="support-amount"
                            bind:value={defaultUnitPrice.major.ldc.building}
                            class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                        <span class="text-xs">₩</span>
                    </div>
                </div>
                <div class="flex justify-between items-end">
                    <label class="ml-1 text-xs" for="support-amount">부지</label>
                    <div>
                        <input type="number"
                            id="support-amount"
                            bind:value={defaultUnitPrice.new.ldc.site}
                            class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                        <span class=" text-xs">₩</span>
                    </div>
                </div>
            </div>
            <h4 class="mt-2 ml-1 font-bold text-xs">전체도급</h4>
            <div>
                <div class="flex justify-between items-end">
                    <label class="ml-1 text-xs" for="support-amount">건축</label>
                    <div>
                        <input type="number"
                            id="support-amount"
                            bind:value={defaultUnitPrice.major.contract.building}
                            class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                        <span class="text-xs">₩</span>
                    </div>
                </div>
                <div class="flex justify-between items-end">
                    <label class="ml-1 text-xs" for="support-amount">부지</label>
                    <div>
                        <input type="number"
                            id="support-amount"
                            bind:value={defaultUnitPrice.major.contract.site}
                            class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                        <span class=" text-xs">₩</span>
                    </div>
                </div>
            </div>
        </div>

        <div class="py-4">
            <h3 class="font-bold">상가</h3>
            <div class="flex justify-between items-end">
                <label class="ml-1 text-xs" for="support-max-rate">전통소</label>
                <div>
                    <input type="number"
                        id="support-max-rate"
                        bind:value={defaultUnitPrice.store.requiredContract}
                        class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                    <span class="text-xs">₩</span>
                </div>
            </div>
            <h4 class="mt-2 ml-1 font-bold text-xs">LDC 공사</h4>
            <div>
                <div class="flex justify-between items-end">
                    <label class="ml-1 text-xs" for="support-amount">건축</label>
                    <div>
                        <input type="number"
                            id="support-amount"
                            bind:value={defaultUnitPrice.store.ldc.building}
                            class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                        <span class="text-xs">₩</span>
                    </div>
                </div>
            </div>
            <h4 class="mt-2 ml-1 font-bold text-xs">전체도급</h4>
            <div>
                <div class="flex justify-between items-end">
                    <label class="ml-1 text-xs" for="support-amount">건축</label>
                    <div>
                        <input type="number"
                            id="support-amount"
                            bind:value={defaultUnitPrice.store.contract.building}
                            class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                        <span class="text-xs">₩</span>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <div class="py-4">
        <button class="px-4 py-1 rounded-lg
            text-white bg-gray-500 hover:bg-gray-600 active:bg-gray-700"
            on:click={saveDefaultUnitPrice}>
            저장
        </button>
    </div>
</div>