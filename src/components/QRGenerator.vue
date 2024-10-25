<script setup lang="ts">
import { ref, onMounted } from 'vue';
import QRious from 'qrious';

const inputText = ref('100');
const inputText2 = ref('');
const qr = ref<QRious | null>(null);

const updateQRCode = () => {
  if (!qr.value) {
    qr.value = new QRious({
      element: document.getElementById('qr-code') as HTMLCanvasElement,
      size: 300,
      level: 'M',
    });
  }

  // Pevně dané údaje pro platbu
  const iban = 'CZ6062106701002212108389'; // nahraď svým IBAN
  const currency = 'CZK';
  const prijemce = 'MAREK FORSTER'; // max. 35 znaků
  const msg = 'TESTOVACI QR PLATBA Z WEBU';
  var d1 = new Date().toISOString();
  const dateISO = d1.substring(0, 4) + d1.substring(5, 7) + d1.substring(8, 10);

  // Získání částky z inputu
  var amount = document.getElementById('amount').value;
  if (!amount) amount = 0;
  if (amount < 0 || amount > 99999) {
    alert('Prosím, zadejte platnou částku mezi 1 a 99999 Kč');
    amount = 0;
    return;
  }

  // Získání VS z inputu
  const vs = document.getElementById('vs').value;
  if (vs < 0 || vs > 999999999) {
    alert('Prosím, zadejte platný VS');
    return;
  }

  // Formátování QR kódu podle standardu QR Platba
  var qrData = `SPD*1.0*ACC:${iban}*AM:${amount}*CC:${currency}*DT:${dateISO}*PT:IP*RN:${prijemce}*MSG:${msg}`;
  if (vs && vs != 0) qrData += `*X-VS:${vs}*RF:${vs}`;

  qr.value.value = qrData;
};

onMounted(() => {
  updateQRCode();
});
</script>

<template>
  <section class="py-20 bg-gray-50 dark:bg-gray-800">
    <div class="container">
      <div class="max-w-xl mx-auto text-center">
        <h2 class="text-3xl font-bold mb-8 text-gray-900 dark:text-white">
          Generátor QR kódu<br />pro okamžitou platbu
        </h2>
        <div class="mb-6">
          <label for="amount" class="dark:text-white px-2">Částka:</label>
          <input
            id="amount"
            type="text"
            v-model="inputText"
            @input="updateQRCode"
            placeholder="Zadejte částku"
            class="w-fullx px-4 py-2 rounded-lg border border-gray-300 dark:border-gray-600 focus:ring-2 focus:ring-primary dark:bg-gray-700 dark:text-white"
          />
          <span class="dark:text-white px-2">Kč</span>
        </div>
        <div class="mb-6">
          <label for="vs" class="dark:text-white px-2">VS:</label>
          <input
            id="vs"
            type="text"
            v-model="inputText2"
            @input="updateQRCode"
            placeholder="Zadejte variabilní symbol"
            class="w-fullx px-4 py-2 rounded-lg border border-gray-300 dark:border-gray-600 focus:ring-2 focus:ring-primary dark:bg-gray-700 dark:text-white"
          />
        </div>
        <div
          class="bg-white dark:bg-gray-700x p-6 rounded-lg shadow-md inline-block"
        >
          <canvas id="qr-code"></canvas>
        </div>
      </div>
    </div>
  </section>
</template>
