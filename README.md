# Uberrrr
Tech Stack:  Frontend: React with tailwind css  Backend: Node.js + Express.js  Database: MongoDB (can be swapped with Firebase or PostgreSQL)  Maps: Google Maps API or Mapbox 
Frontend
// App.jsx
import React, { useState } from 'react';
import { Button } from "@/components/ui/button";

export default function App() {
  const [pickup, setPickup] = useState('');
  const [dropoff, setDropoff] = useState('');
  const [status, setStatus] = useState('');

  const handleBook = async () => {
    const response = await fetch('http://localhost:3000/book', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ pickup, dropoff }),
    });
    const data = await response.json();
    setStatus(data.message);
  };

  return (
    <div className="p-4 max-w-md mx-auto">
      <h1 className="text-2xl font-bold mb-4">Book a Ride</h1>
      <input
        className="w-full p-2 border rounded mb-2"
        placeholder="Pickup Location"
        value={pickup}
        onChange={(e) => setPickup(e.target.value)}
      />
      <input
        className="w-full p-2 border rounded mb-2"
        placeholder="Dropoff Location"
        value={dropoff}
        onChange={(e) => setDropoff(e.target.value)}
      />
      <Button onClick={handleBook}>Book Ride</Button>
      <p className="mt-4">{status}</p>
    </div>
  );
}
Backend
// server.js
const express = require('express');
const cors = require('cors');
const mongoose = require('mongoose');

const app = express();
app.use(cors());
app.use(express.json());

mongoose.connect('mongodb://localhost:27017/uberClone', {
  useNewUrlParser: true,
  useUnifiedTopology: true,
});

const Ride = mongoose.model('Ride', {
  pickup: String,
  dropoff: String,
  status: { type: String, default: 'pending' },
  timestamp: { type: Date, default: Date.now },
});

app.post('/book', async (req, res) => {
  const { pickup, dropoff } = req.body;
  const ride = new Ride({ pickup, dropoff });
  await ride.save();
  res.json({ message: 'Ride booked successfully!', rideId: ride._id });
});

app.get('/rides', async (req, res) => {
  const rides = await Ride.find().sort({ timestamp: -1 });
  res.json(rides);
});

app.listen(3000, () => {
  console.log('Server running on http://localhost:3000');
});
