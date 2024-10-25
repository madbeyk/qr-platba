<script setup lang="ts">
import { ref, onMounted } from 'vue';
import QRious from 'qrious';

const inputText = ref('250');
const inputText2 = ref('');
const qr = ref<QRious | null>(null);
var showResult = ref(false);

const updateQRCode = () => {
  if (!qr.value) {
    qr.value = new QRious({
      //element: document.getElementById('qr-code') as HTMLCanvasElement,
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
  //var amount = document.getElementById('amount').value;
  var amount = inputText.value;
  if (!amount) amount = "0";
  /*
  if (amount < 0 || amount > 99999) {
    alert('Prosím, zadejte platnou částku mezi 1 a 99999 Kč');
    amount = "0";
    return;
  }
  */

  // Získání VS z inputu
  //const vs = document.getElementById('vs').value;
  var vs = inputText2.value;
  /*
  if (vs < 0 || vs > 9999999999) {
    alert('Prosím, zadejte platný VS');
    return;
  }
  */

  // Formátování QR kódu podle standardu QR Platba
  var qrData = `SPD*1.0*ACC:${iban}*AM:${amount}*CC:${currency}*DT:${dateISO}*PT:IP*RN:${prijemce}*MSG:${msg}`;
  if (vs && vs != "0") qrData += `*X-VS:${vs}*RF:${vs}`;

  qr.value.value = qrData;
};

onMounted(() => {
  updateQRCode();
});

const getResult = () => {
  console.log('Generate clicked! '+showResult.value);
  showResult.value = !showResult.value;
  updateQRCode();
};

</script>

<template>
  <section class="py-16 bg-gray-50 dark:bg-gray-800">
    <div class="container">
      <div class="max-w-xl mx-auto text-center">
        <h2 class="text-3xl font-bold mb-8 text-gray-900 dark:text-white">
          Generátor QR kódu<br />pro okamžitou platbu
        </h2>
        <div class="mb-6" v-if="!showResult">
          <label for="amount" class="dark:text-white px-2">Částka:</label>
          <input
            id="amount"
            type="number"
            step="0.01"
            min="0"
            max="99999"
            v-model="inputText"
            @input="updateQRCode"
            placeholder="Zadejte částku"
            class="w-fullx px-4 py-2 rounded-lg border border-gray-300 dark:border-gray-600 focus:ring-2 focus:ring-primary dark:bg-gray-700 dark:text-white"
          />
          <span class="dark:text-white px-2">Kč</span>
        </div>
        <div class="mb-6" v-if="!showResult">
          <label for="vs" class="dark:text-white px-2">VS:</label>
          <input
            id="vs"
            type="number"
            step="1"
            min="1"
            max="9999999999"
            maxlength="10"
            v-model="inputText2"
            @input="updateQRCode"
            placeholder="Zadejte variabilní symbol"
            class="w-fullx px-4 py-2 rounded-lg border border-gray-300 dark:border-gray-600 focus:ring-2 focus:ring-primary dark:bg-gray-700 dark:text-white"
          />
        </div>
        <div class="mb-6" v-if="!showResult">
          <button class="button bg-gray-300 hover:bg-gray-400 text-gray-800 font-bolder py-2 px-4 rounded inline-flex items-center" @click="getResult"> 
            <svg class="fill-current w-4 h-4 mr-2" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path d="M3 9h6V3H3zm1-5h4v4H4zm1 1h2v2H5zm10 4h6V3h-6zm1-5h4v4h-4zm1 1h2v2h-2zM3 21h6v-6H3zm1-5h4v4H4zm1 1h2v2H5zm15 2h1v2h-2v-3h1zm0-3h1v1h-1zm0-1v1h-1v-1zm-10 2h1v4h-1v-4zm-4-7v2H4v-1H3v-1h3zm4-3h1v1h-1zm3-3v2h-1V3h2v1zm-3 0h1v1h-1zm10 8h1v2h-2v-1h1zm-1-2v1h-2v2h-2v-1h1v-2h3zm-7 4h-1v-1h-1v-1h2v2zm6 2h1v1h-1zm2-5v1h-1v-1zm-9 3v1h-1v-1zm6 5h1v2h-2v-2zm-3 0h1v1h-1v1h-2v-1h1v-1zm0-1v-1h2v1zm0-5h1v3h-1v1h-1v1h-1v-2h-1v-1h3v-1h-1v-1zm-9 0v1H4v-1zm12 4h-1v-1h1zm1-2h-2v-1h2zM8 10h1v1H8v1h1v2H8v-1H7v1H6v-2h1v-2zm3 0V8h3v3h-2v-1h1V9h-1v1zm0-4h1v1h-1zm-1 4h1v1h-1zm3-3V6h1v1z"/><path fill="none" d="M0 0h24v24H0z"/></svg>
            <span>Generuj QR kód platby</span>
          </button>
        </div>

        <div
          class="bg-white dark:bg-gray-700x p-6 rounded-lg shadow-md inline-block w-9/12  max-w-md max-h-lg" 
        >
          <canvas class="w-full h-full" id="qr-code"></canvas>
        </div>

        <div class="mb-6 text-black dark:text-white" v-if="showResult">
          <table class="w-full justify-center my-4">
            <tbody>
              <tr>
                <td class="text-right w-6/12">Částka:</td>
                <td class="text-left pl-4">{{inputText}} Kč</td>
              </tr>
              <tr>
                <td class="text-right w-6/12">VS / Ref ID:</td>
                <td class="text-left pl-4">{{inputText2 ? inputText2: "neuvedeno"}}</td>
              </tr>
            </tbody>  
          </table>
          <button class="button bg-gray-300 hover:bg-gray-400 text-gray-800 font-bolder py-2 px-4 rounded inline-flex items-center" @click="getResult"> 
            <svg class="fill-current w-4 h-4 mr-2" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path d="M3 9h6V3H3zm1-5h4v4H4zm1 1h2v2H5zm10 4h6V3h-6zm1-5h4v4h-4zm1 1h2v2h-2zM3 21h6v-6H3zm1-5h4v4H4zm1 1h2v2H5zm15 2h1v2h-2v-3h1zm0-3h1v1h-1zm0-1v1h-1v-1zm-10 2h1v4h-1v-4zm-4-7v2H4v-1H3v-1h3zm4-3h1v1h-1zm3-3v2h-1V3h2v1zm-3 0h1v1h-1zm10 8h1v2h-2v-1h1zm-1-2v1h-2v2h-2v-1h1v-2h3zm-7 4h-1v-1h-1v-1h2v2zm6 2h1v1h-1zm2-5v1h-1v-1zm-9 3v1h-1v-1zm6 5h1v2h-2v-2zm-3 0h1v1h-1v1h-2v-1h1v-1zm0-1v-1h2v1zm0-5h1v3h-1v1h-1v1h-1v-2h-1v-1h3v-1h-1v-1zm-9 0v1H4v-1zm12 4h-1v-1h1zm1-2h-2v-1h2zM8 10h1v1H8v1h1v2H8v-1H7v1H6v-2h1v-2zm3 0V8h3v3h-2v-1h1V9h-1v1zm0-4h1v1h-1zm-1 4h1v1h-1zm3-3V6h1v1z"/><path fill="none" d="M0 0h24v24H0z"/></svg>
            <span>Zpátky k zadání parametrů</span>
          </button>
        </div>

      </div>
    </div>
  </section>
</template>
