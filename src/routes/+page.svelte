<script>
	import Calendar from '@event-calendar/core';
	import TimeGrid from '@event-calendar/time-grid';
	import Interation from '@event-calendar/interaction';

	let plugins = [TimeGrid, Interation];
	let options = {
		view: 'timeGridWeek',
		slotMinTime: '06:00:00',
		allDaySlot: false,
		events: [
			{
				id: 'hi',
				start: new Date(Date.now()),
				end: new Date(Date.now() + 10000000),
				title: 'example'
			}
		],
		eventDragStart: (info) => console.log('HII', info)
	};
</script>

<Calendar {plugins} {options} />

<svelte:head>
	<title>Schedule</title>
	<meta name="schedule" content="schedule" />
</svelte:head>
