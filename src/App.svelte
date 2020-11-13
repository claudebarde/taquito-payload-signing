<script lang="ts">
  import { onMount } from "svelte";
  import { TezosToolkit } from "@taquito/taquito";
  import { TezBridgeSigner } from "@taquito/tezbridge-signer";
  import { BeaconWallet } from "@taquito/beacon-wallet";

  let Tezos, tezbridgeSigner, beaconWallet;
  let payload = "Taquito is awesome!";
  let signedResult = "";

  const initTezbridgeSigner = async () => {
    signedResult = "";
    if (!tezbridgeSigner) {
      tezbridgeSigner = new TezBridgeSigner();
    }

    signedResult = await tezbridgeSigner.sign(payload);
  };

  const initBeaconSigner = async () => {
    signedResult = "";
    if (!beaconWallet) {
      beaconWallet = new BeaconWallet({ name: "Taquito Payload Signing" });
    }
    await beaconWallet.requestPermissions({ network: { type: "custom" } });
    Tezos.setWalletProvider(beaconWallet.client);

    signedResult = await beaconWallet.client.requestSignPayload({ payload });
  };

  onMount(() => {
    Tezos = new TezosToolkit("https://delphinet-tezos.giganode.io");
  });
</script>

<style>
  main {
    height: 100%;
    display: grid;
    place-items: center;
  }

  .main-block {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    width: 50%;
  }

  #logo {
    display: flex;
    justify-content: center;
  }

  #logo img {
    width: 50%;
  }

  #payload-input {
    width: 100%;
    height: 150px;
  }

  #signed-result {
    width: 100%;
    height: 200px;
    padding: 10px;
    margin: 20px 0px;
    font-size: 1.1rem;
    white-space: pre-wrap; /* css-3 */
    white-space: -moz-pre-wrap; /* Mozilla, since 1999 */
    white-space: -pre-wrap; /* Opera 4-6 */
    white-space: -o-pre-wrap; /* Opera 7 */
    word-wrap: break-word; /* Internet Explorer 5.5+ */
    overflow: auto;
  }
</style>

<main>
  <div class="main-block">
    <h1>Taquito Payload Signing</h1>
    <div id="logo"><img src="built-with-taquito.png" alt="taquito" /></div>
    <p>Please insert below the payload you want to sign:</p>
    <textarea name="payload-input" id="payload-input" bind:value={payload} />
    <p>Sign with:</p>
    <div class="buttons">
      <button id="tezbridge" on:click={initTezbridgeSigner}>TezBridge</button>
      <button id="beacon" on:click={initBeaconSigner}>Beacon</button>
      <button id="thanos">Thanos</button>
    </div>
    <pre
      id="signed-result">
      {signedResult ? `Result: ${JSON.stringify(signedResult, null, 2)}` : ''}
    </pre>
  </div>
</main>
