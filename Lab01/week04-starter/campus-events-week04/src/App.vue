<script setup lang="ts">
import { computed, ref, watch } from "vue";
import EventList from "./components/EventList.vue";
import { initialEvents } from "./data/events";
import type { CampusEvent } from "./types";

// A reactive copy of the hard-coded events
const events = ref<CampusEvent[]>(initialEvents.map(e => ({ ...e })));

// TODO 1: a ref `myBookings` holding the ids of the events I booked (one entry per seat)
const myBookings = ref<number[]>([]);

// TODO 4 (bonus): keep my bookings after a page reload
//   - watch myBookings and save it to localStorage (JSON.stringify)
//   - read it back when the app starts, and add those seats to `booked`
try {
  const raw = localStorage.getItem("myBookings");
  if (raw) {
    const saved = JSON.parse(raw);
    if (Array.isArray(saved)) {
      myBookings.value = saved;
      for (const id of saved) {
        const ev = events.value.find(e => e.id === id);
        if (ev) {
          ev.booked++;
        }
      }
    }
  }
} catch {
  // Ignore invalid JSON in localStorage
}

watch(
  myBookings,
  (ids) => {
    localStorage.setItem("myBookings", JSON.stringify(ids));
  },
  { deep: true }
);

// TODO 2: a computed `bookingCount`
const bookingCount = computed(() => myBookings.value.length);

// TODO 3: onBook(id): find the event; if it still has a seat,
//         increase its `booked` and add the id to myBookings
function onBook(id: number) {
  const ev = events.value.find(e => e.id === id);
  if (ev && ev.booked < ev.capacity) {
    ev.booked++;
    myBookings.value.push(id);
  }
}
</script>

<template>
  <main>
    <header>
      <h1>Campus Events</h1>
      <!-- TODO 5: show the real count ("1 seat" / "N seats") -->
      <p class="mine">
        You have booked {{ bookingCount === 1 ? "1 seat" : `${bookingCount} seats` }}.
      </p>
    </header>
    <EventList :events="events" @book="onBook" />
  </main>
</template>

<style scoped>
header { margin-bottom: 16px; }
.mine { color: #6b7280; margin: 0; }
</style>
