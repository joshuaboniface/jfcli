# jfcli: A CLI administration tool for Jellyfin

`jfcli` is a Python 3 Click application designed to provide an administrative interface to Jellyfin.

In contrast to several other Jellyfin CLIs, `jfcli` does not enable or support playback, playback control, etc. It's sole function is to provide administrative tasks to Jellyfin via its API or, if required, direct interface with the Jellyin database.

`jfcli` is tested only on Linux; while it should work cross platform, this is not guaranteed by the author. Outside assistance is always apprecaited here.

## What does it do

`jfcli` seeks to provide three main functions:

1. Perform the actions normally performed by the "Jellyfin Setup Wizard" at instance creation, to allow this to happen programatically.

2. Manage server resources (users, libraries, etc.) and configuration.

3. Perform regular maintenance tasks outside of Jellyfin's in-built scheduled tasks (scan libraries, prune transcoding temporary data, etc.), to support e.g. cron-based operations.

## How does it work

Where at all possible, `jfcli` uses the Jellyfin API to communicate with a running Jellyfin instance. However it can also be used to programatically edit static files (configs, databases, etc.) for a non-running Jellyfin instance. It will try to autodetect which method is best for any given command at runtime.

`jfcli` can be configured in several ways. A set of environment variables, all prefixed with `JFCLI_` can be used to set the values, or a JSON configuration can be specified instead. The configuration can contain multiple servers and one is selected by providing the name in an environment variable or as a CLI flag.

`jfcli` is a [Click](https://click.palletsprojects.com/en/latest/) application, designed for CLI use across all platforms. It follows standard argument and option syntax and includes build-in documentation with the `-h`/`--help` flags.

## License

`jfcli` is licensed under the GNU GPL v3. It can thus be distributed with Jellyfin itself, or separately.

## Quick How-To

Running the setup wizard in non-interactive mode against a local system (note: setting up libraries in this stage is not supported); this will automatically create a `jfcli` API key for future use instead of a user/password:

```
$ JFCLI_URI="http://localhost:8096" jfcli wizard --display-language "EN_US" --admin-username "jellyfin" --admin-password "test123" --metadata-language "EN" --metdata-country "Canada" --allow-remote --disallow-upnp --listen-addr "127.0.0.1" --listen-port "8096"
```

Restarting a running server, with user/password:

```
$ JFCLI_URI="http://localhost:8096" JFCLI_USER="jellyfin" JFCLI_PASSWORD="test123" jfcli server restart
```

Adding a user, with API key (note: the libraries flags are redundant but here for illustration):

```
$ JFCLI_URI="http://localhost:8096" JFCLI_KEY="39d630e6-499b-43c8-8a8d-e1813d83122c" jfcli user add --username test --password test123 --enable-all-libraries --enable-library "Movies"
```

Adding a (Movie) library; exact syntax differs for different library content types:

```
$ JFCLI_URI="http://localhost:8096" JFCLI_KEY="39d630e6-499b-43c8-8a8d-e1813d83122c" jfcli library add --content-type "Movies" --display-name "Movies" --folder /srv/media/Movies --folder /srv/media/Movies2 --download-language "EN" --country "Canada" --prefer-embedded --monitor-realtime --downloader TheMovieDb --downloader OMDB --automatic-refresh 30 --save-metadata nfo --image-fetcher TheMovieDb --image-fetcher OMDB --image-fetcher ScreenGrabber --save-artwork --extract-chapter-images --extract-chapter-images-on-scan
```
