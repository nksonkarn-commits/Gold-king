# 🎰 Gold King - Online Gaming Platform

A complete web-based gaming application with Admin, Master, and User panels for managing draws, users, and bets.

---

## 📋 Features

### 👑 Admin Panel (`admin.html`)
- **Draw Control**: Start/stop/reset timers with custom durations
- **Result Management**: Reveal winning numbers and manage result types
- **Master Management**: Create master accounts and manage token transfers
- **Live Analytics**: View bet distributions with MAX/MIN highlights
- **User Dashboard**: Monitor all users, their balances, and activity
- **Transaction History**: Track all system transactions and payouts

### 🧑‍💼 Master Panel (`master.html`)
- **User Creation**: Create new player and agent accounts
- **Token Transfer**: Transfer tokens between users with various reasons
- **Quick Stats**: Monitor total balance, active games, and daily payouts
- **User Management**: View and manage all created users
- **Transaction Tracking**: Complete history of all transfers and transactions

### 👤 Game App (`user.html`)
- **Live Draw Timer**: Real-time countdown to next draw
- **Multiple Game Types**:
  - Seedha Number (90× multiplier)
  - Group Bets (1.9× multiplier)
  - Andar Bahar (9× multiplier)
- **Betting Interface**: Place bets with amount validation
- **Auto Balance Update**: Real-time balance updates after each bet
- **Bet History**: Track all recent bets and their status
- **Win Celebration**: Animated win notifications with payout display

---

## 🚀 Setup Instructions

### Step 1: Create Firebase Project

1. Go to [Firebase Console](https://console.firebase.google.com)
2. Click "Create a new project"
3. Enter project name: `Gold-King`
4. Choose region and create

### Step 2: Enable Services

1. **Authentication**:
   - Go to Authentication → Sign-in method
   - Enable "Email/Password"

2. **Firestore Database**:
   - Create a new Firestore Database
   - Choose "Start in test mode" (for development)
   - Select location: India (asia-south1)

### Step 3: Configure Files

Replace `YOUR_API_KEY` etc in all HTML files with your Firebase config:

```javascript
const firebaseConfig = {
    apiKey: "YOUR_API_KEY",
    authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
    projectId: "YOUR_PROJECT_ID",
    storageBucket: "YOUR_PROJECT_ID.appspot.com",
    messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
    appId: "YOUR_APP_ID"
};
```

Get these from Firebase → Project Settings → General tab

### Step 4: Create Admin User

1. Create a user via Firebase Authentication console
2. Add to Firestore under `users/{uid}`:
   ```json
   {
       "email": "admin@goldking.com",
       "role": "admin",
       "balance": 1000000,
       "type": "admin",
       "status": "active"
   }
   ```

### Step 5: Deploy

**Option A: Firebase Hosting**
```bash
npm install -g firebase-tools
firebase init hosting
firebase deploy
```

**Option B: Manual Host**
- Upload files to any web hosting service
- Or open directly in browser (for local testing)

---

## 📊 Firestore Collections

### `users`
```json
{
    "email": "string",
    "balance": "number",
    "type": "admin|master|player|agent",
    "totalBets": "number",
    "createdAt": "timestamp",
    "status": "active|inactive"
}
```

### `bets`
```json
{
    "userId": "string",
    "gameType": "number|group|andarbahar",
    "number": "number",
    "amount": "number",
    "timestamp": "timestamp",
    "status": "pending|won|lost",
    "result": "number|null"
}
```

### `results`
```json
{
    "number": "number",
    "type": "string",
    "timestamp": "timestamp",
    "drawNumber": "number",
    "totalBets": "number",
    "winnerCount": "number"
}
```

### `transactions`
```json
{
    "from": "string|null",
    "to": "string",
    "amount": "number",
    "type": "transfer|bet|winnings|bonus",
    "reason": "string",
    "timestamp": "timestamp",
    "status": "pending|completed|failed"
}
```

---

## 🔐 Firestore Security Rules

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Users can only read/write their own documents
    match /users/{userId} {
      allow read: if request.auth.uid == userId;
      allow write: if request.auth.uid == userId || isAdmin();
    }
    
    // Bets can be read by user and admin
    match /bets/{document=**} {
      allow create: if request.auth != null;
      allow read: if isOwner() || isAdmin();
      allow update: if isAdmin();
    }
    
    // Results visible to all
    match /results/{document=**} {
      allow read: if request.auth != null;
      allow write: if isAdmin();
    }
    
    // Transactions
    match /transactions/{document=**} {
      allow read: if isRelated() || isAdmin();
      allow create: if isAdmin();
    }
    
    function isAdmin() {
      return get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin';
    }
    
    function isOwner() {
      return resource.data.userId == request.auth.uid;
    }
    
    function isRelated() {
      let transaction = resource.data;
      return transaction.from == request.auth.uid || transaction.to == request.auth.uid;
    }
  }
}
```

---

## 💡 Usage Guide

### Admin Workflow
1. Login with admin credentials
2. Access admin.html
3. Use Draw Control to start/stop draws
4. Reveal results → Calculate payouts
5. Monitor users and transactions

### Master Workflow
1. Create user accounts
2. Transfer tokens as bonuses/refunds
3. Track transaction history
4. Monitor user balances

### Player Workflow
1. Login to user.html
2. Select game type
3. Choose number and bet amount
4. Place bet
5. View results and payouts

---

## 🎮 Game Types & Multipliers

| Game | Multiplier | Description |
|------|-----------|-------------|
| Seedha Number | 90× | Pick exact number (0-99) |
| Group | 1.9× | Pick number group |
| Andar Bahar | 9× | Red/Black style betting |

---

## 📱 Responsive Design

- ✅ Mobile-optimized UI
- ✅ Desktop-friendly layouts
- ✅ Touch-friendly buttons
- ✅ Auto-scaling components

---

## 🔧 Customization

### Change Colors
- Admin: Purple gradient (lines 21-22 in admin.html)
- Master: Pink/Red gradient (lines 21-22 in master.html)
- User: Blue gradient (lines 21-22 in user.html)

### Adjust Game Multipliers
- Edit multiplier values in `user.html` around line 150

### Change Timer Duration
- Default: 30 seconds (user.html line 400)

---

## 🐛 Troubleshooting

### Firebase Connection Issues
- Verify API keys are correct
- Check Firestore location matches config
- Enable required APIs in Google Cloud Console

### Authentication Errors
- Ensure Email/Password is enabled in Auth
- Create user in Firebase console first

### Data Not Saving
- Check Firestore security rules
- Verify user has necessary permissions
- Check browser console for errors

---

## 📞 Support

For issues or questions:
1. Check Firestore logs in Firebase Console
2. Review browser console for errors
3. Verify all configuration steps are completed

---

## 📄 License

This project is provided as-is for educational and commercial use.

---

## ⭐ Key Notes

- All amounts are in Indian Rupees (₹)
- Timers reset to 30 seconds by default
- Admin must approve all transactions
- Test with test data before going live
- Keep Firebase API keys secure

---

**Version**: 1.0  
**Last Updated**: 2026-05-25  
**Status**: Production Ready ✅