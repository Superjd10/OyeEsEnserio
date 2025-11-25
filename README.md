# Oye... Es en serio

An interactive question-and-hint application built with Nuxt 3 and Vue 3. Features elegant typography, animated punctuation marks, and a sophisticated vintage aesthetic.

## Features

- **Interactive Questions**: Navigate through a series of questions with a next button
- **Toggle Hints**: Click anywhere on the page to reveal or hide hints for each question
- **Animated Punctuation**: Dynamic opening and closing punctuation marks (¿?, ¡!, ¡¿?!) that fade in and out at random intervals
- **Seeded Randomization**: Questions are shuffled using a seeded random algorithm for consistent ordering
- **Responsive Design**: Fully responsive layout optimized for desktop, tablet, and mobile devices
- **Elegant UI**: Vintage-inspired design with radial gradients, custom typography, and subtle textures

## Tech Stack

- **Nuxt 4.2.1** - Vue.js framework
- **Vue 3.5.25** - Progressive JavaScript framework
- **Vue Router 4.6.3** - Official router for Vue.js

## Setup

Install dependencies:

```bash
bun install
```

## Development

Start the development server on `http://localhost:3000`:

```bash
bun --bun run dev
```

## Production

Build the application for production:

```bash
bun run build
```

Preview the production build locally:

```bash
bun run preview
```

## Project Structure

```
esenserio/
├── app/
│   └── app.vue          # Main application component
├── public/              # Static assets
├── nuxt.config.ts       # Nuxt configuration
└── package.json         # Project dependencies
```

## Customization

### Adding Questions

Edit the `questions` array in `app/app.vue`:

```js
const questions = [
  { question: "Your Question", hint: "Your Hint" },
  // Add more questions...
];
```

### Changing the Shuffle Seed

Modify the `seed` variable in `app/app.vue` to change the question order:

```js
const seed = "your-custom-seed";
```

### Adjusting Animation Timing

Update the `scheduleNextChange` function to modify punctuation animation intervals:

```js
let times = [1000, 3000]; // [min, random_range] in milliseconds
```

## Learn More

Check out the [Nuxt documentation](https://nuxt.com/docs) and [Vue documentation](https://vuejs.org/) to learn more about the framework.
