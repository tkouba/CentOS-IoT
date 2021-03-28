# How to install Node JS and NPM on the Raspberry Pi
> Original https://www.makersupplies.sg/blogs/tutorials/how-to-install-node-js-and-npm-on-the-raspberry-pi

> This is not required for [HARDWARIO Raspbian](pihub.md)

## Step 1: Update and upgrade OS
```
sudo apt update
sudo apt upgrade
```

## Step 2: Determine architecture of RPi

```
uname -m
```

For Raspberry Pi Zero is `armv6l`.

## Step 3: Download the NodeJS Binaries

Visit the [NodeJS downloads page](https://nodejs.org/en/download/) and copy the link to the version that you need. Latest release for `ARMv6` is v11.x. You can download it from https://nodejs.org/download/release/latest-v11.x/ 

```
wget https://nodejs.org/download/release/latest-v11.x/node-v11.15.0-linux-armv6l.tar.gz
```

## Step 4: Extract the file

```
tar -xzf node-v11.15.0-linux-armv6l.tar.gz
```

## Step 5: Copy the files to a directory in PATH


```
cd node-v11.15.0-linux-armv6l/
```
Remove README, CHANGELOG and LICENSE files
```
rm CHANGELOG.md LICENSE README.md
```
Copy to PATH
```
sudo cp -R * /usr/local/
```

## Step 6: Check if installation was successful

```
node -v
```
```
npm -v
```

## Step 7: Clean downloaded and extracted files

Go to back
```
cd ..
```
Remove downloaded file and extracted directory
```
rm node-v11.15.0-linux-armv6l.tar.gz
rm -R node-v11.15.0-linux-armv6l
```
