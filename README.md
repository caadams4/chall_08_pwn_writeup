# chall_08_pwn_writeup
overwrite the PLT pointer

This one threw my through a loop....but I got it!

Lets recon:
  checksec/file/imports/symbols: 
  
![image](https://user-images.githubusercontent.com/79220528/159551575-0fc0e318-3275-4546-87cd-6e42f4483b68.png)

Ok, No PIE. The addresses in the binary will remain constant. It's 64 bit dynamically linked. Only system calls are imported. 

We check symbols and find an uncalled win function:

  ![image](https://user-images.githubusercontent.com/79220528/159551961-a46d2815-4fc6-4850-b2bb-bb94137ad1b6.png)

Now let view the binary in radare2:

  ![image](https://user-images.githubusercontent.com/79220528/159552260-cdd27e29-d0f7-47b1-b562-4a8cec6dcfc4.png)

We see the program calls two scanfs and a puts. 
  The first scanf is a long double: 10 bytes 
  The second scanf is a double: 8 byts
  
  Ok, that might not be too important.
  
Later down the program we see some arithmatic, lets see. 

  Notice obj.target is loaded into rax and added to the firstscanf * 8. Oh, and the second scanf writes a value the address at *obj.target + 1stInput*  
  
  What if change the value at obj.target + 1stInput to point to the win function instead of puts.
  
So we know:

  -No PIE. Addresses are constant. elf.sym.got.puts is puts location in the GOT and elf.sym.got.puts - obj.target will give us the offset. divide that by 8 to get the first scanf input.
  
  -We want to write the win function at the puts location. Win function address == 4198950 
  
Here we go

![image](https://user-images.githubusercontent.com/79220528/159557636-24a2bb74-4947-4bd3-a319-5461ec6e07cc.png)

$$$$






  
  
  
  
  
