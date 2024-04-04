<script lang="ts" setup>
import { computed, ref } from '@vue/reactivity';
import { useBaseStore, useBlockchain, useFormatter } from '@/stores';
import type { PaginatedTxs } from '@/types';
import { useRouter } from 'vue-router';
import { onMounted } from 'vue';
const props = defineProps(['chain']);
const vueRouters = useRouter();
const tab = ref('recent');

const base = useBaseStore()
const chainStore = useBlockchain()

const format = useFormatter();
const hashReg = /^[A-Z\d]{64}$/;
const hash = ref('');
const current = chainStore?.current?.chainName || '';
onMounted(() => {
    tab.value = String(vueRouters.currentRoute.value.query.tab || 'recent');
    console.log(tab.value);
});
function search() {
    if (hashReg.test(hash.value)) {
      vueRouters.push({ path: `/${current}/tx/${hash.value}` });
    }
}
</script>
<template>
    <div>


        <div v-show="tab === 'recent'" class="#saved rounded overflow-x-auto">
            <table class="table w-full table-compact">
                <thead class="bg-base-200">
                    <tr>
                        <th style="position: relative; z-index: 2;">{{ $t('account.height') }}</th>
                        <th style="position: relative; z-index: 2;">{{ $t('account.hash') }}</th>
                        <th>{{ $t('account.messages') }}</th>
                        <th>{{ $t('block.fees') }}</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(item, index) in base.txsInRecents" :index="index" class="hover">
                        <td class="text-sm  #saved">
                            <RouterLink :to="`/${props.chain}/block/${item.height}`">{{ item.height }}</RouterLink>
                        </td>
                        <td class="truncate  #saved" width="50%">
                            <RouterLink :to="`/${props.chain}/tx/${item.hash}`">{{
                item.hash
            }}</RouterLink>
                        </td>
                        <td>{{ format.messages(item.tx.body.messages) }}</td>
                        <td>{{ format.formatTokens(item.tx.authInfo.fee?.amount) }}</td>
                    </tr>
                </tbody>
            </table>
          
        </div>

        <div v-show="tab === 'search'" class="#saved rounded overflow-x-auto">
            <div class="p-4">
                <div class="form-control">
                    <input v-model="hash" type="text" class="input input-bordered" placeholder="Search by Tx Hash" @blur="search"/>
                </div>
            </div>
        </div>
    </div>
</template>

<route>
    {
      meta: {
        i18n: 'tx',
        order: 5
      }
    }
  </route>


<style>

.tab-act{
    color: white;
    background-color: black;
}
</style>