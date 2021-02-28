# mdserve

`mdserve` is a minimalistic HTTP server used to render markdown files.

```bash
$ go install scm.lagraff.me/mdserve
$ mkdir /tmp/files
$ echo "# Hello World\nThis is my file" > /tmp/files/index.md
$ mdserve -dir /tmp/files
2020/11/27 17:20:16 Starting server (:8080)...
2020/11/27 17:20:45 method=GET host=localhost:8080 path=/index status=200 dur=109.484µs
2020/11/27 17:21:24 method=GET host=localhost:8080 path=/favicon.ico status=404 dur=5.281µs
``` 

It works with nested directories, so you can organize your files
however you'd like.

You can view all the options using `mdserve -h`.

## Usage
### Markdown Files
`mdserve` will serve markdown files from your current working directory if you do not specify
a directory via `-dir PATH`.

All markdown files **must** include the file extension `.md` on your filesystem, although the
server can handle requests which omit the extension (this can be disabled).

```bash
$ echo "# My markdown file" > my_first_file.md
$ echo "# You cannot see this" > my_other_file
$ mdserve &
$ curl localhost:8080/my_first_file     # return HTTP 200 w/ file
$ curl localhost:8080/my_first_file.md  # return HTTP 200 w/ file
$ curl localhost:8080/my_other_file     # return HTTP 404
```
    
### Media Files

Your markdown files can reference media files, like images, audio, and video.
By default, this behavior is **disabled**.
    
To serve media files, there are two settings:
- `-media` — local path to directory containing media files; or empty to disable.
- `-mediaPrefix` — the URL prefix required when requesting media files; or empty to disable.

If either option is empty, media files will not be served.

```bash
$ mkdir ~/images
$ mkdir ~/markdown
$ curl https://images.pexels.com/photos/3169299/pexels-photo-3169299.jpeg > ~/images/cool_photo.jpeg
$ echo "![alt text for cool photo](/static/acool_photo.jpeg)" > ~/markdown/pictures.md
$ mdserve -dir ~/markdown -media ~/images -mediaPrefix /static
```

In this example, the media file `cool_photo.jpeg` is stored in your local folder `~/images`, 
and is retrieved when the browser makes a request to `localhost:8080/static/cool_photo.jpeg`.

Files outside of your `-dir` or `-media` will NOT be served up by the system.

### Additional Options

- `-ext` — flag to require URLs to end with .md extension for markdown files.
- `-p \ -port` — integer for port for server to listen on.

## Help
You can use `-h / --help` at any time to view available options.

```bash
$ mdserve -h
Usage of mdserve:
  -dir string
        path to directory of markdown files (default ".")
  -ext
        require .md extension when routing requests
  -h    show help (shorthand)
  -help
        show help
  -media string
        local path to media files; or empty to disable (default ".")
  -mediaPrefix string
        url prefix to serve media files; or empty to disable (default "/static")
  -p int
        port to receive HTTP requests (shorthand) (default 8080)
  -port int
        port to receive HTTP requests (default 8080)
```
