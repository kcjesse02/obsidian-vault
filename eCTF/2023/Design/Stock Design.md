### unlock car
- receive unlock packet with secret
- the secret is not obfuscated 
- randomly generate a secret at the production: a random # of bytes of data
	- store secrets in EEPROM
- transmission is just plane text: encrypt
### Package
- encrypts Car ID and Feature ID using Host Secrets
- do not have the secret in the code
- store in EEPROM
- 