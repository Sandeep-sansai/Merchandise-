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