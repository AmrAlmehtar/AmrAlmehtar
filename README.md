/// === [1] Frontend - React.js === ///
// Install React.js and create a project
// Open the command line and run:
npx create-react-app frontend
cd frontend
npm install axios react-router-dom tailwindcss

// main.js - Entry point
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';
import './index.css';

ReactDOM.render(<App />, document.getElementById('root'));

// App.js - Main application
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

// pages/Home.js - Display listings
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
            <h1>Classified Listings</h1>
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

/// === [2] Backend - Node.js + Express === ///
// Install Express.js and Mongoose
// Open the command line inside a new `backend` folder and run:
npm init -y
npm install express mongoose cors dotenv

// server.js - Create API for listings
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

/// === [3] Database - MongoDB Atlas === ///
// Use MongoDB Atlas to create a database, then place the connection string in `.env`
MONGO_URI=mongodb+srv://your-username:your-password@cluster.mongodb.net/dubizzle_clone?retryWrites=true&w=majority

/// === [4] Run the project === ///
// Start the backend server:
cd backend
node server.js

// Start the frontend:
cd frontend
npm start

// Open `http://localhost:3000` to see the website working 🎉
