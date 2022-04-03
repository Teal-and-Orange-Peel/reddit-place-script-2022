# Reddit Place Script 2022 (R/Israel edition)

## About

 - This is a script to draw a PNG onto r/place (<https://www.reddit.com/r/place/>).
 - This version expands upon the work done by LordKnish and adds additional improvements to the handling of banned accounts by allowing the script to gracefully skip over them rather than experiencing performance problems or throwing fatal errors. This version also implements time fuzzing to reduce the chance of bans, as well as few other minor bug fixes. 
 - **Make sure to check #place-battle-plan for the current image, and replace unknown.png and new_image.png accordingly! It is critical that all operators use the exact same image. It must be scaled down to the correct size, and be exact pixel-for-pixel to prevent bot instances from overwriting each other.** 
 
### Upgrading from previous versions: 

 - For existing bot setups, you can update to this version by simply replacing main.py with the file on this repository. No other files were changed for this version, so simply replace main.py and relaunch the script! 
 
## Features

- Support for multiple accounts
- Determines the cooldown time remaining for each account
- Detects existing matching pixels on the r/place map and skips them
- Automatically converts colors to the r/place color palette

## Requirements

- [Python 3](https://www.python.org/downloads/)
- [A Reddit App Client ID and App Secret Key](https://www.reddit.com/prefs/apps)

## How to Get App Client ID and App Secret Key

You need to generate an app client id and app secret key for each account in order to use this script.

Steps:

1. Visit <https://www.reddit.com/prefs/apps>
2. Click "create (another) app" button at very bottom
3. Select the "script" option and fill in the fields with anything (you can use http://example.com for the URL fields, or any URL of your choosing) 

If you don't want to create a development app for each account, you can add each username as a developer in the developer app settings. You will need to duplicate the client ID and secret in .env, though.

## Python Package Requirements

Install requirements from 'requirements.txt' file.

```shell
pip3 install -r requirements.txt
```

## Get Started

Create a file called '.env' *(important: Make sure to use a code editor such as Geany (Mac OS) or Notepad++ (Windows) to create this file! Standard text editors and file browsers won't let you save this correctly.)*

Put in the following content:

```text
ENV_PLACE_USERNAME='["developer_username"]'
ENV_PLACE_PASSWORD='["developer_password"]'
ENV_PLACE_APP_CLIENT_ID='["app_client_id"]'
ENV_PLACE_SECRET_KEY='["app_secret_key"]'
ENV_DRAW_X_START="x_position_start_integer"
ENV_DRAW_Y_START="y_position_start_integer"
ENV_R_START='["0"]'
ENV_C_START='["0"]'
```

- ENV_PLACE_USERNAME is an array of usernames of developer accounts
- ENV_PLACE_PASSWORD is an array of the passwords of developer accounts
- ENV_PLACE_APP_CLIENT_ID is an array of the client ids for the app / script registered with Reddit
- ENV_PLACE_SECRET_KEY is an array of the secret keys for the app / script registered with Reddit
- ENV_DRAW_X_START specifies the x position to draw the image on the r/place canvas
- ENV_DRAW_Y_START specifies the y position to draw the image on the r/place canvas
- ENV_R_START is an array which specifies which x position of the original image to start at while drawing it
- ENV_C_START is an array which specifies which y position of the original image to start at while drawing it

### Notes: 
- Multiple fields can be passed into the arrays to spawn a thread for each one.
- Change unknown.png/untitled.jpg to specify what image to draw. One pixel is drawn every 5 minutes. (unknown.png is preferred. Don't provide both, the script will only use one!) 
- PNG has priority over JPG

## Run the Script

```python
python3 main.py
```

## Multiple Workers

If you want two threads drawing the image at once you could have a setup like this:

```text
ENV_PLACE_USERNAME='["developer_username_1", "developer_username_2"]'
ENV_PLACE_PASSWORD='["developer_password_1", "developer_password_2"]'
ENV_PLACE_APP_CLIENT_ID='["app_client_id_1", "app_client_id_2"]'
ENV_PLACE_SECRET_KEY='["app_secret_key_1", "app_secret_key_2"]'
ENV_DRAW_X_START="x_position_start_integer"
ENV_DRAW_Y_START="y_position_start_integer"
ENV_R_START='["0", "0"]'
ENV_C_START='["0", "50"]'
```

The same pattern can be used for multiple drawing at once. Note that the "ENV_PLACE_USERNAME", "ENV_PLACE_PASSWORD", "ENV_PLACE_APP_CLIENT_ID", "ENV_PLACE_SECRET_KEY", "ENV_R_START", and "ENV_C_START" variables MUST be string arrays of the same size.

Also note that I did the following in the above example:

```text
ENV_R_START='["0", "0"]'
ENV_C_START='["0", "50"]'
```

In this case, the first worker will start drawing from (0, 0) and the second worker will start drawing from (0, 50) from the input image.jpg file.

This is useful if you want different threads drawing different parts of the image with different accounts.

## Other Settings

```text
ENV_THREAD_DELAY='0'
ENV_UNVERIFIED_PLACE_FREQUENCY='True'
```

- ENV_THREAD_DELAY Adds a delay between starting a new thread. Can be used to avoid ratelimiting
- ENV_UNVERIFIED_PLACE_FREQUENCY is for setting the pixel place frequency to the unverified account frequency (20 minutes)

- Transparency can be achieved by using the RGB value (69, 42, 0) in any part of your image
- If you'd like, you can enable Verbose Mode by adding --verbose to "python main.py". This will output a lot more information, and not neccessarily in the right order, but it is useful for development and debugging.
## Developing
The nox CI job will run flake8 on the code. You can also do this locally by pip installing nox on your system and running 
`nox` in the repository directory.

## Known issues
- Mac OS users need to run their Python Installations "Install Certificates.command" before this script will connect. Nondescript errors may result otherwise. This can usually be found in /Applications/Python 3.10/Install Certificates.command

## FAQ

### How do I create .env? 

- Use a code editor (such as Notepad++ or Geany, or any code editor of your choice), and save the filename as .env exactly. It is a hidden file and won't be visible in your file browser afterwards (if you can still see it in your file browser, it wasn't created correctly). Do not use a standard text editor or word processor to create this, as it won't be saved in the correct format! 

### How do I run this? 

- Open a terminal on your computer, and navigate to the folder that this script is located on your computer. 
- Windows: `Dir` lists the files in the current folder, and `Dir FOLDERNAME` changes the terminal's current "working directory" to this new folder. 
- Mac OS/Linux: `ls` lists everything in the current directory. `cd FOLDERNAME` changes into the new directory. 

Once you've navigated to the correct folder on your terminal, run `python3 main.py`

### Can the script deal with JPG compression? 

 - Yes, to a limited extent. The script will try to find the closest matching pixel and use it. However, PNGs are lossless and much more accurate, and do not suffer from compression artifacts that may negatively impact the final image. PNG's are very strongly preferred. 

### Do coordinates and images have to be exact? 

- Yes, it must be pixel perfect. Coordinates that are even a single pixel off will cause bot instances to overwrite each other.

### What scale are the images drawn at? 

- Images are pixel-for-tile. 1 pixel = 1 tile. (Make sure to get images directly from #place-battle-plan! It's very important that all images are exactly the same, even slight differences between bot operators will cause the bots to overwrite each other). 
