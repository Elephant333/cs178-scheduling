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
		select: (info) => addEvent(info),
		eventClick: (info) => handleEventClick(info.event.id)
	};
	let preferred = false;

	function handleEventClick(id) {
		if (!preferred) {
			removeEventById(id);
		} else {
			options.events = options.events.map((event) => {
				if (event.id === id) {
					return {
						...event,
						backgroundColor: event.backgroundColor === 'green' ? 'grey' : 'green'
					};
				}
				return event;
			});
		}
	}

	function addEvent(info) {
		// Assignment is what triggers a refresh to the DOM
		options.events = [
			...options.events,
			{ id: uuidv4(), selectBackgroundColor: 'grey', backgroundColor: 'grey', ...info }
		];
	}

	function removeEventById(id) {
		options.events = options.events.filter((event) => event.id !== id);
	}
</script>

<div>
	<Button
		variant="unelevated"
		color={preferred ? 'primary' : 'secondary'}
		on:click={() => (preferred = !preferred)}>Select Preferred Slots</Button
	>
</div>
<Calendar {plugins} {options} />

<svelte:head>
	<title>Schedule</title>
	<meta name="schedule" content="schedule" />
</svelte:head>

<style>
	div {
		display: flex;
		justify-content: center;
	}
</style>
