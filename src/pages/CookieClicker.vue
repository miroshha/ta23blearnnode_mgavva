<script setup>
import { computed, ref } from 'vue';
import Modal from '../components/Modal.vue';

let modalActive = ref(false);
const cookies = ref(0);

let stats = ref({
    'allTimeCookies': {
        count: 0,
        name: 'Cookies baked (all time)'
    },
    'clicked': {
        count: 0,
        name: 'Coockie clicks'
    },
})

const buildings = ref([
    { name: 'Cursor', price: 15, cps: 0.1, count: 0 },
    { name: 'Grandma', price: 100, cps: 1, count: 0 },
    { name: 'Farm', price: 1000, cps: 10, count: 0 },
    { name: 'Factory', price: 10000, cps: 100, count: 0 },
    { name: 'Dragclick', price: 15000, cps: 170, count: 0 },
]);

const upgrades = ref([
    { name: 'Double click', description: 'The cursor is twice as efficient', price: 100, key: 'Cursor', emoji: '👆', isBought: false },
    { name: 'Forwards from grandma', description: 'Grandmas is twice as efficient', price: 500, key: 'Grandma', emoji: '👵', isBought: false },
    { name: 'Texas farmer', description: 'Farms works twice as efficient', price: 1500, key: 'Farm', emoji: '🧺', isBought: false },
])

let buyUpgrade = (key) => {
    let building = buildings.value.find(m => m.name === key)
    building.count *= 2
    let upgrade = upgrades.value.find(m => m.key === key)
    upgrade.isBought = true
    cookies.value -= building.price
}

function buyBuilding(building) {
    cookies.value -= building.price;
    building.price += Math.ceil(building.price / 100 * 15);
    building.count++;
}
let cps = computed(() => {
    let cps = 0;
    buildings.value.forEach(building => {
        cps += building.cps * building.count;
    });
    return cps;
});

let cookieClick = () => {
    // console.log(cookies.value++)
    cookies.value++;
    stats.value.clicked.count++;
    stats.value.allTimeCookies.count++;
}

setInterval(() => {
    cookies.value += cps.value;
    stats.value.allTimeCookies.count += cps.value;
    document.title = '🍪' + cookies.value.toFixed(1) + ' Cookies!';
}, 1000);

</script>
<template>
    <div class="buttons is-centered mb-5">
        <button class="button is-primary" @click="modalActive = true">Stats</button>
    </div>
    <div class="columns is-centered">
        <div class="column is-4 has-background-primary has-text-centered has-text-white">
            <h1 class="is-size-1">{{ +cookies.toFixed(1) }} cookies</h1>
            <h3 class="is-size-3">{{ +cps.toFixed(1) }} cps</h3>
            <figure class="image is-square" @click="cookieClick">
                <img
                    src="https://sweetlorens.com/cdn/shop/products/Copy-of-Chocolate-Chunk-Full-Cookie-transparent-background.png?v=1687811511" />
            </figure>
        </div>
        <div class="column is-4 has-background-link">
            asdas
        </div>
        <div class="column is-3 has-background-warning">
            <div class="columns is-flex-direction-column" style="height: 100%;">
                <div class="column is-flex-grow-0">
                    <div class="dropdown is-hoverable mr-5" v-for="upgrade in upgrades">
                        <div class="dropdown-trigger">
                            <button @click="buyUpgrade(upgrade.key)" class="button has-background-primary is-size-3"
                                aria-haspopup="true" aria-controls="dropdown-menu4" style="height: 60px; width: 60px;"
                                v-if="!upgrade.isBought"
                                :disabled="cookies < upgrade.price"
                                >
                                {{ upgrade.emoji }}
                            </button>
                        </div>
                        <div class="dropdown-menu" id="dropdown-menu4" role="menu">
                            <div class="dropdown-content">
                                <div class="dropdown-item">
                                    <div class="is-flex is-flex-direction-row mb-1 ">
                                        <p class="mr-1 has-text-weight-medium">{{ upgrade.name }}</p>
                                        <p class="has-text-weight-bold">{{ upgrade.price }}🍪</p>
                                    </div>
                                    <p>{{ upgrade.description }}</p>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="column">
                    <button v-for="building in buildings" :disabled="cookies < building.price"
                        @click="buyBuilding(building)" class="button is-primary is-medium is-fullwidth mb-4">
                        <div class=".container.is-fullhd is-justify-content-space-between">
                            <p>{{ building.name }} 🍪{{ building.price }}</p>
                            <p class="has-text-light">{{ building.count }}</p>
                        </div>
                    </button>
                </div>
            </div>
        </div>
    </div>
    <Modal :active="modalActive" @close="modalActive = false">
        <div class="notification is-info">
            <button class="delete"></button>
            <p class="mb-3 is-size-3 has-text-centered has-text-weight-medium">Statistics</p>
            <ul>
                <li v-for="el in stats">{{ el.name }}: {{ el.count.toFixed(1) }}</li>
            </ul>
        </div>
    </Modal>
</template>