/* Center the card on the page */
mat-card {
  margin: 20px auto;
  max-width: 600px;
  padding: 20px;
  box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.1);
  border-radius: 8px;
}

/* Form styling */
form {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

/* Material form field adjustments */
mat-form-field {
  width: 100%;
}

/* Align the button to the center */
button {
  align-self: center;
  padding: 10px 20px;
  font-size: 16px;
  text-transform: uppercase;
}

/* Styling the flight results */
h2 {
  text-align: center;
  margin-top: 20px;
  color: #3f51b5;
  font-weight: bold;
}

ul {
  list-style-type: none;
  padding: 0;
  margin: 0;
}

li {
  background: #f9f9f9;
  border: 1px solid #ddd;
  border-radius: 4px;
  padding: 10px;
  margin: 5px 0;
  font-size: 16px;
}

/* Highlight the card header */
h1 {
  text-align: center;
  font-size: 24px;
  margin-bottom: 20px;
  color: #3f51b5;
  font-weight: bold;
}

/* Add hover effect for list items */
li:hover {
  background: #e0f7fa;
  cursor: pointer;
}

/* Responsive design */
@media (max-width: 768px) {
  mat-card {
    max-width: 90%;
  }
}





tvrvrcecebtnevececec





/* Container Styling */
.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
  font-family: 'Arial', sans-serif;
}

/* Header Styling */
h1 {
  text-align: center;
  font-size: 32px;
  color: #3f51b5;
  font-weight: bold;
  margin-bottom: 30px;
}

h2, h3 {
  color: #2c3e50;
  font-weight: bold;
  margin-bottom: 15px;
}

/* Form Styling */
form {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
  justify-content: space-between;
  margin-bottom: 30px;
}

mat-form-field {
  flex: 1;
  min-width: 200px;
}

button {
  margin-top: 10px;
  background-color: #3f51b5;
  color: white;
  font-size: 16px;
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  text-transform: uppercase;
  cursor: pointer;
  transition: background-color 0.3s;
}

button:hover {
  background-color: #283593;
}

/* Table Styling */
table {
  width: 100%;
  margin: 20px 0;
  border-collapse: collapse;
  box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.1);
  border-radius: 8px;
  overflow: hidden;
}

/* Table Headers */
th {
  background-color: #3f51b5;
  color: white;
  text-align: left;
  padding: 10px;
  font-size: 16px;
}

/* Table Rows */
td {
  padding: 10px;
  text-align: left;
  font-size: 14px;
  color: #2c3e50;
}

tr:nth-child(even) {
  background-color: #f9f9f9;
}

tr:hover {
  background-color: #e3f2fd;
  cursor: pointer;
}

/* Table Divider */
.mat-elevation-z8 {
  border: 1px solid #ddd;
}

/* Section Spacing */
h2, h3 {
  margin-top: 40px;
  border-bottom: 2px solid #3f51b5;
  display: inline-block;
  padding-bottom: 5px;
}

/* Responsive Design */
@media (max-width: 768px) {
  form {
    flex-direction: column;
    gap: 10px;
  }

  mat-form-field {
    min-width: unset;
    width: 100%;
  }

  button {
    width: 100%;
  }

  table {
    font-size: 12px;
  }

  th, td {
    padding: 8px;
  }
}