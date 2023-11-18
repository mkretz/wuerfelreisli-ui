<script lang="ts">
	import type { Leg } from '../lib/Journey';
	import { Autocomplete, popup } from '@skeletonlabs/skeleton';
	import type { AutocompleteOption, PopupSettings } from '@skeletonlabs/skeleton';
	let pastLegs: Leg[] = [
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
			arrivalStation: 'Bern',
			arrivalTime: '13:40',
			arrivalPlatform: '7'
		}
	];

	let popupSettings: PopupSettings = {
		event: 'focus-click',
		target: 'popupAutocomplete',
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
</script>

<div class="container h-full mx-auto flex justify-center items-center">
	<div class="space-y-10 w-full flex flex-col items-center">
		<input
			class="input autocomplete"
			type="search"
			name="autocomplete-search"
			bind:value={nextDepartureStation}
			placeholder="Search..."
			use:popup={popupSettings}
		/>
		<div data-popup="popupAutocomplete">
			<Autocomplete
				bind:input={nextDepartureStation}
				options={stationOptions}
				on:selection={onStationSelection}
			/>
		</div>
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
