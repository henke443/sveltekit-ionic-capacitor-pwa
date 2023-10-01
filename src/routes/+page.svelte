<script lang="ts">
	import { Geolocation } from '@capacitor/geolocation';
	let loc: any = null;
	async function getCurrentPosition() {
		const res = await Geolocation.getCurrentPosition();
		loc = res;
	}

	let modal!: HTMLIonModalElement;
	let input!: HTMLIonInputElement;

	function cancel() {
		modal?.dismiss(null, 'cancel');
	}

	function confirm() {
		modal.dismiss(input?.value, 'confirm');
	}

	const willDismissHdnl = (ev: any) => {
		console.log('willDismissCalled, event:', ev);

		if (!ev || !ev['detail']) {
			return;
		}
		console.log('ev.detail.role:', ev.detail.role);
		if (ev.detail.role === 'confirm') {
			const message = document.querySelector('#message');
			if (message) message.textContent = `Hello ${ev.detail.data}!`;
		}
	};

	////////////////////////////////////////////////////
	import { pwaBeforeInstallPrompt, canInstall } from '$lib/services/pwa';
	import { isPlatform } from '@ionic/core';
	import { modalController } from 'ionic-svelte/utils/controllers';
	import { download as downloadIcon } from 'ionicons/icons';
	import IOSInstall from '$lib/components/IOSInstall.svelte';

	let iosInstall = isPlatform('ios') && !isPlatform('pwa');

	const showIOSinstall = async () => {
		const modal = await modalController.create({
			component: IOSInstall,
			componentProps: {},
			showBackdrop: true,
			backdropDismiss: false
		});

		modal.onDidDismiss().then((value) => {
			console.log('Dismissed the modal', value);
			if (value.role === 'backdrop') console.log('Backdrop clicked');
		});

		await modal.present();
	};
</script>

<ion-header>
	<ion-toolbar>
		<ion-title>Inline Modal</ion-title>
	</ion-toolbar>
</ion-header>

<ion-content class="ion-padding">
	<ion-button id="open-modal" expand="block">Open</ion-button>
	<p id="message">
		This modal example uses triggers to automatically open a modal when the button is clicked.
	</p>
	{#if iosInstall}
		<ion-item on:click={showIOSinstall} on:keyup={showIOSinstall}>
			<ion-icon icon={allIonicIcons['download']} slot="start" />
			<ion-label>Install this app as PWA</ion-label>
		</ion-item>
		<ion-item />
	{/if}
	{#if $canInstall}
		<ion-item
			on:keyup={() => {
				const prompt = $pwaBeforeInstallPrompt;
				prompt.prompt();
			}}
			on:click={() => {
				const prompt = $pwaBeforeInstallPrompt;
				prompt.prompt();
			}}
		>
			<ion-icon icon={allIonicIcons['download']} slot="start" />
			<ion-label>Install this app as PWA</ion-label>
		</ion-item>
		<ion-item />
	{/if}

	<ion-modal on:willDismiss={willDismissHdnl} bind:this={modal} trigger="open-modal">
		<ion-header>
			<ion-toolbar>
				<ion-buttons slot="start">
					<ion-button on:click={cancel}>Cancel</ion-button>
				</ion-buttons>
				<ion-title>Welcome</ion-title>
				<ion-buttons slot="end">
					<ion-button on:click={confirm} strong={true}>Confirm</ion-button>
				</ion-buttons>
			</ion-toolbar>
		</ion-header>
		<ion-content class="ion-padding">
			<ion-item>
				<ion-label position="stacked">Enter your name</ion-label>
				<ion-input bind:this={input} type="text" placeholder="Your name" />
			</ion-item>
			<ion-item
				><div style="padding: 20px 0">
					<h1>Geolocation</h1>
					<p>Your location is:</p>
					<p>Latitude: {loc?.coords.latitude}</p>
					<p>Longitude: {loc?.coords.longitude}</p>

					<ion-button
						role="button"
						tabindex="0"
						on:keydown={getCurrentPosition}
						on:click={getCurrentPosition}
					>
						Get Current Location
					</ion-button>
				</div>
			</ion-item>
		</ion-content>
	</ion-modal>
</ion-content>
