<script setup lang="ts">
import { computed, ref } from 'vue';
import isNumber from 'is-number';
import BigNumber from 'bignumber.js';

let va = ref(BigNumber(0));


function b() {
    if (isWrongVA.value) {
        return '0';
    }

    return va.value.toString(2);
}

let binaryVA = computed(() => {
    return b()
})

let rawVA = computed(() => {
    if (isWrongVA.value) {
        return '0';
    }

    return va.value.toString(10)
})

let hexVA = computed(() => {
    if (isWrongVA.value) {
        return '0';
    }

    return va.value.toString(16).toUpperCase()
})


let isWrongVA = computed(() => {
    return !isNumber(va.value.toString()) || va.value.toString(2).length > 39;
})

let offset = computed(() => {
    return cut(0, 12)
})

let vpn0 = computed(() => {
    return cut(12, 9)
})

let vpn1 = computed(() => {
    return cut(21, 9)
})

let vpn2 = computed(() => {
    return cut(30, 9)
})

function cut(s: number, l: number): string {
    if (isWrongVA.value) {
        return '0';
    }

    let v = b();
    // if (v.length < l) {
    //     return '0';
    // }

    v = v.substring(0, v.length - s);
    if (v.length < l) {
        return v;
    }

    return v.substring(v.length - l)
}


function dec(v: string): string {
    let bv = +`0b${v}`;
    if (isNumber(bv)) {

        return underline_per(BigNumber(bv).toString(10), 3)
    }
    return '0';
}

function hex(binaryValue: string): string {
    let bv = +`0b${binaryValue}`;
    if (isNumber(bv)) {
        let v = BigNumber(bv).toString(16).toUpperCase()

        return '0x' + underline_per(v, 2)
    }
    return '0x0';
}

function underline_per(s: string, n: number): string {

    const res = [];
    for (let i = s.length - 1, j = 0; i >= 0; i--) {
        if (j === n) {
            res.unshift('_');
            j = 0;
        }
        const c = s[i];
        res.unshift(c);
        j++;
    }

    return res.join('');
}

</script>

<template>

    <div class="pageTableBox">
        <div class="input">
            <label for="inputPTE">Input Virtual Address:</label>
            <input type="text" id="inputPTE" @input="ev => va = BigNumber((ev.target as any).value ?? '0')">
            <span class="error" v-if="isWrongVA">NaN</span>
        </div>
        <table>
            <thead>
                <tr>
                    <th>Bits</th>
                    <th>
                        <span class="bits"><span>38</span>
                            <span>30</span></span>
                    </th>
                    <th>
                        <span class="bits"><span>29</span>
                            <span>21</span></span>
                    </th>
                    <th>
                        <span class="bits"><span>20</span>
                            <span>12</span></span>
                    </th>
                    <th>
                        <span class="bits"><span>11</span>
                            <span>0</span></span>
                    </th>
                </tr>
                <tr>
                    <th></th>
                    <th>VPN[2]</th>
                    <th>VPN[1]</th>
                    <th>VPN[0]</th>
                    <th>Page Offset</th>
                </tr>
            </thead>
            <tbody>
                <tr>
                    <td>Bin</td>
                    <td>{{ underline_per(vpn2, 8) }}</td>
                    <td>{{ underline_per(vpn1, 8) }}</td>
                    <td>{{ underline_per(vpn0, 8) }}</td>
                    <td>{{ underline_per(offset, 8) }}</td>
                </tr>
                <tr>
                    <td></td>
                    <td colspan="3" class="border-none">
                        {{ underline_per(vpn2 + vpn1 + vpn0, 8) }}
                    </td>
                    <td></td>
                </tr>
                <tr>
                    <td>Dec</td>
                    <td>{{ dec(vpn2) }}</td>
                    <td>{{ dec(vpn1) }}</td>
                    <td>{{ dec(vpn0) }}</td>
                    <td>{{ dec(offset) }}</td>
                </tr>
                <tr>
                    <td></td>
                    <td colspan="3" class="border-none">
                        {{ dec(vpn2 + vpn1 + vpn0) }}
                    </td>
                    <td></td>
                </tr>
                <tr>
                    <td>Hex</td>
                    <td>{{ hex(vpn2) }}</td>
                    <td>{{ hex(vpn1) }}</td>
                    <td>{{ hex(vpn0) }}</td>
                    <td>{{ hex(offset) }}</td>
                </tr>
                <tr>
                    <td></td>
                    <td colspan="3" class="border-none">
                        {{ hex(vpn2 + vpn1 + vpn0) }}
                    </td>
                    <td></td>
                </tr>
            </tbody>
        </table>
        <div>
            <div>Virtual Address: {{ underline_per(rawVA, 3) }}</div>
            <div>Binary: {{ underline_per(binaryVA, 8) }}</div>
            <div>Hex: 0x{{ underline_per(hexVA, 2) }}</div>
        </div>
    </div>
</template>


<style scoped>
.pageTableBox {
    display: flex;
    flex-direction: column;
}

.input {
    display: flex;
    gap: 1rem
}

.input>input {
    flex-grow: 1;
}

.input>.error {
    color: red;
}

table {
    border: 1px solid;
    margin: 1rem
}

tr>* {
    border: 1px solid;
    padding: .2rem;
}

.bits {
    display: flex;
    justify-content: space-between;
}

.border-none {
    border: none !important;
}
</style>
