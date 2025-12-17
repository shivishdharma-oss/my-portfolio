<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Shivish | Pro Portfolio</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">

    <style>
        :root { --bg: #f0f2f5; --card: white; --text: #333; --primary: #007bff; }
        
        @keyframes slideUp {
            from { opacity: 0; transform: translateY(30px); }
            to { opacity: 1; transform: translateY(0); }
        }

        body { font-family: 'Segoe UI', sans-serif; margin: 0; background-color: var(--bg); color: var(--text); transition: 0.3s; overflow-x: hidden; }
        header { background: #003366; color: white; padding: 60px 20px; text-align: center; }
        
        .container { 
            max-width: 800px; 
            margin: -40px auto 50px; 
            background: var(--card); 
            padding: 30px; 
            border-radius: 15px; 
            box-shadow: 0 10px 30px rgba(0,0,0,0.1); 
            text-align: center; 
            animation: slideUp 0.8s ease-out;
        }
        
        .profile-pic { border-radius: 50%; width: 120px; height: 120px; border: 5px solid white; margin-bottom: 15px; transition: 0.5s; }
        .profile-pic:hover { transform: rotate(10deg) scale(1.1); }

        .video-container { position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; border-radius: 10px; margin: 20px 0; }
        .video-container iframe { position: absolute; top: 0; left: 0; width: 100%; height: 100%; border: none; }

        .social-icons { margin: 25px 0; }
        .social-icons a { text-decoration: none; color: inherit; font-size: 1.8rem; margin: 0 15px; transition: 0.3s; display: inline-block; }
        .social-icons a:hover { color: var(--primary); transform: translateY(-5px); }

        .btn { display: inline-block; padding: 12px 30px; background: var(--primary); color: white; text-decoration: none; border-radius: 30px; transition: 0.3s; border: none; cursor: pointer; }
        .btn:active { transform: scale(0.95); }
        
        section { margin-top: 40px; padding-top: 20px; border-top: 1px solid #eee; }

        #toast {
            visibility: hidden;
            min-width: 250px;
            background-color: #333;
            color: #fff;
            text-align: center;
            border-radius: 8px;
            padding: 16px;
            position: fixed;
            z-index: 1001;
            left: 50%;
            bottom: 30px;
            transform: translateX(-50%);
        }
        #toast.show { visibility: visible; animation: slideUp 0.5s, slideUp 0.5s reverse 2.5s; }
    </style>
</head>
<body id="bodyTag">

    <button onclick="toggleDark()" style="position: fixed; top: 20px; right: 20px; padding: 10px 15px; cursor: pointer; z-index: 1000; border-radius: 50px; border: none; background: #333; color: white;">🌓 Toggle Mode</button>

    <header>
        <h1 id="greeting">Welcome to my Space</h1>
    </header>

    <main class="container">
        <img src="https://cdn-icons-png.flaticon.com/512/3135/3135715.png" alt="Shivish" class="profile-pic">
        <h2>Shivish</h2>
        <p>Building the future, one line of code at a time.</p>

        <nav class="social-icons">
            <a href="#"><i class="fab fa-instagram"></i></a>
            <a href="#"><i class="fab fa-youtube"></i></a>
            <a href="#"><i class="fab fa-x-twitter"></i></a>
            <a href="#"><i class="fab fa-linkedin"></i></a>
        </nav>

        <section>
            <h3>Featured Content</h3>
            <div class="video-container">
                <iframe src="https://www.youtube.com/embed/cWppAbqm9I8" allowfullscreen></iframe>
            </div>
        </section>

        <section id="contact">
            <h3>Send a Message</h3>
            <form onsubmit="handleSubmit(event)">
                <input type="text" id="userName" placeholder="Name" required style="width:100%; padding:10px; margin:5px 0; border-radius:5px; border:1px solid #ddd;">
                <button type="submit" class="btn" style="margin-top:10px;">Send Message</button>
            </form>
        </section>
    </main>

    <div id="toast">Message Sent Successfully! 🚀</div>

    <script>
        if (localStorage.getItem('theme') === 'dark') {
            applyDarkMode();
        }

        function toggleDark() {
            if (localStorage.getItem('theme') !== 'dark') {
                applyDarkMode();
                localStorage.setItem('theme', 'dark');
            } else {
                applyLightMode();
                localStorage.setItem('theme', 'light');
            }
        }

        function applyDarkMode() {
            document.body.style.backgroundColor = "#1e1e1e";
            document.body.style.color = "white";
            document.querySelector('.container').style.backgroundColor = "#2d2d2d";
        }

        function applyLightMode() {
            document.body.style.backgroundColor = "#f0f2f5";
            document.body.style.color = "#333";
            document.querySelector('.container').style.backgroundColor = "white";
        }

        function handleSubmit(event) {
            event.preventDefault();
            const name = document.getElementById('userName').value;
            const toast = document.getElementById('toast');
            
            toast.innerText = `Thanks for reaching out, ${name}!`;
            toast.className = "show";
            
            setTimeout(() => { toast.className = toast.className.replace("show", ""); }, 3000);
            
            event.target.reset();
        }

        const hour = new Date().getHours();
        const greet = hour < 12 ? "Morning" : hour < 18 ? "Afternoon" : "Evening";
        document.getElementById('greeting').innerText = `Good ${greet}, I'm Shivish`;
    </script>
</body>
</html>
