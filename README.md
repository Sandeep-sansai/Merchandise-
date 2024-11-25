Here’s a simple implementation of your requirement using iframes without CSS and JavaScript. The navigation bar remains on the left, and the content dynamically loads different pages into the main area using iframes.

Main Page: index.html

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Navigation with iframes</title>
</head>
<body>
  <!-- Navigation Sidebar -->
  <table border="1" style="width: 100%; height: 100vh;">
    <tr>
      <!-- Sidebar -->
      <td style="width: 20%; vertical-align: top; text-align: left;">
        <h2>Navigation</h2>
        <ul>
          <li><a href="about.html" target="contentFrame">About Me</a></li>
          <li><a href="education.html" target="contentFrame">Education</a></li>
          <li><a href="hobbies.html" target="contentFrame">Hobbies</a></li>
        </ul>
      </td>
      
      <!-- Main Content Area -->
      <td style="width: 80%;">
        <iframe name="contentFrame" src="about.html" style="width: 100%; height: 100%; border: none;"></iframe>
      </td>
    </tr>
  </table>
</body>
</html>

About Me Page: about.html

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>About Me</title>
</head>
<body>
  <h1>About Me</h1>
  <p>This is the About Me section. Here you can add information about yourself.</p>
</body>
</html>

Education Page: education.html

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Education</title>
</head>
<body>
  <h1>Education</h1>
  <p>This is the Education section. Add your educational background here.</p>
</body>
</html>

Hobbies Page: hobbies.html

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Hobbies</title>
</head>
<body>
  <h1>Hobbies</h1>
  <p>This is the Hobbies section. Add details about your hobbies here.</p>
</body>
</html>

How It Works:

	1.	Table Layout:
	•	The navigation bar is placed in the left column of a table (<td>), and the iframe is in the right column.
	2.	Dynamic Content Loading:
	•	The iframe has a name attribute (contentFrame), and each navigation link (<a>) uses the target attribute to load its respective page into the iframe.
	3.	Default Content:
	•	The iframe loads the about.html page by default using the src attribute.

This approach keeps the sidebar always visible while dynamically changing the content in the iframe.