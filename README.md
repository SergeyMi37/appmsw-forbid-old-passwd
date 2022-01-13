![](https://github.com/SergeyMi37/appmsw-forbid-old-passwd/blob/master/doc/Screenshot_2rcc.png)
## forbid-old-passwd
[![Gitter](https://img.shields.io/badge/Available%20on-Intersystems%20Open%20Exchange-00b2a9.svg)](https://openexchange.intersystems.com/package/forbid-old-passwd)
[![DC](https://img.shields.io/badge/Available%20article%20on-Intersystems%20Community-orange)](https://community.intersystems.com/post/program-prohibit-use-old-passwords)

 [![Quality Gate Status](https://community.objectscriptquality.com/api/project_badges/measure?project=intersystems_iris_community%2Fappmsw-forbid-old-passwd&metric=alert_status)](https://community.objectscriptquality.com/dashboard?id=intersystems_iris_community%2Fappmsw-forbid-old-passwd)
 <img alt="GitHub last commit" src="https://img.shields.io/github/last-commit/SergeyMi37/appmsw-forbid-old-passwd">
 [![license](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)



To meet the requirements of section 8.2.5 PCI DSS "Prohibit the use of old passwords", a small application has been implemented that will be launched by the system when a user tries to change a password and check if it was used before.


## Installation with ZPM

If ZPM the current instance is not installed, then in one line you can install the latest version of ZPM.
```
zn "%SYS" d ##class(Security.SSLConfigs).Create("z") s r=##class(%Net.HttpRequest).%New(),r.Server="pm.community.intersystems.com",r.SSLConfiguration="z" d r.Get("/packages/zpm/latest/installer"),$system.OBJ.LoadStream(r.HttpResponse.Data,"c")
```
If ZPM is installed, then can be set with the command
```
zpm:USER>install appmsw-forbid-old-passwd
```
## Installation with Docker

## Prerequisites
Make sure you have [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) and [Docker desktop](https://www.docker.com/products/docker-desktop) installed.

## Installation 
Clone/git pull the repo into any local directory

```
git clone https://github.com/SergeyMi37/appmsw-forbid-old-passwd
```

Open the terminal in this directory and run:

```
docker-compose build
```

3. Run the IRIS container with your project:

```
docker-compose up -d
```

## How to Test it
Open IRIS terminal:

```
docker-compose exec iris iris session iris
...
%SYS>set ss=##class(Security.System).%OpenId("SYSTEM")
%SYS>set ss.PasswordValidationRoutine="CHECK^PASSWORD"
%SYS>write ss.%Save()
1
```
Or add a parameter through the interface:
`Password validation routine`
![](https://github.com/SergeyMi37/appmsw-forbid-old-passwd/blob/master/doc/Screenshot_1rcc.png)

## How to install without container and without zpm

Step 1: Be logged into github and download PASSWORD.mac
Step 2: Import and compile PASSWORD.mac into the %SYS namespace.
Step 3: Configure the instance to use PASSWORD routine
```
USER>zn "%SYS"
%SYS>set ss=##class(Security.System).%OpenId("SYSTEM")
%SYS>set ss.PasswordValidationRoutine="CHECK^PASSWORD"
%SYS>write ss.%Save()
1
```
Step 5: Confirm setting in Management Portal
System Administration > Security > System Wide Parameters > “Password validation routine” should say “CHECK^PASSWORD”
Step 6: Test by making a new password for a user. This logs the password into the secure log location.
 Then, try to change the password for this user again, but making the password the same as the first one.
 You should see the error "This password has already been used."