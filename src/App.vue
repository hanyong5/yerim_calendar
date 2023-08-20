<script>
import { defineComponent } from 'vue';
import { getPageTable } from 'vue-notion';
import FullCalendar from '@fullcalendar/vue3';
import dayGridPlugin from '@fullcalendar/daygrid';
import timeGridPlugin from '@fullcalendar/timegrid';
import interactionPlugin from '@fullcalendar/interaction';
import { createEventId } from './event-utils';

export default defineComponent({
	components: {
		FullCalendar,
	},
	data() {
		return {
			calendarOptions: {
				plugins: [
					dayGridPlugin,
					timeGridPlugin,
					interactionPlugin, // needed for dateClick
				],
				headerToolbar: {
					left: 'prev next',
					center: 'title',
					right: 'prev,next today',
				},
				initialView: 'dayGridMonth',
				// initialEvents: INITIAL_EVENTS, // alternatively, use the `events` setting to fetch from a feed
				editable: true,
				selectable: true,
				selectMirror: true,
				dayMaxEvents: true,
				// locale: 'en',
				dayNames:['일','월','화','수','목','금','토'],
				monthNamesShort: ["1월", "2월", "3월", "4월", "5월", "6월", "7월", "8월", "9월", "10월", "11월", "12월"],
    dayNames: ["일요일", "월요일", "화요일", "수요일", "목요일", "금요일", "토요일"],
    dayNamesShort: ["일", "월", "화", "수", "목", "금", "토"],
				weekends: true,
				// select: this.handleDateSelect,
				// eventClick: this.handleEventClick,
				// eventsSet: this.handleEvents,
				fixedWeekCount: false,
				/* you can update a remote database when these fire:
        eventAdd:
        eventChange:
        eventRemove:
        */
				businessHours: false,
				nowIndicator: false,
				dayMaxEvents: false, // allow "more" link when too many events
				events: [],
			},

			currentEvents: [],
		};
	},
	methods: {
		handleWeekendsToggle() {
			this.calendarOptions.weekends = !this.calendarOptions.weekends; // update a property
		},
		handleDateSelect(selectInfo) {
			let title = prompt('Please enter a new title for your event');
			let calendarApi = selectInfo.view.calendar;

			calendarApi.unselect(); // clear date selection

			if (title) {
				calendarApi.addEvent({
					id: createEventId(),
					title,
					start: selectInfo.startStr,
					end: selectInfo.endStr,
					allDay: selectInfo.allDay,
				});
			}
		},
		handleEventClick(clickInfo) {
			if (
				confirm(
					`Are you sure you want to delete the event '${clickInfo.event.title}'`,
				)
			) {
				clickInfo.event.remove();
			}
		},
		handleEvents(events) {
			this.currentEvents = events;
		},
		
	},
	mounted() {
		getPageTable('40ddd1dd95834d54b92a6d08ed27a277').then(b => {
			this.calendarOptions.events = b;
			console.log(b);
		});
	},
});
</script>

<template>
	<FullCalendar class="demo-app-calendar" :options="calendarOptions">
		<template v-slot:eventContent="arg">
			<!-- <b>{{ arg.timeText }}</b> -->
			<i>{{ arg.event.title }}</i>
		</template>
	</FullCalendar>
</template>

<style lang="css">
body {
	font-size: 1em;
}
h2 {
	margin: 0;
	font-size: 14px;
}

ul {
	margin: 0;
	padding: 0 0 0 1.5em;
}

li {
	margin: 1.5em 0;
	padding: 0;
}

b {
	/* used for event dates/times */
	margin-right: 3px;
}

.demo-app {
	display: flex;
	min-height: 100%;
	font-family: Arial, Helvetica Neue, Helvetica, sans-serif;
	font-size: 14px;
}

.demo-app-sidebar {
	width: 300px;
	line-height: 1.5;
	background: #eaf9ff;
	border-right: 1px solid #d3e2e8;
}

.demo-app-sidebar-section {
	padding: 2em;
}

.demo-app-main {
	flex-grow: 1;
	padding: 3em;
}

.fc {
	/* the calendar root */
	max-width: 1100px;
	margin: 0 auto;
}
#fc-dom-1.fc-toolbar-title {
	font-size: 1.4em;
}
.fc-event {
	font-size: 1em;
	font-weight: normal !important;
}
.fc-day-sun {
	color: red;
	font-weight: bold;
}
.fc-day-sat {
	color: blue;
}
.fc-toolbar-chunk div:first-child {
	display: none;
}
.fc .fc-scrollgrid-liquid {
	height: 180%;
}
</style>
