<script lang="ts">
  import { onMount } from "svelte";
  import { TezosToolkit } from "@taquito/taquito";
  import { TezBridgeSigner } from "@taquito/tezbridge-signer";
  import { BeaconWallet } from "@taquito/beacon-wallet";

  let Tezos, tezbridgeSigner, beaconWallet;
  let payload = "Taquito is awesome!";
  let signedResult = { tezbridge: "", beacon: "", thanos: "" };

  const initTezbridgeSigner = async () => {
    signedResult.tezbridge = "";
    if (!tezbridgeSigner) {
      tezbridgeSigner = new TezBridgeSigner();
    }

    signedResult.tezbridge = await tezbridgeSigner.sign(payload);
  };

  const initBeaconSigner = async () => {
    signedResult.beacon = "";
    if (!beaconWallet) {
      beaconWallet = new BeaconWallet({ name: "Taquito Payload Signing" });
    }
    await beaconWallet.requestPermissions({ network: { type: "custom" } });
    Tezos.setWalletProvider(beaconWallet.client);

    signedResult.beacon = await beaconWallet.client.requestSignPayload({
      payload
    });
  };

  onMount(() => {
    Tezos = new TezosToolkit("https://delphinet-tezos.giganode.io");
  });
</script>

<style>
  main {
    height: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;
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

  #results {
    height: 300px;
    width: 100%;
    margin: 20px 0px;
    overflow: auto;
    padding: 10px 30px;
  }

  .signed-result {
    width: 100%;
    padding: 10px;
    font-size: 1.1rem;
    white-space: pre-wrap; /* css-3 */
    white-space: -moz-pre-wrap; /* Mozilla, since 1999 */
    white-space: -pre-wrap; /* Opera 4-6 */
    white-space: -o-pre-wrap; /* Opera 7 */
    word-wrap: break-word; /* Internet Explorer 5.5+ */
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
    <div id="results">
      {#if signedResult.tezbridge}
        <pre
          class="signed-result">
      {`Tezbridge result: ${JSON.stringify(signedResult.tezbridge, null, 2)}`}
    </pre>
      {/if}
      {#if signedResult.beacon}
        <pre
          class="signed-result">
      {`Beacon result: ${JSON.stringify(signedResult.beacon, null, 2)}`}
    </pre>
      {/if}
    </div>
  </div>
</main>
