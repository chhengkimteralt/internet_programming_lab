# Week 4 Practice: Vue Components and Reactivity - Answers

### 1. In EventList, what goes wrong if you use the array index as `:key` and the list gets filtered while a card has local state (for example an open details panel)?
When using the array index as `:key`, Vue pairs existing DOM elements and component instances based on their numeric position rather than their true entity identity. If the list is filtered, an event moving to index 0 will inherit the component instance and local state (such as an open details panel) belonging to whichever event was previously at index 0. Binding `:key="e.id"` with a stable, unique identifier ensures Vue tracks each component instance accurately across list mutations.

### 2. Why is `visible` a computed and not a function called from the template? What is the difference when you type in the search box?
A `computed` property caches its calculated result and only re-evaluates when its reactive dependencies (`search`, `category`, `events`) change. In contrast, calling a method in the template executes on every single component re-render, even if unrelated state changes. With `computed`, Vue efficiently recalculates the filtered array only when the user types in the search box or changes category, eliminating redundant array iterations.

### 3. EventCard emits `book` instead of changing `event.booked`. Give two reasons.
First, props follow a one-way data flow in Vue and are strictly read-only, meaning a child component must never mutate data owned by its parent directly. Second, emitting events keeps `EventCard` reusable and modular, allowing the parent (`App.vue`) to serve as the single source of truth for managing state mutations, validation (such as checking capacity), and side effects like updating `myBookings` and saving to `localStorage`.

### 4. You write `const { booked } = reactive(event); booked++`. The page does not change. Why?
Destructuring an object from `reactive()` extracts `booked` as a standalone primitive value (a number), breaking its connection to Vue's reactive `Proxy`. When you execute `booked++`, you are only incrementing a local variable in memory rather than triggering the reactive proxy's setter trap. Because the setter is never triggered, Vue has no way of detecting the update and will not trigger a re-render.

### 5. Where does `myBookings` live, and why not in EventCard or EventList?
`myBookings` lives in `App.vue` because in Vue architecture, shared state belongs in the lowest common ancestor of all components that need it. The header in `App.vue` needs `myBookings` to display the total booked seats count and handle `localStorage` synchronization, while `EventList` and `EventCard` are descendant presentation components. Placing `myBookings` in `EventCard` or `EventList` would prevent the top-level header from accessing that state without awkward prop-drilling or event tunneling.
