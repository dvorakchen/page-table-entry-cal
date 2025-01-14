<script setup lang="ts">
import { computed, ref } from 'vue';
import isNumber from 'is-number';
import BigNumber from 'bignumber.js';

let pte = ref(BigNumber(0));

function b() {
    if (isWrongPTE.value) {
        return '0';
    }

    return pte.value.toString(2);
}

let binaryPTE = computed(() => {
    return b()
})

let rawPTE = computed(() => {
    if (isWrongPTE.value) {
        return '0';
    }

    return pte.value.toString(10)
})

let hexPTE = computed(() => {
    if (isWrongPTE.value) {
        return '0';
    }

    return pte.value.toString(16).toUpperCase()
})

let isWrongPTE = computed(() => {
    return !isNumber(pte.value.toString()) || pte.value.toString(2).length > 64;
})

function cut(s: number, l: number): string {
    if (isWrongPTE.value) {
        return '0';
    }

    let v = b();
    if (v.length < l) {
        return '0';
    }

    v = v.substring(0, v.length - s);
    if (v.length < l) {
        return v;
    }

    return v.substring(v.length - l)
}

let v = computed(() => {
    return cut(0, 1);
})

let r = computed(() => {
    return cut(1, 1);
})

let w = computed(() => {
    return cut(2, 1);
})

let x = computed(() => {
    return cut(3, 1);
})

let u = computed(() => {
    return cut(4, 1);
})

let g = computed(() => {
    return cut(5, 1);
})

let a = computed(() => {
    return cut(6, 1);
})

let d = computed(() => {
    return cut(7, 1);
})

let rsw = computed(() => {
    return cut(8, 2);
})

let ppn0 = computed(() => {
    return cut(10, 9);
})

let ppn1 = computed(() => {
    return cut(19, 9);
})

let ppn2 = computed(() => {
    return cut(28, 26);
})

let reserved = computed(() => {
    return cut(54, 7);
})

let pbmt = computed(() => {
    return cut(61, 2);
})

let n = computed(() => {
    return cut(63, 1);
})

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
    <div>
        <div class="pageTableBox">
            <div class="input">
                <label for="inputPTE">Input Page Table Entry:</label>
                <input type="text" id="inputPTE" @input="ev => pte = BigNumber((ev.target as any).value ?? '0')">
                <span class="error" v-if="isWrongPTE">NaN</span>
            </div>
            <!-- {{ pte.toString() }} -->
            <table>
                <thead>
                    <tr>
                        <th>Bits</th>
                        <th>63</th>
                        <th>
                            <span class="bits"><span>62</span>
                                <span>61</span></span>
                        </th>
                        <th>
                            <span class="bits"><span>60</span>
                                <span>54</span></span>
                        </th>
                        <th>
                            <span class="bits"><span>53</span>
                                <span>28</span></span>
                        </th>
                        <th>
                            <span class="bits"><span>27</span>
                                <span>19</span></span>
                        </th>
                        <th>
                            <span class="bits"><span>18</span>
                                <span>10</span></span>
                        </th>
                        <th>
                            <span class="bits"><span>9</span>
                                <span>8</span></span>
                        </th>
                        <th>7</th>
                        <th>6</th>
                        <th>5</th>
                        <th>4</th>
                        <th>3</th>
                        <th>2</th>
                        <th>1</th>
                        <th>0</th>
                    </tr>
                    <tr>
                        <th></th>
                        <th>N</th>
                        <th>PBMT</th>
                        <th>Reserved</th>
                        <th>PPN[2]</th>
                        <th>PPN[1]</th>
                        <th>PPN[0]</th>
                        <th>RSW</th>
                        <th>D</th>
                        <th>A</th>
                        <th>G</th>
                        <th>U</th>
                        <th>X</th>
                        <th>W</th>
                        <th>R</th>
                        <th>V</th>
                    </tr>
                </thead>
                <tbody>
                    <tr>
                        <td>Bin</td>
                        <td>{{ underline_per(n, 8) }}</td>
                        <td>{{ underline_per(pbmt, 8) }}</td>
                        <td>{{ underline_per(reserved, 8) }}</td>
                        <td>{{ underline_per(ppn2, 8) }}</td>
                        <td>{{ underline_per(ppn1, 8) }}</td>
                        <td>{{ underline_per(ppn0, 8) }}</td>
                        <td>{{ underline_per(rsw, 8) }}</td>
                        <td>{{ underline_per(d, 8) }}</td>
                        <td>{{ underline_per(a, 8) }}</td>
                        <td>{{ underline_per(g, 8) }}</td>
                        <td>{{ underline_per(u, 8) }}</td>
                        <td>{{ underline_per(x, 8) }}</td>
                        <td>{{ underline_per(w, 8) }}</td>
                        <td>{{ underline_per(r, 8) }}</td>
                        <td>{{ underline_per(v, 8) }}</td>
                    </tr>
                    <tr>
                        <td colspan="4"></td>
                        <td colspan="3">
                            {{ underline_per(ppn2 + ppn1 + ppn0, 8) }}
                        </td>
                        <td colspan="9"></td>
                    </tr>
                    <tr>
                        <td>Dec</td>
                        <td>{{ dec(n) }}</td>
                        <td>{{ dec(pbmt) }}</td>
                        <td>{{ dec(reserved) }}</td>
                        <td>{{ dec(ppn2) }}</td>
                        <td>{{ dec(ppn1) }}</td>
                        <td>{{ dec(ppn0) }}</td>
                        <td>{{ dec(rsw) }}</td>
                        <td>{{ dec(d) }}</td>
                        <td>{{ dec(a) }}</td>
                        <td>{{ dec(g) }}</td>
                        <td>{{ dec(u) }}</td>
                        <td>{{ dec(x) }}</td>
                        <td>{{ dec(w) }}</td>
                        <td>{{ dec(r) }}</td>
                        <td>{{ dec(v) }}</td>
                    </tr>
                    <tr>
                        <td colspan="4"></td>
                        <td colspan="3">
                            {{ dec(ppn2 + ppn1 + ppn0) }}
                        </td>
                        <td colspan="9"></td>
                    </tr>
                    <tr>
                        <td>Hex</td>
                        <td>{{ hex(n) }}</td>
                        <td>{{ hex(pbmt) }}</td>
                        <td>{{ hex(reserved) }}</td>
                        <td>{{ hex(ppn2) }}</td>
                        <td>{{ hex(ppn1) }}</td>
                        <td>{{ hex(ppn0) }}</td>
                        <td>{{ hex(rsw) }}</td>
                        <td>{{ hex(d) }}</td>
                        <td>{{ hex(a) }}</td>
                        <td>{{ hex(g) }}</td>
                        <td>{{ hex(u) }}</td>
                        <td>{{ hex(x) }}</td>
                        <td>{{ hex(w) }}</td>
                        <td>{{ hex(r) }}</td>
                        <td>{{ hex(v) }}</td>
                    </tr>
                    <tr>
                        <td colspan="4"></td>
                        <td colspan="3">
                            {{ hex(ppn2 + ppn1 + ppn0) }}
                        </td>
                        <td colspan="9"></td>
                    </tr>
                </tbody>
            </table>
        </div>
        <div>
            <div>Page Table Encry: {{ underline_per(rawPTE, 3) }}</div>
            <div>Binary: {{ underline_per(binaryPTE, 8) }}</div>
            <div>Hex: {{ underline_per(hexPTE, 2) }}</div>
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
</style>
