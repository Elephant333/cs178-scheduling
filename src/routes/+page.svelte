<script>
	import Calendar from '@event-calendar/core';
	import TimeGrid from '@event-calendar/time-grid';
	import Interation from '@event-calendar/interaction';
	import '@event-calendar/core/index.css';
	import { v4 as uuidv4 } from 'uuid';

	let plugins = [TimeGrid, Interation];
	let options = {
		view: 'timeGridWeek',
		allDaySlot: false,
		events: [],
		selectable: true,
		select: (info) => addEvent(info)
	};

	function addEvent(info) {
		// Assignment is what triggers a refresh to the DOM
		options.events = [...options.events, { id: uuidv4(), ...info }];
	}
</script>

<Calendar {plugins} {options} />

<svelte:head>
	<title>Schedule</title>
	<meta name="schedule" content="schedule" />
</svelte:head>
