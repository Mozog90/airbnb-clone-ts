# Airbnb Clone (React + TypeScript)

A plain client-side React app (Vite + TypeScript), converted from a Next.js app.
No server-side rendering, no file-based routing — routes are defined explicitly
with `react-router-dom` in `src/App.tsx`.

## What changed from the Next.js version

- **Routing**: Next.js's file-based `app/` routing (`page.jsx`, `layout.jsx`) is
  replaced by `react-router-dom`. Routes live in `src/App.tsx`:
  - `/` → `src/pages/Home.tsx` (rentals list + search)
  - `/bookings` → `src/pages/Bookings.tsx` (list of bookings)
  - `/bookings/add` → `src/pages/AddBooking.tsx` (booking form)
- **Layout**: the old `layout.jsx` (header + nav) is now `src/components/Layout.tsx`,
  wrapping the routed pages in `App.tsx` instead of being picked up automatically.
- **`next/link`** → `react-router-dom`'s `Link`, pointing at the routes above.
- **`next/image`** and `next/font` aren't used (this isn't a Next.js app), and the
  default Next.js starter page/boilerplate CSS module was dropped as unrelated to
  the rental app itself.
- **TypeScript**: every component and context is typed. Shared `Rental` and
  `Booking` interfaces live in `src/types/index.ts`. Contexts
  (`RentalContext`, `BookingContext`) throw if used outside their provider,
  and are properly typed via generics on `createContext`.
- **`"use client"`** directives are removed — meaningless outside Next.js.

## Notes

- `src/components/BookingForm.tsx` is a standalone, reusable version of the
  booking form (takes `rentals`/`updateBookings` as props) — kept from the
  original file set alongside the context-driven version used on the
  `/bookings/add` page.
- The Home page fetches rentals from `http://localhost:3001/rentals` (as in
  the original) — point this at your own API, or swap in mock data.

## Run it

```bash
npm install
npm run dev
```

Build for production:

```bash
npm run build
npm run preview
```

---

# AD361 LP06 – Experience-Based Testing

## Feature: Creating a Booking

For this assignment, I tested the booking feature of the Airbnb application. I focused on creating reservations, checking form validation, and verifying whether bookings were saved and displayed correctly.

## Step 1: Error Guessing

Based on my experience using booking websites, I came up with three things that might go wrong:

1. The application might allow someone to create a reservation using a date in the past.
2. The booking form might accept zero or negative guests.
3. A confirmed reservation might not appear correctly in the bookings list.

These were predictions before performing the tests.

## Step 2: Exploratory Testing

### Testing Charter

My goal was to see whether I could successfully create a booking and how the application handled missing or invalid information.

### Exploratory Testing Notes

| What I Tried | What Happened | What I Decided to Try Next |
|---|---|---|
| Submitted the form without entering a date | The browser displayed "Please fill out this field" for Date | Try entering a past date |
| Entered a date in the past | The application accepted the booking | Test the number of guests |
| Entered 0 guests | The browser required a value of at least 1 | Try creating a valid booking |
| Created a booking with valid information | The booking was successful | Check the bookings list |
| Opened the bookings list | Both the valid booking and the past-date booking appeared | Try another invalid guest value |
| Entered -1 guests | The application rejected the value | Check whether bookings remain saved |
| Refreshed the application after creating a booking | The booking disappeared from the list | Record this as a possible persistence issue |

### Exploratory Testing Summary

The form correctly required a date and rejected zero or negative guests. Creating a valid booking also worked, and the reservation appeared in the bookings list.

However, I found two possible issues. The application accepted a booking with a past date, and bookings disappeared after refreshing the page. I would want to confirm the expected requirements before deciding whether these are actual defects.

## Step 3: Checklist-Based Testing

I created a checklist based on my error guesses and what I discovered during exploratory testing.

| Check | Result | Notes |
|---|---|---|
| Verify the booking form requires a date | Passed | Browser displayed a required-field message |
| Check whether past dates are rejected | Failed | Application accepted a past date |
| Verify 0 guests is rejected | Passed | Minimum value of 1 required |
| Verify negative guests are rejected | Passed | Application rejected -1 |
| Verify a valid booking can be created | Passed | Booking was successful |
| Verify confirmed bookings appear in the bookings list | Passed | Both test bookings appeared |
| Verify bookings remain after refreshing | Failed | Bookings disappeared after refresh |

### Checklist Summary

Five of the seven checks passed. The two failed checks involved past-date validation and saving bookings between page loads.

The past-date check assumes users should only be able to reserve current or future dates. The refresh check assumes confirmed reservations should remain available after reloading the application.

## Step 4: Acceptance Testing

I wrote the following acceptance criteria from the user's perspective.

| Acceptance Criteria (Given, When, Then) | Result | Notes |
|---|---|---|
| Given I am on the booking form, when I submit without entering a date, then I should be asked to provide one. | Passed | Required-field message appeared |
| Given I enter zero or negative guests, when I attempt to confirm the booking, then the application should reject the value. | Passed | Both 0 and -1 were rejected |
| Given I enter valid booking information, when I confirm the reservation, then the application should create my booking. | Passed | Booking was successful |
| Given I have created a booking, when I open the bookings list, then my reservation should appear. | Passed | Booking appeared in the list |
| Given I have confirmed a booking, when I refresh the application and return to the bookings list, then my reservation should still be available. | Failed | Booking disappeared after refreshing |

### Acceptance Testing Summary

Four of the five acceptance criteria passed.

The application allowed me to create a booking, required a date, rejected invalid guest counts, and displayed confirmed reservations.

The failed acceptance criterion involved keeping reservations after refreshing the application. From a user's perspective, I would expect a confirmed booking to remain available.

## Overall Testing Summary

The booking feature worked for basic interactions, but I found two areas that could use improvement:

1. **Date validation:** The application accepted reservations with past dates.
2. **Booking persistence:** Reservations disappeared after refreshing the page.

Both would be worth discussing with the development team to confirm the intended behavior.

This assignment helped me see the differences between error guessing, exploratory testing, checklist-based testing, and acceptance testing. Each approach helped me look at the same feature from a slightly different perspective.