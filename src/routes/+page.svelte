<script lang="ts">
	import type { Leg } from '../lib/Journey';
	import { Autocomplete, popup } from '@skeletonlabs/skeleton';
	import type { AutocompleteOption, PopupSettings } from '@skeletonlabs/skeleton';
	import { faDice } from '@fortawesome/free-solid-svg-icons';
	import Fa from 'svelte-fa';
	let testLegs: Leg[] = [
		{
			departureStation: 'Gümligen',
			departureTime: '10:32',
			departurePlatform: '2',
			trainDesignation: 'RE203',
			arrivalStation: 'Laupen',
			arrivalTime: '11:15',
			arrivalPlatform: '3'
		},
		{
			departureStation: 'Laupen',
			departureTime: '11:35',
			departurePlatform: '2',
			trainDesignation: 'R27',
			arrivalStation: 'Neuchatel',
			arrivalTime: '12:30',
			arrivalPlatform: '5'
		},
		{
			departureStation: 'Neuchatel',
			departureTime: '13:00',
			departurePlatform: '7',
			trainDesignation: 'IC326',
			arrivalStation: 'Genf',
			arrivalTime: '14:15',
			arrivalPlatform: '7'
		},
		{
			departureStation: 'Genf',
			departureTime: '14:25',
			departurePlatform: '7',
			trainDesignation: 'IC5',
			arrivalStation: 'Bern',
			arrivalTime: '16:12',
			arrivalPlatform: '7'
		}
	];
	let pastLegs: Leg[] = [];

	let currentLeg: Leg;

	let testLegIndex = 0;

	let popupSettings: PopupSettings = {
		event: 'focus-click',
		target: 'popupFeatured',
		placement: 'bottom'
	};

	let nextDepartureStation = '';

	const stationOptions: AutocompleteOption<string>[] = [
		{ label: 'Lugano', value: 'vanilla', keywords: 'plain, basic', meta: { healthy: false } },
		{ label: 'Burgdorf', value: 'chocolate', keywords: 'dark, white', meta: { healthy: false } },
		{ label: 'Lausanne', value: 'strawberry', keywords: 'fruit', meta: { healthy: true } },
		{
			label: 'Langenthal',
			value: 'neapolitan',
			keywords: 'mix, strawberry, chocolate, vanilla',
			meta: { healthy: false }
		},
		{ label: 'Thun', value: 'pineapple', keywords: 'fruit', meta: { healthy: true } },
		{ label: 'Basel', value: 'peach', keywords: 'fruit', meta: { healthy: true } }
	];

	function onStationSelection(event: CustomEvent<AutocompleteOption<string>>): void {
		nextDepartureStation = event.detail.label;
	}

	let legInProgress = false;

	function assignNextLeg() {
		currentLeg = testLegs[testLegIndex];
		testLegIndex++;
		nextDepartureStation = currentLeg.arrivalStation;
		legInProgress = true;
	}

	function cancelCurrentLeg() {
		testLegIndex--;
		nextDepartureStation = '';
		legInProgress = false;
	}

	function completeCurrentLeg() {
		legInProgress = false;
		let newPastLegs = pastLegs;
		newPastLegs.push(currentLeg);
		pastLegs = newPastLegs;
		pastLegs.sort((a: Leg, b: Leg) => b.arrivalTime.localeCompare(a.arrivalTime));
	}
</script>

<div class="container h-full mx-auto flex justify-center items-center">
	<div class="space-y-10 w-full flex flex-col items-center">
		<h3 class="h3">Dein nächster Zug</h3>
		{#if !legInProgress}
			<div class="grid grid-flow-col grid-cols-4 gap-4 w-4/5 h-48 content-center">
				<div class="col-span-3">
					<input
						class="input autocomplete"
						type="search"
						name="autocomplete-search"
						bind:value={nextDepartureStation}
						placeholder="Abfahrtsbahnhof"
					/>
					<!-- <div data-popup="popupAutocomplete"> -->
					<!-- 	<Autocomplete -->
					<!-- 		bind:input={nextDepartureStation} -->
					<!-- 		options={stationOptions} -->
					<!-- 		on:selection={onStationSelection} -->
					<!-- 	/> -->
					<!-- </div> -->
				</div>
				<div>
					<button type="button" class="btn variant-filled-primary" on:click={assignNextLeg}>
						<Fa icon={faDice} size="2x" />
						<span>würfeln</span>
					</button>
				</div>
			</div>
		{/if}
		{#if legInProgress}
			<div class="w-4/5 h-48 space-y-5 content-center">
				<div class="w-full card">
					<section class="p-10">
						<div class="grid grid-cols-3 gap-4">
							<div class="text-left">
								{currentLeg.departureStation} ab {currentLeg.departureTime}, Gleis {currentLeg.departurePlatform}
							</div>
							<div class="text-center">{currentLeg.trainDesignation}</div>
							<div class="text-right">
								{currentLeg.arrivalStation} an {currentLeg.departureTime} Gleis {currentLeg.arrivalPlatform}
							</div>
						</div>
					</section>
				</div>
				<div class="grid grid-flow-col grid-cols-4 gap-4 w-full">
					<button
						type="button"
						class="btn variant-filled-primary col-span-3"
						on:click={completeCurrentLeg}
					>
						<span>angekommen</span>
					</button>
					<button
						type="button"
						class="btn variant-filled-error col-span-1"
						on:click={cancelCurrentLeg}
					>
						<span>neu würfeln</span>
					</button>
				</div>
			</div>
		{/if}
		<h3 class="h3">Dein bisheriges Reisli</h3>
		{#each pastLegs as leg}
			<div class="w-4/5 card">
				<section class="p-10">
					<div class="grid grid-cols-3 gap-4">
						<div class="text-left">
							{leg.departureStation} ab {leg.departureTime}, Gleis {leg.departurePlatform}
						</div>
						<div class="text-center">{leg.trainDesignation}</div>
						<div class="text-right">
							{leg.arrivalStation} an {leg.departureTime} Gleis {leg.arrivalPlatform}
						</div>
					</div>
				</section>
			</div>
		{/each}
	</div>
</div>
