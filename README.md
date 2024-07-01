To provide a fully functional example of a PC booking system project, including both front-end (HTML, CSS, JavaScript) and a basic back-end server (using Node.js with Express and a mock in-memory database), I'll guide you through setting up the project step by step.

### Project Structure:

1. **Folder Structure**:
   - Create a folder for your project. Inside it, organize the files as follows:
     ```
     pc-booking-system/
     ├── public/
     │   ├── css/
     │   │   └── style.css
     │   └── scripts.js
     ├── server/
     │   └── server.js
     ├── index.html
     └── package.json
     ```

   - **Explanation**:
     - `public/`: Contains front-end assets.
     - `server/`: Contains the Node.js server file.
     - `index.html`: Main HTML file.
     - `package.json`: Node.js project file for managing dependencies.

2. **Front-end (HTML, CSS, JavaScript)**:

   **index.html**:
   ```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <title>PC Booking System</title>
       <link rel="stylesheet" href="public/css/style.css">
   </head>
   <body>
       <header>
           <h1>PC Booking System</h1>
           <nav>
               <ul>
                   <li><a href="#home">Home</a></li>
                   <li><a href="#login">Login</a></li>
                   <li><a href="#booking">Booking</a></li>
                   <li><a href="#admin">Admin</a></li>
               </ul>
           </nav>
       </header>
       <main>
           <section id="home">
               <h2>Welcome to the PC Booking System</h2>
           </section>
           <section id="login">
               <h2>Login</h2>
               <form id="loginForm">
                   <label for="username">Username:</label>
                   <input type="text" id="username" name="username" required>
                   <label for="password">Password:</label>
                   <input type="password" id="password" name="password" required>
                   <button type="submit">Login</button>
               </form>
           </section>
           <section id="booking">
               <h2>Book a PC</h2>
               <div id="pcAvailability">
                   <!-- PC availability will be dynamically loaded here -->
               </div>
               <form id="bookingForm">
                   <label for="pcSelect">Select PC:</label>
                   <select id="pcSelect" name="pcSelect">
                       <!-- Options will be dynamically loaded -->
                   </select>
                   <label for="bookingTime">Booking Time:</label>
                   <input type="datetime-local" id="bookingTime" name="bookingTime" required>
                   <button type="submit">Book</button>
               </form>
           </section>
           <section id="admin">
               <h2>Admin Panel</h2>
               <div id="adminPanel">
                   <!-- Admin functionalities will be implemented here -->
               </div>
           </section>
       </main>
       <footer>
           <p>&copy; 2024 PC Booking System</p>
       </footer>
       <script src="public/scripts.js"></script>
   </body>
   </html>
   ```

   **public/css/style.css**:
   ```css
   /* public/css/style.css */
   body {
       font-family: Arial, sans-serif;
       margin: 0;
       padding: 0;
       background-color: #f0f0f0;
   }
   header {
       background-color: #333;
       color: #fff;
       padding: 10px 0;
       text-align: center;
   }
   nav ul {
       list-style-type: none;
       padding: 0;
   }
   nav ul li {
       display: inline;
       margin: 0 10px;
   }
   main {
       padding: 20px;
   }
   form {
       margin-bottom: 20px;
   }
   footer {
       background-color: #333;
       color: #fff;
       text-align: center;
       padding: 10px 0;
       position: fixed;
       bottom: 0;
       width: 100%;
   }
   ```

   **public/scripts.js**:
   ```javascript
   // public/scripts.js
   document.addEventListener('DOMContentLoaded', function() {
       fetchPCAvailability(); // Fetch PC availability when the page loads
       
       // Handle booking form submission
       document.getElementById('bookingForm').addEventListener('submit', function(event) {
           event.preventDefault(); // Prevent default form submission
       
           // Get form data
           const pcId = document.getElementById('pcSelect').value;
           const bookingTime = document.getElementById('bookingTime').value;
       
           // Example validation (add more as needed)
           if (!pcId || !bookingTime) {
               alert('Please select a PC and enter a booking time.');
               return;
           }
       
           // Example: Send booking request to server (replace with actual API call)
           const bookingData = {
               pcId: pcId,
               bookingTime: bookingTime
           };
       
           fetch('/api/bookPC', {
               method: 'POST',
               headers: {
                   'Content-Type': 'application/json'
               },
               body: JSON.stringify(bookingData)
           })
           .then(response => {
               if (!response.ok) {
                   throw new Error('Failed to book PC');
               }
               return response.json();
           })
           .then(data => {
               // Handle success, e.g., show confirmation message
               alert('PC booked successfully!');
               // Optionally, update UI or fetch new PC availability
               fetchPCAvailability(); // Example to refresh PC availability
           })
           .catch(error => {
               console.error('Error booking PC:', error);
               alert('Failed to book PC. Please try again later.');
           });
       });
   });
   
   function fetchPCAvailability() {
       fetch('/api/pcAvailability')
           .then(response => response.json())
           .then(data => {
               const pcSelect = document.getElementById('pcSelect');
               pcSelect.innerHTML = '';
               data.forEach(pc => {
                   const option = document.createElement('option');
                   option.value = pc.id;
                   option.textContent = pc.name;
                   pcSelect.appendChild(option);
               });
           })
           .catch(error => console.error('Error fetching PC availability:', error));
   }
   ```

3. **Back-end (Node.js with Express)**:

   **server/server.js**:
   ```javascript
   // server/server.js
   const express = require('express');
   const bodyParser = require('body-parser');
   const app = express();
   const PORT = process.env.PORT || 3000;

   // In-memory data (mock database)
   let pcAvailability = [
       { id: 1, name: 'PC-1', available: true },
       { id: 2, name: 'PC-2', available: true },
       { id: 3, name: 'PC-3', available: false }, // Example unavailable PC
       { id: 4, name: 'PC-4', available: true },
       { id: 5, name: 'PC-5', available: true }
   ];

   app.use(bodyParser.json());

   // Serve static files from the 'public' folder
   app.use(express.static('public'));

   // Endpoint to fetch PC availability
   app.get('/api/pcAvailability', (req, res) => {
       res.json(pcAvailability.filter(pc => pc.available));
   });

   // Endpoint to book a PC
   app.post('/api/bookPC', (req, res) => {
       const { pcId, bookingTime } = req.body;

       // Example: Update availability in mock database
       const pcIndex = pcAvailability.findIndex(pc => pc.id === parseInt(pcId));
       if (pcIndex !== -1) {
           pcAvailability[pcIndex].available = false; // Mark PC as booked
           res.status(200).json({ message: 'PC booked successfully' });
       } else {
           res.status(404).json({ error: 'PC not found' });
       }
   });

   // Start the server
   app.listen(PORT, () => {
       console.log(`Server is running on http://localhost:${PORT}`);
   });
   ```

4. **Setting Up Dependencies**:

   - **package.json**: Create or update `package.json` to include necessary dependencies and scripts.
     ```json
     {
       "name": "pc-booking-system",
       "version": "1.0.0",
       "description": "PC Booking System",
       "main": "server/server.js",
       "scripts": {
         "start": "node server/server.js"
       },
       "dependencies": {
         "express": "^4.17.1",
         "body-parser": "^1.19.0"
       }
     }
     ```
     - Install dependencies by running `npm install` in the root directory of your project.

5. **Run the Project**:
   - Start the server: `npm start` (or `node server/server.js`).
   - Open your browser and navigate to `http://localhost:3000` to see the PC Booking System in action.

### Explanation:

- **Front-end**: 
  - `index.html` provides the structure and layout of the PC Booking System.
  - `style.css` styles

 the application for better visual appeal.
  - `scripts.js` handles dynamic content loading (PC availability) and form submissions (booking).

- **Back-end**: 
  - `server.js` uses Express.js to create HTTP endpoints (`/api/pcAvailability` and `/api/bookPC`) to serve and process data related to PC availability and booking.
  - It uses a mock in-memory database (`pcAvailability`) to simulate PC availability and booking functionality.

- **Integration**: 
  - Front-end communicates with the back-end via HTTP requests (`fetch` API).
  - Booking functionality simulates marking a PC as booked by updating `pcAvailability` in memory.

This setup provides a basic framework for a PC Booking System. Depending on your actual requirements, you may need to enhance error handling, implement authentication, and integrate with a real database for persistence. Adjust and expand the code according to your specific needs and scale.