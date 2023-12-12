# avaya-IP-Office-CCR-Reset
.BAT file to Stop CCR Services, Clear the System Logs of Errors and Restar the services
Echo off
::Created by James McLain
::Cablelink Solutons 
::20 Feb 2014
::With help from Avaya Tier 3 
::and Googleing for scripts form the web 
::Credit for copyed scripts is noted as they come up in the program 
:: the orginal order in the stoping and starting was from Avaya Tier 3 

Echo The CCR services will stop now.


Echo off

net stop "Avaya IPOCCR Alarm Keep Alive Service"
net stop "Avaya IPOCCR Data Analyser Service"
net stop "Avaya IPOCCR Printer Discovery Service"
net stop "Avaya IPOCCR Real Time Database Monitor"
net stop "Avaya IPOCCR Real Time Service"

Echo IIS will have a reset now.

iisreset.exe

Echo Clear the System Logs of CCR Errors 

@echo off

::Originally written by the user wesil on spiceworks.com
::Modified by Rectifier 06-30-2012
::This batch requires you to download the sysinternals (microsoft) utility psloglist
::Try http://technet.microsoft.com/en-us/sysinternals or google psloglist 
:: or https://learn.microsoft.com/en-us/sysinternals/downloads/psloglist
 
::Tested on a local machine using psloglist.exe version 2.71 on Windows 7 x64 Home Premium
::Use the ping command to wait from XP onward, timeout works for server 2k3
::The -g switch only works after the -c switch; this saves the log to file and clears it
 
::You can change the file extension from evt (older) to evtx (newer) and vice versa
 
::Change pstooldir to where psloglist.exe is located
set pstooldir=C:\Program Files (x86)\Avaya\IP Office\PSTools
cd /d %pstooldir%
::Error handling
if not exist psloglist.exe goto ERR0
if not exist "Event Logs" md "Event Logs"
if not exist "Event Logs" goto ERR1
::Change logdir to where you want to save the event logs
set logdir=C:\Program Files (x86)\Avaya\IP Office\Event Logs
::Template command for saving an event log:
::psloglist.exe -s -c -g "yourdir\filename %date:~4,2%-%date:~7,2%-%date:~10,4%.extension" "log name"
::Saved files appear as "filename MM-DD-YYYY.extension" without quotations
 
::Exports to file and then clears the following event logs
psloglist.exe -s -c -g "%logdir%\Security %date:~4,2%-%date:~7,2%-%date:~10,4%.evtx" Security
psloglist.exe -s -c -g "%logdir%\Application %date:~4,2%-%date:~7,2%-%date:~10,4%.evtx" Application
psloglist.exe -s -c -g "%logdir%\System %date:~4,2%-%date:~7,2%-%date:~10,4%.evtx" System
psloglist.exe -s -c -g "%logdir%\Internet Explorer %date:~4,2%-%date:~7,2%-%date:~10,4%.evtx" "Internet Explorer"
goto end
 
:ERR0
echo. & echo.
echo psloglist.exe not found !
