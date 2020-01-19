# chuangmi
camera
@ECHO OFF
title Plugin CPU
adb wait-for-device
set package=com.xiaomi.smarthome
adb shell am force-stop %package%
timeout /t 2 > nul
if exist CPU (rd /s/q CPU&timeout /t 1 > nul&md CPU) else (md CPU)
adb shell am kill-all
timeout /t 1 > nul
adb shell settings put system screen_brightness 125
adb shell am start %package%/.SmartHomeMainActivity > nul
setlocal enabledelayedexpansion
cd CPU
:a
set m=0
for /f "tokens=9 delims= " %%a in ('adb shell "ps | grep -v core | grep -v acp | grep smarthome"') do (
	set /a m+=1
	if !m!==1 set x=%%a
	if !m!==2 set y=%%a)
if !m! lss 2 echo Plugin don't start! && goto a
cls && echo The plugin has been started && echo.
for /l %%a in (1 1 30) do ( if "!x:~%%a,1!"=="" set j=%%a&&goto :d )
:d
for /l %%a in (1 1 30) do (	if "!y:~%%a,1!"=="" set k=%%a&&goto :f )
:f
if !j! gtr !k! (set max=!x!) else (set max=!y!)
:qq
for /f "tokens=5" %%a in ('adb shell "top -n 1 | grep -v core | grep %max%"') do (
	set l=%%a
	echo CPU=!l!
	set vam=!l:~0,-1!
	echo !vam! >> CPU.txt)
goto qq
