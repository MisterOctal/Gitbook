---
icon: server
---

# How Websites Work

Date: 02.03.2026

Room Category: Theory / Web

Core Concept: The building blocks of the web (HTML, CSS, JS) and basic web vulnerabilities.

Continuing with the web fundamentals! As I push to finish my HND by October this year, getting extremely comfortable with how web applications are built is a top priority. When I start at University College Birmingham in 2027, having this baseline will give me a massive head start. From a SOC analyst perspective, understanding the difference between client-side (Front End) and server-side (Back End) is essential for figuring out where an attack is actually happening. Plus, knowing how HTML and JavaScript interact is exactly what I need to spot Cross-Site Scripting (XSS) or injection attacks on the job (and on my Security+ exam in November!).

***

## Task 1: How Websites Work

Every website interaction involves two main components. The "Front End" (Client-Side) is what your browser renders and what the user actually sees and interacts with. The "Back End" (Server-Side) is the dedicated computer sitting in a data center somewhere processing your requests, querying databases, and sending the data back.

<figure><img src="../.gitbook/assets/image (3) (1).png" alt="" width="563"><figcaption></figcaption></figure>

Question: What term refers to the way your browser renders a website?

> Answer: Front End

***

## Task 2: HTML

HTML (HyperText Markup Language) is the skeleton of every website. It defines the structure using "tags" like `<h1>` for headings, `<p>` for paragraphs, and `<img>` for images. In this task, we fix broken HTML code and ensure the dog image is render. Below is the HTML code used:

```html
<!DOCTYPE html>
<html>
    <head>
        <title>TryHackMe HTML Editor</title>
    </head>
    <body>
        <h1>Cat Website!</h1>
        <p>See images of all my cats!</p>
        <img src='img/cat-1.jpg'>
        <img src='img/cat-2.jpg'>
        <img src='img/dog-1.png'>
    </body>
</html>
```

Question: One of the images on the cat website is broken - fix it, and the image will reveal the hidden text answer!

> Answer: HTMLHERO

Question: Add a dog image to the page by adding another img tag (\<img>) on line 11. The dog image location is img/dog-1.png. What is the text in the dog image?

> Answer: DOGHTML

***

## Task 3: JavaScript

While HTML is the skeleton and CSS is the skin, JavaScript is the muscle. It controls the functionality and interactivity of the page. Without it, web pages would just be static text documents. Unfortunately, it's also what attackers use to execute client-side attacks, stealing session cookies or redirecting users to malicious sites.

In this task, we just have to render some HTML and JS to get the flag. Below is the code used:

```html
<!DOCTYPE html>
<html>
    <head>
        <title>TryHackMe Editor</title>
    </head>
    <body>
        <div id="demo">Hi there!</div>
        <script type="text/javascript">
             document.getElementById("demo").innerHTML = "Hack the Planet";
        </script>
    </body>
</html>
```

Question: Click the "View Site" button on this task. On the right-hand side, add JavaScript that changes the demo element's content to "Hack the Planet"

> Answer: JSISFUN

***

## Task 4: Sensitive Data Exposure

This task highlights a very common security flaw: developers leaving sensitive information in the source code! Because HTML and client-side JavaScript are rendered locally on our browser, anyone can right-click and "View Page Source". If a developer accidentally leaves a password, a hidden admin link, or a framework version number in an HTML comment ( `<!-- like this -->` ), attackers will easily find it during the reconnaissance phase.

After clicking on the provided link and viewing the page source, we see this code:

```html
<!DOCTYPE html>
<html>
<head>
    <title>How websites work</title>
    <link rel="stylesheet" href="css/style.css"></link>
</head>

<body>
    <div id='html-code-box'>
        <div id='html-bar'>
            <span id='html-url'>https://vulnerable-site.com</span>
        </div>
        <div class='theme' id='html-code'>
            <div class='logo-pos'><img src='img/logo_white.png'></div>
            <p id='login-msg'></p>
            <form method='post' id='form' autocomplete="off">
                <div class='form-field'>
                    <input class="input-text" type="text" name="username" placeholder="Username..">
                </div>
                <div class='form-field'>
                    <input class="input-text" type="password" name="password" placeholder="Password..">
                </div>
                <button onclick="login()" type='button' class='login'>Login</button>
                <!--
                    TODO: Remove test credentials!
                        Username: admin
                        Password: testpasswd
                -->
            </form>
            <div class="footer">Copyright Â© Vulnerable Website</div>
        </div>
    </div>
    <script src='js/script.js'></script>
<script defer src="https://static.cloudflareinsights.com/beacon.min.js/v67327c56f0bb4ef8b305cae61679db8f1769101564043" integrity="sha512-rdcWY47ByXd76cbCFzznIcEaCN71jqkWBBqlwhF1SY7KubdLKZiEGeP7AyieKZlGP9hbY/MhGrwXzJC/HulNyg==" data-cf-beacon='{"version":"2024.11.0","token":"8131a75048a9466ca61b8a59c3385e25","r":1,"server_timing":{"name":{"cfCacheStatus":true,"cfEdge":true,"cfExtPri":true,"cfL4":true,"cfOrigin":true,"cfSpeedBrain":true},"location_startswith":null}}' crossorigin="anonymous"></script>
</body>

</html>
```

Upon further inspection, we find out that the password is simply named 'testpasswd'!

Question: View the website on [this link](https://static-labs.tryhackme.cloud/sites/howwebsiteswork/html_data_exposure/). What is the password hidden in the source code?

> Answer: testpasswd

***

## Task 5: HTML Injection

If a website takes user input (like a name or a comment) and outputs it directly to the page without sanitizing it first, an attacker can inject their own HTML or JavaScript. For example, instead of entering my name as "Admin", I could enter `<h1>Admin</h1>`, and the website would render it as a giant header. This is the foundational concept behind XSS!

<figure><img src="../.gitbook/assets/image (4) (1).png" alt="" width="563"><figcaption></figcaption></figure>

In this task, we just have to insert this short HTML snippet into the username field:

```html
<a href="http://hacker.com">Link</a>
```

Question: Inject HTML into the page. What is the flag you receive from injecting HTML?

> Answer: HTML\_INJ3CTI0N

***

## Conclusion

That wraps up "How Websites Work"! It's a quick room, but seeing how HTML, CSS, and JS piece together to create the web is super important. The brief introductions to Sensitive Data Exposure and HTML Injection were great teasers for the actual web exploitation rooms I'll be tackling later on. One step closer to being ready for the Security+!
