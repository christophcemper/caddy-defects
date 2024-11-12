#### 1. What version of Caddy are you running (`caddy -version`)?

now 2.8.4 with 

`xcaddy build --output bin/LRTcaddy --with github.com/christophcemper/caddy-netlify-redirects@v0.2.4 --with github.com/caddyserver/transform-encoder --with github.com/pteich/caddy-tlsconsul --with github.com/caddyserver/cache-handler`

after 10 month stable 2.7.6. with

`xcaddy build --output bin/LRTcaddy --with github.com/christophcemper/caddy-netlify-redirects@v0.2.4 --with github.com/caddyserver/transform-encoder --with github.com/pteich/caddy-tlsconsul`

that did not experience this very rare, unseen before crash

#### 2. What are you trying to do?

Multiple production hosts, reverse proxies, etc.

We added the module `github.com/caddyserver/cache-handler` for a project
and (unfortunately) also upgraded from 2.7.6 to 2.8.4. in one step.

Since then we had 2 occassions of a crash of Caddy on Oct 28 and Nov 10 
that caused a total outage and could only be remedied with a manual restart.



#### 3. What is your entire Caddyfile?

Sorry we cannot share it in public, happy to send directly to mholt or other key people.

#### 4. How did you run Caddy (give the full command and describe the execution environment)?

```
/usr/bin/caddy run --environ --config /etc/caddy/Caddyfile


```

via a systemd service

``` 
systemctl status caddy
● caddy.service - Caddy
     Loaded: loaded (/lib/systemd/system/caddy.service; enabled; vendor preset: enabled)
     Active: active (running) since Sun 2024-11-10 11:45:13 UTC; 7h ago
       Docs: https://caddyserver.com/docs/
    Process: 1929477 ExecReload=/usr/bin/caddy reload --config /etc/caddy/Caddyfile --force (code=exited, status=0/SUCCESS)
   Main PID: 1879405 (caddy)
      Tasks: 39 (limit: 154411)
     Memory: 2.2G
     CGroup: /system.slice/caddy.service
             └─1879405 /usr/bin/caddy run --environ --config /etc/caddy/Caddyfile
```

#### 5. What did you expect to see?

Stable caddy like all the time before.

#### 6. What did you see instead (give full error messages and/or log)?

## Oct 28 (intro for 1st goroutine - longer log attached)



```
Oct 28 15:05:08 web01 caddy[2621539]: fatal **error:** concurrent map read and map write

Oct 28 15:05:08 web01 caddy[2621539]: goroutine 3873976361 [running]:

Oct 28 15:05:08 web01 caddy[2621539]: github.com/caddyserver/caddy/v2/modules/caddyhttp.GetVar({0x1f9bd08?, 0xc015b73290?}, {0x1a68ef6, 0xe})

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/vars.go:323 +0x57

Oct 28 15:05:08 web01 caddy[2621539]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f3291d534b0, 0xc049806a80}, 0xc045f00480)

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:257 +0x1c6

Oct 28 15:05:08 web01 caddy[2621539]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f3291d534b0?, 0xc049806a80?}, 0xe?)

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Oct 28 15:05:08 web01 caddy[2621539]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f3291d534b0, 0xc049806a80}, 0xc045f00480)

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Oct 28 15:05:08 web01 caddy[2621539]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f3291d534b0?, 0xc049806a80?}, 0xe?)

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Oct 28 15:05:08 web01 caddy[2621539]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f3291d534b0, 0xc049806a80}, 0xc045f00480)

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Oct 28 15:05:08 web01 caddy[2621539]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f3291d534b0?, 0xc049806a80?}, 0xe?)

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Oct 28 15:05:08 web01 caddy[2621539]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f3291d534b0, 0xc049806a80}, 0xc045f00480)

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Oct 28 15:05:08 web01 caddy[2621539]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f3291d534b0?, 0xc049806a80?}, 0xe?)

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Oct 28 15:05:08 web01 caddy[2621539]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f3291d534b0, 0xc049806a80}, 0xc045f00480)

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Oct 28 15:05:08 web01 caddy[2621539]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f3291d534b0?, 0xc049806a80?}, 0xe?)

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Oct 28 15:05:08 web01 caddy[2621539]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f3291d534b0, 0xc049806a80}, 0xc045f00480)

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Oct 28 15:05:08 web01 caddy[2621539]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f3291d534b0?, 0xc049806a80?}, 0xe?)

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Oct 28 15:05:08 web01 caddy[2621539]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f3291d534b0, 0xc049806a80}, 0xc045f00480)

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Oct 28 15:05:08 web01 caddy[2621539]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f3291d534b0?, 0xc049806a80?}, 0xe?)

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Oct 28 15:05:08 web01 caddy[2621539]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f3291d534b0, 0xc049806a80}, 0xc045f00480)

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Oct 28 15:05:08 web01 caddy[2621539]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f3291d534b0?, 0xc049806a80?}, 0xe?)

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Oct 28 15:05:08 web01 caddy[2621539]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f3291d534b0, 0xc049806a80}, 0xc045f00480)

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Oct 28 15:05:08 web01 caddy[2621539]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f3291d534b0?, 0xc049806a80?}, 0xe?)

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Oct 28 15:05:08 web01 caddy[2621539]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f3291d534b0, 0xc049806a80}, 0xc045f00480)

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Oct 28 15:05:08 web01 caddy[2621539]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f3291d534b0?, 0xc049806a80?}, 0xe?)

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Oct 28 15:05:08 web01 caddy[2621539]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f3291d534b0, 0xc049806a80}, 0xc045f00480)

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Oct 28 15:05:08 web01 caddy[2621539]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f3291d534b0?, 0xc049806a80?}, 0xe?)

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Oct 28 15:05:08 web01 caddy[2621539]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f3291d534b0, 0xc049806a80}, 0xc045f00480)

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Oct 28 15:05:08 web01 caddy[2621539]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f3291d534b0?, 0xc049806a80?}, 0xe?)

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Oct 28 15:05:08 web01 caddy[2621539]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f3291d534b0, 0xc049806a80}, 0xc045f00480)

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Oct 28 15:05:08 web01 caddy[2621539]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f86720?, {0x7f3291d534b0?, 0xc049806a80?}, 0x2c2acc0?)

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Oct 28 15:05:08 web01 caddy[2621539]: github.com/caddyserver/caddy/v2/modules/caddyhttp.(*Server).ServeHTTP(0xc0029f4908, {0x1f98160, 0xc012dee000}, 0xc000c76480)

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/server.go:384 +0x111e

Oct 28 15:05:08 web01 caddy[2621539]: net/http.serverHandler.ServeHTTP({0xc0414a2f80?}, {0x1f98160?, 0xc012dee000?}, 0xc03c2f0ce8?)

Oct 28 15:05:08 web01 caddy[2621539]:         net/http/server.go:3142 +0x8e

Oct 28 15:05:08 web01 caddy[2621539]: net/http.initALPNRequest.ServeHTTP({{0x1f9bd08?, 0xc010a6b9e0?}, 0xc02ec19508?, {0xc0004b5770?}}, {0x1f98160, 0xc012dee000}, 0xc000c76480)

Oct 28 15:05:08 web01 caddy[2621539]:         net/http/server.go:3750 +0x231

Oct 28 15:05:08 web01 caddy[2621539]: golang.org/x/net/http2.(*serverConn).runHandler(0x448ebd?, 0x0?, 0x0?, 0xc0490e41c0?)

Oct 28 15:05:08 web01 caddy[2621539]:         golang.org/x/net@v0.29.0/http2/server.go:2414 +0x113

Oct 28 15:05:08 web01 caddy[2621539]: created by golang.org/x/net/http2.(*serverConn).scheduleHandler in goroutine 3874036131

Oct 28 15:05:08 web01 caddy[2621539]:         golang.org/x/net@v0.29.0/http2/server.go:2348 +0x21d

Oct 28 15:05:08 web01 caddy[2621539]: goroutine 1 [select (no cases), 18227 minutes]:

Oct 28 15:05:08 web01 caddy[2621539]: github.com/caddyserver/caddy/v2/cmd.cmdRun({0x0?})

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/caddyserver/caddy/v2@v2.8.4/cmd/commandfuncs.go:283 +0xbfc

Oct 28 15:05:08 web01 caddy[2621539]: github.com/caddyserver/caddy/v2/cmd.init.1.func2.WrapCommandFuncForCobra.1(0xc0005a8608, {0x1a4fb72?, 0x4?, 0x1a4fb3a?})

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/caddyserver/caddy/v2@v2.8.4/cmd/cobra.go:137 +0x2f

Oct 28 15:05:08 web01 caddy[2621539]: github.com/spf13/cobra.(*Command).execute(0xc0005a8608, {0xc00069a630, 0x3, 0x3})

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/spf13/cobra@v1.8.0/command.go:983 +0xaca

Oct 28 15:05:08 web01 caddy[2621539]: github.com/spf13/cobra.(*Command).ExecuteC(0x2c61ba0)

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/spf13/cobra@v1.8.0/command.go:1115 +0x3ff

Oct 28 15:05:08 web01 caddy[2621539]: github.com/spf13/cobra.(*Command).Execute(...)

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/spf13/cobra@v1.8.0/command.go:1039

Oct 28 15:05:08 web01 caddy[2621539]: github.com/caddyserver/caddy/v2/cmd.Main()

Oct 28 15:05:08 web01 caddy[2621539]:         github.com/caddyserver/caddy/v2@v2.8.4/cmd/main.go:75 +0x1d8

Oct 28 15:05:08 web01 caddy[2621539]: main.main()

Oct 28 15:05:08 web01 caddy[2621539]:         caddy/main.go:15 +0xf

Oct 28 15:05:08 web01 caddy[2621539]: goroutine 12 [select, 18227 minutes]:
```





## Nov 10 (Intro for 1st go routine, longer log attached)



```
Nov 10 11:29:32 web01 caddy[2314484]: fatal **error:** concurrent map read and map write

Nov 10 11:29:32 web01 caddy[2314484]: goroutine 3800443627 [running]:

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.GetVar({0x1f9bd08?, 0xc03724ac90?}, {0x1a68ef6, 0xe})

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/vars.go:323 +0x57

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f430fb2afd0, 0xc01424db80}, 0xc006df2b40)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:257 +0x1c6

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f430fb2afd0?, 0xc01424db80?}, 0xe?)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f430fb2afd0, 0xc01424db80}, 0xc006df2b40)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f430fb2afd0?, 0xc01424db80?}, 0xe?)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f430fb2afd0, 0xc01424db80}, 0xc006df2b40)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f430fb2afd0?, 0xc01424db80?}, 0xe?)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f430fb2afd0, 0xc01424db80}, 0xc006df2b40)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f430fb2afd0?, 0xc01424db80?}, 0xe?)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f430fb2afd0, 0xc01424db80}, 0xc006df2b40)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f430fb2afd0?, 0xc01424db80?}, 0xe?)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f430fb2afd0, 0xc01424db80}, 0xc006df2b40)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f430fb2afd0?, 0xc01424db80?}, 0xe?)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f430fb2afd0, 0xc01424db80}, 0xc006df2b40)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f430fb2afd0?, 0xc01424db80?}, 0xe?)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f430fb2afd0, 0xc01424db80}, 0xc006df2b40)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f430fb2afd0?, 0xc01424db80?}, 0xe?)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f430fb2afd0, 0xc01424db80}, 0xc006df2b40)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f430fb2afd0?, 0xc01424db80?}, 0xe?)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f430fb2afd0, 0xc01424db80}, 0xc006df2b40)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f430fb2afd0?, 0xc01424db80?}, 0xe?)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f430fb2afd0, 0xc01424db80}, 0xc006df2b40)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f430fb2afd0?, 0xc01424db80?}, 0xe?)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f430fb2afd0, 0xc01424db80}, 0xc006df2b40)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f430fb2afd0?, 0xc01424db80?}, 0xe?)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f430fb2afd0, 0xc01424db80}, 0xc006df2b40)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f430fb2afd0?, 0xc01424db80?}, 0xe?)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f430fb2afd0, 0xc01424db80}, 0xc006df2b40)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f430fb2afd0?, 0xc01424db80?}, 0xe?)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f430fb2afd0, 0xc01424db80}, 0xc006df2b40)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f430fb2afd0?, 0xc01424db80?}, 0xe?)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f430fb2afd0, 0xc01424db80}, 0xc006df2b40)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f430fb2afd0?, 0xc01424db80?}, 0xe?)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f430fb2afd0, 0xc01424db80}, 0xc006df2b40)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f430fb2afd0?, 0xc01424db80?}, 0xe?)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f430fb2afd0, 0xc01424db80}, 0xc006df2b40)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f430fb2afd0?, 0xc01424db80?}, 0xe?)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f430fb2afd0, 0xc01424db80}, 0xc006df2b40)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f430fb2afd0?, 0xc01424db80?}, 0xe?)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f430fb2afd0, 0xc01424db80}, 0xc006df2b40)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f430fb2afd0?, 0xc01424db80?}, 0xe?)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f430fb2afd0, 0xc01424db80}, 0xc006df2b40)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f430fb2afd0?, 0xc01424db80?}, 0xe?)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f430fb2afd0, 0xc01424db80}, 0xc006df2b40)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f430fb2afd0?, 0xc01424db80?}, 0xe?)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f430fb2afd0, 0xc01424db80}, 0xc006df2b40)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f430fb2afd0?, 0xc01424db80?}, 0xe?)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f430fb2afd0, 0xc01424db80}, 0xc006df2b40)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f9bd08?, {0x7f430fb2afd0?, 0xc01424db80?}, 0xe?)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.RouteList.Compile.wrapRoute.func1.1({0x7f430fb2afd0, 0xc01424db80}, 0xc006df2b40)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/routes.go:268 +0x244

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.HandlerFunc.ServeHTTP(0x1f86720?, {0x7f430fb2afd0?, 0xc01424db80?}, 0x2c2acc0?)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/caddyhttp.go:58 +0x29

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/modules/caddyhttp.(*Server).ServeHTTP(0xc0003cdb08, {0x1f98160, 0xc0046985b8}, 0xc0209e3c20)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/modules/caddyhttp/server.go:384 +0x111e

Nov 10 11:29:32 web01 caddy[2314484]: net/http.serverHandler.ServeHTTP({0x1a4fc00?}, {0x1f98160?, 0xc0046985b8?}, 0xc033cc79e0?)

Nov 10 11:29:32 web01 caddy[2314484]:         net/http/server.go:3142 +0x8e

Nov 10 11:29:32 web01 caddy[2314484]: net/http.initALPNRequest.ServeHTTP({{0x1f9bd08?, 0xc02b94c900?}, 0xc0211ef888?, {0xc01d9165a0?}}, {0x1f98160, 0xc0046985b8}, 0xc0209e3c20)

Nov 10 11:29:32 web01 caddy[2314484]:         net/http/server.go:3750 +0x231

Nov 10 11:29:32 web01 caddy[2314484]: golang.org/x/net/http2.(*serverConn).runHandler(0x448ebd?, 0x0?, 0x0?, 0xc00048f778?)

Nov 10 11:29:32 web01 caddy[2314484]:         golang.org/x/net@v0.29.0/http2/server.go:2414 +0x113

Nov 10 11:29:32 web01 caddy[2314484]: created by golang.org/x/net/http2.(*serverConn).scheduleHandler in goroutine 3800442958

Nov 10 11:29:32 web01 caddy[2314484]:         golang.org/x/net@v0.29.0/http2/server.go:2348 +0x21d

Nov 10 11:29:32 web01 caddy[2314484]: goroutine 1 [select (no cases), 18469 minutes]:

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/cmd.cmdRun({0x0?})

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/cmd/commandfuncs.go:283 +0xbfc

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/cmd.init.1.func2.WrapCommandFuncForCobra.1(0xc0005e2308, {0x1a4fb72?, 0x4?, 0x1a4fb3a?})

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/cmd/cobra.go:137 +0x2f

Nov 10 11:29:32 web01 caddy[2314484]: github.com/spf13/cobra.(*Command).execute(0xc0005e2308, {0xc000307d70, 0x3, 0x3})

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/spf13/cobra@v1.8.0/command.go:983 +0xaca

Nov 10 11:29:32 web01 caddy[2314484]: github.com/spf13/cobra.(*Command).ExecuteC(0x2c61ba0)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/spf13/cobra@v1.8.0/command.go:1115 +0x3ff

Nov 10 11:29:32 web01 caddy[2314484]: github.com/spf13/cobra.(*Command).Execute(...)

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/spf13/cobra@v1.8.0/command.go:1039

Nov 10 11:29:32 web01 caddy[2314484]: github.com/caddyserver/caddy/v2/cmd.Main()

Nov 10 11:29:32 web01 caddy[2314484]:         github.com/caddyserver/caddy/v2@v2.8.4/cmd/main.go:75 +0x1d8

Nov 10 11:29:32 web01 caddy[2314484]: main.main()

Nov 10 11:29:32 web01 caddy[2314484]:         caddy/main.go:15 +0xf
```





#### 7. How can someone who is starting from scratch reproduce this behavior as minimally as possible?

Unfortunately, we could not reproduce the issue yet in any way, nor do we know how to isolate this down to the host/domain that is causing this.

We have not yet tried to down-grade to 2.7.6. and see if that happens again with the cache module,
but then if it only occurs every 3 or so weeks randomly we're not even sure if that makes sense at this point.

We would kindly ask for help to enable/change to more helpful logging details for this than the raw unqualified go stack-trace

