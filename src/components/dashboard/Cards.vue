<script setup lang="ts">
import { useCard } from "../../store/cardStore";
import ChartComponent from "./ChartComponent.vue"

const cardStore = useCard();
const cardData = cardStore.cardData;

// localStorage.setItem("cardData", JSON.stringify(cardData));
</script>

<template>
    <ul class="box-info">
        <li v-for="(card, index) in cardData" :key="index">
            <ChartComponent :classChart="card.class" :percentage="card.percentage" />
            <span class="text">
                <h3>{{ card.value }}</h3>
                <p>{{ card.label }}</p>
            </span>
        </li>
    </ul>
</template>