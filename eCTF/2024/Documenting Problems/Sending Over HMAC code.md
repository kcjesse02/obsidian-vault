Problem: component doesn't process packet from AP

### AP side
transmit buffer includes the message (128 bytes (or half the size of the buffer))
and SHA256 hash (64 bytes)

```
command code(1)
0_1_______________________________________________________ 256
| |                          |                 |         |
| |      message(128)        |     hash(64)    |  extra  |
|_|__________________________|_________________|_________|

(all values in bytes)
```

Here is the code that will do this
```c
int validate_components() {

	// Buffers for board link communication
	uint8_t receive_buffer[MAX_I2C_MESSAGE_LEN];
	uint8_t transmit_buffer[MAX_I2C_MESSAGE_LEN];

	MXC_TRNG_Init();

	unsigned char message [128];
	unsigned char hashed_message[128];

	Hmac hmac;

	/*
	1. get key from depl (build time) for AP and
	2. create random challenge message (AP) wc_HMACUpdate
	3. create package for sending (AP) wc_HmacFinal
	*/

	if(wc_HmacSetKey(&hmac, SHA256, secret_key, sizeof(secret_key)) != 0){
		print_error("error setting key");

	}

  

	// Send validate command to each component
	for (unsigned i = 0; i < flash_status.component_cnt; i++) {
		print_info("enter loop %d\n", i);
		// Set the I2C address of the component
		i2c_addr_t addr = component_id_to_i2c_addr(flash_status.component_ids[i]);
		
		// Create command message
		//defines the packet structure of the transmit buffer
		command_message* command = (command_message*) transmit_buffer;
		command->opcode = COMPONENT_CMD_VALIDATE;
	
		//generate the message
		//put the message in the message part of the transmit buffer
		MXC_TRNG_Random(command->message, 128);
		//update the hash to use the message
		if(wc_HmacUpdate(&hmac, command->message, 128) != 0){
			print_error("error setting message");
		}
		//print_info("%s", transmit_buffer);
		if(wc_HmacFinal(&hmac, command->hash) != 0){
			print_error("error hashing message");
		}
		//print_info("%s", command->hash);
		// Send out command and receive result
		int len = issue_cmd(addr, transmit_buffer, receive_buffer);
		MXC_Delay(10000);
		if (len == ERROR_RETURN) {
			print_error("Could not validate component\n");

			return ERROR_RETURN;
		}
		print_info("issued command\n");

		validate_message* validate = (validate_message*) receive_buffer;
		// Check that the result is correct
		if (validate->component_id != flash_status.component_ids[i]) {
			print_error("Component ID: 0x%08x invalid\n", flash_status.component_ids[i]);
			return ERROR_RETURN;
		}
		print_info("exit loop %d\n", i);
		MXC_Delay(500000); //software delay to allow for i2c to go high}

  

return SUCCESS_RETURN;

}
```
### Component side
doesn't seem to recieve command when I print out the command struct, its all zeroes

```
```