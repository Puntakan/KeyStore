<script setup>
import { ref, watchEffect, computed} from 'vue'
import LucideTrash2 from './assets/Trash.vue'
const cashier = ref(true)
const history = ref(false)


const cashierPage = () => {
    cashier.value = true
    history.value = false
}

const historyPage = () => {
    cashier.value = false
    history.value = true
}

const guest = ref(true)
const member = ref(false)
const customer = ref('Guest');

const cusOfGuest = () => {
    customer.value = 'Guest'
    guest.value = true
    member.value = false
    discount
}

const cusOfMember = () => {
    customer.value = 'Member'
    guest.value = false
    member.value = true
}

const numberInput = ref('');
const numbers = ref([]);
const addNumber = () => {
    numbers.value.push(Number(numberInput.value));
    numberInput.value = '';
};
const subtotal = computed(() => {
    return Number(numbers.value.reduce((acc, curr) => acc + curr, 0).toFixed(2));
});



const discount = computed(() => {
    if (customer.value === 'Guest') {
        return 0; // ถ้าเป็น Guest ไม่มีส่วนลด
    } else if (subtotal.value >= 100 && subtotal.value < 500) {
        return 5;
    } else if (subtotal.value >= 500 && subtotal.value < 1000) {
        return 10;
    } else if (subtotal.value >= 1000 && subtotal.value < 2500) {
        return 20;
    } else if (subtotal.value >= 2500) {
        return 30;
    } else {
        return 0;
    }
});


const total = computed(() => {
    return Number((subtotal.value * (1 - discount.value / 100)).toFixed(2))
})
// ref(subtotal - subtotal * discount / 100)

// watchEffect(() => {
//     total.value = (subtotal.value * (1 - discount.value / 100))
// })

const minus = (discount) => {
    if (discount == 0) {
        return discount
    }
    else {

        return '-' + discount
    }
}

const comma = (num) => {
    return num.toLocaleString("en")
}


const deleteItem = (index) => {
  numbers.value.splice(index, 1);
}

</script>
 
<template>
    <div class="w-full h-screen">
        <div class="w-full h-14 bg" style="background: #DFE6F9;">
            <!-- Header -->
            <div class="flex flex-row w-full h-full">
                <div class="w-48 h-full font-bold text-xl flex items-center justify-center"
                    style="background-color: #304477; color: #FFC635;">
                    KeyStore
                </div>
                <button class="w-40 h-full flex items-center justify-center font-sans text-lg text-black"
                    @click="cashierPage()">
                    Shops
                </button>
                <button class="w-40 h-full flex items-center justify-center font-sans text-lg text-black"
                    @click="historyPage()">
                    History
                </button>
            </div>
        </div>

        <!-- Cashier -->
        <div v-show="cashier">
            <div class="w-full h-16 flex items-center text-2xl font-medium" style="color: #304477;">
                <div class="mx-40">
                    Cashier
                </div>
            </div>

            <!-- Content -->
            <div class="w-4/5 h-4/5 mx-auto rounded-3xl" style="background-color: #BEBEBE;">

                <div class="flex flex-col">
                    <div class="flex flex-row mt-7 justify-center">
                        <input v-model="numberInput" type="number" class="w-10/12 h-10 rounded-lg mr-5 text-black"
                            style="background-color: #FFFFFF;" min="0"
                            @input="numberInput = Math.max($event.target.value, 0)">

                        <button @click='addNumber()' class="w-20 h-10 rounded-lg flex items-center justify-center"
                            style="background-color: #4263EB; color: #FFFFFF">Add</button>
                    </div>

                    <div class="w-11/12 h-48 mx-auto mt-6 bg-white  rounded-lg text-black">
                        <div class="ml-10" v-if="numbers.length > 0"> No </div>
                        <div class="scrollable" ref="scrollable">
                            <div class="content" ref="content">

                                <div class="ml-10 mr-10 flex flex-col" v-for="(number, index) in numbers" :key="index">
                                    <div class="flex flex-row items-center mb-1">
                                        <div>
                                            {{ comma(index + 1) }}. {{ comma(number) }} ฿
                                        </div>
                                        <button class="flex items-center ml-auto" @click="deleteItem(index)">
                                            <LucideTrash2 /> &nbsp; &nbsp;
                                        </button>
                                    </div>
                                    <hr class="w-12/12">
                                </div>
                            </div>
                        </div>
                    </div>
                </div>

                <div class="flex flex-row mt-3 ml-14">
                    <div class="text-black">Customer: </div>
                    <button class="w-6 h-6 rounded-full border-2 border-white bg-white ml-4 focus:outline-none"
                        :class="{ 'bg-blue-500': customer === 'Guest' }" @click="cusOfGuest()"></button>
                    <div class="text-black ml-1">Guest</div>

                    <button class="w-6 h-6 rounded-full border-2 border-white bg-white ml-4 focus:outline-none"
                        :class="{ 'bg-blue-500': customer === 'Member' }" @click="cusOfMember()"></button>
                    <div class="text-black ml-1">Member</div>
                </div>

                <div class="text-black ml-14 mt-3"> Discount ({{ customer }}) :</div>

                <div class="flex flex-row mt-3 ml-14" v-show="guest">
                    <div class="w-20 h-11 flex justify-center items-center rounded-lg text-lg bg-gray-300">5%</div>

                    <div class="w-20 h-11 flex justify-center items-center rounded-lg ml-2 text-lg bg-gray-300">10%</div>

                    <div class="w-20 h-11 flex justify-center items-center rounded-lg ml-2 text-lg bg-gray-300">20%</div>

                    <div class="w-20 h-11 flex justify-center items-center rounded-lg ml-2 text-lg bg-gray-300">30%</div>
                </div>

                <div class="flex flex-row mt-3 ml-14" v-show="member">
                    <div class="w-20 h-11 flex justify-center items-center rounded-lg text-lg" :class="{
                        'bg-green-500 text-black': subtotal >= 100 && subtotal < 500,
                        'bg-gray-300': subtotal < 100 || subtotal >= 500 }"> 5%
                    </div>
                    <div class="w-20 h-11 flex justify-center items-center rounded-lg ml-2 text-lg" :class="{
                        'bg-green-500 text-black': subtotal >= 500 && subtotal < 1000,
                        'bg-gray-300': subtotal < 500 || subtotal >= 1000 }"> 10%
                    </div>
                    <div class="w-20 h-11 flex justify-center items-center rounded-lg ml-2 text-lg" :class="{
                        'bg-green-500 text-black': subtotal >= 1000 && subtotal < 2500,
                        'bg-gray-300': subtotal < 1000 || subtotal >= 2500 }"> 20%
                    </div>
                    <div class="w-20 h-11 flex justify-center items-center rounded-lg ml-2 text-lg" :class="{
                        'bg-green-500 text-black': subtotal >= 2500,
                        'bg-gray-300': subtotal < 2500 }">30%
                    </div>
                </div>

                <div class="flex flex-row justify-end mr-14 mt-5 font-medium text-gray-500">
                    <div class="w-auto flex justify-end items-center ">Sub Total : {{ comma((subtotal).toFixed(2)) }} ฿</div>
                </div>

                <div class="flex flex-row justify-end mr-14 font-medium text-red-600">
                    <div class="w-auto  flex justify-center items-center ">Discount : {{ minus(comma(((subtotal * discount) / 100).toFixed(2))) }} ฿ </div>
                </div>

                <div class="flex flex-row justify-end mr-14 font-bold text-xl text-green-700">
                    <div class="w-auto flex justify-center items-center ">Total : {{ comma((total).toFixed(2)) }} ฿</div>

                </div>

                <div class="flex flex-row justify-end mr-14 mb-2">
                    <button
                        class="w-20 h-10 flex justify-center items-center bg-blue-700 rounded-lg font-sans text-sm text-white mb-5 mt-2">
                        Confirm
                    </button>
                </div>
            </div>
        </div>

        <div v-show="history" class="h-full">
            <div class="w-full h-16 flex items-center text-2xl font-medium" style="color: #304477;">
                <div class="mx-40">
                    History
                </div>
            </div>

            <div class="w-5/6 h-4/6 mx-auto rounded-3xl" style="background-color: #BEBEBE;">
                <div class="grid grid-cols-10 gap-2 text-black">
                    <div class="col-span-1 justify-self-center"></div>
                    <div class="col-span-2 justify-self-center">Date</div>
                    <div class="col-span-2 justify-self-center">Customers</div>
                    <div class="col-span-2 justify-self-center">Discount</div>
                    <div class="col-span-2 justify-self-center">Purchase</div>
                    <div class="col-span-1 justify-self-center"></div>
                </div>
            </div>
            <div class="mt-4 mx-40 w-16 h-8 flex justify-center items-center rounded-md" style="background-color: #BEBEBE; color: #000;">
                <button @click="cashierPage()">
                    Back
                </button>
            </div>
        </div>
    </div>
</template>
 
<style scoped>
.scrollable {
    overflow: auto;
    height: 80%;
}

.content {
    height: 100%;
}
</style>