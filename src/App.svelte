<script>
	import { defaultChainStore, web3, selectedAccount, connected, chainData } from 'svelte-web3';
	import { writable } from 'svelte/store'

	const acc1Balance = writable(0)
	const acc2Balance = writable(0)
	const coin = writable("ETH")
	const dec = writable(0)
	const balance = writable(0)

	async function connect() {
		console.log(window?.ethereum);
		await defaultChainStore.setBrowserProvider();

		let accBalance = await $web3.eth.getBalance(`${$selectedAccount}`);
		let hhBalance1 = await $web3.eth.getBalance(`0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266`);
		let hhBalance2 = await $web3.eth.getBalance(`0x70997970c51812dc3a010c7d01b50e0d17dc79c8`);
		let decim = await $chainData.nativeCurrency.decimals
		let tok = await $chainData.nativeCurrency.symbol

		balance.set(accBalance)
		acc1Balance.set(hhBalance1)
		acc2Balance.set(hhBalance2)
		coin.set(tok)
		dec.set(decim)
	}

	function disconnect() {
		defaultChainStore.close();
		acc1Balance.set(0)
		acc2Balance.set(0)
		coin.set("ETH")
		dec.set(0)
	}

	function checkConnected() {
		console.log($connected);
	}

	async function  consoleLog() {
		console.log("web3:", $web3)
		console.log("selectedAccount:", $selectedAccount)
		console.log("chainData:", $chainData)

		
	}

	function values() {
		console.log($acc1Balance, $acc2Balance, $coin, $dec)
	}

	async function trans1to2() {
		$web3.eth.sendTransaction({
			from: `0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266`,
			to: `0x70997970c51812dc3a010c7d01b50e0d17dc79c8`,
			value: `1000000000000000000`
		}).then(function(receipt){
			console.log("r:", receipt);
		})
	}

	async function trans2to1() {

	}


</script>

<main>
	<h1>Web3 Sandbox</h1>
	<p1>{$selectedAccount ? "Wallet: " + $selectedAccount : ""}<br></p1>
	<p1>{$balance ? "Account Balance: " + $balance : ""}</p1>

	<div>
		<button on:click={connect}>Connect</button>
		<button on:click={disconnect}>Disconnect</button>
	</div> 
	<div>
		<button on:click={checkConnected}>Check If Connected</button>
		<button on:click={consoleLog}>Console Log</button>		
		<button on:click={values}>Values</button>

	</div>
	<div>
		<p1>{"HH Account 1 Balance: " + ($acc1Balance / (10 ** $dec)) + " " + $coin}<br></p1>
		<p1>{"HH Account 2 Balance: " + ($acc2Balance / (10 ** $dec)) + " " + $coin}<br></p1>
	</div>
	<div>
		<br> 
		<button on:click={trans1to2}>1 to 2</button>
		<br>
		<button on:click={trans2to1}>2 to 1</button>
		<br>
	</div> 

</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>