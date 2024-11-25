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
 yehdh         <li><a href="education.html" target="contentFrame">Education</a></li>
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