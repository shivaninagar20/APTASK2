# APTASK2
Below is a simple step-by-step guide to develop a web application that performs CRUD operations with basic user authentication.
Example stack: PHP + MySQL + HTML/CSS + Bootstrap (because you previously asked about PHP & MySQL).


---

✅ Features we will build

Feature	Description

Register	New users can sign up
Login	Only authenticated users can access CRUD pages
Logout	Destroy user session
CRUD	Create, Read, Update & Delete records (example: Manage Students / Tasks / Products)



---

📌 Folder Structure

crud-app/
│ index.php
│ login.php
│ register.php
│ logout.php
│ database.php
│ style.css
│ dashboard.php
│ add.php
│ edit.php
│ delete.php
└── /uploads (if needed)


---

🔹 1. Create Database

Run this SQL in phpMyAdmin:

CREATE DATABASE crud_app;

USE crud_app;

CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  username VARCHAR(100) NOT NULL,
  password VARCHAR(255) NOT NULL
);

CREATE TABLE items (
  id INT AUTO_INCREMENT PRIMARY KEY,
  title VARCHAR(200) NOT NULL,
  description TEXT
);


---

🔹 2. Database Connection → database.php

<?php
$conn = mysqli_connect("localhost", "root", "", "crud_app");
if (!$conn) {
    die("Database Connection Failed!");
}
?>


---

🔹 3. User Registration → register.php

<?php
include "database.php";
if (isset($_POST["register"])) {
    $username = $_POST["username"];
    $password = password_hash($_POST["password"], PASSWORD_DEFAULT);

    mysqli_query($conn, "INSERT INTO users(username, password) VALUES('$username', '$password')");
    header("Location: login.php");
}
?>
<form method="post">
  Username: <input type="text" name="username" required><br>
  Password: <input type="password" name="password" required><br>
  <button name="register">Register</button>
</form>


---

🔹 4. Login → login.php

<?php
session_start();
include "database.php";

if (isset($_POST["login"])) {
    $username = $_POST["username"];
    $password = $_POST["password"];

    $result = mysqli_query($conn, "SELECT * FROM users WHERE username='$username'");
    $user = mysqli_fetch_assoc($result);

    if ($user && password_verify($password, $user['password'])) {
        $_SESSION["user"] = $username;
        header("Location: dashboard.php");
    } else {
        echo "Invalid login!";
    }
}
?>
<form method="post">
  Username: <input type="text" name="username" required><br>
  Password: <input type="password" name="password" required><br>
  <button name="login">Login</button>
</form>


---

🔹 5. Logout → logout.php

<?php
session_start();
session_destroy();
header("Location: login.php");
?>


---

🔹 6. CRUD Dashboard → dashboard.php

<?php
session_start();
if (!isset($_SESSION["user"])) header("Location: login.php");
include "database.php";
$result = mysqli_query($conn, "SELECT * FROM items");
?>
<h2>Welcome, <?php echo $_SESSION["user"]; ?></h2>
<a href="add.php">Add New</a> | <a href="logout.php">Logout</a>
<table border="1">
<tr><th>ID</th><th>Title</th><th>Description</th><th>Action</th></tr>
<?php while($row = mysqli_fetch_assoc($result)) { ?>
<tr>
<td><?= $row['id'] ?></td>
<td><?= $row['title'] ?></td>
<td><?= $row['description'] ?></td>
<td>
<a href="edit.php?id=<?= $row['id'] ?>">Edit</a>
<a href="delete.php?id=<?= $row['id'] ?>">Delete</a>
</td>
</tr>
<?php } ?>
</table>


---

🔹 7. Add Item → add.php

<?php
session_start();
if (!isset($_SESSION["user"])) header("Location: login.php");
include "database.php";
if (isset($_POST["add"])) {
    $title = $_POST["title"];
    $desc = $_POST["description"];
    mysqli_query($conn, "INSERT INTO items(title, description) VALUES('$title', '$desc')");
    header("Location: dashboard.php");
}
?>
<form method="post">
  Title: <input type="text" name="title"><br>
  Description: <textarea name="description"></textarea><br>
  <button name="add">Save</button>
</form>


---

🔹 8. Edit Item → edit.php

<?php
session_start();
if (!isset($_SESSION["user"])) header("Location: login.php");
include "database.php";

$id = $_GET["id"];
$result = mysqli_query($conn, "SELECT * FROM items WHERE id=$id");
$data = mysqli_fetch_assoc($result);

if (isset($_POST["update"])) {
    $title = $_POST["title"];
    $desc = $_POST["description"];
    mysqli_query($conn, "UPDATE items SET title='$title', description='$desc' WHERE id=$id");
    header("Location: dashboard.php");
}
?>
<form method="post">
  Title: <input type="text" name="title" value="<?= $data['title'] ?>"><br>
  Description: <textarea name="description"><?= $data['description'] ?></textarea><br>
  <button name="update">Update</button>
</form>


---

🔹 9. Delete Item → delete.php

<?php
include "database.php";
$id = $_GET["id"];
mysqli_query($conn, "DELETE FROM items WHERE id=$id");
header("Location: dashboard.php");
?>


---

🎉 Your CRUD Web App with Authentication is Ready!

✔ Login required to access CRUD pages

✔ Secure password storing

✔ Full Create, Read, Update, Delete operations


---

🚀 Optional Enhancements

Feature	Tools

UI Design	Bootstrap / Tailwind
Search & Pagination	AJAX / PHP
File Upload	Move uploads into folder
Role Based Access	Admin/User separation
API Version	REST API with JSON


