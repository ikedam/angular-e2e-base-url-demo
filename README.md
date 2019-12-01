# AngularE2eBaseUrlDemo

## Abstract

This is a demonstration to run e2e with selenium node in another host,
and a demonstration to explain `--base-url` is useful to use with `--dev-server-target`.

## How to use

1. Run e2e tests

    ```
    docker-compose -f docker-compose-e2e.yaml run --rm --use-aliases angular
    ```

2. It fails as the browser (the `chrome` container) tries to connect http://localhost:4200/ . It should be `http://angular:4200/` .

    ```
    docker-compose -f docker-compose-e2e.yaml logs chrome
    ```

    Output:

    ```
    ... (snip) ...
    chrome_1   | [1575173816.375][INFO]: Done waiting for pending navigations. Status: ok
    chrome_1   | [1575173816.376][INFO]: [d83c3381f24927ee12406e474ba24a50] RESPONSE Navigate
    chrome_1   | [1575173816.393][INFO]: [d83c3381f24927ee12406e474ba24a50] COMMAND ExecuteScript {
    chrome_1   |    "args": [  ],
    chrome_1   |    "script": "window.name = \"NG_DEFER_BOOTSTRAP!\" + window.name;window.location.replace(\"http://localhost:4200/\");"
    chrome_1   | }
    ... (snip) ...
    ```

3. clean up
    ```
    docker-compose -f docker-compose-e2e.yaml down
    ```

## What happens

* The `angular` container runs `npm scripts e2e`, which lanches an angular server (webpack dev server) and then starts protractor.
* Protractor connects to the `chrome` container with the webdriver protocol and handle it to connect to the angular server.
* Actually, it handles to connect to http://localhost:4200/.
    But it's the URL for the `angular` container, not the one for the `chrome` container.
    It should be `http://angular:4200/` to let the `chrome` container to connect to the angular server successfully.

## See also

* https://github.com/angular/angular-cli/issues/9242
* https://github.com/angular/angular-cli/issues/13611
* https://github.com/angular/angular-cli/pull/13614
