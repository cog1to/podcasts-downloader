## Podcast downloader script

Single-file no-dependency Python3 script to download podcasts. Intended to use
on Linux systems.

This is **WIP**, I'll be adding features as I need them. Feel free to create
feature requests and/or report bugs.

### Installation

`cp podcasts /usr/local/bin/podcasts` or wherever you want it to be.

### Setup

The script checks for possible config locations:
- The first command-line parameter, if provided.
- The directory from which the script is executed for `config` file.
- The `$XDG_CONFIG_HOME/podcasts/config` file.
- The `$HOME/.podcasts` file.

If none of them found, the script stops.

### Running

Just run `podcasts`:

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

### Configuration

The config file is JSON-formatted. Here's the example:
```json
{
  "max-episodes": 5,
  "feeds": [
    {
      "title": "The Haskell Cast",
      "path": "~/audio/podcasts/The Haskell Cast",
      "url": "http://www.haskellcast.com/feed.xml",
      "file-format": "{pub} - {title}.mp3"
    },
    {
      "title": "TechSNAP MP3",
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

`title`: User-friendly title

`path`: Path to hold downloaded episodes

`url`: Feed URL

`max-episodes`: Overrides the episode count to download for specific feed.

`file-format`: Overrides default naming rule for episodes. Known placeholders
are
- `pub` - publication date in `YYYY-MM-DD` format
- `title` - episode title
- `episode` - episode number (usually present in iTunes feeds)
- `season` - season number (usually present in iTunes feeds)
- `file` - filename extracted from the episode's URL

### License

GNU GPLv2
