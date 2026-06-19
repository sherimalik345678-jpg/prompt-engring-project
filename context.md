# Evently Project Context

Welcome to the **Evently** workspace context documentation. This document serves as a guide for developer onboarding, codebase structure analysis, state management patterns, and system design overview.

---

## 1. Project Overview

**Evently** is a premium, client-side single-page application (SPA) designed for event planning and coordination. It enables users to plan events, manage guest RSVPs, track tasks on a checklist, manage expenses using a multi-currency budget tracker, and record quick sticky notes. 

The application utilizes high-fidelity visuals featuring:
*   A default premium **Dark Purple** theme.
*   An alternate **Light Green** theme.
*   Glassmorphism aesthetics, smooth animations, CSS variable-driven layouts, and a responsive structure.
*   Full persistent local storage for zero-setup offline operation.

---

## 2. Directory Structure & Key Files

The project has a minimalistic, single-page architecture where the entire frontend codebase resides in a unified file:

*   [index.html](file:///c:/Users/Hp/Desktop/project/index.html): The main entry point containing all HTML structural components, CSS design tokens/rules (under `<style>`), and JavaScript application logic (under `<script>`).
*   [evently.code.txt](file:///c:/Users/Hp/Desktop/project/evently.code.txt): A raw text representation of the [index.html](file:///c:/Users/Hp/Desktop/project/index.html) codebase for backup or archival reference.
*   [context.md](file:///c:/Users/Hp/Desktop/project/context.md): This documentation file providing project context.

---

## 3. Tech Stack

*   **HTML5**: Semantic document layout with structural sections, forms, and custom modal panels.
*   **CSS3**: Custom variables (`:root` theme tokens), responsive flex/grid wrappers, transitions, and hover-triggered micro-animations.
*   **Vanilla JavaScript (ES6+)**: Event handling, DOM manipulation, LocalStorage sync, time-based countdown intervals, calculations, and data formatting.

---

## 4. State & Local Storage Schema

All application states are stored in the user's browser `localStorage` under specific keys mapped in the [STORAGE](file:///c:/Users/Hp/Desktop/project/index.html#L1450-L1459) namespace object:

| State Key | LocalStorage Key | Fallback Value | Description |
| :--- | :--- | :--- | :--- |
| `theme` | `evtly_theme` | `false` (Dark) | Boolean indicating if the Light Green theme is active. |
| `currency` | `evtly_currency` | `'USD'` | String representing active currency (e.g., `'USD'`, `'EUR'`, `'GBP'`, `'INR'`). |
| `events` | `evtly_events` | `[]` | Array of planned events. |
| `guests` | `evtly_guests` | `[]` | Array of managed guests. |
| `tasks` | `evtly_tasks` | `[]` | Array of checklist items. |
| `expenses` | `evtly_expenses` | `[]` | Array of calculated costs. |
| `notes` | `evtly_notes` | `[]` | Array of quick sticky notes. |
| `totalBudget` | `evtly_totalBudget` | `null` | Maximum budget limit allowed. |

### Data Object Interfaces

*   **Event**:
    ```typescript
    interface Event {
      id: string;          // Timestamp string
      title: string;       // Name of the event
      category: string;    // 'wedding' | 'birthday' | 'meeting' | 'other'
      date: string;        // Date & time string
      desc: string;        // Event description
    }
    ```
*   **Guest**:
    ```typescript
    interface Guest {
      id: string;          // Timestamp string
      name: string;        // Guest's name
      status: string;      // RSVP status: 'Pending' | 'Yes' | 'No'
      eventId: string;     // The ID of the assigned event (or empty string)
    }
    ```
*   **Task**:
    ```typescript
    interface Task {
      id: string;          // Timestamp string
      name: string;        // Task description
      completed: boolean;  // Completion state
    }
    ```
*   **Expense**:
    ```typescript
    interface Expense {
      id: string;          // Timestamp string
      title: string;       // Cost item title
      amount: number;      // Numeric expense cost
    }
    ```
*   **Note**:
    ```typescript
    interface Note {
      id: string;          // Timestamp string
      text: string;        // Content text
      createdAt: string;   // ISO timestamp string
    }
    ```

---

## 5. CSS Architecture & Theme System

The design system utilizes HSL/Hex color variables loaded into `:root` for dark styling, and overridden in `body.light` for light styling.

### Themes:
1.  **Dark Purple (Default)**: Deep midnight purple background (`#0b0718`) combined with soft pastel purple accent highlights (`#a78bfa`).
2.  **Light Green**: Soft green background (`#f2fbf5`) paired with vibrant emerald green accents (`#22c55e`).

### key CSS Features:
*   **Background Blobs**: A fixed canvas background utilizing multiple overlayed radial gradients (`body::before`) to create soft, glowing ambient bubbles that move or fade based on active theme settings.
*   **Responsive Grid**: CSS grid structures with dynamic clamp padding and auto-fit columns (`grid-template-columns: repeat(auto-fit, minmax(..., 1fr))`) to support seamless desktop, tablet, and mobile screens.
*   **Glassmorphism**: Cards and navigation bars feature semi-transparent background blends coupled with `backdrop-filter: blur(12px)` and delicate borders.

---

## 6. Key JavaScript Modules & Functions

All logical interactions are structured inside an IIFE closure to prevent global namespace pollution:

### Storage & Utility Helpers:
*   [load(key, fallback)](file:///c:/Users/Hp/Desktop/project/index.html#L1462-L1467): Loads and parses JSON from local storage with safe catch handling.
*   [save(key, data)](file:///c:/Users/Hp/Desktop/project/index.html#L1469-L1471): Stringifies and persists objects back into the browser's storage.
*   [escapeHTML(str)](file:///c:/Users/Hp/Desktop/project/index.html#L173-L176): Escapes user input to prevent Cross-Site Scripting (XSS) injection.
*   [formatCurrency(amount)](file:///c:/Users/Hp/Desktop/project/index.html#L1482-L1488): Formats raw numbers according to selected currency symbols.

### UI & Section Management:
*   [toggleTheme()](file:///c:/Users/Hp/Desktop/project/index.html#L1592-L1600): Switches theme classes on the `body` and updates user preference in localStorage.
*   [scrollToSection(id)](file:///c:/Users/Hp/Desktop/project/index.html#L1541-L1546): Implements smooth scrolling to view containers.
*   [highlightNavLink(sectionId)](file:///c:/Users/Hp/Desktop/project/index.html#L1572-L1576): Updates navigation bar active states based on scroll intersection positions.

### Feature Managers:
*   **Events**:
    *   [addEvent()](file:///c:/Users/Hp/Desktop/project/index.html#L1618): Creates a new event record or saves edits to an existing event, parsing inputs safely.
    *   [editEvent(id)](file:///c:/Users/Hp/Desktop/project/index.html#L1681): Populates the form container with existing details to support updates.
    *   [deleteEvent(id)](file:///c:/Users/Hp/Desktop/project/index.html#L1700): Deletes the event and cleans up associated guest associations.
    *   [updateAllCountdowns()](file:///c:/Users/Hp/Desktop/project/index.html#L1820-L1860): Runs on a `setInterval` of 1 second to update countdown badges indicating time remaining.
*   **Guests**:
    *   [addGuest()](file:///c:/Users/Hp/Desktop/project/index.html#L1876): Adds a guest, assigns them to an event, and triggers UI updates.
    *   [toggleGuestRSVP(id)](file:///c:/Users/Hp/Desktop/project/index.html#L1897): Steps guest RSVP values through pending, yes, and no states.
*   **Tasks**:
    *   [addTask()](file:///c:/Users/Hp/Desktop/project/index.html#L1962): Appends checklist items.
    *   [toggleTask(id)](file:///c:/Users/Hp/Desktop/project/index.html#L1972): Flags checked items and computes overall completion status.
*   **Budget**:
    *   [addExpense()](file:///c:/Users/Hp/Desktop/project/index.html#L2024): Adds an expense item and verifies numerical validity.
    *   [updateBudgetDisplay()](file:///c:/Users/Hp/Desktop/project/index.html#L2051): Recalculates remaining limits, turning alerts red if overspent.

---

## 7. Interactive Dashboard Sections

*   **Hero Unit**: Sets the visual context and triggers quick actions.
*   **Dashboard metrics**: Real-time calculated counters tracking RSVPs, progress bars, and total spends.
*   **Events Grid**: Category-badged grid view containing customized details and countdown timers.
*   **Guest Control Panel**: Quick input filters, lists, and RSVP toggle badges.
*   **Checklist Tracker**: Status-bar driven visual workflow.
*   **Budget Workspace**: Expense listing table with remaining balance indicators.
*   **Notes Wall**: Grid of quick sticky post-it cards.

---

## 8. Development & Modification Workflow

Since the project operates fully client-side without compilation steps (Vite/Next.js/Webpack), updating features is straightforward:

1.  **Locate Target Codes**: Open [index.html](file:///c:/Users/Hp/Desktop/project/index.html) in your editor.
2.  **Adjust Layout/Styles**: Modify CSS variables inside the theme blocks or HTML markup directly.
3.  **Refactor Logic**: Change functions in the main JavaScript `<script>` closure.
4.  **Local Testing**: Run a lightweight server to avoid CORS issues if loading local assets or modules in the future:
    ```bash
    # Python 3
    python -m http.server 8000
    
    # Node.js (global static server)
    npx live-server
    ```
5.  **Offline Cache Clearing**: Clear browser local storage keys starting with `evtly_` to reset the application state to factory defaults during testing.
