# TryHackMe – Offensive Security Intro (Summary)

## 🎯 Objective of the Room
The purpose of this TryHackMe room is to introduce fundamental Offensive Security
concepts and demonstrate how attackers enumerate web applications to discover
hidden or unlinked pages using automated tools such as `dirb`.

## 🔧 Tool Used: dirb
`dirb` is a directory enumeration tool that performs a brute-force search for
common directories and files using predefined wordlists.  
It is commonly used during the reconnaissance phase of a penetration test to
map exposed application surfaces.

## 🛠 What I Did During the Exercise
1. Executed a directory enumeration scan using `dirb` against a target web application.
2. Used the default wordlist (`common.txt`) to simulate a realistic attacker approach.
3. Reviewed the scan output, including HTTP status codes (e.g., 200, 301) and
   response sizes, to identify accessible and potentially sensitive endpoints.
4. Identified endpoints that suggested internal or administrative functionality.
5. Accessed one of the exposed pages in a browser to demonstrate the potential
   impact of insufficient access controls.

## 📊 Key Learnings
- How directory enumeration tools operate and what they reveal about web applications.
- How to interpret `dirb` output, including discovered paths and HTTP response codes.
- How unlinked or exposed endpoints can lead to serious security risks.
- The role of enumeration in the reconnaissance phase of an attack.
- How seemingly minor misconfigurations can expose critical functionality.

## 🛡 Security Best Practices Learned
- Remove unused or legacy directories and files from production environments.
- Protect administrative and internal endpoints with proper authentication and authorization.
- Do not rely on obscurity as a security mechanism.
- Regularly scan web applications for exposed paths.
- Apply the principle of least privilege to all sensitive resources.

## 📸 Screenshots
The `screenshots` folder contains:
- A terminal screenshot demonstrating the execution of the `dirb` enumeration tool.
- Partial scan output illustrating how discovered paths and HTTP responses appear
  (without revealing flags or challenge solutions).
- A browser screenshot of an exposed page to demonstrate potential security impact.
