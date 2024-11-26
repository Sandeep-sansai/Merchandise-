To achieve your requirement of a left-side navbar with options and content displayed on the right side, where only the right-side content reloads when you click on the navbar links, you can use HTML and iframes.

Here’s the code:

HTML Code:

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Left Navbar with Iframe</title>
  <style>
    body {
      margin: 0;
      font-family: Arial, sans-serif;
      display: flex;
      height: 100vh;
    }
    .navbar {
      width: 20%;
      background-color: #333;
      color: white;
      display: flex;
      flex-direction: column;
      padding: 20px;
    }
    .navbar a {
      text-decoration: none;
      color: white;
      padding: 10px;
      margin: 5px 0;
      display: block;
      border-radius: 5px;
      text-align: center;
    }
    .navbar a:hover {
      background-color: #575757;
    }
    .content {
      width: 80%;
      height: 100%;
      border: none;
    }
  </style>
</head>
<body>
  <!-- Navbar Section -->
  <div class="navbar">
    <a href="home.html" target="contentFrame">Home</a>
    <a href="about.html" target="contentFrame">About Me</a>
    <a href="education.html" target="contentFrame">Education</a>
    <a href="hobbies.html" target="contentFrame">Hobbies</a>
  </div>

  <!-- Content Section with iframe -->
  <iframe class="content" name="contentFrame" src="home.html"></iframe>
</body>
</html>

Steps:

	1.	Create separate HTML files for each of the pages (home.html, about.html, education.html, hobbies.html) with the desired content.

home.html

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Home</title>
</head>
<body>
  <h1>Welcome to the Home Page</h1>
  <p>This is the content for the Home page.</p>
</body>
</html>

about.html

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>About Me</title>
</head>
<body>
  <h1>About Me</h1>
  <p>This is the content for the About Me page.</p>
</body>
</html>

education.html

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Education</title>
</head>
<body>
  <h1>Education</h1>
  <p>This is the content for the Education page.</p>
</body>
</html>

hobbies.html

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Hobbies</title>
</head>
<body>
  <h1>Hobbies</h1>
  <p>This is the content for the Hobbies page.</p>
</body>
</html>

Explanation:

	1.	Navbar Section:
	•	The links (<a> tags) point to separate HTML pages (home.html, about.html, etc.).
	•	The target="contentFrame" attribute ensures the links open in the iframe.
	2.	Iframe Section:
	•	The <iframe> element acts as the content container.
	•	The src="home.html" loads the default content (Home) when the page is initially opened.
	•	The name="contentFrame" allows the navbar links to target the iframe and load content dynamically.
	3.	Styling:
	•	The navbar is styled to take up 20% of the screen width, and the iframe takes up 80%.
	•	Hover effects are added to the navbar options for better interactivity.

Now, when you click on a navbar option, only the right-side iframe reloads with the respective content, keeping the rest of the page static.