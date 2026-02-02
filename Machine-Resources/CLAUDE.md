# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Hack The Box Machine Scraper — a bash script that scrapes the HTB website to collect machine metadata (names, URLs, image links) and downloads machine avatar images for offline reference. No API keys or authentication required; it scrapes publicly available pages.

## Running the Script

```bash
./Resources
```

The script is a self-contained bash executable designed for incremental updates:

- **First run**: Fetches the HTB machines listing page, extracts all machine details, downloads all avatar images in batches of 20 with 10-second delays for rate limiting
- **Subsequent runs**: Only processes new machines or those with missing images, using the persistent cache to skip already-processed entries
- **Status output**: Reports how many existing machines were skipped vs. how many new ones need processing

## Dependencies

Standard Unix utilities only: `curl`, `wget`, `awk`, `grep` (with PCRE `-P` flag), `sed`. No package managers or virtual environments needed.

## Architecture

The project is a single bash script (`Resources`) with this pipeline:

1. **Cache loading**: Loads previously fetched machine names from `machine_name_cache.txt` into an associative array
2. **Page fetching**: `curl` fetches the HTB machines listing page
3. **HTML parsing**: `awk` extracts the machines HTML section (skipping "not-found" sections)
4. **Data extraction**: `grep -oP` extracts three parallel arrays: about-page URLs, app URLs (converted from www via `sed`), and image URLs from the AWS S3 CDN
5. **Incremental processing**: Identifies new machines by checking cache and existing images, only processes missing/new entries
6. **Name fetching**: For uncached machines only, fetches each about page and extracts the machine name from the `<h1>` tag, immediately persisting to cache
7. **Batch download**: `wget` downloads images to `htb_machine_images/` in parallel batches of 20 with 10-second delays
8. **Cleanup**: Removes temporary HTML debug files

## Key Output Files

- `htb_machines.txt` — pipe-delimited: `Machine Name | About URL | App URL | Image URL` (rebuilt on each run with all machines)
- `machine_name_cache.txt` — persistent cache mapping about-page URLs to machine names (format: `URL|Machine Name`)
- `htb_machine_images/` — PNG avatar images named `{machine_name}.png`

## Important Details

- **Incremental updates**: On subsequent runs, the script only processes new machines or those with missing images, making it significantly faster
- **Caching mechanism**: Machine names are cached after first fetch, eliminating redundant about-page requests
- **Early exit**: If all machines are cached and images exist, the script exits immediately without unnecessary processing
- **Parallel downloads**: Images are downloaded in parallel within each batch of 20 for faster execution
- **Image CDN base URL**: `https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/`
- **User-agent**: The script uses a Mozilla user-agent string for `curl` requests
- **Temporary files**: `debug_full_page.html`, `debug_machine_section.html`, and `machine_page.html` are created during execution; individual machine pages are removed immediately after name extraction, others are cleaned up at the end

## Performance Optimization

The script employs several optimizations for efficient execution:

- **First run**: Fetches all machine names and downloads all images (slower, full scrape)
- **Subsequent runs**: Only processes new machines added to HTB since last run (much faster)
- **Cache persistence**: `machine_name_cache.txt` persists between runs, eliminating redundant HTTP requests
- **Skip logic**: Machines with both cached names AND existing images are completely skipped
- **Batch reporting**: Shows count of existing vs. new machines at start: "Found X existing machines (skipped), Y new/missing machines to process"

To force a full refresh, delete `machine_name_cache.txt` and/or the `htb_machine_images/` directory.
