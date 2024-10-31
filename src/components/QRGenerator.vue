<script setup lang="ts">
import { ref, onMounted } from 'vue';
import QRious from 'qrious';
const qr = ref<QRious | null>(null);

//var clicked = false;

// get url parameters or set default
const urlParams = new URLSearchParams(window.location.search);
const amountUrl = urlParams.get("amount") || "300";
const vsUrl = urlParams.get("vs") || "";

const clicked = ref(false);

// function for parsing amount in CZK input (amount)
const parseAmount = (amount:string) : string => {
  if (!amount) amount = "0";
  var amountP=(Math.floor(parseFloat(amount)*100))/100;
  if (isNaN(amountP)) {
    amountP=-1;
    } else {
    if (amountP < 0 || amountP > 99999.99) {
      amountP = -2;
      }      
    }
  return amountP.toString();  
  }

// function for parsing variable symbol input (vs)
const parseVS = (vs:string) : string => {
  if (!vs) vs = "";
  var vsP=Math.floor(parseInt(vs));
  if (isNaN(vsP)) {
    vsP=-1;
    } else {
      if (vsP < 0 || vsP > 9999999999) {
        vsP = -2;
      }
    }  
  return vsP.toString();  
  }

// parsing url parameters
var parsedAmount = parseAmount(amountUrl);
if (parseFloat(parsedAmount)<0) {
  parsedAmount="0";
  }

var parsedVS = parseVS(vsUrl);
if (parseInt(parsedVS)<0) {
  parsedVS="";
  }

// fill input boxes
const inputText = ref(parsedAmount);
const inputText2 = ref(parsedVS);

var showResult = ref(false);
var inputError = ref(false);
var amountNum = ref("0");
var vsNum = ref("0");
var linkUrl = ref("");

if(urlParams.has("amount")) {
  showResult.value = true;
  }

const updateQRCode = () => {
  if (!qr.value) {
    qr.value = new QRious({
      element: document.getElementById('qr-code') as HTMLCanvasElement,
      backgroundAlpha: 0,
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

  inputError.value = false;
  
  amountNum.value=parseAmount(inputText.value);
  if (parseFloat(amountNum.value)<0) {
    inputError.value = true;
    amountNum.value = "0";
    }

  vsNum.value=parseVS(inputText2.value);
  //console.log('parseint:'+parseInt(vsNum.value))
  if (parseInt(vsNum.value)<0) {
    if (parseInt(vsNum.value)==-1) vsNum.value = "";
    if (parseInt(vsNum.value)<-1) {
      vsNum.value = "";
      inputError.value = true;
      }
    }


  // Formátování QR kódu podle standardu QR Platba
  if (!inputError.value) {
    var qrData = `SPD*1.0*ACC:${iban}*AM:${amountNum.value}*CC:${currency}*DT:${dateISO}*PT:IP*RN:${prijemce}*MSG:${msg}`;
    if (vsNum.value != "0") qrData += `*X-VS:${vsNum.value}*RF:${vsNum.value}`;
    } else {
    qrData = "SPD*1.0*"; 
    }

  qr.value.value = qrData;
  //console.log('updateQRcode inputError:'+inputError.value + ' amount:' + amountNum.value +  ' VS:'+vsNum.value);

  // Url pro odkaz na aktuální nastavení platby
  const url = new URL(window.location.href);
  const params = new URLSearchParams(url.search);
  if (params.has('amount')) params.delete("amount");
  if (params.has('vs')) params.delete("vs");
  params.append('amount',amountNum.value);
  if (parseInt(vsNum.value)>0) params.append('vs',vsNum.value);
  url.search=params.toString();
  linkUrl.value=url.toString();
};

onMounted(() => {
  updateQRCode();
});

const getResult = () => {
  showResult.value = !showResult.value;
  updateQRCode();
};

const copyUrl = () => {
    navigator.clipboard.writeText(linkUrl.value);
    clicked.value=true;
    var x = setInterval(() => {
      clicked.value=false;
      clearInterval(x);  
      }, 3000);    
}

</script>

<template>
  <section class="py-16 bg-gray-50 dark:bg-gray-800">
    <div class="container">
      <div class="max-w-xl mx-auto text-center">
        <h2 class="md:text-3xl text-2xl font-bold mb-8 text-gray-900 dark:text-white">
          Okamžitý převod peněz<br />pomocí QR kódu
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
            min="0"
            max="9999999999"
            maxlength="10"
            v-model="inputText2"
            @input="updateQRCode"
            placeholder="Zadejte variabilní symbol"
            class="w-fullx px-4 py-2 rounded-lg border border-gray-300 dark:border-gray-600 focus:ring-2 focus:ring-primary dark:bg-gray-700 dark:text-white"
          />
        </div>

        <div v-if="inputError" class="flex items-center p-4 mb-4 text-sm text-red-800 rounded-lg bg-red-200 dark:bg-gray-600 dark:text-red-400 w-9/12 max-w-md max-h-lg  mx-auto" role="alert">
          <span class="sr-only">Info</span>
          <div>
            <span class="font-medium">Pozor! Zadání obsahuje chyby.</span>
            <br/>
            Částka musí být číslo v rozmezí 0 až 99999,99 Kč.
            <br/>
            Variabilní symbol musí být celé číslo v rozmezí 1 až 9999999999 nebo nezadané.
          </div>
        </div>

        <div class="mb-6" v-if="!showResult">
          <button 
          :class="{'bg-red-300' : inputError, 'bg-gradient-to-r from-blue-500 via-blue-600 to-blue-700 hover:bg-gradient-to-br' : !inputError}"
            class="text-white focus:ring-4 focus:outline-none focus:ring-blue-300 dark:focus:ring-blue-800 font-medium rounded-lg text-sm px-5 py-2.5 text-center me-2 mb-2 inline-flex items-center" 
            @click="getResult" 
            :disabled="inputError"
            > 
            <svg class="fill-current w-4 h-4 mr-2" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path d="M3 9h6V3H3zm1-5h4v4H4zm1 1h2v2H5zm10 4h6V3h-6zm1-5h4v4h-4zm1 1h2v2h-2zM3 21h6v-6H3zm1-5h4v4H4zm1 1h2v2H5zm15 2h1v2h-2v-3h1zm0-3h1v1h-1zm0-1v1h-1v-1zm-10 2h1v4h-1v-4zm-4-7v2H4v-1H3v-1h3zm4-3h1v1h-1zm3-3v2h-1V3h2v1zm-3 0h1v1h-1zm10 8h1v2h-2v-1h1zm-1-2v1h-2v2h-2v-1h1v-2h3zm-7 4h-1v-1h-1v-1h2v2zm6 2h1v1h-1zm2-5v1h-1v-1zm-9 3v1h-1v-1zm6 5h1v2h-2v-2zm-3 0h1v1h-1v1h-2v-1h1v-1zm0-1v-1h2v1zm0-5h1v3h-1v1h-1v1h-1v-2h-1v-1h3v-1h-1v-1zm-9 0v1H4v-1zm12 4h-1v-1h1zm1-2h-2v-1h2zM8 10h1v1H8v1h1v2H8v-1H7v1H6v-2h1v-2zm3 0V8h3v3h-2v-1h1V9h-1v1zm0-4h1v1h-1zm-1 4h1v1h-1zm3-3V6h1v1z"/><path fill="none" d="M0 0h24v24H0z"/></svg>
            <span>Generuj QR kód platby</span>
          </button>
        </div>

        <div 
          v-show="!inputError"
          :class="{'bg-red-600' : inputError, 'bg-white' : !inputError}"
          class="p-6 rounded-lg shadow-md inline-block w-9/12 max-w-md max-h-lg" 
        >
          <canvas class="w-full h-full" id="qr-code"></canvas>
        </div>

        <div class="mb-6 text-black dark:text-white" v-if="showResult">
          <table class="w-full justify-center my-4">
            <tbody>
              <tr>
                <td class="text-right w-6/12">Částka:</td>
                <td class="text-left pl-4">{{amountNum}} Kč</td>
              </tr>
              <tr>
                <td class="text-right w-6/12">VS / Ref ID:</td>
                <td class="text-left pl-4">{{vsNum ? vsNum: "neuvedeno"}}</td>
              </tr>
              <tr v-if="!inputError">
                <td class="text-right w-6/12">
                  <a class="underline text-blue-600 hover:text-blue-800 visited:text-purple-600" :href="linkUrl">Odkaz na tuto platbu</a></td>
                <td class="text-left pl-4">
                  <button 
                  class="button text-xs  text-gray-800 font-bolder py-2 px-4 rounded inline-flex items-center my-4" 
                  type="button" 
                  @click="copyUrl()"
                  :class="{'bg-blue-300 hover:bg-blue-400' : clicked, 'bg-gray-300 hover:bg-gray-400' : !clicked}"
                  >
                  <svg class="fill-current w-4 h-4 mr-2" viewBox="0 0 48 48"  xmlns="http://www.w3.org/2000/svg"><path d="M0 0h48v48h-48z" fill="none"/><path d="M32 2h-24c-2.21 0-4 1.79-4 4v28h4v-28h24v-4zm6 8h-22c-2.21 0-4 1.79-4 4v28c0 2.21 1.79 4 4 4h22c2.21 0 4-1.79 4-4v-28c0-2.21-1.79-4-4-4zm0 32h-22v-28h22v28z"/></svg>
                  {{ clicked? "Odkaz zkopírován" : "Zkopírovat odkaz" }}
                </button>
                </td>
              </tr>
            </tbody>  
          </table>
          <button class="text-white bg-gradient-to-r from-blue-500 via-blue-600 to-blue-700 hover:bg-gradient-to-br focus:ring-4 focus:outline-none focus:ring-blue-300 dark:focus:ring-blue-800 font-medium rounded-lg text-sm px-5 py-2.5 text-center me-2 mb-2 inline-flex items-center" @click="getResult"> 
            <svg class="fill-current w-4 h-4 mr-2" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path d="M3 9h6V3H3zm1-5h4v4H4zm1 1h2v2H5zm10 4h6V3h-6zm1-5h4v4h-4zm1 1h2v2h-2zM3 21h6v-6H3zm1-5h4v4H4zm1 1h2v2H5zm15 2h1v2h-2v-3h1zm0-3h1v1h-1zm0-1v1h-1v-1zm-10 2h1v4h-1v-4zm-4-7v2H4v-1H3v-1h3zm4-3h1v1h-1zm3-3v2h-1V3h2v1zm-3 0h1v1h-1zm10 8h1v2h-2v-1h1zm-1-2v1h-2v2h-2v-1h1v-2h3zm-7 4h-1v-1h-1v-1h2v2zm6 2h1v1h-1zm2-5v1h-1v-1zm-9 3v1h-1v-1zm6 5h1v2h-2v-2zm-3 0h1v1h-1v1h-2v-1h1v-1zm0-1v-1h2v1zm0-5h1v3h-1v1h-1v1h-1v-2h-1v-1h3v-1h-1v-1zm-9 0v1H4v-1zm12 4h-1v-1h1zm1-2h-2v-1h2zM8 10h1v1H8v1h1v2H8v-1H7v1H6v-2h1v-2zm3 0V8h3v3h-2v-1h1V9h-1v1zm0-4h1v1h-1zm-1 4h1v1h-1zm3-3V6h1v1z"/><path fill="none" d="M0 0h24v24H0z"/></svg>
            <span>Zpátky k zadání parametrů</span>
          </button>
        </div>

      </div>
    </div>
  </section>
</template>
