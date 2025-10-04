<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>YouTube Neon Background</title>
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }

    body {
      height: 100vh;
      display: flex;
      align-items: center;
      justify-content: center;
      flex-direction: column;
      overflow: hidden;
      background: radial-gradient(circle at center, #000000, #120000, #240000);
      font-family: 'Poppins', sans-serif;
      position: relative;
    }

    /* Neon Sparks Animation */
    .spark {
      position: absolute;
      width: 4px;
      height: 4px;
      background: red;
      border-radius: 50%;
      opacity: 0.8;
      animation: fly 5s linear infinite;
    }

    @keyframes fly {
      0% {
        transform: translateY(100vh) translateX(0);
        opacity: 0;
      }
      50% {
        opacity: 1;
      }
      100% {
        transform: translateY(-10vh) translateX(calc(50vw - 200px));
        opacity: 0;
      }
    }

    /* YouTube Logo */
    .logo {
      width: 200px;
      height: 200px;
      border-radius: 50%;
      box-shadow: 0 0 40px rgba(255, 0, 0, 0.9),
                  0 0 100px rgba(255, 0, 0, 0.6);
      transition: transform 0.4s ease, box-shadow 0.4s ease;
      z-index: 2;
    }

    .logo:hover {
      transform: scale(1.1);
      box-shadow: 0 0 80px rgba(255, 0, 0, 1),
                  0 0 160px rgba(255, 0, 0, 0.8);
    }

    h1 {
      margin-top: 25px;
      color: white;
      font-size: 1.5rem;
      text-shadow: 0 0 15px rgba(255, 0, 0, 0.8);
      z-index: 2;
    }
  </style>
</head>
<body>
  <!-- Neon Sparks -->
  <script>
    const count = 60; // Number of sparks
    for (let i = 0; i < count; i++) {
      const spark = document.createElement("div");
      spark.className = "spark";
      spark.style.left = Math.random() * 100 + "vw";
      spark.style.animationDuration = 3 + Math.random() * 3 + "s";
      spark.style.animationDelay = Math.random() * 5 + "s";
      spark.style.background = Math.random() > 0.5 ? "red" : "#ff3b3b";
      document.body.appendChild(spark);
    }
  </script>

  <!-- YouTube Logo -->
  <img 
    src="https://yt3.googleusercontent.com/HiWYvJKbI0RB9wQWI2YbXmJmXGOYQBBF4bM4x7jy30Sfjty0Feh4buQakdjxR0o3SMhkCcq1rQ=s160-c-k-c0x00ffffff-no-rj"
    alt="YouTube Logo"
    class="logo"
  >
  <h1>Right-click → Save Logo</h1>
</body>
</html>
