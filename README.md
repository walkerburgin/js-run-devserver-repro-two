# js-run-devserver-repro-two

Run `pnpm dev` (or `ibazel run //apps/foo-app:dev`) and grab the `js_run_devserver` sandbox location:

```console

  VITE v5.3.1  ready in 83 ms

  ➜  Local:   http://localhost:8080/
  ➜  Network: use --host to expose
  vite:resolve 0.27ms build/ts/App.js -> /private/var/folders/dt/89md32m53r31bbz6kn64qbl00000gp/T/js_run_devserver-FMhRHv6XAoVP/_main/apps/foo-app/build/ts/App.js +0ms
  vite:resolve 0.15ms ./SideEffect -> /private/var/folders/dt/89md32m53r31bbz6kn64qbl00000gp/T/js_run_devserver-FMhRHv6XAoVP/_main/apps/foo-app/build/ts/SideEffect.js +1ms
  vite:resolve 1.62ms /build/ts/App.js -> /private/var/folders/dt/89md32m53r31bbz6kn64qbl00000gp/T/js_run_devserver-FMhRHv6XAoVP/_main/apps/foo-app/build/ts/App.js +1s
  vite:resolve 0.14ms ./SideEffect -> /private/var/folders/dt/89md32m53r31bbz6kn64qbl00000gp/T/js_run_devserver-FMhRHv6XAoVP/_main/apps/foo-app/build/ts/SideEffect.js +6ms
  vite:resolve 0.48ms /@vite/client -> /private/var/tmp/_bazel_wburgin/e3d4cc4864541609b701cdbf4cfaaeed/execroot/_main/bazel-out/darwin_arm64-fastbuild/bin/node_modules/.aspect_rules_js/vite@5.3.1/node_modules/vite/dist/client/client.mjs +6ms
  vite:resolve 0.10ms @vite/env -> /private/var/tmp/_bazel_wburgin/e3d4cc4864541609b701cdbf4cfaaeed/execroot/_main/bazel-out/darwin_arm64-fastbuild/bin/node_modules/.aspect_rules_js/vite@5.3.1/node_modules/vite/dist/client/env.mjs +4ms
```

Inspect `/private/var/folders/dt/89md32m53r31bbz6kn64qbl00000gp/T/js_run_devserver-FMhRHv6XAoVP/_main/apps/foo-app/build/ts/`:

```console
➜  ls -l /private/var/folders/dt/89md32m53r31bbz6kn64qbl00000gp/T/js_run_devserver-FMhRHv6XAoVP/_main/apps/foo-app/build/ts/
total 32
-r-xr-xr-x  1 wburgin  staff   89 Jul 24 15:58 App.js
-r-xr-xr-x  1 wburgin  staff  164 Jul 24 15:58 App.js.map
-r-xr-xr-x  1 wburgin  staff   94 Jul 24 15:58 SideEffect.js
-r-xr-xr-x  1 wburgin  staff  158 Jul 24 15:58 SideEffect.js.map
```

Delete `SideEffect.tsx` and observe that ibazel rebuilds, but `SideEffects.js` is not mentioned in the output:

```
iBazel [4:02PM]: IBAZEL BUILD SUCCESS
IBAZEL_BUILD_COMPLETED SUCCESS
Syncing 11 files && folders...
Syncing 11 other files && folders...
Skipping file apps/foo-app/node_modules/vite since its timestamp has not changed
Skipping file node_modules/ts-bazel-plugin since its timestamp has not changed
Skipping file node_modules/typescript since its timestamp has not changed
Skipping file node_modules/vite since its timestamp has not changed
Skipping file node_modules/@bazel/ibazel since its timestamp has not changed
Skipping file apps/foo-app/vite.config.mjs since its timestamp has not changed
Skipping file apps/foo-app/package.json since its timestamp has not changed
Skipping file apps/foo-app/index.html since its timestamp has not changed
Skipping file apps/foo-app/src/App.tsx since its timestamp has not changed
Skipping file apps/foo-app/build/ts/App.js since contents have not changed
Skipping file apps/foo-app/build/ts/App.js.map since contents have not changed
0 file synced in 1 ms
```

Re-inspect `/private/var/folders/dt/89md32m53r31bbz6kn64qbl00000gp/T/js_run_devserver-FMhRHv6XAoVP/_main/apps/foo-app/build/ts/`:

```console
➜  ls -l /private/var/folders/dt/89md32m53r31bbz6kn64qbl00000gp/T/js_run_devserver-FMhRHv6XAoVP/_main/apps/foo-app/build/ts/
total 32
-r-xr-xr-x  1 wburgin  staff   89 Jul 24 15:58 App.js
-r-xr-xr-x  1 wburgin  staff  164 Jul 24 15:58 App.js.map
-r-xr-xr-x  1 wburgin  staff   94 Jul 24 15:58 SideEffect.js
-r-xr-xr-x  1 wburgin  staff  158 Jul 24 15:58 SideEffect.js.map
```

Note that `SideEffects.js` is still present in the sandbox, and therefore still visible to Vite!
