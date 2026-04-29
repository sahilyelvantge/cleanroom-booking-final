# Cleanroom Booking System - BITS Pilani Hyderabad

A React + Firebase web app for student booking requests, faculty review, and admin approval for cleanroom usage.

## Tech Stack

- Frontend: React (`react`, `react-dom`, `react-scripts`)
- Routing: `react-router-dom`
- UI: Material UI (`@mui/material`, `@mui/icons-material`, `@emotion/react`, `@emotion/styled`)
- Date/Time: `@mui/x-date-pickers`, `date-fns`
- Backend services: Firebase Authentication + Firestore
- Optional deployment: Firebase Hosting

## Project Workflow (Approval Logic)

1. Student creates booking -> status `pending_faculty`
2. Faculty approves/rejects:
   - Approve -> status `pending_admin`
   - Reject -> status `rejected`
3. Admin final action:
   - Approve -> status `approved`
   - Reject -> status `rejected`

## Prerequisites

- Node.js v16+ (recommended v18 or v20 LTS)
- npm
- A Firebase project (professor/college-owned for production)

## Local Setup (Professor / IT)

### 1) Install dependencies

```bash
npm install --legacy-peer-deps
```

### 2) Environment variables

Create `.env` in project root (same folder as `package.json`):

```env
REACT_APP_FIREBASE_API_KEY=your_api_key
REACT_APP_FIREBASE_AUTH_DOMAIN=your_auth_domain
REACT_APP_FIREBASE_PROJECT_ID=your_project_id
REACT_APP_FIREBASE_STORAGE_BUCKET=your_storage_bucket
REACT_APP_FIREBASE_MESSAGING_SENDER_ID=your_messaging_sender_id
REACT_APP_FIREBASE_APP_ID=your_app_id
REACT_APP_FIREBASE_MEASUREMENT_ID=your_measurement_id

REACT_APP_ADMIN_USERNAME=CleanMnff
REACT_APP_ADMIN_PASSWORD=CleanMnff
```

### 3) Run locally

```bash
npm start
```

Open `http://localhost:3000`.

## Faculty Configuration

Faculty names and emails are configured in:

- `src/config/facultyList.js`

IDs are in the format `faculty1`, `faculty2`, ...

## Firebase Setup (Required)

### A) Authentication

- Go to Firebase Console -> Authentication -> Sign-in method
- Enable Google provider
- Add authorized domains (`localhost`, and deployment domain if hosted)

### B) Firestore Database

Create Firestore database and publish rules.

For local/demo usage:

```txt
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /bookings/{bookingId} {
      allow read, write: if request.auth != null;
    }
  }
}
```

### C) Required Firestore Composite Index

Student dashboard query requires this index on `bookings` collection:

- `studentEmail` -> Ascending
- `submittedAt` -> Descending
- `__name__` -> Descending (auto)

If missing, Firebase console shows a link in browser error: click it and create index.

## Recommended Validation Checklist

After setup, test in this order:

1. Student submits booking
2. Faculty approves booking
3. Admin approves/rejects booking
4. Student sees updated status in dashboard

## Security Notes

- Do not commit `.env`
- `.env.example` can be committed with placeholder values only
- For production handover, use a college-owned Firebase project
- Current admin login is env-based in frontend; use strong credentials and controlled access

## Optional: Deploy to Firebase Hosting

If needed, deploy with Firebase Hosting:

```bash
npm run build
npm install -g firebase-tools
firebase login
firebase use <firebase-project-id>
firebase deploy
```

Then add hosting domain in Firebase Auth authorized domains.

## Folder Overview

- `src/pages/Student.js` -> Student booking UI
- `src/pages/Faculty.js` -> Faculty approval UI
- `src/pages/Admin.js` -> Admin final approval/rejection UI
- `src/services/BookingService.js` -> Firestore CRUD + status updates
- `src/config/facultyList.js` -> Faculty name/email mapping
- `firestore.rules` -> Firestore security rules
- `firebase.json` -> Firebase hosting/firestore config
