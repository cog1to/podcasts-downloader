## Podcast downloader script

Single-file no-dependency Python3 script to download podcasts. Intended to use
on Linux systems.

This is **WIP**, I'll be adding features as I need them. Feel free to create
feature requests and/or report bugs.

### Installation

`cp podcasts /usr/local/bin/podcasts` or wherever you want it to be.

### Setup

The script checks for possible config locations:
- The last command-line parameter, if provided.
- The directory from which the script is executed for `config` file.
- The `$XDG_CONFIG_HOME/podcasts/config` file.
- The `$HOME/.podcasts` file.

If none of them found, the script stops.

### Configuration

The config file is JSON-formatted. Here's the example:
```json
{
  "max-episodes": 5,
  "feeds": [
    {
      "title": "The Haskell Cast",
      "alias": "thc",
      "path": "~/audio/podcasts/The Haskell Cast",
      "url": "http://www.haskellcast.com/feed.xml",
      "file-format": "{pub} - {title}.mp3",
      "auto": false,
    },
    {
      "title": "TechSNAP MP3",
      "alias": "ts",
      "path": "~/audio/podcasts/TechSNAP MP3",
      "url": "http://feeds.feedburner.com/techsnapmp3",
      "max-episodes": 3
    }
  ]
}
```

#### Global configuration

`max-episodes`: How many episodes from the top of the feed to download.
Can be overriden by the feed config.

#### Feed configuration

`title`: Podcast's title

`alias`: Short handle for commands

`path`: Path to hold downloaded episodes

`url`: Feed URL. To find a podcast's feed URL you can use the search function
of the script, described below.

`max-episodes`: Overrides the episode count to download for specific feed.

`file-format`: Overrides default naming rule for episodes. Known placeholders
are
- `pub` - publication date in `YYYY-MM-DD` format
- `title` - episode title
- `episode` - episode number (usually present in iTunes feeds)
- `season` - season number (usually present in iTunes feeds)
- `file` - filename extracted from the episode's URL

`auto`: Use to explicitly disable/enable auto-download when running in batch
mode (without arguments). If set to `false`, the podcast will be skipped. You
can still force update it by rinning `podcasts <name-of-the-podcast>`.

### Running

The script has three run modes: list, download and search.

#### Listing

Running `podcasts list` will print the list of podcasts parsed from the config.

```bash
>>> podcasts list
hc - The Haskell Cast - http://www.haskellcast.com/feed.xml
otm - On The Metal - https://feeds.transistor.fm/on-the-metal-0294649e-ec23-4eab-975a-9eb13fd94e06
```

#### Searching for podcasts

Run `podcasts search <your-search-term>`. It will return a list of podcast
names with their feed URLs.

```bash
>>> podcasts search "Nextlander"
The Nextlander Podcast
  https://audioboom.com/channels/5116059.rss
The Nextlander Watchcast
  https://audioboom.com/channels/5117047.rss
```

Search is performed via iTunes Search API.

#### Updating and downloading episodes

Run `podcasts` or `podcasts <title>`. Running without arguments will update and
download all feeds that are not marked with `"auto": false` in the config.
Running with a feed alias as an argument will force update that specific feed,
ignoring `auto` config.

```bash
>>> podcasts
Fetching feed 'The Haskell Cast'... Done
  Episode 'Episode 14 - Richard Eisenberg on Dependent Types ...' exists, skipping
  Downloading 'Episode 13 - John Wiegley on Categories and Compil...'... Done
  Episode 'Episode 12 - Neil Mitchell on Development Tools' exists, skipping
Fetching feed 'TechSNAP MP3'... Done
  Downloading '430: All Good Things'... Done
  Downloading '429: Curious About Caddy'... Done
  Downloading '428: RAID Reality Check'... Done
```

The script downloads RSS feed into each feed's respective directory, and then
tries to download N last episodes for each feed, as specified in the config
file. The default file naming rule is `YYYY-MM-DD - Episode Title.mp3`.

The scripts tries to check the file size reported by the feed with the size
of the file on disk. If size is not specified or different, the episode is
downloaded.

### License

GNU GPLv2
