<script lang="ts">
	import IonTabs from '$lib/IonTabs.svelte';

	import { videocam as videocamIcon, pin as pinIcon } from 'ionicons/icons';
	import { onMount } from 'svelte';

	import { goto } from '$app/navigation';

	const myTabs = [
		{
			label: 'Explain',
			icon: pinIcon,
			tab: '/tabs/test1'
		},
		{ label: 'Controllers', icon: videocamIcon, tab: '/tabs/test2' }
	];

	const logStuff = (event: any) => {
		console.log(
			'available tabs:',
			event.detail.tabs.map((tab: any) => tab.tab)
		);
		if (event.detail.tabs.length !== myTabs.length) {
			console.error('tabs length mismatch');
			event.preventDefault();
		}
	};

	let loaded = false;

	let ionTabsComp: IonTabs;

	/*onMount(() => {
		const currentTab = window.location.pathname.split('/').at(-1);
		if (currentTab === '') {
			goto(`/tabs/${myTabs[0].tab}`).then(() => {
			});
		} else if (!myTabs.some((tab) => tab.tab === currentTab)) {
			goto(`/tabs/${myTabs[0].tab}`).then(() => {
				ionTabsComp.$set({ tabs: myTabs });
			});
		}
	});*/
</script>

<!-- if url matches one of the tabs then show tab bar, else redirect... -->
<IonTabs
	slot="bottom"
	bind:this={ionTabsComp}
	tabs={myTabs}
	on:ionTabsWillChange={logStuff}
	on:ionTabsDidChange={logStuff}
>
	<slot />
</IonTabs>
