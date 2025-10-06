CloudSEK CTF Challenge Writeup
Challenge 1 — Initial Recon: GHunt & OSINT
Objective:
Identify whether the target email is linked to Google, GitHub, or Maps activity.
Steps Taken:
1.	Used GHunt to investigate the target email:
2.	suryanandanmajumder@gmail.com
3.	Confirmed that the email is associated with a Google account.
4.	GHunt results revealed linked resources:
5.	Google Maps reviewer profile
6.	GitHub profile
Findings:
The Google Maps reviewer page and GitHub profile both contained artifacts.
The first flag was exposed in github commits .
Flag:
CloudSEK{Flag_1_w3lc0m3_70_7h3_c7f}
Challenge 2 — GitHub → Telegram Bot → Pastebin (Flag 2)
Objective:
Follow the trail from GitHub to discover hidden content.
Steps Taken:
1.	The GitHub repository referenced a Telegram bot.
2.	Repository instructions mentioned a “sentimental prompt” that had to be sent to the bot.
3.	Interacted with the Telegram bot using this special prompt.
4.	The bot responded with a Pastebin link.
5.	The Pastebin file contained a downloadable flag2.wav audio file.
6.	Analyzed the .wav file using Audacity, sox, and a Python script.
Extracted Morse code signals hidden in the audio.
Translated Morse code → plain text.
Findings:
GitHub → Telegram bot → Pastebin → Audio file → Morse → Text
Flag:
FLAG2!W3!H473!AI!B07S
Challenge 3 — Pastebin → APK / GraphQL → Flag Retrieval
Objective:
Exploit backend services to extract user credentials and the next flag.
Steps Taken:
The Pastebin link contained:
Assesment report of an APK file.
Hints regarding backend infrastructure.
Uploaded APK to Bevigil for static analysis.
Discovered Firebase configuration details:
firebase_api_key
firebase_app_id
firebase_database_url: https://strike-bank-1729.firebaseio.com
Found GraphQL endpoints:
/graphql/name/users
/graphql/flag
/graphql/notes
Probed the GraphQL endpoint:
http://15.206.47.5:9090/graphql
Used the generateToken to obtain a valid JWT token for a target user.
With the token, queried the protected endpoint:
{
userDetail(id: "R2W8K5Z") {
credentials {
username
password
}
flag
}
}
Findings:
The GraphQL query returned sensitive information:
Username
Password
Flag
Results:
username: r00tus3r
password: 
flag: CloudSEK{Flag_3_gr4phq1_!$_fun}

