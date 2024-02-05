<script>
	import Calendar from '@event-calendar/core';
	import TimeGrid from '@event-calendar/time-grid';
	import Interation from '@event-calendar/interaction';
	import '@event-calendar/core/index.css';
	import { v4 as uuidv4 } from 'uuid';
	import Button from '@smui/button';

	let plugins = [TimeGrid, Interation];
	let options = {
		view: 'timeGridWeek',
		allDaySlot: false,
		events: [],
		selectable: true,
		slotMinTime: '10:00:00',
		slotMaxTime: '23:00:00',
		headerToolbar: { start: '', center: '', end: '' },
		selectBackgroundColor: 'grey',
		select: (info) => addEvent(info),
		eventClick: (info) => handleEventClick(info.event.id),
		eventDrop: (info) => handleEventUpdate(info),
		eventResize: (info) => handleEventUpdate(info),
	};
	let preferred_slots = false;
	let last_resort_slots = false;
	let copied = [];
	let copiedDay = 0;
	let copyButtons = Array(7).fill(true); // false means copy, true means paste
	
	function addEvent(info) {
		// Assignment is what triggers a refresh to the DOM
		let color = 'grey'
		if (preferred_slots || last_resort_slots) {
			color = last_resort_slots ? '#e6e600' : 'green';
		}
		options.events = [
			...options.events,
			{ id: uuidv4(), backgroundColor: color, ...info }
		];
	}
	
	function handleEventClick(id) {
		if (!preferred_slots && !last_resort_slots) {
			removeEventById(id);
		} else {
			options.events = options.events.map((event) => {
				if (event.id === id) {
					let color = last_resort_slots ? '#e6e600' : 'green';
					return {
						...event,
						backgroundColor: event.backgroundColor === color ? 'grey' : color
					};
				}
				return event;
			});
		}
	}
	
	function handleEventUpdate(info) {
		let event = JSON.parse(JSON.stringify(info.event));
		let start = new Date(event.start);
		let end = new Date(event.end);

		start = new Date(start.setDate(start.getDate()));
		end = new Date(end.setDate(end.getDate()));

		event.start = start;
		event.end = end;
		event.id = uuidv4();

		let id = info.event.id;
		options.events = [
			...options.events, event
		];
		removeEventById(id);
	}
	
	function handleButtonClick(isPreferred) {
		if (isPreferred) {
			preferred_slots = !preferred_slots;
			if (preferred_slots) {
				last_resort_slots = false;
				options.selectBackgroundColor = 'green';
			}
			else {
				options.selectBackgroundColor = 'grey';
			}
		} else {
			last_resort_slots = !last_resort_slots;
			if (last_resort_slots) {
				preferred_slots = false;
				options.selectBackgroundColor = '#e6e600';
			}
			else {
				options.selectBackgroundColor = 'grey';
			}
		}
	}

	function removeEventById(id) {
		options.events = options.events.filter((event) => event.id !== id);
	}

	function handleCopy(day) {
		if (copyButtons[day] == true && copyButtons.includes(false)) {
			copyButtons = Array(7).fill(true);
			copied = [];
			return
		}
		// only copy events from the specific day
		copied = options.events.filter(event => {
        const eventDate = new Date(event.start);
        return eventDate.getDay() === day;
    });
		copiedDay = day;
		copyButtons = Array(7).fill(false);
		copyButtons[day] = true;
	}

	function handlePaste(day) {
		for (let i = 0; i < copied.length; i++) {
			let event = JSON.parse(JSON.stringify(copied[i]));
			let start = new Date(event.start);
			let end = new Date(event.end);
	
			start = new Date(start.setDate(start.getDate() + (day - copiedDay)))
			end = new Date(end.setDate(end.getDate() + (day - copiedDay)));
	
			event.start = start;
			event.end = end;
			event.id = uuidv4();
	
			options.events = [
				...options.events, event
			];
		}
		copyButtons = Array(7).fill(true);
		copied = [];
	}
</script>

<div class="local">
	<Button
		class={preferred_slots ? 'button-green' : ''}
		variant="unelevated"
		color="secondary"
		on:click={() => handleButtonClick(true)}>Select Preferred Slots</Button
	>
	<Button
		class={last_resort_slots ? 'button-yellow' : ''}
		variant="unelevated"
		color="secondary"
		on:click={() => handleButtonClick(false)}>Select Last Resort Slots</Button
	>
</div>

<Calendar {plugins} {options} />

<div class="footer">
	{#each Array(7) as _, day}
		<Button
			class={''}
			variant={copyButtons[day] ? 'outlined' : 'unelevated'}
			color="secondary"
			on:click={() => copyButtons[day] ? handleCopy(day) : handlePaste(day)}>
			{copyButtons[day] ? 'Copy' : 'Paste'}
		</Button>
	{/each}
</div>

<svelte:head>
	<title>Schedule</title>
	<meta name="schedule" content="schedule" />
</svelte:head>

<style>
	.local {
		display: flex;
		justify-content: space-evenly;
	}

	.footer {
		display: grid;
		grid-template-columns: repeat(7, 1fr);
    grid-gap: 10px;
		margin-left: 85px;
		margin-top: 10px;
	}

	.local :global(.button-green) {
		background-color: green !important;
	}

	.local :global(.button-yellow) {
		color: black;
		background-color: #e6e600 !important;
	}
</style>
