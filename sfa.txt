<!DOCTYPE html>
<html lang="en">
<head>
  <!-- Required meta tags -->
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

  <!-- Bootstrap CSS -->
  <link rel="stylesheet" href="css/bootstrap.min.css">
  <link href="css/style_home.css" type="text/css" rel="stylesheet">

  <!-- Browser Tab title -->
  <title>SQLi Lab</title>
</head>
<body>
  <nav class="navbar fixed-top navbar-expand-lg navbar-light" style="background-color: #3EA055;">
    <div class="collapse navbar-collapse" id="navbarTogglerDemo01">
      <a class="navbar-brand" href="unsafe_home.php" ><img src="seed_logo.png" style="height: 40px; width: 200px;" alt="SEEDLabs"></a>

      <?php
      session_start();
      // Extract the username and password from the GET request or session
      $input_uname = $_GET['username'] ?? $_SESSION['name'];
      $input_pwd = $_GET['Password'] ?? $_SESSION['pwd'];
      $hashed_pwd = sha1($input_pwd);

      // Function to create a SQL connection
      function getDB() {
        $dbhost = "localhost";
        $dbuser = "root";
        $dbpass = "seedubuntu";
        $dbname = "Users";
        $conn = new mysqli($dbhost, $dbuser, $dbpass, $dbname);
        if ($conn->connect_error) {
          die("Connection failed: " . $conn->connect_error);
        }
        return $conn;
      }

      // Create a connection
      $conn = getDB();

      // Prepare the SQL statement with placeholders
      $stmt = $conn->prepare("SELECT id, name, eid, salary, birth, ssn, phoneNumber, address, email, nickname, Password FROM credential WHERE name = ? AND Password = ?");
      $stmt->bind_param("ss", $input_uname, $hashed_pwd);
      $stmt->execute();
      $result = $stmt->get_result();

      // Fetch the results into an array
      $return_arr = array();
      while ($row = $result->fetch_assoc()) {
        array_push($return_arr, $row);
      }

      if (!empty($return_arr)) {
        // User exists and is successfully authenticated
        $user_data = $return_arr[0]; // Assuming the first result is the correct user
        drawLayout($user_data['id'], $user_data['name'], $user_data['eid'], $user_data['salary'], $user_data['birth'], $user_data['ssn'], $user_data['phoneNumber'], $user_data['address'], $user_data['email'], $user_data['nickname'], $user_data['Password']);
      } else {
        // User authentication failed
        echo "<div class='container text-center'>";
        echo "<div class='alert alert-danger'>";
        echo "The account information you provide does not exist.";
        echo "<br>";
        echo "</div>";
        echo "<a href='index.html'>Go back</a>";
        echo "</div>";
        return;
      }
      $stmt->close();
      $conn->close();

      function drawLayout($id, $name, $eid, $salary, $birth, $ssn, $phoneNumber, $address, $email, $nickname, $password) {
        // Layout drawing logic goes here
      }
      ?>
    </div>
    <script type="text/javascript">
    function logout(){
      location.href = "logoff.php";
    }
    </script>
  </body>
</html>

