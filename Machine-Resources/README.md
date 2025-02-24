# Hack The Box Machine Scraper

## Overview
This script automates the process of scraping machine information from the Hack The Box website. It extracts machine names, about page links, machine URLs, and machine images, and then downloads the images while ensuring that only missing images are fetched.

## Features
- Extracts machine names, about links, machine URLs, and images from Hack The Box.
- Downloads machine images in batches of 20 to avoid rate-limiting.
- Skips downloading images that already exist.
- Stores extracted data in `htb_machines.txt`.
- Saves machine images in the `htb_machine_images/` directory.
- Cleans up temporary files after execution.

## Prerequisites
Ensure you have the following dependencies installed on your system:
- `curl`
- `wget`
- `awk`
- `grep`
- `sed`

These utilities are available by default in most Linux distributions.

## Installation
Clone this repository and navigate to the script directory:
```sh
git clone https://github.com/0xDTC/0xHackTheBox.git
cd 0xHackTheBox/Machine-Resources
```

Make the script executable:
```sh
chmod +x resources
```

## Usage
Run the script:
```sh
./resources
```
The script will:
1. Fetch the Hack The Box machine page.
2. Extract machine details and save them in `htb_machines.txt`.
3. Download machine images while skipping existing ones.

## Output
- `htb_machines.txt` : Contains extracted machine details in the following format:
  ```
  Machine Name | Machine About URL | Machine URL | Machine Image URL
  ------------|-------------------|------------|-------------------
  Lame    | https://www.hackthebox.com/machines/Lame | https://app.hackthebox.com/machines/Lame | https://labs.hackthebox.com/storage/avatars/000000xxxxx.png
  ```
- `htb_machine_images/` : Directory containing machine images named after the respective machine.

## Notes
- If the script encounters rate-limiting issues, consider increasing the sleep duration between downloads.
- The script is designed for educational purposes and respects Hack The Box's terms of service.

## Contributions
Pull requests and improvements are welcome! Feel free to fork and modify the script to suit your needs.