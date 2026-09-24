# Room-Reservation
room reservation for delayed exams, quizzes, meetings, and etc.
# index.php
<?php
session_start();
?>

<!DOCTYPE html>

<html lang="en">

<head>

    <meta charset="UTF-8">

    <meta name="viewport"
          content="width=device-width, initial-scale=1.0">

    <title>
        Cagayan de Oro College | Room Reservation System
    </title>

    <link rel="stylesheet"
          href="style.css">

</head>


<body class="home-page">


<!-- =====================================================
     NAVBAR
===================================================== -->

<header class="home-navbar">


    <a href="index.php"
       class="brand">


        <img
            src="logo.png"
            alt="Cagayan de Oro College Logo"
        >


        <div class="brand-text">

            <span class="brand-title">
                CAGAYAN DE ORO COLLEGE
            </span>

            <span class="brand-subtitle">
                ROOM RESERVATION SYSTEM
            </span>

        </div>


    </a>


    <nav class="home-nav">

        <a href="index.php">
            Home
        </a>

        <a href="login.php">
            Login
        </a>

        <a href="register.php">
            Register
        </a>

    </nav>


</header>


<!-- =====================================================
     HERO
===================================================== -->

<section class="hero">


    <div class="hero-logo">

        <img
            src="logo.png"
            alt="Cagayan de Oro College Logo"
        >

    </div>


    <div class="hero-content">

        <h1>
            CAGAYAN DE ORO COLLEGE
        </h1>

        <h2>
            ROOM RESERVATION SYSTEM
        </h2>

        <p>
            Efficient room management for a
            better learning environment.
        </p>

    </div>


</section>


<!-- =====================================================
     ACCESS OPTIONS
===================================================== -->

<section class="home-options">


    <!-- TEACHER LOGIN -->

    <div class="option-card">

        <div class="option-icon">
            👤
        </div>


        <h3>
            Teacher Login
        </h3>


        <p>
            Access your account to reserve
            rooms and manage your reservations.
        </p>


        <a
            href="login.php"
            class="home-button"
        >
            Login
            <span>→</span>
        </a>


        <p>

            Don't have an account?

            <a href="register.php">
                <strong>
                    Register here →
                </strong>
            </a>

        </p>

    </div>


    <!-- TEACHER REGISTRATION -->

    <div class="option-card">

        <div class="option-icon">
            👤+
        </div>


        <h3>
            Teacher Registration
        </h3>


        <p>
            Create your account to access
            the room reservation system.
        </p>


        <a
            href="register.php"
            class="home-button secondary-button"
        >
            Create Account
            <span>→</span>
        </a>


        <p>

            Already have an account?

            <a href="login.php">
                <strong>
                    Login here →
                </strong>
            </a>

        </p>

    </div>


    <!-- ADMIN -->

    <div class="option-card">

        <div class="option-icon">
            ⚙
        </div>


        <h3>
            Admin Login
        </h3>


        <p>
            Access the administrator dashboard
            to manage reservation requests.
        </p>


        <a
            href="admin_login.php"
            class="home-button"
        >
            Login as Administrator
            <span>→</span>
        </a>


        <p>

            <a href="index.php">
                Back to Homepage →
            </a>

        </p>

    </div>


</section>


<!-- =====================================================
     FOOTER
===================================================== -->

<footer class="site-footer">


    <div>
        Cagayan de Oro College
        &nbsp; | &nbsp;
        Room Reservation System
    </div>


    <div class="motto">
        Pro Deo et Humanitate
        <span style="color: #d6bd22;">
            ━━━
        </span>
    </div>


</footer>


</body>

</html> 

#logo.php
#put the logo of picture of your School

#register.php
<?php

session_start();

require_once "config.php";
require_once "functions.php";

$message = "";

if ($_SERVER["REQUEST_METHOD"] === "POST") {

    // Get and clean inputs
    $full_name = clean_input($_POST["full_name"] ?? "");
    $email = clean_input($_POST["email"] ?? "");
    $department = clean_input($_POST["department"] ?? "");
    $password = $_POST["password"] ?? "";
    $confirm_password = $_POST["confirm_password"] ?? "";

    // =========================
    // VALIDATION
    // =========================

    if (
        empty($full_name) ||
        empty($email) ||
        empty($department) ||
        empty($password) ||
        empty($confirm_password)
    ) {

        $message = error_message(
            "Please fill in all required fields."
        );

    } elseif (!filter_var($email, FILTER_VALIDATE_EMAIL)) {

        $message = error_message(
            "Please enter a valid email address."
        );

    } elseif (strlen($password) < 6) {

        $message = error_message(
            "Password must be at least 6 characters."
        );

    } elseif ($password !== $confirm_password) {

        $message = error_message(
            "Passwords do not match."
        );

    } else {

        // =========================
        // CHECK DUPLICATE EMAIL
        // =========================

        $check = $pdo->prepare(
            "SELECT id FROM teachers WHERE email = ?"
        );

        $check->execute([$email]);

        if ($check->fetch()) {

            $message = error_message(
                "An account with this email already exists."
            );

        } else {

            // =========================
            // HASH PASSWORD
            // =========================

            $hashed_password = password_hash(
                $password,
                PASSWORD_DEFAULT
            );

            // =========================
            // INSERT TEACHER
            // =========================

            $stmt = $pdo->prepare(
                "INSERT INTO teachers
                (full_name, email, password, department)
                VALUES (?, ?, ?, ?)"
            );

            $stmt->execute([
                $full_name,
                $email,
                $hashed_password,
                $department
            ]);

            $message = success_message(
                "Teacher account created successfully!"
            );
        }
    }
}
?>

<!DOCTYPE html>
<html lang="en">

<head>

    <meta charset="UTF-8">

    <meta name="viewport"
          content="width=device-width, initial-scale=1.0">

    <title>Teacher Registration - COC Room Reservation</title>

    <link rel="stylesheet" href="style.css">

</head>

<body>

<div class="container">

    <div class="card">

        <h1>Teacher Registration</h1>

        <p class="small-text">
            Create your teacher account
        </p>

        <?php echo $message; ?>

        <form method="POST">

            <label for="full_name">
                Full Name
            </label>

            <input
                type="text"
                id="full_name"
                name="full_name"
                placeholder="Enter your full name"
                required
            >


            <label for="email">
                School Email
            </label>

            <input
                type="email"
                id="email"
                name="email"
                placeholder="Enter your school email"
                required
            >


            <label for="department">
                Department
            </label>

            <input
                type="text"
                id="department"
                name="department"
                placeholder="Enter your department"
                required
            >


            <label for="password">
                Password
            </label>

            <input
                type="password"
                id="password"
                name="password"
                placeholder="Minimum of 6 characters"
                required
            >


            <label for="confirm_password">
                Confirm Password
            </label>

            <input
                type="password"
                id="confirm_password"
                name="confirm_password"
                placeholder="Re-enter your password"
                required
            >


            <button type="submit">
                Create Account
            </button>

        </form>

        <p>
            Already have an account?
            <a href="login.php">
                Login here
            </a>
        </p>

        <p>
            <a href="index.php">
                ← Back to Homepage
            </a>
        </p>

    </div>

</div>

</body>

</html>

login.php
<?php

session_start();

require_once "config.php";
require_once "functions.php";

$message = "";

if ($_SERVER["REQUEST_METHOD"] === "POST") {

    // Get inputs
    $email = clean_input($_POST["email"] ?? "");
    $password = $_POST["password"] ?? "";

    // =========================
    // VALIDATION
    // =========================

    if (empty($email) || empty($password)) {

        $message = error_message(
            "Please enter your email and password."
        );

    } elseif (!filter_var($email, FILTER_VALIDATE_EMAIL)) {

        $message = error_message(
            "Please enter a valid email address."
        );

    } else {

        // =========================
        // FIND TEACHER
        // =========================

        $stmt = $pdo->prepare(
            "SELECT * FROM teachers WHERE email = ?"
        );

        $stmt->execute([$email]);

        $teacher = $stmt->fetch();

        // =========================
        // VERIFY LOGIN
        // =========================

        if (
            $teacher &&
            password_verify($password, $teacher["password"])
        ) {

            // Create teacher session
            $_SESSION["teacher_id"] = $teacher["id"];
            $_SESSION["teacher_name"] = $teacher["full_name"];
            $_SESSION["teacher_email"] = $teacher["email"];
            $_SESSION["teacher_department"] = $teacher["department"];

            // Redirect to reservation page
            header("Location: reserve.php");
            exit;

        } else {

            $message = error_message(
                "Invalid email or password."
            );
        }
    }
}

?>

<!DOCTYPE html>
<html lang="en">

<head>

    <meta charset="UTF-8">

    <meta name="viewport"
          content="width=device-width, initial-scale=1.0">

    <title>Teacher Login - COC Room Reservation</title>

    <link rel="stylesheet" href="style.css">

</head>

<body>

<div class="container">

    <div class="card">

        <h1>Teacher Login</h1>

        <p class="small-text">
            COC Teacher Room Reservation System
        </p>

        <?php echo $message; ?>

        <form method="POST">

            <label for="email">
                School Email
            </label>

            <input
                type="email"
                id="email"
                name="email"
                placeholder="Enter your school email"
                required
            >


            <label for="password">
                Password
            </label>

            <input
                type="password"
                id="password"
                name="password"
                placeholder="Enter your password"
                required
            >


            <button type="submit">
                Login
            </button>

            <p style="text-align:center; margin-top:15px;">
    <a href="forgot_password.php">
        Forgot Password?
    </a>
</p>

        </form>

        <p>
            Don't have an account?
            <a href="register.php">
                Register here
            </a>
        </p>

        <p>
            <a href="index.php">
                ← Back to Homepage
            </a>
        </p>

    </div>

</div>

</body>

</html>

#logout.php
<?php

session_start();

// Remove all session data
$_SESSION = [];

// Destroy session
session_destroy();

// Return to login page
header("Location: login.php");
exit;

#reserve.php
<?php

session_start();

require_once "config.php";
require_once "functions.php";


// Make sure teacher is logged in
require_teacher_login();


// =========================
// DEFAULT VALUES
// =========================

$message = "";

$selected_date = $_GET["date"] ?? date("Y-m-d");
$start_time = $_GET["start_time"] ?? "";
$end_time = $_GET["end_time"] ?? "";


// =========================
// HANDLE POST REQUEST
// =========================

if ($_SERVER["REQUEST_METHOD"] === "POST") {

    // ==================================================
    // CHECK AVAILABILITY
    // ==================================================

    if (isset($_POST["check_availability"])) {

        $selected_date = clean_input(
            $_POST["reservation_date"] ?? ""
        );

        $start_time = clean_input(
            $_POST["start_time"] ?? ""
        );

        $end_time = clean_input(
            $_POST["end_time"] ?? ""
        );


        // Validate date and time

        if (
            empty($selected_date) ||
            empty($start_time) ||
            empty($end_time)
        ) {

            $message = error_message(
                "Please select a date, start time, and end time."
            );

        } elseif ($selected_date < date("Y-m-d")) {

            $message = error_message(
                "Reservation date cannot be in the past."
            );

        } elseif ($start_time >= $end_time) {

            $message = error_message(
                "End time must be later than start time."
            );
        }
    }


    // ==================================================
    // RESERVE ROOM
    // ==================================================

    elseif (isset($_POST["reserve"])) {

        $teacher_id = $_SESSION["teacher_id"];

        $room_id = intval(
            $_POST["room_id"] ?? 0
        );

        $reservation_date = clean_input(
            $_POST["reservation_date"] ?? ""
        );

        $start_time = clean_input(
            $_POST["start_time"] ?? ""
        );

        $end_time = clean_input(
            $_POST["end_time"] ?? ""
        );

        $purpose = clean_input(
            $_POST["purpose"] ?? ""
        );


        $selected_date = $reservation_date;


        // =========================
        // VALIDATION
        // =========================

        if (
            empty($room_id) ||
            empty($reservation_date) ||
            empty($start_time) ||
            empty($end_time) ||
            empty($purpose)
        ) {

            $message = error_message(
                "Please fill in all required fields."
            );

        } elseif ($reservation_date < date("Y-m-d")) {

            $message = error_message(
                "Reservation date cannot be in the past."
            );

        } elseif ($start_time >= $end_time) {

            $message = error_message(
                "End time must be later than start time."
            );

        } else {


            // =========================
            // CHECK IF ROOM EXISTS
            // =========================

            $room_check = $pdo->prepare(
                "SELECT *
                 FROM rooms
                 WHERE id = ?
                 AND status = 'Available'"
            );

            $room_check->execute([
                $room_id
            ]);

            $room = $room_check->fetch();


            if (!$room) {

                $message = error_message(
                    "The selected room is not available."
                );

            } else {


                // =========================
                // CHECK TIME CONFLICT
                // =========================

                $conflict = $pdo->prepare(
                    "SELECT id
                     FROM reservations
                     WHERE room_id = ?
                     AND reservation_date = ?
                     AND status IN ('Pending', 'Approved')
                     AND start_time < ?
                     AND end_time > ?"
                );

                $conflict->execute([
                    $room_id,
                    $reservation_date,
                    $end_time,
                    $start_time
                ]);


                if ($conflict->fetch()) {

                    $message = error_message(
                        "This room is already reserved/requested for that time."
                    );

                } else {


                    // =========================
                    // INSERT RESERVATION
                    // =========================

                    $stmt = $pdo->prepare(
                        "INSERT INTO reservations
                        (
                            teacher_id,
                            room_id,
                            reservation_date,
                            start_time,
                            end_time,
                            purpose,
                            status
                        )
                        VALUES (?, ?, ?, ?, ?, ?, 'Pending')"
                    );


                    $stmt->execute([
                        $teacher_id,
                        $room_id,
                        $reservation_date,
                        $start_time,
                        $end_time,
                        $purpose
                    ]);


                    $message = success_message(
                        "Reservation submitted successfully!"
                    );


                    // Clear time after successful reservation
                    $start_time = "";
                    $end_time = "";
                }
            }
        }
    }
}


// =========================
// GET ALL ROOMS
// =========================

if (
    !empty($selected_date) &&
    !empty($start_time) &&
    !empty($end_time)
) {

    /*
     * Get all rooms and determine
     * whether each room is reserved
     * for the selected date/time.
     */

    $stmt = $pdo->prepare(
        "SELECT
            rooms.*,

            CASE
                WHEN EXISTS (
                    SELECT 1
                    FROM reservations
                    WHERE reservations.room_id = rooms.id
                    AND reservations.reservation_date = ?
                    AND reservations.status IN ('Pending', 'Approved')
                    AND reservations.start_time < ?
                    AND reservations.end_time > ?
                )
                THEN 1
                ELSE 0
            END AS reserved

        FROM rooms

        ORDER BY rooms.room_name"
    );


    $stmt->execute([
        $selected_date,
        $end_time,
        $start_time
    ]);


    $rooms = $stmt->fetchAll();

} else {

    // No date/time selected yet
    $rooms = $pdo->query(
        "SELECT
            *,
            0 AS reserved
         FROM rooms
         ORDER BY room_name"
    )->fetchAll();
}

?>

<!DOCTYPE html>

<html lang="en">

<head>

    <meta charset="UTF-8">

    <meta name="viewport"
          content="width=device-width, initial-scale=1.0">

    <title>
        Reserve Room - COC Room Reservation
    </title>

    <link rel="stylesheet"
          href="style.css">

</head>


<body>


<!-- =========================
     NAVBAR
========================= -->

<div class="navbar">

    <h2>
        COC Room Reservation
    </h2>


    <div class="nav-links">

        <span>

            Welcome,
            <?php
            echo htmlspecialchars(
                $_SESSION["teacher_name"]
            );
            ?>

        </span>


        <a href="my_reservations.php">
            My Reservations
        </a>


        <a href="logout.php">
            Logout
        </a>

    </div>

</div>


<!-- =========================
     MAIN PAGE
========================= -->

<div class="page">


    <h1 class="page-title">
        Reserve a Room
    </h1>


    <?php echo $message; ?>


    <!-- =========================
         CHECK AVAILABILITY
    ========================= -->

    <div class="card"
         style="width: 100%; margin-bottom: 25px;">

        <h2>
            Check Room Availability
        </h2>


        <form method="POST">


            <label for="reservation_date">
                Reservation Date
            </label>


            <input
                type="date"
                id="reservation_date"
                name="reservation_date"

                min="<?php
                echo date("Y-m-d");
                ?>"

                value="<?php
                echo htmlspecialchars(
                    $selected_date
                );
                ?>"

                required
            >


            <label for="start_time">
                Start Time
            </label>


            <input
                type="time"
                id="start_time"
                name="start_time"

                value="<?php
                echo htmlspecialchars(
                    $start_time
                );
                ?>"

                required
            >


            <label for="end_time">
                End Time
            </label>


            <input
                type="time"
                id="end_time"
                name="end_time"

                value="<?php
                echo htmlspecialchars(
                    $end_time
                );
                ?>"

                required
            >


            <button
                type="submit"
                name="check_availability"
            >
                Check Availability
            </button>


        </form>

    </div>


    <!-- =========================
         ROOM LIST
    ========================= -->

    <?php if (!empty($rooms)): ?>


        <h2>
            COC Rooms and Facilities
        </h2>


        <div class="room-grid">


            <?php foreach ($rooms as $room): ?>


                <div class="room-card">


                    <h3>

                        <?php
                        echo htmlspecialchars(
                            $room["room_name"]
                        );
                        ?>

                    </h3>


                    <p>

                        <strong>
                            Building:
                        </strong>

                        <?php
                        echo htmlspecialchars(
                            $room["building"]
                        );
                        ?>

                    </p>


                    <p>

                        <strong>
                            Capacity:
                        </strong>

                        <?php
                        echo htmlspecialchars(
                            $room["capacity"]
                        );
                        ?>

                        persons

                    </p>


                    <!-- =========================
                         MAINTENANCE
                    ========================= -->

                    <?php if (
                        $room["status"] === "Maintenance"
                    ): ?>


                        <p>

                            <span
                                class="status status-rejected"
                            >

                                🔴 Under Maintenance

                            </span>

                        </p>


                    <!-- =========================
                         RESERVED
                    ========================= -->

                    <?php elseif (
                        $room["reserved"]
                    ): ?>


                        <p>

                            <span
                                class="status status-rejected"
                            >

                                🔴 Reserved

                            </span>

                        </p>


                    <!-- =========================
                         AVAILABLE
                    ========================= -->

                    <?php else: ?>


                        <p>

                            <span
                                class="status status-approved"
                            >

                                🟢 Available

                            </span>

                        </p>


                        <!-- RESERVATION FORM -->

                        <?php if (
                            !empty($start_time) &&
                            !empty($end_time)
                        ): ?>


                            <form method="POST">


                                <input
                                    type="hidden"
                                    name="room_id"
                                    value="<?php
                                    echo $room["id"];
                                    ?>"
                                >


                                <input
                                    type="hidden"
                                    name="reservation_date"
                                    value="<?php
                                    echo htmlspecialchars(
                                        $selected_date
                                    );
                                    ?>"
                                >


                                <input
                                    type="hidden"
                                    name="start_time"
                                    value="<?php
                                    echo htmlspecialchars(
                                        $start_time
                                    );
                                    ?>"
                                >


                                <input
                                    type="hidden"
                                    name="end_time"
                                    value="<?php
                                    echo htmlspecialchars(
                                        $end_time
                                    );
                                    ?>"
                                >


                                <label
                                    for="purpose_<?php
                                    echo $room["id"];
                                    ?>"
                                >
                                    Purpose
                                </label>


                                <textarea
                                    id="purpose_<?php
                                    echo $room["id"];
                                    ?>"

                                    name="purpose"

                                    rows="3"

                                    placeholder="Enter purpose of reservation"

                                    required
                                ></textarea>


                                <button
                                    type="submit"
                                    name="reserve"
                                >
                                    Reserve This Room
                                </button>


                            </form>


                        <?php endif; ?>


                    <?php endif; ?>


                </div>


            <?php endforeach; ?>


        </div>


    <?php endif; ?>


</div>


</body>

</html>

#my_reservation.php
<?php

session_start();

require_once "config.php";
require_once "functions.php";

// Make sure teacher is logged in
require_teacher_login();

$teacher_id = $_SESSION["teacher_id"];

// =========================
// GET TEACHER RESERVATIONS
// =========================

$stmt = $pdo->prepare(
    "SELECT
        reservations.id,
        rooms.room_name,
        rooms.building,
        rooms.capacity,
        reservations.reservation_date,
        reservations.start_time,
        reservations.end_time,
        reservations.purpose,
        reservations.status,
        reservations.created_at

    FROM reservations

    INNER JOIN rooms
        ON reservations.room_id = rooms.id

    WHERE reservations.teacher_id = ?

    ORDER BY
        reservations.reservation_date DESC,
        reservations.start_time DESC"
);

$stmt->execute([$teacher_id]);

$reservations = $stmt->fetchAll();

?>

<!DOCTYPE html>
<html lang="en">

<head>

    <meta charset="UTF-8">

    <meta name="viewport"
          content="width=device-width, initial-scale=1.0">

    <title>My Reservations - COC Room Reservation</title>

    <link rel="stylesheet" href="style.css">

</head>

<body>


<!-- =========================
     NAVBAR
========================= -->

<div class="navbar">

    <h2>COC Room Reservation</h2>

    <div class="nav-links">

        <span>
            Welcome,
            <?php echo htmlspecialchars($_SESSION["teacher_name"]); ?>
        </span>

        <a href="reserve.php">
            Reserve Room
        </a>

        <a href="logout.php">
            Logout
        </a>

    </div>

</div>


<!-- =========================
     MAIN PAGE
========================= -->

<div class="page">

    <h1 class="page-title">
        My Reservations
    </h1>


    <?php if (empty($reservations)): ?>

        <div class="card"
             style="width: 100%;">

            <h2>
                No Reservations Yet
            </h2>

            <p>
                You have not made any room reservations.
            </p>

            <a href="reserve.php"
               class="home-button">

                Reserve a Room

            </a>

        </div>


    <?php else: ?>


        <!-- =========================
             RESERVATION TABLE
        ========================= -->

        <div class="table-container">

            <table>

                <thead>

                    <tr>

                        <th>Room</th>

                        <th>Building</th>

                        <th>Date</th>

                        <th>Time</th>

                        <th>Purpose</th>

                        <th>Status</th>

                    </tr>

                </thead>


                <tbody>

                    <?php foreach ($reservations as $reservation): ?>

                        <tr>

                            <td>
                                <?php
                                echo htmlspecialchars(
                                    $reservation["room_name"]
                                );
                                ?>
                            </td>


                            <td>
                                <?php
                                echo htmlspecialchars(
                                    $reservation["building"]
                                );
                                ?>
                            </td>


                            <td>
                                <?php
                                echo htmlspecialchars(
                                    $reservation["reservation_date"]
                                );
                                ?>
                            </td>


                            <td>

                                <?php
                                echo date(
                                    "g:i A",
                                    strtotime(
                                        $reservation["start_time"]
                                    )
                                );
                                ?>

                                -

                                <?php
                                echo date(
                                    "g:i A",
                                    strtotime(
                                        $reservation["end_time"]
                                    )
                                );
                                ?>

                            </td>


                            <td>
                                <?php
                                echo htmlspecialchars(
                                    $reservation["purpose"]
                                );
                                ?>
                            </td>


                            <td>

                                <?php if (
                                    $reservation["status"]
                                    === "Pending"
                                ): ?>

                                    <span class="status status-pending">
                                        🟡 Pending
                                    </span>


                                <?php elseif (
                                    $reservation["status"]
                                    === "Approved"
                                ): ?>

                                    <span class="status status-approved">
                                        🟢 Approved
                                    </span>


                                <?php elseif (
                                    $reservation["status"]
                                    === "Rejected"
                                ): ?>

                                    <span class="status status-rejected">
                                        🔴 Rejected
                                    </span>


                                <?php elseif (
                                    $reservation["status"]
                                    === "Cancelled"
                                ): ?>

                                    <span class="status status-cancelled">
                                        ⚪ Cancelled
                                    </span>

                                <?php endif; ?>

                            </td>

                        </tr>

                    <?php endforeach; ?>

                </tbody>

            </table>

        </div>


        <div style="text-align: center; margin-top: 25px;">

            <a href="reserve.php"
               class="home-button"
               style="display: inline-block; width: auto;">

                + Make Another Reservation

            </a>

        </div>


    <?php endif; ?>

</div>


</body>

</html>

#forgot_password.php
<?php

session_start();

require_once "config.php";
require_once "functions.php";

$message = "";

if ($_SERVER["REQUEST_METHOD"] === "POST") {

    $email = trim($_POST["email"] ?? "");

    if (empty($email)) {

        $message = error_message(
            "Please enter your email address."
        );

    } elseif (!filter_var($email, FILTER_VALIDATE_EMAIL)) {

        $message = error_message(
            "Please enter a valid email address."
        );

    } else {

        // =========================
        // CHECK TEACHER ACCOUNT
        // =========================

        $stmt = $pdo->prepare(
            "SELECT id
             FROM teachers
             WHERE email = ?"
        );

        $stmt->execute([$email]);

        $teacher = $stmt->fetch();


        if ($teacher) {

            $token = bin2hex(
                random_bytes(32)
            );

            $expires = date(
                "Y-m-d H:i:s",
                time() + 3600
            );

            $stmt = $pdo->prepare(
    "UPDATE teachers
     SET reset_token = ?,
         reset_expires = DATE_ADD(NOW(), INTERVAL 1 HOUR)
     WHERE id = ?"
);

$stmt->execute([
    $token,
    $teacher["id"]
]); 
            header(
                "Location: reset_password.php?token="
                . urlencode($token)
                . "&role=teacher"
            );

            exit;
        }


        // =========================
        // CHECK ADMIN ACCOUNT
        // =========================

        $stmt = $pdo->prepare(
            "SELECT id
             FROM admins
             WHERE email = ?"
        );

        $stmt->execute([$email]);

        $admin = $stmt->fetch();


        if ($admin) {

            $token = bin2hex(
                random_bytes(32)
            );

            $expires = null;

            $stmt = $pdo->prepare(
    "UPDATE admins
     SET reset_token = ?,
         reset_expires = DATE_ADD(NOW(), INTERVAL 1 HOUR)
     WHERE id = ?"
);

$stmt->execute([
    $token,
    $admin["id"]
]);

            header(
                "Location: reset_password.php?token="
                . urlencode($token)
                . "&role=admin"
            );

            exit;
        }


        // =========================
        // EMAIL NOT FOUND
        // =========================

        $message = success_message(
            "If the email is registered, a password reset request has been created."
        );
    }
}

?>

<!DOCTYPE html>

<html lang="en">

<head>

    <meta charset="UTF-8">

    <meta name="viewport"
          content="width=device-width, initial-scale=1.0">

    <title>
        Forgot Password | COC
    </title>

    <link rel="stylesheet"
          href="style.css">

</head>


<body>

<div class="container">

    <div class="card">


        <!-- LOGO -->

        <div style="text-align:center; margin-bottom:20px;">

            <img
                src="logo.png"
                alt="COC Logo"
                style="
                    width:100px;
                    height:100px;
                    object-fit:contain;
                "
            >

        </div>


        <!-- TITLE -->

        <h1>
            Forgot Password?
        </h1>


        <p class="small-text">

            Enter your registered email address
            to reset your password.

        </p>


        <?php echo $message; ?>


        <!-- FORM -->

        <form method="POST">

            <label for="email">
                Email Address
            </label>


            <input
                type="email"
                id="email"
                name="email"
                placeholder="Enter your email"
                required
            >


            <button type="submit">
                Continue
            </button>

        </form>


        <!-- LINKS -->

        <p>

            Remember your password?

            <a href="login.php">
                Back to Teacher Login
            </a>

        </p>


        <p>

            <a href="admin_login.php">
                Admin Login
            </a>

        </p>


    </div>

</div>

</body>

</html>

#reset_password.php
<?php

session_start();

require_once "config.php";
require_once "functions.php";

$message = "";

$token = $_GET["token"]
    ?? $_POST["token"]
    ?? "";

$role = $_GET["role"]
    ?? $_POST["role"]
    ?? "";


if (
    empty($token) ||
    !in_array($role, ["teacher", "admin"])
) {

    die(
        "Invalid password reset request."
    );
}


/*
 * Select correct table
 */

$table = (
    $role === "teacher"
)
    ? "teachers"
    : "admins";


/*
 * Find valid token
 */

$stmt = $pdo->prepare(
    "SELECT id
     FROM $table
     WHERE reset_token = ?
     AND reset_expires > NOW()"
);

$stmt->execute([
    $token
]);

$account = $stmt->fetch();


if (!$account) {

    die(
        "This password reset link is invalid or has expired."
    );
}


/*
 * Handle new password
 */

if ($_SERVER["REQUEST_METHOD"] === "POST") {

    $password = $_POST["password"] ?? "";

    $confirm_password =
        $_POST["confirm_password"] ?? "";


    if (
        empty($password) ||
        empty($confirm_password)
    ) {

        $message = error_message(
            "Please fill in all fields."
        );

    } elseif (strlen($password) < 6) {

        $message = error_message(
            "Password must be at least 6 characters."
        );

    } elseif (
        $password !== $confirm_password
    ) {

        $message = error_message(
            "Passwords do not match."
        );

    } else {

        $hashed_password =
            password_hash(
                $password,
                PASSWORD_DEFAULT
            );


        $stmt = $pdo->prepare(
            "UPDATE $table
             SET password = ?,
                 reset_token = NULL,
                 reset_expires = NULL
             WHERE id = ?"
        );


        $stmt->execute([
            $hashed_password,
            $account["id"]
        ]);


        $message = success_message(
            "Your password has been successfully changed."
        );


        /*
         * Disable form after successful reset
         */

        $token = "";
    }
}

?>

<!DOCTYPE html>

<html lang="en">

<head>

    <meta charset="UTF-8">

    <meta name="viewport"
          content="width=device-width, initial-scale=1.0">

    <title>
        Reset Password | COC
    </title>

    <link rel="stylesheet"
          href="style.css">

</head>

<body>

<div class="container">

    <div class="card">

        <div style="text-align:center; margin-bottom:20px;">

            <img
                src="logo.png"
                alt="COC Logo"
                style="
                    width:100px;
                    height:100px;
                    object-fit:contain;
                "
            >

        </div>


        <h1>
            Reset Password
        </h1>


        <p class="small-text">

            Create a new password for your
            COC account.

        </p>


        <?php echo $message; ?>


        <?php if (!empty($token)): ?>

            <form method="POST">

                <input
                    type="hidden"
                    name="token"
                    value="<?php
                    echo htmlspecialchars($token);
                    ?>"
                >

                <input
                    type="hidden"
                    name="role"
                    value="<?php
                    echo htmlspecialchars($role);
                    ?>"
                >


                <label for="password">
                    New Password
                </label>

                <input
                    type="password"
                    id="password"
                    name="password"
                    placeholder="Enter new password"
                    minlength="6"
                    required
                >


                <label for="confirm_password">
                    Confirm New Password
                </label>

                <input
                    type="password"
                    id="confirm_password"
                    name="confirm_password"
                    placeholder="Confirm new password"
                    minlength="6"
                    required
                >


                <button type="submit">
                    Reset Password
                </button>

            </form>

        <?php else: ?>

            <a
                href="<?php
                echo (
                    $role === "admin"
                )
                    ? "admin_login.php"
                    : "login.php";
                ?>"
                class="home-button"
                style="width:100%;"
            >
                Go to Login
            </a>

        <?php endif; ?>


    </div>

</div>

</body>

</html>

#admin_login.php
<?php

session_start();

require_once "config.php";
require_once "functions.php";

$message = "";

if ($_SERVER["REQUEST_METHOD"] === "POST") {

    // Get inputs
    $email = clean_input($_POST["email"] ?? "");
    $password = $_POST["password"] ?? "";

    // =========================
    // VALIDATION
    // =========================

    if (empty($email) || empty($password)) {

        $message = error_message(
            "Please enter your email and password."
        );

    } elseif (!filter_var($email, FILTER_VALIDATE_EMAIL)) {

        $message = error_message(
            "Please enter a valid email address."
        );

    } else {

        // =========================
        // FIND ADMIN
        // =========================

        $stmt = $pdo->prepare(
            "SELECT * FROM admins WHERE email = ?"
        );

        $stmt->execute([$email]);

        $admin = $stmt->fetch();

        // =========================
        // VERIFY PASSWORD
        // =========================

        if (
            $admin &&
            password_verify(
                $password,
                $admin["password"]
            )
        ) {

            $_SESSION["admin_logged_in"] = true;
            $_SESSION["admin_id"] = $admin["id"];
            $_SESSION["admin_name"] = $admin["full_name"];
            $_SESSION["admin_email"] = $admin["email"];

            header("Location: admin.php");
            exit;

        } else {

            $message = error_message(
                "Invalid admin email or password."
            );
        }
    }
}

?>

<!DOCTYPE html>
<html lang="en">

<head>

    <meta charset="UTF-8">

    <meta name="viewport"
          content="width=device-width, initial-scale=1.0">

    <title>Admin Login - COC Room Reservation</title>

    <link rel="stylesheet" href="style.css">

</head>

<body>

<div class="container">

    <div class="card">

        <h1>Admin Login</h1>

        <p class="small-text">
            COC Teacher Room Reservation System
        </p>

        <?php echo $message; ?>

        <form method="POST">

            <label for="email">
                Admin Email
            </label>

            <input
                type="email"
                id="email"
                name="email"
                placeholder="Enter admin email"
                required
            >

            <label for="password">
                Password
            </label>

            <input
                type="password"
                id="password"
                name="password"
                placeholder="Enter admin password"
                required
            >

            <button type="submit">
                Login as Administrator
            </button>

            <p style="text-align:center; margin-top:15px;">
    <a href="forgot_password.php">
        Forgot Password?
    </a>
</p>

        </form>

        <p>
            <a href="login.php">
                ← Teacher Login
            </a>
        </p>

        <p>
            <a href="index.php">
                ← Back to Homepage
            </a>
        </p>

    </div>

</div>

</body>

</html>

#admin.php
<?php

session_start();

require_once "config.php";
require_once "functions.php";

// Make sure admin is logged in
require_admin_login();

// =========================
// GET ALL RESERVATIONS
// =========================

$stmt = $pdo->query(
    "SELECT
        reservations.id,
        reservations.reservation_date,
        reservations.start_time,
        reservations.end_time,
        reservations.purpose,
        reservations.status,

        teachers.full_name AS teacher_name,
        teachers.email AS teacher_email,
        teachers.department,

        rooms.room_name,
        rooms.building,
        rooms.capacity

    FROM reservations

    INNER JOIN teachers
        ON reservations.teacher_id = teachers.id

    INNER JOIN rooms
        ON reservations.room_id = rooms.id

    ORDER BY
        reservations.reservation_date ASC,
        reservations.start_time ASC"
);

$reservations = $stmt->fetchAll();


// =========================
// ADMIN MESSAGES
// =========================

$admin_message = "";

if (isset($_SESSION["admin_success"])) {

    $admin_message = success_message(
        $_SESSION["admin_success"]
    );

    unset($_SESSION["admin_success"]);
}

if (isset($_SESSION["admin_error"])) {

    $admin_message = error_message(
        $_SESSION["admin_error"]
    );

    unset($_SESSION["admin_error"]);
}
?>

<!DOCTYPE html>
<html lang="en">

<head>

    <meta charset="UTF-8">

    <meta name="viewport"
          content="width=device-width, initial-scale=1.0">

    <title>Admin Dashboard - COC Room Reservation</title>

    <link rel="stylesheet" href="style.css">

    <style>

        .admin-page-title {
            display: flex;
            justify-content: space-between;
            align-items: center;
            gap: 15px;
            margin-bottom: 25px;
        }

        .admin-page-title h1 {
            margin: 0;
        }

        .admin-info {
            color: #6b7280;
            font-size: 14px;
        }

        .action-container {
            display: flex;
            gap: 8px;
            align-items: center;
        }

        .action-container form {
            margin: 0;
        }

        .action-container button {
            width: auto;
            margin: 0;
            padding: 7px 12px;
            font-size: 13px;
        }

        .approve-btn {
            background: #16a34a;
        }

        .approve-btn:hover {
            background: #15803d;
        }

        .reject-btn {
            background: #dc2626;
        }

        .reject-btn:hover {
            background: #b91c1c;
        }

        .no-action {
            color: #6b7280;
            font-size: 13px;
        }

        @media (max-width: 800px) {

            .admin-page-title {
                flex-direction: column;
                align-items: flex-start;
            }

        }

    </style>

</head>

<body>


<!-- =========================
     NAVBAR
========================= -->

<div class="navbar">

    <h2>COC Admin Dashboard</h2>

    <div class="nav-links">

        <span>
            Admin:
            <?php
            echo htmlspecialchars($_SESSION["admin_name"]);
            ?>
        </span>

        <a href="admin_logout.php">
            Logout
        </a>

    </div>

</div>


<!-- =========================
     MAIN PAGE
========================= -->

<div class="page">


    <div class="admin-page-title">

        <div>

            <h1>
                Reservation Requests
            </h1>

            <p class="admin-info">
                Review and manage teacher room reservations.
            </p>

        </div>

    </div>


    <!-- =========================
         ADMIN MESSAGE
    ========================= -->

    <?php echo $admin_message; ?>


    <!-- =========================
         RESERVATION TABLE
    ========================= -->

    <?php if (empty($reservations)): ?>

        <div class="card"
             style="width: 100%;">

            <h2>
                No Reservation Requests
            </h2>

            <p>
                There are currently no reservation requests.
            </p>

        </div>


    <?php else: ?>


        <div class="table-container">

            <table>

                <thead>

                    <tr>

                        <th>Teacher</th>

                        <th>Department</th>

                        <th>Room</th>

                        <th>Date</th>

                        <th>Time</th>

                        <th>Purpose</th>

                        <th>Status</th>

                        <th>Action</th>

                    </tr>

                </thead>


                <tbody>

                    <?php foreach ($reservations as $reservation): ?>

                        <tr>


                            <!-- TEACHER -->

                            <td>

                                <strong>
                                    <?php
                                    echo htmlspecialchars(
                                        $reservation["teacher_name"]
                                    );
                                    ?>
                                </strong>

                                <br>

                                <small>
                                    <?php
                                    echo htmlspecialchars(
                                        $reservation["teacher_email"]
                                    );
                                    ?>
                                </small>

                            </td>


                            <!-- DEPARTMENT -->

                            <td>

                                <?php
                                echo htmlspecialchars(
                                    $reservation["department"]
                                );
                                ?>

                            </td>


                            <!-- ROOM -->

                            <td>

                                <strong>
                                    <?php
                                    echo htmlspecialchars(
                                        $reservation["room_name"]
                                    );
                                    ?>
                                </strong>

                                <br>

                                <small>
                                    Capacity:
                                    <?php
                                    echo htmlspecialchars(
                                        $reservation["capacity"]
                                    );
                                    ?>
                                </small>

                            </td>


                            <!-- DATE -->

                            <td>

                                <?php
                                echo htmlspecialchars(
                                    $reservation["reservation_date"]
                                );
                                ?>

                            </td>


                            <!-- TIME -->

                            <td>

                                <?php

                                echo date(
                                    "g:i A",
                                    strtotime(
                                        $reservation["start_time"]
                                    )
                                );

                                echo " - ";

                                echo date(
                                    "g:i A",
                                    strtotime(
                                        $reservation["end_time"]
                                    )
                                );

                                ?>

                            </td>


                            <!-- PURPOSE -->

                            <td>

                                <?php
                                echo htmlspecialchars(
                                    $reservation["purpose"]
                                );
                                ?>

                            </td>


                            <!-- STATUS -->

                            <td>

                                <?php if (
                                    $reservation["status"]
                                    === "Pending"
                                ): ?>

                                    <span class="status status-pending">
                                        🟡 Pending
                                    </span>


                                <?php elseif (
                                    $reservation["status"]
                                    === "Approved"
                                ): ?>

                                    <span class="status status-approved">
                                        🟢 Approved
                                    </span>


                                <?php elseif (
                                    $reservation["status"]
                                    === "Rejected"
                                ): ?>

                                    <span class="status status-rejected">
                                        🔴 Rejected
                                    </span>


                                <?php elseif (
                                    $reservation["status"]
                                    === "Cancelled"
                                ): ?>

                                    <span class="status status-cancelled">
                                        ⚪ Cancelled
                                    </span>

                                <?php endif; ?>

                            </td>


                            <!-- ACTION -->

                            <td>

                                <?php if (
                                    $reservation["status"]
                                    === "Pending"
                                ): ?>

                                    <div class="action-container">


                                        <!-- APPROVE -->

                                        <form
                                            method="POST"
                                            action="admin_action.php"
                                        >

                                            <input
                                                type="hidden"
                                                name="reservation_id"
                                                value="<?php
                                                echo $reservation["id"];
                                                ?>"
                                            >

                                            <input
                                                type="hidden"
                                                name="action"
                                                value="approve"
                                            >

                                            <button
                                                type="submit"
                                                class="approve-btn"
                                            >
                                                Approve
                                            </button>

                                        </form>


                                        <!-- REJECT -->

                                        <form
                                            method="POST"
                                            action="admin_action.php"
                                        >

                                            <input
                                                type="hidden"
                                                name="reservation_id"
                                                value="<?php
                                                echo $reservation["id"];
                                                ?>"
                                            >

                                            <input
                                                type="hidden"
                                                name="action"
                                                value="reject"
                                            >

                                            <button
                                                type="submit"
                                                class="reject-btn"
                                            >
                                                Reject
                                            </button>

                                        </form>


                                    </div>


                                <?php else: ?>

                                    <span class="no-action">
                                        No action
                                    </span>

                                <?php endif; ?>

                            </td>

                        </tr>

                    <?php endforeach; ?>

                </tbody>

            </table>

        </div>

    <?php endif; ?>


</div>


</body>

</html>

#adminb_logout.php
<?php

session_start();

$_SESSION = [];

session_destroy();

header("Location: admin_login.php");

exit;

#admin_action.php
<?php

session_start();

require_once "config.php";
require_once "functions.php";

// Make sure admin is logged in
require_admin_login();


// =========================
// GET FORM DATA
// =========================

$reservation_id = intval(
    $_POST["reservation_id"] ?? 0
);

$action = $_POST["action"] ?? "";


// =========================
// VALIDATE ACTION
// =========================

if (
    $reservation_id <= 0 ||
    !in_array($action, ["approve", "reject"])
) {
    header("Location: admin.php");
    exit;
}


// =========================
// GET RESERVATION
// =========================

$stmt = $pdo->prepare(
    "SELECT *
     FROM reservations
     WHERE id = ?"
);

$stmt->execute([$reservation_id]);

$reservation = $stmt->fetch();


// Reservation does not exist
if (!$reservation) {

    header("Location: admin.php");
    exit;
}


// =========================
// APPROVE RESERVATION
// =========================

if ($action === "approve") {

    /*
     * Check again for conflicts.
     *
     * We exclude the current reservation
     * using id != ?
     */

    $conflict = $pdo->prepare(
        "SELECT id
         FROM reservations
         WHERE room_id = ?
         AND reservation_date = ?
         AND id != ?
         AND status = 'Approved'
         AND start_time < ?
         AND end_time > ?"
    );

    $conflict->execute([
        $reservation["room_id"],
        $reservation["reservation_date"],
        $reservation["id"],
        $reservation["end_time"],
        $reservation["start_time"]
    ]);


    if ($conflict->fetch()) {

        // Another approved reservation conflicts
        $_SESSION["admin_error"] =
            "Cannot approve. Another approved reservation already uses this room during the selected time.";

    } else {

        // Approve reservation
        $update = $pdo->prepare(
            "UPDATE reservations
             SET status = 'Approved'
             WHERE id = ?"
        );

        $update->execute([
            $reservation_id
        ]);

        $_SESSION["admin_success"] =
            "Reservation approved successfully.";
    }
}


// =========================
// REJECT RESERVATION
// =========================

if ($action === "reject") {

    $update = $pdo->prepare(
        "UPDATE reservations
         SET status = 'Rejected'
         WHERE id = ?"
    );

    $update->execute([
        $reservation_id
    ]);

    $_SESSION["admin_success"] =
        "Reservation rejected successfully.";
}


// =========================
// RETURN TO DASHBOARD
// =========================

header("Location: admin.php");

exit;

#config.php
<?php

$host = "localhost";
$dbname = "coc_room_db";
$username = "root";
$password = "";

try {
    $pdo = new PDO(
        "mysql:host=$host;dbname=$dbname;charset=utf8mb4",
        $username,
        $password
    );

    $pdo->setAttribute(
        PDO::ATTR_ERRMODE,
        PDO::ERRMODE_EXCEPTION
    );

    $pdo->setAttribute(
        PDO::ATTR_DEFAULT_FETCH_MODE,
        PDO::FETCH_ASSOC
    );

} catch (PDOException $e) {
    die("Database connection failed. Please check XAMPP/MySQL.");
}

#functions.php
<?php

// =========================
// SANITIZE INPUT
// =========================
function clean_input($data)
{
    return htmlspecialchars(trim($data), ENT_QUOTES, 'UTF-8');
}


// =========================
// CHECK IF TEACHER IS LOGGED IN
// =========================
function require_teacher_login()
{
    if (!isset($_SESSION['teacher_id'])) {
        header("Location: login.php");
        exit;
    }
}


// =========================
// CHECK IF ADMIN IS LOGGED IN
// =========================
function require_admin_login()
{
    if (!isset($_SESSION['admin_logged_in'])) {
        header("Location: admin_login.php");
        exit;
    }
}


// =========================
// SUCCESS MESSAGE
// =========================
function success_message($message)
{
    return '<div class="alert success">' .
           htmlspecialchars($message) .
           '</div>';
}


// =========================
// ERROR MESSAGE
// =========================
function error_message($message)
{
    return '<div class="alert error">' .
           htmlspecialchars($message) .
           '</div>';
}

#style.css
/* =========================================================
   COC TEACHER ROOM RESERVATION SYSTEM
   Main Design
========================================================= */

:root {
    --coc-green: #075c35;
    --coc-dark-green: #03452a;
    --coc-light-green: #eaf4e8;

    --coc-gold: #d6bd22;
    --coc-light-gold: #f7ed9c;

    --cream: #f8f8ef;
    --white: #ffffff;

    --text: #183027;
    --muted: #68756f;

    --border: #dfe6df;

    --success: #16834b;
    --danger: #c63c3c;
    --warning: #c59b00;

    --shadow-sm:
        0 4px 14px rgba(3, 69, 42, 0.08);

    --shadow-md:
        0 12px 35px rgba(3, 69, 42, 0.12);

    --radius: 16px;
}


/* =========================================================
   RESET
========================================================= */

* {
    box-sizing: border-box;
}

html {
    scroll-behavior: smooth;
}

body {
    margin: 0;

    font-family:
        "Segoe UI",
        Arial,
        sans-serif;

    color: var(--text);

    background:
        linear-gradient(
            135deg,
            #f8f8ef 0%,
            #f1f6ed 50%,
            #f9f9f2 100%
        );

    min-height: 100vh;
}


/* =========================================================
   LINKS
========================================================= */

a {
    color: var(--coc-green);

    text-decoration: none;

    transition:
        color 0.2s ease,
        opacity 0.2s ease;
}

a:hover {
    color: var(--coc-dark-green);
}


/* =========================================================
   NAVBAR
========================================================= */

.navbar {
    width: 100%;

    min-height: 76px;

    padding: 12px 6%;

    background:
        linear-gradient(
            135deg,
            var(--coc-dark-green),
            var(--coc-green)
        );

    color: white;

    display: flex;

    align-items: center;

    justify-content: space-between;

    gap: 25px;

    box-shadow:
        0 4px 18px rgba(3, 69, 42, 0.2);

    position: sticky;

    top: 0;

    z-index: 1000;
}


/* Navbar brand */

.navbar h2 {
    margin: 0;

    font-size: 20px;

    font-weight: 700;

    letter-spacing: 0.3px;
}


/* Navbar links */

.nav-links {
    display: flex;

    align-items: center;

    gap: 22px;

    flex-wrap: wrap;
}

.nav-links span {
    font-size: 14px;

    opacity: 0.9;
}

.nav-links a {
    color: white;

    font-weight: 600;

    font-size: 14px;

    padding: 8px 12px;

    border-radius: 8px;
}

.nav-links a:hover {
    background: rgba(255, 255, 255, 0.12);

    color: white;
}


/* =========================================================
   GENERAL PAGE
========================================================= */

.page {
    width: min(1400px, 92%);

    margin: 0 auto;

    padding: 45px 0 70px;
}

.page-title {
    text-align: center;

    color: var(--coc-dark-green);

    font-size: clamp(28px, 4vw, 42px);

    margin:
        10px 0 35px;

    font-weight: 750;
}


/* =========================================================
   CONTAINER / LOGIN CARDS
========================================================= */

.container {
    width: 100%;

    min-height: 100vh;

    padding: 40px 20px;

    display: flex;

    justify-content: center;

    align-items: center;

    background:
        radial-gradient(
            circle at top left,
            rgba(214, 189, 34, 0.16),
            transparent 32%
        ),
        linear-gradient(
            135deg,
            #f8f8ef,
            #edf5ea
        );
}

.card {
    width: 430px;

    max-width: 100%;

    background: rgba(255, 255, 255, 0.96);

    padding: 38px;

    border-radius: 22px;

    border:
        1px solid rgba(7, 92, 53, 0.08);

    box-shadow: var(--shadow-md);
}

.card h1 {
    margin:
        0 0 8px;

    text-align: center;

    color: var(--coc-dark-green);

    font-size: 30px;
}

.card h2 {
    color: var(--coc-dark-green);

    margin-top: 0;
}

.card p {
    color: var(--muted);

    line-height: 1.6;
}


/* =========================================================
   FORM
========================================================= */

label {
    display: block;

    margin:
        18px 0 7px;

    color: var(--text);

    font-weight: 650;

    font-size: 14px;
}

input,
textarea,
select {
    width: 100%;

    padding: 13px 15px;

    border:
        1px solid var(--border);

    border-radius: 10px;

    background: #ffffff;

    color: var(--text);

    font-size: 15px;

    font-family: inherit;

    outline: none;

    transition:
        border 0.2s ease,
        box-shadow 0.2s ease;
}

input:focus,
textarea:focus,
select:focus {
    border-color: var(--coc-green);

    box-shadow:
        0 0 0 3px
        rgba(7, 92, 53, 0.10);
}

textarea {
    resize: vertical;

    min-height: 90px;
}


/* =========================================================
   BUTTONS
========================================================= */

button,
.home-button {
    display: inline-flex;

    align-items: center;

    justify-content: center;

    gap: 8px;

    min-height: 46px;

    padding: 12px 20px;

    border: none;

    border-radius: 10px;

    background:
        linear-gradient(
            135deg,
            var(--coc-green),
            var(--coc-dark-green)
        );

    color: white;

    font-size: 15px;

    font-weight: 650;

    font-family: inherit;

    cursor: pointer;

    transition:
        transform 0.2s ease,
        box-shadow 0.2s ease,
        background 0.2s ease;
}

button {
    width: 100%;

    margin-top: 24px;
}

button:hover,
.home-button:hover {
    color: white;

    transform: translateY(-2px);

    box-shadow:
        0 8px 20px
        rgba(7, 92, 53, 0.22);
}


/* =========================================================
   ALERTS
========================================================= */

.alert {
    padding: 13px 16px;

    margin:
        0 0 20px;

    border-radius: 10px;

    font-size: 14px;

    font-weight: 600;

    border: 1px solid transparent;
}

.success {
    background: #e5f6ec;

    color: #096638;

    border-color: #bce4ca;
}

.error {
    background: #fdeaea;

    color: #a72f2f;

    border-color: #f1c3c3;
}


/* =========================================================
   SMALL TEXT
========================================================= */

.small-text {
    text-align: center;

    color: var(--muted);

    font-size: 14px;

    margin:
        0 0 25px;
}


/* =========================================================
   ROOM GRID
========================================================= */

.room-grid {
    display: grid;

    grid-template-columns:
        repeat(
            auto-fit,
            minmax(280px, 1fr)
        );

    gap: 22px;

    margin-top: 20px;
}

.room-card {
    background: rgba(255, 255, 255, 0.96);

    border:
        1px solid var(--border);

    border-radius: var(--radius);

    padding: 25px;

    box-shadow: var(--shadow-sm);

    position: relative;

    overflow: hidden;

    transition:
        transform 0.2s ease,
        box-shadow 0.2s ease;
}

.room-card::before {
    content: "";

    position: absolute;

    top: 0;
    left: 0;

    width: 100%;

    height: 4px;

    background:
        linear-gradient(
            90deg,
            var(--coc-green),
            var(--coc-gold)
        );
}

.room-card:hover {
    transform: translateY(-4px);

    box-shadow: var(--shadow-md);
}

.room-card h3 {
    margin:
        0 0 18px;

    color: var(--coc-dark-green);

    font-size: 22px;
}

.room-card p {
    margin:
        9px 0;

    color: var(--muted);

    line-height: 1.5;
}

.room-card strong {
    color: var(--text);
}


/* =========================================================
   STATUS
========================================================= */

.status {
    display: inline-flex;

    align-items: center;

    gap: 5px;

    padding: 7px 12px;

    border-radius: 999px;

    font-size: 13px;

    font-weight: 700;
}

.status-approved {
    background: #def5e7;

    color: #08713d;
}

.status-pending {
    background: #fff5c7;

    color: #8a6a00;
}

.status-rejected {
    background: #fde3e3;

    color: #a92e2e;
}

.status-cancelled {
    background: #edf0ef;

    color: #59645f;
}


/* =========================================================
   TABLE
========================================================= */

.table-container {
    width: 100%;

    overflow-x: auto;

    background: white;

    border-radius: 16px;

    box-shadow: var(--shadow-sm);

    border:
        1px solid var(--border);
}

table {
    width: 100%;

    min-width: 900px;

    border-collapse: collapse;
}

thead {
    background:
        linear-gradient(
            135deg,
            var(--coc-dark-green),
            var(--coc-green)
        );

    color: white;
}

th {
    padding: 16px;

    text-align: left;

    font-size: 14px;

    white-space: nowrap;
}

td {
    padding: 16px;

    border-bottom:
        1px solid #edf0ed;

    font-size: 14px;

    vertical-align: middle;
}

tbody tr {
    transition:
        background 0.15s ease;
}

tbody tr:hover {
    background: #f7faf5;
}

tbody tr:last-child td {
    border-bottom: none;
}


/* =========================================================
   HOME / LANDING PAGE
========================================================= */

.home-page {
    min-height: 100vh;

    background:
        radial-gradient(
            circle at 10% 20%,
            rgba(214, 189, 34, 0.15),
            transparent 25%
        ),
        linear-gradient(
            135deg,
            #f8f8ef,
            #eef5ea
        );
}

.home-navbar {
    min-height: 78px;

    padding:
        12px 6%;

    background:
        linear-gradient(
            135deg,
            #03452a,
            #075c35
        );

    display: flex;

    align-items: center;

    justify-content: space-between;

    box-shadow:
        0 4px 20px
        rgba(3, 69, 42, 0.18);
}

.brand {
    display: flex;

    align-items: center;

    gap: 13px;

    color: white;
}

.brand:hover {
    color: white;
}

.brand img {
    width: 54px;

    height: 54px;

    object-fit: contain;
}

.brand-text {
    display: flex;

    flex-direction: column;
}

.brand-title {
    font-size: 20px;

    font-weight: 750;

    letter-spacing: 0.3px;
}

.brand-subtitle {
    font-size: 11px;

    letter-spacing: 2px;

    opacity: 0.82;
}

.home-nav {
    display: flex;

    align-items: center;

    gap: 24px;
}

.home-nav a {
    color: white;

    font-size: 14px;

    font-weight: 600;

    padding:
        8px 2px;

    border-bottom:
        2px solid transparent;
}

.home-nav a:hover {
    color: var(--coc-light-gold);

    border-bottom-color:
        var(--coc-gold);
}


/* Hero */

.hero {
    width: min(1250px, 90%);

    margin: 0 auto;

    padding:
        80px 0 60px;

    display: grid;

    grid-template-columns:
        0.75fr 1.25fr;

    align-items: center;

    gap: 55px;
}

.hero-logo {
    display: flex;

    justify-content: center;

    align-items: center;
}

.hero-logo img {
    width: min(330px, 80%);

    filter:
        drop-shadow(
            0 15px 20px
            rgba(3, 69, 42, 0.12)
        );
}

.hero-content {
    position: relative;

    padding-left: 35px;

    border-left:
        2px solid var(--coc-gold);
}

.hero-content h1 {
    margin: 0;

    color: var(--coc-dark-green);

    font-family:
        Georgia,
        "Times New Roman",
        serif;

    font-size:
        clamp(38px, 5vw, 64px);

    line-height: 1.05;

    letter-spacing: -1px;
}

.hero-content h2 {
    margin:
        18px 0 22px;

    color: var(--coc-green);

    font-size:
        clamp(18px, 2vw, 25px);

    letter-spacing: 5px;

    font-weight: 600;
}

.hero-content p {
    max-width: 600px;

    color: var(--muted);

    font-family:
        Georgia,
        "Times New Roman",
        serif;

    font-size: 20px;

    line-height: 1.7;

    font-style: italic;
}


/* Home cards */

.home-options {
    width: min(1250px, 90%);

    margin: 15px auto 70px;

    display: grid;

    grid-template-columns:
        repeat(3, 1fr);

    gap: 25px;
}

.option-card {
    background:
        rgba(255, 255, 255, 0.96);

    border:
        1px solid #e1e6df;

    border-radius: 18px;

    padding: 35px 30px;

    text-align: center;

    box-shadow: var(--shadow-sm);

    position: relative;

    overflow: hidden;

    transition:
        transform 0.2s ease,
        box-shadow 0.2s ease;
}

.option-card::before {
    content: "";

    position: absolute;

    top: 0;
    left: 0;

    width: 70px;

    height: 70px;

    background:
        var(--coc-light-green);

    border-bottom-right-radius:
        70px;
}

.option-card::after {
    content: "";

    position: absolute;

    top: 0;
    right: 0;

    width: 75px;

    height: 75px;

    background:
        var(--coc-light-gold);

    border-bottom-left-radius:
        75px;
}

.option-card:hover {
    transform: translateY(-6px);

    box-shadow: var(--shadow-md);
}

.option-icon {
    width: 72px;

    height: 72px;

    margin:
        0 auto 22px;

    border-radius: 50%;

    background:
        var(--coc-light-gold);

    display: flex;

    align-items: center;

    justify-content: center;

    color: var(--coc-dark-green);

    font-size: 32px;

    position: relative;

    z-index: 2;
}

.option-card h3 {
    margin:
        0 0 12px;

    color: var(--coc-dark-green);

    font-family:
        Georgia,
        "Times New Roman",
        serif;

    font-size: 26px;

    position: relative;

    z-index: 2;
}

.option-card p {
    min-height: 55px;

    color: var(--muted);

    line-height: 1.6;

    position: relative;

    z-index: 2;
}

.option-card .home-button {
    width: 100%;

    margin-top: 20px;

    position: relative;

    z-index: 2;
}

.option-card .secondary-button {
    background: white;

    color: var(--coc-green);

    border:
        2px solid var(--coc-green);

    box-shadow: none;
}

.option-card .secondary-button:hover {
    background:
        var(--coc-light-green);

    color: var(--coc-dark-green);
}


/* =========================================================
   FOOTER
========================================================= */

.site-footer {
    min-height: 75px;

    padding:
        20px 6%;

    background:
        linear-gradient(
            135deg,
            var(--coc-dark-green),
            var(--coc-green)
        );

    color: white;

    display: flex;

    align-items: center;

    justify-content: space-between;

    gap: 20px;

    font-size: 14px;
}

.site-footer .motto {
    font-family:
        Georgia,
        "Times New Roman",
        serif;

    font-style: italic;

    color: #fff;

    font-size: 17px;
}


/* =========================================================
   ADMIN BUTTONS
========================================================= */

.action-container {
    display: flex;

    gap: 7px;

    align-items: center;

    flex-wrap: wrap;
}

.action-container form {
    margin: 0;
}

.action-container button {
    width: auto;

    min-height: 36px;

    margin: 0;

    padding: 8px 13px;

    font-size: 13px;
}

.approve-btn {
    background:
        linear-gradient(
            135deg,
            #16834b,
            #096638
        );
}

.reject-btn {
    background:
        linear-gradient(
            135deg,
            #d04a4a,
            #a92e2e
        );
}

.no-action {
    color: var(--muted);

    font-size: 13px;
}


/* =========================================================
   RESPONSIVE
========================================================= */

@media (max-width: 900px) {

    .hero {
        grid-template-columns: 1fr;

        text-align: center;

        padding-top: 50px;
    }

    .hero-content {
        padding:
            25px 0 0;

        border-left: none;

        border-top:
            2px solid var(--coc-gold);
    }

    .hero-content p {
        margin-left: auto;
        margin-right: auto;
    }

    .home-options {
        grid-template-columns: 1fr;
    }

    .home-navbar {
        flex-direction: column;

        padding: 15px 5%;
    }

    .home-nav {
        gap: 18px;
    }

    .navbar {
        flex-direction: column;

        padding:
            15px 5%;
    }

    .nav-links {
        justify-content: center;
    }
}


@media (max-width: 600px) {

    .page {
        width: 94%;

        padding-top: 30px;
    }

    .card {
        padding: 27px 22px;
    }

    .hero {
        width: 94%;
    }

    .hero-logo img {
        width: 230px;
    }

    .hero-content h1 {
        font-size: 38px;
    }

    .hero-content h2 {
        font-size: 16px;

        letter-spacing: 3px;
    }

    .hero-content p {
        font-size: 17px;
    }

    .home-options {
        width: 94%;
    }

    .site-footer {
        flex-direction: column;

        text-align: center;
    }
}

#database.sql

#test_connection.php
<?php

require_once "config.php";

echo "Database connection successful!";
