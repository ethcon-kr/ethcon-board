<template>
  <div id="app">
    <ethboard-header></ethboard-header>
    <ethboard-body>
      <div slot="ethboard-body">
        <b-input placeholder="Enter your testnet account address"
          size="is-medium"
          v-model="accountToFaucetPeth"
        ></b-input>
        <b-button class="faucet-button"
          v-on:click="showMessage"
        > Request PETH </b-button>
      </div>
    </ethboard-body>
    <!--<ethboard-post>-->
      <!--<div slot="ethboard-post">-->
        <!--<b-input placeholder="Enter text to Post"-->
          <!--size="is-medium"-->
          <!--v-model="postToboard"-->
        <!--&gt;</b-input>-->
        <!--<b-button class="post-button"-->
          <!--v-on:click="post_string"-->
        <!--&gt; Post Text </b-button>-->
      <!--</div>-->
    <!--</ethboard-post>-->
    <board
      v-bind:message="message"
    ></board>
    <metamask></metamask>
    <faucet-footer
      v-bind:transactionHash="transactionHash"
      v-bind:errorMessage="errorMessage"
    ></faucet-footer>
  </div>
</template>

<script>
import EthBoardHeader from './components/EthboardHeader.vue';
import EthBoardBody   from './components/EthboardBody.vue';
import EthBoardFooter from './components/EthboardFooter.vue';
import boardABI       from './contracts/DSMessage.json'
import MetaMask from './components/MetaMask.vue';
import Web3         from 'web3'

export default {
  name: 'app',
  data() {
    return {
      web3: null,
      operator: null,
      accountToFaucetPeth: null,
      message: '',
      postTexts: null,
      transactionHash: '',
      sendPokeMessage: '',
      errorMessage: '',
    }
  },
  components: {
    'ethboard-header': EthBoardHeader,
    'ethboard-body': EthBoardBody,
    'faucet-footer': EthBoardFooter,
      'metamask' : MetaMask,
  },
  created () {
    // web3 version 0.20.3, https://github.com/ethereum/wiki/wiki/JavaScript-API
    this.web3 = new Web3(new Web3.providers.WebsocketProvider('ws://54.180.107.35:8546'));

    var storageContract = web3.eth.contract(boardABI);
    var storageContractInstance = storageContract.at('0xd91fb8750ea1decef4cee9d8314f4a60de039457'); // change to deployed contract address
    var event = storageContractInstance.Created({}, []) // inside arguments 필터 역할을 한다.

    const self = this;
    event.watch((error, result) => {
      if (!error) console.log(result.args.value);
      self.message = result;
    });
  },
  methods: {
    showMessage: async function () {
      if (!this.web3.utils.isAddress(this.accountToFaucetPeth)) {
        this.errorMessage = 'invalid address';
        this.transactionHash = '';
        this.accountToFaucetPeth = '';
        return;
      }
      this.web3.eth.sendTransaction({from: this.operator, to: this.accountToFaucetPeth, value: 1000000000000000000, gasPrice: 1}, (err, hash) => {
        if (err) {
          this.errorMessage = err;
          this.transactionHash = '';
          this.accountToFaucetPeth = '';
          return;
        }
        this.errorMessage = '';
        this.transactionHash= hash;
        this.accountToFaucetPeth = '';
      })
    },
    postText: async function () {
      if (this.postTexts.length > 16) {
        this.errorMessage = 'Too long text';
        this.transactionHash = '';
        return;
      }
      const data = `0x1504460f${this.postTexts.substring(2)}`
      this.web3.eth.sendTransaction({from: this.operator, to: '0x8d896cca54f657d873add4365a9c5319e5d28ebb', data: data, gasPrice: 1}, (err, hash) => {
        if (err) {
          this.errorMessage = err;
          this.transactionHash = '';
          return;
        }
        this.errorMessage = '';
        this.transactionHash= hash;
      })
    },
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
.faucet-button {
  margin-top: 10px;
}

.post-button{
  margin-top: 10px;
}
</style>
