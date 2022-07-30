# The Feather Protocol

The Feather Protocol describes the standard HTTP API supported by all Feather runtimes.

## Version

This document describes the Feather Protocol, version `0.1`.

## The Protocol

### Code Execution

The feather protocol supports three ways to execute code.

The first approach is to send the code as a payload.

```
$ curl -d 'print("hello, world!")' https://feather-app/runtimes/python
hello, world!
```

The second approach is to send the file as multi-part formdata. This
allows sending one or more files for execution.

```
$ cat main.py
print("hello, world!")

$ curl -Fmain.py=@main.py https://feather-app/runtimes/python
hello, world!
```

The third approach is to send the payload as JSON.

```
$ cat payload.json
{
    "files": [
        {"name": "main.py", "contents": "print('hello, world!')"}
    ],
}
$ curl \
    -H 'Content-type: application/json' \
    -d @payload.json                    \
    https://feather-app/runtimes/python
hello, world!
```

### Request and Response Headers

The feather protocol supports special headers to specify command-line arguments,
environment variables and the entrypoint, the source file to execute.

The response includes headers to indicate the exit status and the time
taken to execute the request.

```
$ curl -i \
    -H 'x-webrepl-args: webrepl' \
    -H 'x-webrepl-env: X=1 DEBUG=true' \
    -H 'x-webrepl-entrypoint: hello.py' \
    -d hello.py=@hello.py
HTTP/1.1 200 OK
Content-type: text/plain
x-webrepl-exit-status: 0
x-webrepl-time-taken: 0.1

sys.argv: ['webrepl']
Hello, webrepl!
```