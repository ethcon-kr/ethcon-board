<template>
  <div id="ethboard-metamask">
    <slot name="ethboard-metamask"></slot>
    <p> This Section is Metamask button</p>
    <b-input placeholder="Enter text to Post"
          size="is-medium"
          v-model="sendPokeMessage"
        ></b-input>
        <b-button class="send_tx"
          v-on:click="sendPokeTransaction"
        > Post Message </b-button>
  </div>
</template>

<script>
export default {
  created () {
    // const yourAddress = '0x0c54FcCd2e384b4BB6f2E405Bf5Cbc15a017AaFb';
    // const value = '0xde0b6b3a7640000'; // an ether has 18 decimals, here in hex.
    // const desiredNetwork = '3'; // '1' is the Ethereum main network ID.

    if (typeof window.ethereum === 'undefined') {
      // This is the step to make sure metamask is installed.
      alert('Looks like you need a Dapp browser to get started.');
      alert('Consider installing MetaMask!')
    }
  },
  methods: {
    sendPokeTransaction : function () {
      // make transaction parameters in computed function.

      if (this.sendPokeMessage.length > 16) {
        this.errorMessage = 'Too long text';
        this.transactionHash = '';
        return;
      }
      // TODO : convert sendPokeMessage input convert to hex
      const input_data = `0x1504460f${this.sendPokeMessage.substring(0)}`;

      const transactionParameters = {
        nonce: '0x00', // ignored by MetaMask
        gasPrice: '0x01', // customizable by user during MetaMask confirmation.
        gasLimit: '0x2710',  // customizable by user during MetaMask confirmation.
        to: '0x8d896cca54f657d873add4365a9c5319e5d28ebb', // Required except during contract publications.
        from: web3.eth.accounts[0], // must match user's active address.
        value: '0x00', // Only required to send ether to the recipient from the initiating external account.
        data: input_data, // Optional, but used for defining smart contract creation and interaction.
        chainId: 3 // Used to prevent transaction reuse across blockchains. Auto-filled by MetaMask.
      };

      // tx signing
      ethereum.sendAsync({
        method: 'eth_sendTransaction',
        params: [transactionParameters],
        from: ethereum.selectedAddress,
      }, function (err, response) {
          const rejected = 'User denied transaction signature.';
          if (response.error && response.error.message.includes(rejected)) {
            return alert(`We can't take your money without your permission.`)
          }

          if (err) {
            return alert('There was an issue, please try again.')
          }

          if (response.result) {
            // If there is a response.result, the call was successful.
            // In the case of this method, it is a transaction hash.
            const txHash = response.result;
            alert('Sent Message!');

            // You can poll the blockchain to see when this transaction has been mined:
            this.pollForCompletion(txHash, callback)
          }
        })
    }
  },
  watch: {
    pollForCompletion: function (txHash, callback) {
      let calledBack = false

      // Normal ethereum blocks are approximately every 15 seconds.
      // Here we'll poll every 2 seconds.
      const checkInterval = setInterval(function () {
        const notYet = 'response has no error or result';
        ethereum.sendAsync({
          method: 'eth_getTransactionByHash',
          params: [ txHash ],
        }, function (err, response) {
          if (calledBack) return;
          if (err || response.error) {
            if (err.message.includes(notYet)) {
              return 'transaction is not yet mined';
            }

            callback(err || response.error)
          }

          // We have successfully verified the mined transaction.
          // Mind you, we should do this server side, with our own blockchain connection.
          // Client side we are trusting the user's connection to the blockchain.
          const transaction = response.result;
          clearInterval(checkInterval);
          calledBack = true;
          callback(null, transaction)
        })
      }, 2000)
    }
  },
}
</script>

<style>
#ethboard-metamask {
  margin-left: 6em;
  margin-right: 6em;
  margin-bottom: 5em;
}

.send_tx{
  margin-top: 10px;
}
</style>
