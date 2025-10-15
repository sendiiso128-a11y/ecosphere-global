# EcoSphere Global — Fullstack Template

This single-file repository contains a complete starter template for **EcoSphere Global**: a React frontend (Vite) + Firebase backend + Serverless functions (OpenAI & Translate proxy). Use this as a base to deploy a full-featured multilingual eco-social app with AI chat and learning modules.

---

## Repository structure (one-file representation)

```
/README.md
/frontend/ (Vite + React)
  ├─ package.json
  ├─ src/main.jsx
  ├─ src/App.jsx
  ├─ src/firebase.js
  ├─ src/pages/Home.jsx
  ├─ src/pages/EcoLearn.jsx
  ├─ src/pages/EcoFeed.jsx
  ├─ src/pages/EcoChat.jsx
  ├─ src/components/Navbar.jsx
  ├─ src/components/MissionCard.jsx
  ├─ src/styles.css
/functions/ (Node Express serverless functions)
  ├─ package.json
  ├─ index.js  // exposes /openai and /translate endpoints
/firebase.json
/.firebaserc
/.env.example
```

---

## README (quick start)

1. Create Firebase project: https://console.firebase.google.com/
2. Enable Authentication (Email/Google), Firestore, and Storage.
3. Get Firebase config and put it into `frontend/.env` (see `.env.example`).
4. Create an OpenAI API key and optionally Google Cloud Translate API key.
5. Deploy serverless functions (Firebase Functions or Vercel serverless) using `functions/` code.
6. Run frontend locally (`npm install && npm run dev`) and point AI calls to deployed function endpoints.

---

## .env.example

```
VITE_FIREBASE_API_KEY=YOUR_FIREBASE_API_KEY
VITE_FIREBASE_AUTH_DOMAIN=YOUR_AUTH_DOMAIN
VITE_FIREBASE_PROJECT_ID=YOUR_PROJECT_ID
VITE_FIREBASE_STORAGE_BUCKET=YOUR_STORAGE_BUCKET
VITE_FIREBASE_MESSAGING_SENDER_ID=YOUR_MESSAGING_ID
VITE_FIREBASE_APP_ID=YOUR_APP_ID

# Your deployed serverless base URL for OpenAI & Translate proxies
REACT_APP_API_BASE=https://your-serverless.example

# For local functions testing, use: http://localhost:5001/<project>/us-central1
```

---

## frontend/package.json

```json
{
  "name": "ecosphere-frontend",
  "version": "0.1.0",
  "private": true,
  "dependencies": {
    "firebase": "^9.23.0",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-router-dom": "^6.14.1",
    "axios": "^1.4.0",
    "localforage": "^1.10.0"
  },
  "devDependencies": {
    "vite": "^5.0.0"
  },
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  }
}
```

---

## frontend/src/firebase.js

```js
import { initializeApp } from 'firebase/app';
import { getAuth, GoogleAuthProvider } from 'firebase/auth';
import { getFirestore } from 'firebase/firestore';
import { getStorage } from 'firebase/storage';

const firebaseConfig = {
  apiKey: import.meta.env.VITE_FIREBASE_API_KEY,
  authDomain: import.meta.env.VITE_FIREBASE_AUTH_DOMAIN,
  projectId: import.meta.env.VITE_FIREBASE_PROJECT_ID,
  storageBucket: import.meta.env.VITE_FIREBASE_STORAGE_BUCKET,
  messagingSenderId: import.meta.env.VITE_FIREBASE_MESSAGING_SENDER_ID,
  appId: import.meta.env.VITE_FIREBASE_APP_ID,
};

const app = initializeApp(firebaseConfig);
export const auth = getAuth(app);
export const googleProvider = new GoogleAuthProvider();
export const db = getFirestore(app);
export const storage = getStorage(app);
```

---

## frontend/src/main.jsx

```jsx
import React from 'react'
import { createRoot } from 'react-dom/client'
import { BrowserRouter, Routes, Route } from 'react-router-dom'
import App from './App'
import './styles.css'

createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <BrowserRouter>
      <Routes>
        <Route path="/*" element={<App/>} />
      </Routes>
    </BrowserRouter>
  </React.StrictMode>
)
```

---

## frontend/src/App.jsx

```jsx
import React from 'react'
import { Routes, Route } from 'react-router-dom'
import Home from './pages/Home'
import EcoLearn from './pages/EcoLearn'
import EcoFeed from './pages/EcoFeed'
import EcoChat from './pages/EcoChat'
import Navbar from './components/Navbar'

export default function App(){
  return (
    <div className="app-root">
      <Navbar />
      <main>
        <Routes>
          <Route path="/" element={<Home/>} />
          <Route path="/learn" element={<EcoLearn/>} />
          <Route path="/feed" element={<EcoFeed/>} />
          <Route path="/chat" element={<EcoChat/>} />
        </Routes>
      </main>
    </div>
  )
}
```

---

## frontend/src/pages/Home.jsx

```jsx
import React from 'react'
import MissionCard from '../components/MissionCard'

export default function Home(){
  // sample missions - in production load from Firestore
  const missions = [
    { id: 1, title: 'Buang sampah di tempatnya', pts: 10 },
    { id: 2, title: 'Hemat air 5 menit', pts: 8 },
    { id: 3, title: 'Tanam 1 pohon', pts: 50 },
  ]

  return (
    <div className="page container">
      <h1>EcoSphere Global</h1>
      <p>One World, One Green Mission</p>
      <section className="missions">
        {missions.map(m => <MissionCard key={m.id} mission={m} />)}
      </section>
    </div>
  )
}
```

---

## frontend/src/components/MissionCard.jsx

```jsx
import React from 'react'

export default function MissionCard({mission}){
  return (
    <div className="mission-card">
      <h3>{mission.title}</h3>
      <p>Points: {mission.pts}</p>
      <button>Mark Done</button>
    </div>
  )
}
```

---

## frontend/src/pages/EcoLearn.jsx

```jsx
import React from 'react'

const lessons = [
  { id: 'compost', title: 'Membuat Kompos dari Sampah Organik', yt: 'https://www.youtube.com/embed/VIDEO_ID' },
  { id: 'pupuk', title: 'Pupuk Organik Tingkat Lanjut', yt: 'https://www.youtube.com/embed/VIDEO_ID' },
]

export default function EcoLearn(){
  return (
    <div className="page container">
      <h2>EcoLearn — Pertanian & Kompos</h2>
      <div className="lessons">
        {lessons.map(l => (
          <article key={l.id} className="lesson">
            <h3>{l.title}</h3>
            <div className="video-wrap">
              <iframe src={l.yt} title={l.title} frameBorder="0" allowFullScreen style={{width:'100%',height:250}} />
            </div>
            <p>Ringkasan singkat dan step-by-step disini.</p>
          </article>
        ))}
      </div>
    </div>
  )
}
```

---

## frontend/src/pages/EcoFeed.jsx

```jsx
import React, {useState, useEffect} from 'react'
import { db } from '../firebase'
import { collection, query, orderBy, onSnapshot } from 'firebase/firestore'

export default function EcoFeed(){
  const [posts, setPosts] = useState([])
  useEffect(()=>{
    // realtime listener - requires proper Firestore rules
    const q = query(collection(db, 'EcoFeed'), orderBy('createdAt', 'desc'))
    const unsub = onSnapshot(q, snap=>{
      const arr = snap.docs.map(d=>({id:d.id,...d.data()}))
      setPosts(arr)
    })
    return ()=>unsub()
  },[])

  return (
    <div className="page container">
      <h2>EcoFeed</h2>
      {posts.length===0 && <p>No posts yet — be the first!</p>}
      <div className="feed">
        {posts.map(p=> (
          <div key={p.id} className="post">
            <img src={p.photoURL} alt="post" />
            <div className="post-body">
              <strong>{p.authorName}</strong>
              <p>{p.caption}</p>
            </div>
          </div>
        ))}
      </div>
    </div>
  )
}
```

---

## frontend/src/pages/EcoChat.jsx

```jsx
import React, {useState} from 'react'
import axios from 'axios'

export default function EcoChat(){
  const [q, setQ] = useState('')
  const [ans, setAns] = useState('')
  const [loading, setLoading] = useState(false)

  async function ask(){
    if(!q) return
    setLoading(true)
    try{
      const base = import.meta.env.REACT_APP_API_BASE || ''
      const res = await axios.post(`${base}/openai`, {question:q})
      setAns(res.data.answer)
    }catch(e){
      console.error(e)
      setAns('Terjadi kesalahan. Coba lagi nanti.')
    }finally{setLoading(false)}
  }

  return (
    <div className="page container">
      <h2>EcoChat — Ask Eco AI</h2>
      <textarea value={q} onChange={e=>setQ(e.target.value)} placeholder="Tanya tentang kompos, pupuk, atau praktik pertanian" />
      <div>
        <button onClick={ask} disabled={loading}>{loading? '...' : 'Tanya'}</button>
      </div>
      <div className="answer">
        <pre>{ans}</pre>
      </div>
    </div>
  )
}
```

---

## frontend/src/components/Navbar.jsx

```jsx
import React from 'react'
import { Link } from 'react-router-dom'

export default function Navbar(){
  return (
    <nav className="nav">
      <div className="brand">EcoSphere Global</div>
      <div className="links">
        <Link to="/">Home</Link>
        <Link to="/learn">EcoLearn</Link>
        <Link to="/feed">EcoFeed</Link>
        <Link to="/chat">EcoChat</Link>
      </div>
    </nav>
  )
}
```

---

## frontend/src/styles.css

```css
:root{--green:#2da84f;--muted:#666}
body{font-family:Inter,system-ui,Arial,Helvetica,sans-serif;margin:0}
.app-root{min-height:100vh;background:#f6fff6}
nav.nav{display:flex;justify-content:space-between;align-items:center;padding:12px 20px;background:white;box-shadow:0 1px 4px rgba(0,0,0,0.06)}
nav .brand{font-weight:700;color:var(--green)}
.container{max-width:960px;margin:24px auto;padding:0 16px}
.mission-card{background:white;padding:12px;border-radius:8px;margin:8px 0;box-shadow:0 1px 6px rgba(0,0,0,0.04)}
.feed .post{display:flex;gap:12px;background:white;padding:10px;border-radius:8px;margin:8px 0}
textarea{width:100%;min-height:120px;padding:8px}
button{background:var(--green);color:white;border:none;padding:8px 12px;border-radius:6px}
```

---

## functions/package.json

```json
{
  "name": "ecosphere-functions",
  "version": "0.1.0",
  "main": "index.js",
  "dependencies": {
    "express": "^4.18.2",
    "axios": "^1.4.0",
    "body-parser": "^1.20.2"
  }
}
```

---

## functions/index.js (Express server for serverless deployment)

```js
// Simple proxy endpoints to call OpenAI and Translate safely from client
// Deploy this as Firebase Functions or as a Vercel/Netlify serverless function

const express = require('express')
const axios = require('axios')
const bodyParser = require('body-parser')
const app = express()
app.use(bodyParser.json())

const OPENAI_KEY = process.env.OPENAI_API_KEY
const GOOGLE_TRANSLATE_KEY = process.env.GOOGLE_TRANSLATE_KEY // optional

app.post('/openai', async (req, res) => {
  const { question, lang } = req.body
  try{
    // prompt includes role and context
    const prompt = `You are Eco AI, an expert in sustainable agriculture and waste management. Answer concisely and include simple steps. If user language is not English, answer in that language.`
    const data = {
      model: 'gpt-4o-mini',
      messages: [
        { role: 'system', content: prompt },
        { role: 'user', content: question }
      ]
    }
    const r = await axios.post('https://api.openai.com/v1/chat/completions', data, {
      headers: { Authorization: `Bearer ${OPENAI_KEY}` }
    })
    const answer = r.data.choices?.[0]?.message?.content || 'No answer'
    res.json({ answer })
  }catch(e){
    console.error(e.response?.data || e.message)
    res.status(500).json({error:'OpenAI error'})
  }
})

app.post('/translate', async (req, res)=>{
  // optional translation endpoint using Google Translate API
  try{
    const { text, target } = req.body
    const r = await axios.post(`https://translation.googleapis.com/language/translate/v2?key=${GOOGLE_TRANSLATE_KEY}`, {q:text,target})
    res.json(r.data)
  }catch(e){
    console.error(e.response?.data || e.message)
    res.status(500).json({error:'Translate error'})
  }
})

// export for serverless
module.exports = app
if(require.main === module){
  const PORT = process.env.PORT || 3000
  app.listen(PORT, ()=>console.log('Server listening', PORT))
}
```

---

## Deployment instructions (short)

1. **Firebase (hosting + functions)**
   - Install Firebase CLI: `npm i -g firebase-tools`
   - `firebase login` -> `firebase init` (select Hosting + Functions + Firestore)
   - Copy `functions/` and `frontend/` into project, set env vars with `firebase functions:config:set openai.key="..."`
   - `firebase deploy` will publish both.

2. **Alternative: Vercel (frontend) + Vercel Serverless (functions)**
   - Create new Vercel project, push frontend to GitHub, connect.
   - Deploy `functions/index.js` as Serverless Function (API route) and set environment variables (OPENAI_API_KEY).
   - In frontend `.env`, set `REACT_APP_API_BASE` to your Vercel function base url.
   - Vercel guide: https://vercel.com/docs

3. **OpenAI key**: get it from https://platform.openai.com/

---

## Security & Notes
- Never expose your OpenAI or Google Translate API keys in client code. Always proxy via serverless
- Set Firestore rules to restrict writes to authenticated users
- For multilingual UX, prefer server-side translations for static content; use client-side translation for user-generated content via Translate API
- Consider caching AI responses for repeated questions to reduce cost

---

## Next steps I can help with
- Generate ready-to-paste Google Sheets structure for no-code alternative
- Provide exact Firebase rules and Cloud Function deploy commands
- Customize UI to support accessibility and offline-first behavior


# End of template
# ecosphere-global
