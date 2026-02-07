---
title: reactFirebase
domain: deployment
tags: [tag1, tag2]
---
# Step 1: Scaffold Project
<!-- JAAVIS:EXEC -->
```bash
# 1. Setup Variables
PROJECT_NAME=$(basename "$PWD" | tr '[:upper:]' '[:lower:]')
echo "🔥 Scaffolding React + Firebase for: $PROJECT_NAME"

# 2. Initialize Vite (in current dir)
# Only run if package.json doesn't exist to avoid overwriting
if [ ! -f "package.json" ]; then
    echo "📦 Initializing Vite Project..."
    npm create vite@latest . -- --template vanilla
    npm install
else
    echo "⚠️  package.json found. Skipping Vite init."
fi

# 3. Install Dependencies
echo "⬇️  Installing Firebase SDK..."
npm install firebase

# 4. Generate Firebase Config (Bypassing interactive 'firebase init')
echo "⚙️  Generating Firebase Hosting & Firestore Config..."

# firebase.json: Configures Hosting (SPA rewrites) and Firestore
cat <<EOF > firebase.json
{
  "hosting": {
    "public": "dist",
    "ignore": [
      "firebase.json",
      "**/.*",
      "**/node_modules/**"
    ],
    "rewrites": [
      {
        "source": "**",
        "destination": "/index.html"
      }
    ]
  },
  "firestore": {
    "rules": "firestore.rules",
    "indexes": "firestore.indexes.json"
  }
}
EOF

# .firebaserc: Sets the default project alias
cat <<EOF > .firebaserc
{
  "projects": {
    "default": "PLACEHOLDER_PROJECT_ID"
  }
}
EOF

# 5. Initialize Source Configs
mkdir -p src

# src/firebase.js
cat <<EOF > src/firebase.js
export const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_PROJECT_ID.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abc123",
};
EOF

# firestore.rules
cat <<EOF > firestore.rules
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /todos/{todoId} {
      allow read, write: if request.auth != null && request.auth.uid == resource.data.uid;
      allow create: if request.auth != null && request.auth.uid == request.resource.data.uid;
    }
  }
}
EOF

# src/main.js
cat <<EOF > src/main.js
import { firebaseConfig } from './firebase';
// Example Logic
console.log("🔥 Firebase Initialized for: $PROJECT_NAME");
EOF

# 6. Verification
if [ -f "src/firebase.js" ]; then
    echo "✅ Project Scaffolded in $(pwd)/"
    echo "👉 Action Items:"
    echo "1. Update 'src/firebase.js' with your actual keys."
    echo "2. Update '.firebaserc' with your project ID."
    echo "3. Run 'npm run build' && 'firebase deploy'."
else
    echo "❌ Error: src/firebase.js was not created."
    exit 1
fi
```

