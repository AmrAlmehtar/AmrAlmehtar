/// === [1] الواجهة الأمامية (Frontend) - React.js === ///
// تثبيت React.js وإنشاء مشروع
// افتح سطر الأوامر وشغل:
npx create-react-app frontend
cd frontend
npm install axios react-router-dom tailwindcss

// main.js - نقطة الدخول
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';
import './index.css';

ReactDOM.render(<App />, document.getElementById('root'));

// App.js - الصفحة الرئيسية
import React from 'react';
import { BrowserRouter as Router, Route, Routes } from 'react-router-dom';
import Home from './pages/Home';
import Listing from './pages/Listing';

function App() {
    return (
        <Router>
            <Routes>
                <Route path='/' element={<Home />} />
                <Route path='/listing/:id' element={<Listing />} />
            </Routes>
        </Router>
    );
}

export default App;

// pages/Home.js - عرض الإعلانات
import React, { useState, useEffect } from 'react';
import axios from 'axios';

function Home() {
    const [listings, setListings] = useState([]);
    
    useEffect(() => {
        axios.get('http://localhost:5000/api/listings')
             .then(res => setListings(res.data))
             .catch(err => console.error(err));
    }, []);
    
    return (
        <div>
            <h1>إعلانات مبوبة</h1>
            {listings.map(listing => (
                <div key={listing._id}>
                    <h2>{listing.title}</h2>
                    <p>{listing.price} $</p>
                </div>
            ))}
        </div>
    );
}

export default Home;


/// === [2] الخلفية (Backend) - Node.js + Express === ///
// تثبيت Express.js و Mongoose
// افتح سطر الأوامر داخل مجلد جديد `backend` وشغل:
npm init -y
npm install express mongoose cors dotenv

// server.js - إنشاء API للإعلانات
const express = require('express');
const mongoose = require('mongoose');
const cors = require('cors');
require('dotenv').config();

const app = express();
app.use(cors());
app.use(express.json());

mongoose.connect(process.env.MONGO_URI, {
    useNewUrlParser: true,
    useUnifiedTopology: true
});

const ListingSchema = new mongoose.Schema({
    title: String,
    description: String,
    price: Number,
    image: String
});

const Listing = mongoose.model('Listing', ListingSchema);

app.get('/api/listings', async (req, res) => {
    const listings = await Listing.find();
    res.json(listings);
});

app.post('/api/listings', async (req, res) => {
    const newListing = new Listing(req.body);
    await newListing.save();
    res.status(201).json(newListing);
});

app.listen(5000, () => console.log('Server running on port 5000'));


/// === [3] قاعدة البيانات - MongoDB Atlas === ///
// استخدم MongoDB Atlas لإنشاء قاعدة بيانات، ثم ضع الرابط في ملف `.env`
MONGO_URI=mongodb+srv://your-username:your-password@cluster.mongodb.net/dubizzle_clone?retryWrites=true&w=majority


/// === [4] تشغيل المشروع === ///
// شغل الخادم (Backend):
cd backend
node server.js

// شغل الواجهة (Frontend):
cd frontend
npm start

// افتح `http://localhost:3000` لرؤية الموقع يعمل 🎉
