<template>
  <div id="app">
    <ethboard-header></ethboard-header>
    <ethboard-body v-bind:posted-messages="postedMessages"></ethboard-body>
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
    <metamask></metamask>
    <ethboard-footer>
      v-bind:transactionHash="transactionHash"
      v-bind:errorMessage="errorMessage"
    </ethboard-footer>
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
  components: {
    'ethboard-header': EthBoardHeader,
    'ethboard-body'  : EthBoardBody,
    'ethboard-footer': EthBoardFooter,
    'metamask'       : MetaMask,
  },
  data() {
    return {
      web3: null,
      operator: null,
      postedMessages: [
          {
              id: 1,
              message: "기록된 데이터 가져오기",
          },
          {
              id: 2,
              message: "Second Message",

          },
          {
              id: 3,
              message: "Third Message",
          }
      ],
      transactionHash: '',
      sendPokeMessage: '',
      errorMessage: '',
    }
  },
  created () {
      this.fetchBoard();
    // web3 version 0.20.3, https://github.com/ethereum/wiki/wiki/JavaScript-API
    // window.console.log("started!");
    // const web3rpc = new Web3(new Web3.providers.HttpProvider('http://54.180.107.35:8545'));
    //
    // var boardContract = web3rpc.eth.Contract(boardABI.abi, '0x8d896cca54f657d873add4365a9c5319e5d28ebb');
    //
    // // const latestPos =  boardContract.methods.().call().then((result)
    //
    // boardContract.methods.getPost().call()
    // .then((result) => {
    //     window.console.log(result);
    //     this.postedMessages[0].message=web3.toAscii(result["1"]);
    // });

    // _, this.postedMessages[0].message = boardContract.getPost;

    // window.console.log(this.postedMessages[0].message);
    //
    // this.web3ws = new Web3(new Web3.providers.WebsocketProvider('ws://54.180.107.35:8546'));
    //
    // var storageContract = web3.eth.contract(boardABI);
    // var storageContractInstance = storageContract.at('0x8d896cca54f657d873add4365a9c5319e5d28ebb'); // change to deployed contract address
    //
    // var event = storageContractInstance.Updated({}, []); // inside arguments 필터 역할을 한다.
    //
    // // TODO: Update messages from ethboard contract
    // // this.postedMessages[0].message = 'Updated Message';
    //
    // const self = this;
    // event.watch((error, result) => {
    //   if (!error) console.log(result.args.value);
    //   self.postedMessages = result;
    // });
  },
  //   beforeDestroy () {
  //     event.stopWatching();
  // },
  mounted() {

  },
  methods: {
    fetchBoard() {
      const web3rpc = new Web3(new Web3.providers.HttpProvider('http://54.180.107.35:8545'));

      var boardContract = web3rpc.eth.Contract(boardABI.abi, '0x8d896cca54f657d873add4365a9c5319e5d28ebb');

    // const latestPos =  boardContract.methods.().call().then((result)

      boardContract.methods.getPost().call()
      .then((result) => {
        window.console.log(result);
        this.postedMessages[0].message=result["1"];
      });
    },
    showMessage: async function () {
      if (!this.web3ws.utils.isAddress(this.accountToFaucetPeth)) {
        this.errorMessage = 'invalid address';
        this.transactionHash = '';
        this.accountToFaucetPeth = '';
        return;
      }
      this.web3rpc.eth.sendTransaction({from: this.operator, to: this.accountToFaucetPeth, value: 1000000000000000000, gasPrice: 1}, (err, hash) => {
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
    // postText: async function () {
    //   if (this.postTexts.length > 16) {
    //     this.errorMessage = 'Too long text';
    //     this.transactionHash = '';
    //     return;
    //   }
    //   const data = `0x1504460f${this.postTexts.substring(2)}`
    //   this.web3.eth.sendTransaction({from: this.operator, to: '0x8d896cca54f657d873add4365a9c5319e5d28ebb', data: data, gasPrice: 1}, (err, hash) => {
    //     if (err) {
    //       this.errorMessage = err;
    //       this.transactionHash = '';
    //       return;
    //     }
    //     this.errorMessage = '';
    //     this.transactionHash= hash;
    //   })
    // },
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

</style>
