# miniserve文件服务器

miniserve文件服务器相关技术笔记。

项目地址[GitHub - svenstaro/miniserve: 🌟 For when you really just want to serve some files over HTTP right now!](https://github.com/svenstaro/miniserve)

### docker部署
```bash
#从docker部署miniserver
docker run -id --name file_server -v /opt/ShareFile:/opt \
-p 80:8080 --restart always  docker.io/svenstaro/miniserve  /opt
#共享容器的/opt目录，并且将/opt挂载到宿主机的/opt/ShareFile

#带参数启动
docker run -id --name file_server -v /opt/ShareFile:/opt -p 80:8080 --restart always \
docker.io/svenstaro/miniserve -H -r -g -z -t 信职云计算FileServer -U -u / /opt
```

```text
For when you really just want to serve some files over HTTP right now!

Usage: miniserve [OPTIONS] [PATH]

Arguments:
  [PATH]  Which path to serve

Options:
  -v, --verbose                                Be verbose, includes emitting access logs
      --index <INDEX>                          The name of a directory index file to serve, like "index.html"
      --spa                                    Activate SPA (Single Page Application) mode
  -p, --port <PORT>                            Port to use [default: 8080]
  -i, --interfaces <INTERFACES>                Interface to listen on
  -a, --auth <AUTH>                            Set authentication. Currently supported formats: username:password, username:sha256:hash, username:sha512:hash (e.g. joe:123,
                                               joe:sha256:a665a45920422f9d417e4867efdc4fb8a04a1f3fff1fa07e998e86f7f7a27ae3)
      --route-prefix <ROUTE_PREFIX>            Use a specific route prefix
      --random-route                           Generate a random 6-hexdigit route
  -P, --no-symlinks                            Hide symlinks in listing and prevent them from being followed
  -H, --hidden                                 Show hidden files
  -c, --color-scheme <COLOR_SCHEME>            Default color scheme [default: squirrel] [possible values: squirrel, archlinux, zenburn, monokai]
  -d, --color-scheme-dark <COLOR_SCHEME_DARK>  Default color scheme [default: archlinux] [possible values: squirrel, archlinux, zenburn, monokai]
  -q, --qrcode                                 Enable QR code display
  -u, --upload-files [<ALLOWED_UPLOAD_DIR>]    Enable file uploading (and optionally specify for which directory)
  -U, --mkdir                                  Enable creating directories
  -m, --media-type <MEDIA_TYPE>                Specify uploadable media types [possible values: image, audio, video]
  -M, --raw-media-type <MEDIA_TYPE_RAW>        Directly specify the uploadable media type expression
  -o, --overwrite-files                        Enable overriding existing files during file upload
  -r, --enable-tar                             Enable uncompressed tar archive generation
  -g, --enable-tar-gz                          Enable gz-compressed tar archive generation
  -z, --enable-zip                             Enable zip archive generation
  -D, --dirs-first                             List directories first
  -t, --title <TITLE>                          Shown instead of host in page title and heading
      --header <HEADER>                        Set custom header for responses
  -l, --show-symlink-info                      Visualize symlinks in directory listing
  -F, --hide-version-footer                    Hide version footer
      --hide-theme-selector                    Hide theme selector
  -W, --show-wget-footer                       If enabled, display a wget command to recursively download the current directory
      --print-completions <shell>              Generate completion file for a shell [possible values: bash, elvish, fish, powershell, zsh]
      --print-manpage                          Generate man page
      --tls-cert <TLS_CERT>                    TLS certificate to use
      --tls-key <TLS_KEY>                      TLS private key to use
      --readme                                 Enable README.md rendering in directories
  -h, --help                                   Print help (see more with '--help')
  -V, --version                                Print version

```
