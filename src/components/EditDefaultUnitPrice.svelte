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
            <h3 class="font-semibold">Support</h3>
            <div class="mt-1 flex justify-between items-end">
                <label class="ml-1 text-xs" for="support-amount">LDC 공사</label>
                <div>
                    <input type="number"
                        id="support-amount"
                        bind:value={defaultUnitPrice.support.ldc}
                        class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                    <span class=" text-xs">₩</span>
                </div>
            </div>
            <div class="mt-1 flex justify-between items-end">
                <label class="ml-1 text-xs" for="support-amount">전체도급</label>
                <div>
                    <input type="number"
                        id="support-amount"
                        bind:value={defaultUnitPrice.support.contract}
                        class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                    <span class="text-xs">₩</span>
                </div>
            </div>
            <div class="mt-1 flex justify-between items-end">
                <label class="ml-1 text-xs" for="support-max-rate">최대비용 증가율</label>
                <div>
                    <input type="number"
                        id="support-max-rate"
                        bind:value={defaultUnitPrice.support.maxRate}
                        class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                    <span class="text-xs">%</span>
                </div>
            </div>
        </div>

        <div class="py-4">
            <h3 class="font-semibold">도급 증가율</h3>
            <div class="mt-1 flex justify-between items-end">
                <label class="ml-1 text-xs" for="support-amount">최소</label>
                <div>
                    <input type="number"
                        id="support-amount"
                        bind:value={defaultUnitPrice.contract.minRate}
                        class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                    <span class="text-xs">%</span>
                </div>
            </div>
            <div class="mt-1 flex justify-between items-end">
                <label class="ml-1 text-xs" for="support-amount">최대</label>
                <div>
                    <input type="number"
                        id="support-amount"
                        bind:value={defaultUnitPrice.contract.maxRate}
                        class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                    <span class=" text-xs">%</span>
                </div>
            </div>
            <div class="mt-1 flex justify-between items-end">
                <label class="ml-1 text-xs" for="support-max-rate">전,통,소 최대</label>
                <div>
                    <input type="number"
                        id="support-max-rate"
                        bind:value={defaultUnitPrice.contract.electMaxRate}
                        class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                    <span class="text-xs">%</span>
                </div>
            </div>
        </div>

        <div class="py-4">
            <h3 class="font-semibold">신축</h3>
            <div class="mt-1 flex justify-between items-end">
                <label class="ml-1 text-xs" for="support-amount">건축</label>
                <div>
                    <input type="number"
                        id="support-amount"
                        bind:value={defaultUnitPrice.new.building}
                        class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                    <span class="text-xs">₩</span>
                </div>
            </div>
            <div class="mt-1 flex justify-between items-end">
                <label class="ml-1 text-xs" for="support-amount">부지</label>
                <div>
                    <input type="number"
                        id="support-amount"
                        bind:value={defaultUnitPrice.new.site}
                        class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                    <span class=" text-xs">₩</span>
                </div>
            </div>
            <div class="mt-1 flex justify-between items-end">
                <label class="ml-1 text-xs" for="support-max-rate">전,통,소</label>
                <div>
                    <input type="number"
                        id="support-max-rate"
                        bind:value={defaultUnitPrice.new.elect}
                        class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                    <span class="text-xs">₩</span>
                </div>
            </div>
        </div>

        <div class="py-4">
            <h3 class="font-semibold">개축</h3>
            <div class="mt-1 flex justify-between items-end">
                <label class="ml-1 text-xs" for="support-amount">건축</label>
                <div>
                    <input type="number"
                        id="support-amount"
                        bind:value={defaultUnitPrice.major.building}
                        class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                    <span class="text-xs">₩</span>
                </div>
            </div>
            <div class="mt-1 flex justify-between items-end">
                <label class="ml-1 text-xs" for="support-amount">부지</label>
                <div>
                    <input type="number"
                        id="support-amount"
                        bind:value={defaultUnitPrice.major.site}
                        class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                    <span class=" text-xs">₩</span>
                </div>
            </div>
            <div class="mt-1 flex justify-between items-end">
                <label class="ml-1 text-xs" for="support-max-rate">전,통,소</label>
                <div>
                    <input type="number"
                        id="support-max-rate"
                        bind:value={defaultUnitPrice.major.elect}
                        class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                    <span class="text-xs">₩</span>
                </div>
            </div>
        </div>

        <div class="py-4">
            <h3 class="font-semibold">상가</h3>
            <div class="mt-1 flex justify-between items-end">
                <label class="ml-1 text-xs" for="support-amount">건축</label>
                <div>
                    <input type="number"
                        id="support-amount"
                        bind:value={defaultUnitPrice.store.building}
                        class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                    <span class="text-xs">₩</span>
                </div>
            </div>
            <div class="mt-1 flex justify-between items-end">
                <label class="ml-1 text-xs" for="support-max-rate">전,통,소</label>
                <div>
                    <input type="number"
                        id="support-max-rate"
                        bind:value={defaultUnitPrice.store.elect}
                        class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                    <span class="text-xs">₩</span>
                </div>
            </div>
        </div>

        <div class="py-4">
            <h3 class="font-semibold">임대</h3>
            <div class="mt-1 flex justify-between items-end">
                <label class="ml-1 text-xs" for="support-amount">최소</label>
                <div>
                    <input type="number"
                        id="support-amount"
                        bind:value={defaultUnitPrice.lease.min}
                        class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                    <span class=" text-xs">₩</span>
                </div>
            </div>
            <div class="mt-1 flex justify-between items-end">
                <label class="ml-1 text-xs" for="support-max-rate">최대</label>
                <div>
                    <input type="number"
                        id="support-max-rate"
                        bind:value={defaultUnitPrice.lease.max}
                        class="px-1 h-5 text-xs text-right ring-[0.5px] ring-gray-400 rounded-sm">
                    <span class="text-xs">₩</span>
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