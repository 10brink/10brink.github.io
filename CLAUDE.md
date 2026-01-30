# CLAUDE.md

Nick Ten Brink's projects, built entirely with Claude Code.

## Project Structure

- `website/` - Personal website (Linktree-style hub)
  - `index.html` - Main landing page
  - `socials.html` - Social media links and music
  - `personal.html` - Personal page
  - `family-photos.html` - Family photo album
  - `projects.html` - Projects showcase
  - `fireworks/` - Fireworks animation project
  - `style.css` - Main stylesheet (shared across pages)
  - `img/` - Images directory
- `fitness/` - Fitness tracking tools
- `games/` - Games
- `workoutlog/` - Workout logging app

## Tech Stack

- Vanilla HTML, CSS, JavaScript (no frameworks)
- Google Fonts (Inter)
- Static site (no build step)

## Design System

- Dark theme with warm accent colors (#d4a574 gold/tan)
- Background: Dark gradient overlay on `website/img/pichu.jpg`
- Card-based link layout with hover effects
- Mobile-responsive design

## Conventions

- Pages use shared `style.css` for consistent styling
- Internal pages include a back link to index
- External links open in new tab with `rel="noopener"`
- SVG icons inline in HTML for link cards

## Documentation

For every project, write a detailed `FORNICK.md` that explains the project in plain language: technical architecture, codebase structure, technologies used, decision rationale, and lessons learned (bugs, pitfalls, best practices). Make it engaging and memorable with analogies and anecdotes.

Include a comment at the top of every file explaining concisely what it does.