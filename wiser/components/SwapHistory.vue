<script setup lang="ts">
/* Import modules. */
import moment from 'moment'
import numeral from 'numeral'

/* Define properties. */
// https://vuejs.org/guide/components/props.html#props-declaration
const props = defineProps({
    data: {
        type: [Object],
    },
})

const ENDPOINT = 'https://nexa.exchange/v1/ticker/quote'

const txHistory = ref(null)

const displayAge = (_timestamp) => {
    let formatted

    formatted = moment.unix(_timestamp).fromNow()

    /* Use abbreviations. */
    formatted = formatted.replaceAll('minutes', 'mins')
    formatted = formatted.replaceAll('minute', 'min')
    formatted = formatted.replaceAll('hours', 'hrs')
    formatted = formatted.replaceAll('hour', 'hr')

    return formatted
}

const init = async () => {
    txHistory.value = []

    const ticker = await $fetch(`${ENDPOINT}/9732745682001b06e332b6a4a0dd0fffc4837c707567f8cbfe0f6a9b12080000`)
        .catch(err => console.error(err))
    console.log('$STUDIO TICKER', ticker)

    const price = ticker.price
    console.log('$STUDIO PRICE', price)

    txHistory.value.push({
        txidem: '25b3f270b8015c14c0797f25d7285e20c0d1a32b50db323484171ca7f249038f',
        quote: {
            ticker: 'STUDIO',
            quantity: 710038,
        },
        usdValue: 0,
        timestamp: 1704164063,
    })

    txHistory.value.push({
        txidem: 'ec8694604ff74eb8ca0e43f5c6a343cb26de4bf35c1c25f4b785c06d480a7846',
        quote: {
            ticker: 'STUDIO',
            quantity: 100542,
        },
        usdValue: 0,
        timestamp: 1704346054,
    })

    txHistory.value.push({
        txidem: '989292006325018ce61186e2191f6264f84c9d2f25827f9353b7dcfad7292464',
        quote: {
            ticker: 'STUDIO',
            quantity: 48757,
        },
        usdValue: 0,
        timestamp: 1704373610,
    })

    /* Add (USD) fiat price to entries. */
    for (let i = 0; i < txHistory.value.length; i++) {
        const usdValue = txHistory.value[i].quote.quantity * price
        console.log('USD VALUE', usdValue)

        /* Set (formatted) value. */
        txHistory.value[i].usdValue = numeral(usdValue).format('$0,0.00')
    }

    /* Reverse history order. */
    txHistory.value.reverse()
}

onMounted(() => {
    init()
})

// onBeforeUnmount(() => {
//     console.log('Before Unmount!')
//     // Now is the time to perform all cleanup operations.
// })
</script>

<template>
    <main class="w-full px-3 sm:px-10">
        <ul role="list" class="divide-y divide-amber-300">

            <li v-for="swap of txHistory" :key="swap.txidem" class="py-3">
                <NuxtLink :to="'https://explorer.nexa.org/tx/' + swap.txidem" target="_blank" class="px-3 py-2 grid grid-cols-4 gap-x-3 items-center border border-transparent rounded hover:bg-amber-50 hover:border-amber-200">
                    <div class="col-span-2 flex flex-row items-center gap-2">
                        <img src="https://nexa.studio/icon.svg" alt="" class="w-10 sm:w-12 h-auto flex-none" />

                        <h3 class="flex flex-col truncate text-xs sm:text-sm font-medium text-gray-400">
                            <span class="-mb-1 lg:-mb-0 text-lg sm:text-xl text-semibold text-gray-600 tracking-widest">
                                {{numeral(swap.quote.quantity).format('0,0[.]00')}}
                            </span>

                            {{swap.quote.ticker}}
                        </h3>
                    </div>

                    <h3 class="flex truncate text-xl sm:text-2xl font-bold text-rose-600 justify-center tracking-widest">
                        {{swap.usdValue}}
                    </h3>

                    <time datetime="2023-01-23T11:00" class="flex justify-end text-xs sm:text-sm text-gray-500 italic">
                        {{displayAge(swap.timestamp)}}
                    </time>
                </NuxtLink>
            </li>

        </ul>
    </main>
</template>
