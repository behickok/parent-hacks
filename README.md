# Parent Hacks

A collection of helpful tools for parents of toddlers and young kids, built with SvelteKit.

## Features

### 1. Pom Pom Tracker
A visual reward system that helps track good behavior throughout the day.
- Add/remove pom poms with a single tap
- Set initial and target counts
- Choose from multiple themes (pom poms, baseballs, unicorns, stars, hearts, flowers)
- Progress bar and celebration when goal is reached
- Data persists using localStorage

### 2. Parent Calming Activity
A mindfulness exercise to help parents calm down during stressful moments.
- Count colored dots on the screen
- Focus on the present moment
- Track your streak of correct answers
- Randomly generated patterns for endless practice

### 3. Visual Timer
A color-coded countdown timer perfect for helping kids understand time.
- Set timer from 1-60 minutes
- Visual clock hand that rotates
- Color changes based on remaining time:
  - Green: Lots of time remaining
  - Yellow: Getting close
  - Red: Almost done
- Clear visual indicators for young children

### 4. Script Library
A comprehensive collection of scripts for common parenting situations.
- 6 categories covering bedtime, aggression, sharing, tantrums, transitions, and defiance
- Multiple scripts per category with detailed guidance
- Practical tips for each situation
- Copy scripts to clipboard for quick reference

## Tech Stack

- **Framework**: SvelteKit
- **Language**: JavaScript with Svelte 5
- **Styling**: Scoped CSS
- **State Management**: Svelte runes ($state, $effect)
- **Storage**: localStorage for persistence

## Developing

Once you've created a project and installed dependencies with `npm install` (or `pnpm install` or `yarn`), start a development server:

```sh
npm run dev

# or start the server and open the app in a new browser tab
npm run dev -- --open
```

## Building

To create a production version of your app:

```sh
npm run build
```

You can preview the production build with `npm run preview`.

> To deploy your app, you may need to install an [adapter](https://svelte.dev/docs/kit/adapters) for your target environment.
