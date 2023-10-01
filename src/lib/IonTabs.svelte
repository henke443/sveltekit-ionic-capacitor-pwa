<script lang="ts">
	import { onMount } from 'svelte';
	import { page } from '$app/stores';

	import { goto } from '$app/navigation';

	export let ionTabsDidChange = () => {};
	export let ionTabsWillChange = () => {};
	export let slot = 'bottom';

	let ionTabBarElement: HTMLIonTabsElement;

	export let tabs: {
		label: string;
		icon: string;
		tab: string;
	}[] = [];

	let controller: HTMLIonTabsElement;
	onMount(async () => {
		// reassignment needed after onMount
		controller = ionTabBarElement;
		const { pathname } = $page.url;

		const goToFirstTab = async () => {
			await goto(tabs[0]?.tab);
			controller.select(tabs[0]?.tab);
		};

		if (!tabs.map((tab) => tab.tab).includes(pathname)) {
			goToFirstTab();
		}
	});

	const tabBarClick = async (selectedTab: string) => {
		console.log('selectedTab:', selectedTab, 'all tabs:', tabs);
		if (!selectedTab || typeof selectedTab !== 'string') {
			console.error('Invalid selectedTab:', selectedTab);
			return;
		}
		await goto(selectedTab).then(() => {
			controller
				.getTab(selectedTab)
				.then((tab) => {
					console.log('tab:', tab, tab?.tab, tab && tab['tabs']);
					controller.select(selectedTab);
				})
				.catch((err) => {
					console.error('getTab error:', err);
				});
		});
	};
</script>

<ion-tabs
	on:ionTabsDidChange={ionTabsDidChange}
	on:ionTabsWillChange={ionTabsWillChange}
	bind:this={ionTabBarElement}
>
	<slot />

	{#if slot === 'bottom' || slot === ''}
		<ion-tab-bar slot="bottom">
			{#each tabs as tab}
				<!-- svelte-ignore a11y-no-static-element-interactions -->
				<ion-tab-button
					tab={tab.tab}
					tabindex="0"
					role="button"
					on:keydown={() => {
						tabBarClick(tab.tab);
					}}
					on:click={() => {
						tabBarClick(tab.tab);
					}}
				>
					<ion-label>{tab.label}</ion-label>
					<ion-icon icon={tab.icon} />
				</ion-tab-button>
			{/each}
		</ion-tab-bar>
	{/if}

	{#if slot === 'top'}
		<ion-tab-bar slot="top">
			{#each tabs as tab}
				<ion-tab-button
					tab={tab.tab}
					on:keydown={() => {
						tabBarClick(tab.tab);
					}}
					on:click={() => {
						tabBarClick(tab.tab);
					}}
				>
					<ion-label>{tab.label}</ion-label>
					<ion-icon icon={tab.icon} />
				</ion-tab-button>
			{/each}
		</ion-tab-bar>
	{/if}
</ion-tabs>
