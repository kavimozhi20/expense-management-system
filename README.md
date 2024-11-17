# expense-management-system
add_expense.php

<?php
session_start();
require 'db.php';

if (!isset($_SESSION['user_id'])) {
    header("Location: login.php");
    exit;
}

if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $amount = $_POST['amount'];
    $category = $_POST['category'];
    $date = $_POST['date'];
    $user_id = $_SESSION['user_id'];

    $stmt = $pdo->prepare("INSERT INTO expenses (user_id, amount, category, date) VALUES (?, ?, ?, ?)");
    $stmt->execute([$user_id, $amount, $category, $date]);
    echo "Expense added successfully!";
}
?>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Add Expense</title>
</head>
<body>
    <h1>Add Expense</h1>
    <form action="" method="post">
         <input type="number" name="amount" placeholder="Amount" required>
        <select id="category" name="category" required>
        <option value="">--Select Category--</option>
       <option value="food">Food</option>
       <option value="entertainment">Entertainment</option>
       <option value="education">Education</option>
       <option value="utilities">Utilities</option>
       <option value="transportation">Transportation</option>
    <option value="others">Others</option>
</select><br><br>

        <input type="date" name="date" required>
        <button type="submit">Add Expense</button>
    </form>
</body>
</html>

category_expenses.php
<div class="category-container">
    <!-- Category 1: Food -->
    <div class="category">
        <h2>Food</h2>
        <table>
            <thead>
                <tr>
                    <th>Date</th>
                    <th>Item</th>
                    <th>Amount</th>
                </tr>
            </thead>
            <tbody>
                <tr>
                    <td>2024-10-01</td>
                    <td>Groceries</td>
                    <td>$50.00</td>
                </tr>
                <tr>
                    <td>2024-10-03</td>
                    <td>Restaurant</td>
                    <td>$30.00</td>
                </tr>
            </tbody>
        </table>
    </div>

    <!-- Category 2: Transportation -->
    <div class="category">
        <h2>Transportation</h2>
        <table>
            <thead>
                <tr>
                    <th>Date</th>
                    <th>Item</th>
                    <th>Amount</th>
                </tr>
            </thead>
            <tbody>
                <tr>
                    <td>2024-10-05</td>
                    <td>Bus Ticket</td>
                    <td>$2.50</td>
                </tr>
                <tr>
                    <td>2024-10-07</td>
                    <td>Taxi</td>
                    <td>$15.00</td>
                </tr>
            </tbody>
        </table>
    </div>

    <!-- Category 3: Entertainment -->
    <div class="category">
        <h2>Entertainment</h2>
        <table>
            <thead>
                <tr>
                    <th>Date</th>
                    <th>Item</th>
                    <th>Amount</th>
                </tr>
            </thead>
            <tbody>
                <tr>
                    <td>2024-10-08</td>
                    <td>Movie</td>
                    <td>$12.00</td>
                </tr>
                <tr>
                    <td>2024-10-10</td>
                    <td>Concert</td>
                    <td>$40.00</td>
                </tr>
            </tbody>
        </table>
    </div>
</div>
/* Category Container */
.category-container {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
}

.category {
    width: 30%; /* Adjust this for 3 columns; change width if fewer or more categories */
    background-color: #f9f9f9;
    margin-bottom: 20px;
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

/* Category Titles */
.category h2 {
    color: #4a90e2;
    text-align: center;
    margin-bottom: 10px;
}

/* Table Styles (same as before) */
table {
    width: 100%;
    border-collapse: collapse;
}

table th, table td {
    border: 1px solid #ddd;
    padding: 8px;
    text-align: left;
}

table th {
    background-color: #4a90e2;
    color: white;
}

table tr:nth-child(even) {
    background-color: #f2f2f2;
}

db.php
<?php
$host = 'localhost';
$db = 'expense_management';
$user = 'root'; // Default XAMPP MySQL username
$pass = ''; // Default password (usually empty)

try {
    $pdo = new PDO("mysql:host=$host;dbname=$db", $user, $pass);
    $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
} catch (PDOException $e) {
    die("Could not connect to the database: " . $e->getMessage());
}
?>
delete_expense.php
<?php
session_start();
require 'db.php';

// Check if user is logged in
if (!isset($_SESSION['user_id'])) {
    header("Location: login.php");
    exit;
}

// Check if an id is provided
if (isset($_GET['id'])) {
    $id = $_GET['id'];

    // Prepare and execute the delete statement
    $stmt = $pdo->prepare("DELETE FROM expenses WHERE id = ? AND user_id = ?");
    if ($stmt->execute([$id, $_SESSION['user_id']])) {
        echo "Expense deleted successfully!";
    } else {
        echo "Failed to delete the expense.";
    }
}

// Redirect to the view expenses page
header("Location: view_expenses.php");
exit();
?>


edit_expense.php



<!DOCTYPE html>
<html>
<head>
    <title>Edit Expense</title>
</head>
<body>
    <h2>Edit Expense</h2>

    <?php
    require 'db.php';

    if (isset($_GET['id'])) {
        $id = $_GET['id'];
        $stmt = $pdo->prepare("SELECT * FROM expenses WHERE id = ?");
        $stmt->execute([$id]);
        $expense = $stmt->fetch(PDO::FETCH_ASSOC);
    }

    if ($_SERVER["REQUEST_METHOD"] == "POST") {
        $amount = $_POST['amount'];
        $category = $_POST['category'];
        $date = $_POST['date'];

        $stmt = $pdo->prepare("UPDATE expenses SET amount = ?, category = ?, date = ? WHERE id = ?");
        $stmt->execute([$amount, $category, $date, $id]);

        echo "Expense updated successfully!";
        header("Location: view_expenses.php");
        exit();
    }
    ?>

    <form action="edit_expense.php?id=<?php echo $id; ?>" method="POST">
        Amount: <input type="number" step="0.01" name="amount" value="<?php echo $expense['amount']; ?>" required><br>
        Category: <input type="text" name="category" value="<?php echo $expense['category']; ?>" required><br>
        Date: <input type="date" name="date" value="<?php echo $expense['date']; ?>" required><br>
        <input type="submit" value="Update Expense">
    </form>
</body>
</html>

login.php
<?php
session_start();
require 'db.php';

if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $username = $_POST['username'];
    $password = $_POST['password'];

    $stmt = $pdo->prepare("SELECT * FROM users WHERE username = ?");
    $stmt->execute([$username]);
    $user = $stmt->fetch();

    if ($user && password_verify($password, $user['password'])) {
        $_SESSION['user_id'] = $user['id'];
        header("Location: view_expenses.php");
        exit;
    } else {
        echo "Invalid username or password.";
    }
}
?>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Login</title>
</head>
<body>
    <h1>Login</h1>
    <form action="" method="post">
        <input type="text" name="username" placeholder="Username" required>
        <input type="password" name="password" placeholder="Password" required>
        <button type="submit">Login</button>
    </form>

</body>
</html>

logout.php
<?php
session_start();
session_destroy();
header("Location: login.php");
exit;
?>

register.php
<?php
require 'db.php';

if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $username = $_POST['username'];
    $password = password_hash($_POST['password'], PASSWORD_DEFAULT);

    $stmt = $pdo->prepare("INSERT INTO users (username, password) VALUES (?, ?)");
    $stmt->execute([$username, $password]);
    echo "Registration successful!";
}
?>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Register</title>
    <h1 class="animated-heading">Expense Management System!!!</h1>
   

    <style>
         body 
         {
            background-color: #397676; 
        }
        .animated-heading {
  font-size: 3em; /* Adjust the font size as needed */
  animation: fadeIn 2s ease-in-out infinite;
}

@keyframes fadeIn {
  0% { opacity: 0; transform: translateY(-10px); }
  50% { opacity: 1; transform: translateY(0); }
  100% { opacity: 0; transform: translateY(10px); }
}


    </style>
    </head>
<body>
    <h1>Register</h1>
     <form action="" method="post">
        <input type="text" name="username" placeholder="Username" required>
        <input type="password" name="password" placeholder="Password" required>
        <button type="submit">Register</button>
    </form>
    <p>Already have an account? <a href="login.php">Login here</a></p>
</body>
</html>

style.css
.button {
    background-color: #4CAF50;
    border: none;
    color: white;
    padding: 10px 20px;
    text-align: center;
    text-decoration: none;
    font-size: 16px;
    cursor: pointer;
    transition: background-color 0.4s;
  }
  
  .button:hover {
    background-color: white;
    color: #4CAF50;
    border: 2px solid #4CAF50;
  }

view_expenses.php

<?php
session_start();
require 'db.php';

if (!isset($_SESSION['user_id'])) {
    header("Location: login.php");
    exit;
}

$user_id = $_SESSION['user_id'];

$stmt = $pdo->prepare("SELECT * FROM expenses WHERE user_id = ?");
$stmt->execute([$user_id]);
$expenses = $stmt->fetchAll();
?>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Your Expenses</title>
</head>
<style>
         body 
         {
            background-color: #73dd15; 
        }
        </style>
<body>
    <h1>Your Expenses</h1>
    <table border="6">
        <tr>
            <th>Amount</th>
            <th>Category</th>
            <th>Date</th>
        </tr>
        <?php foreach ($expenses as $expense): ?>
        <tr>
            <td><?php echo htmlspecialchars($expense['amount']); ?></td>
            <td><?php echo htmlspecialchars($expense['category']); ?></td>
            <td><?php echo htmlspecialchars($expense['date']); ?></td>
            <td><a href="edit_expense.php?id=<?php echo $expense['id']; ?>">Edit</a></td>
            <td><a href="delete_expense.php?id=<?php echo $expense['id']; ?>" onclick="return confirm('Are you sure you want to delete this expense?');">Delete</a></td>
<
        </tr>
        <?php endforeach; ?>
    </table>
    <a href="add_expense.php">Add New Expense</a>
    <a href="logout.php">Logout</a>
</body>
</html>
  

