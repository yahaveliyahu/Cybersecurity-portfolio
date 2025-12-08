# TryHackMe – Offensive Security Intro (Summary)

## 🎯 Objective of the Room
The goal of this TryHackMe room is to introduce basic Offensive Security concepts and demonstrate how attackers discover hidden website pages using automated tools such as `dirb`.

## 🔧 Tool Used: dirb
`dirb` is a web content scanner that performs a brute-force search on possible URLs using common wordlists.  
Its purpose is to identify hidden directories and pages that are not linked publicly but still accessible.

## 🛠 What I Did During the Exercise
1. Executed a basic directory brute-force scan using: dirb http://<TARGET>
2. Used the default wordlist (`common.txt`).
3. Reviewed the output to identify pages marked as “FOUND”.
4. Opened the discovered pages in a browser to analyze their content.
5. Interpreted the scan results to understand what type of information an attacker might extract.

## 📊 Key Learnings
- How directory brute-force tools work.
- How to read and understand `dirb` output (status codes, file paths, sizes).
- Why hidden or unlinked pages can introduce security risks.
- How attackers enumerate website structures during the reconnaissance phase.
- The importance of sanitizing and restricting access to sensitive endpoints.

## 🛡 Security Best Practices Learned
- Remove unused or outdated directories/pages.
- Disable directory listing on the server.
- Protect sensitive admin or debug endpoints with authentication.
- Avoid relying solely on obscurity — hidden URLs should still be secured.
- Regularly scan your own applications to identify accidental exposures.

## 📸 Screenshots
The `screenshots` folder contains:
- A terminal screenshot running the `dirb` command.
- Partial output of the scan showing discovered paths (no answers/flags).
- A browser screenshot of a discovered page (without revealing the room’s solution).




