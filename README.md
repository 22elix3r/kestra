# Kestra issue 20771: local reproduction and verification

Issue: https://github.com/kestra-io/kestra/issues/20771

Reproduced with the unmodified `kestra/kestra:v2.0.5-no-plugins` image, `server local`, basic authentication enabled, and `micronaut.server.context-path: /kestra2`. A reverse proxy is not required to trigger the bug.

Before: login at `/kestra2/api/v1/login` returns 204, then flow search goes to `/api/v1/main/flows/search`, returns 404, and the UI returns to login. See `before-light.png` and `before-requests.json`.

After: the UI built from fix commit `aab54fa1ceb0fa7251d077c8a91257266da0b4ec` was bundled into the develop Docker image's executable JAR. The develop backend reports revision `988aba5`, matching the PR base. Only its UI resources were replaced; backend classes were copied unchanged. The same context path and basic authentication were used.

Chromium completed login and loaded the Flows page with the local `issue20771/context_path_repro` sample flow. All 27 captured API responses succeeded and every API URL included `/kestra2/api/`. Flow search returned 200. See `after-light.png`, `after-dark.png`, and `after-requests.json`.

The app-startup regression test fails before the fix because the request path is `/api/v1/main/flows/search` instead of `/kestra2/api/v1/main/flows/search`. It passes with the fix, together with the domain-root case.

Validation: 408 unit suites / 3,758 tests passed; `npm run check:types`, `npm run build`, `npm run lint`, and `npm run check:ts-any` passed. The final regression case was rerun using a relative `/kestra2` base URL and both tests passed.

These files are review artifacts on a separate branch; they are not part of the source PR.
