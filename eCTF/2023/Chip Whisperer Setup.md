link to jupyter notebook: localhost:8888

usr: vagrant
pswd: vagrant
jupyter: vagrant

#### Steps to get USB to work:
Wow, thanks! I had the same problem. Using `sudo virtualbox` I managed to get USB connected on MacOS Ventura 13.0 with VB 7.0.2.  
1. Start VB using `sudo virtualbox`  
2. Go to your guest OS settings->Ports->USB and select `USB 3.0 (xHCI)`  
3. Then just start your guest OS and USB devices should work. No need to use USB filters.  
  
I'm not sure how safe it is though. ![:D](https://forums.virtualbox.org/images/smilies/icon_biggrin.gif "Very Happy")

