# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Project Overview

This is a dual-platform Logic Pro Timecode Converter with:
1. **Next.js Web Demo** - Interactive web interface for testing conversion logic
2. **Raycast Extension** - Native macOS productivity extension

The core functionality converts Logic Pro's exported timecode data (tab-separated format) into markdown lists with clickable timestamp links, removing project-specific prefixes and frame data for cleaner output.

## Development Commands

```bash
# Web demo development
pnpm dev              # Start Next.js development server on http://localhost:3000
pnpm build            # Build the web demo for production
pnpm start            # Start production server
pnpm lint             # Run Next.js linting

# Raycast extension development  
npm install           # Install dependencies for Raycast extension
npm run dev          # Start Raycast extension development mode
```

## Architecture

### Dual Implementation Pattern
The project maintains two separate implementations of the same core logic:
- **Web Demo**: `components/timecode-converter.tsx` - React component with UI for browser testing
- **Raycast Extension**: `src/index.tsx` - Raycast Form component for native macOS integration

Both implementations share identical conversion algorithms but use different UI frameworks (shadcn/ui vs Raycast API).

### Core Conversion Logic
Located in both implementations with subtle differences:
- **Input Processing**: Handles tab-separated Logic Pro exports with timecode + title + duration format
- **Time Cleaning**: Removes project hour prefixes (`01:`) and frame data (`:22.33`) using regex patterns
- **Output Generation**: Creates markdown with clickable timestamp links in format `* **[MM:SS](#t=MM:SS)** Title`

### Technology Stack
- **Frontend**: Next.js 15, React 19, TypeScript, Tailwind CSS
- **UI Components**: shadcn/ui with Radix UI primitives
- **Raycast**: @raycast/api for native macOS integration
- **Styling**: Tailwind CSS v4 with CSS variables, Geist font family
- **Package Manager**: pnpm for web demo, npm for Raycast extension

### File Structure
```
app/                 # Next.js App Router pages
components/          # React components including core converter
  ui/                # shadcn/ui components
src/                 # Raycast extension entry point
lib/                 # Utility functions (tailwind merge)
```

## Key Implementation Details

### Timecode Processing Algorithm
Both implementations expect input format: `HH:MM:SS.fff\tTitle\tDuration`
1. Split input by multiple tabs/spaces to handle Logic Pro export variations
2. Process in groups of 3 (timecode, title, duration) 
3. Skip duration-only entries (starting with `00:00:00`)
4. Clean timecode by removing hour prefix and frame suffix
5. Generate markdown link format

### Component Architecture
- `TimecodeConverter` (web) uses React state for input/output management
- Raycast extension uses Form API with ActionPanel for native integration
- Both include example data and clipboard functionality
- Error handling with user feedback (toasts in Raycast, console in web)

### Styling System
Uses Tailwind CSS with design tokens:
- CSS custom properties for theming
- Responsive design with container queries
- Dark mode support via CSS variables
- Consistent spacing and typography scales

## Development Notes

The web demo serves as a testing environment for the conversion logic before implementing in Raycast. When modifying conversion algorithms, update both implementations to maintain feature parity.

The project includes comprehensive shadcn/ui components but only uses a subset (Button, Card, Textarea) in the main converter interface.