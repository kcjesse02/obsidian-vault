Start session
```shell
openocd -c 'tcl_port disabled' -c 'telnet_port disabled' -c 'gdb_port 3334' -f interface/ti-icdi.cfg -c 'hla_serial "0E236211"' -f board/ti_ek-tm4c123gxl.cfg
```
