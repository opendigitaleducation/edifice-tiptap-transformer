# Rich content transformer

- [Rich content transformer](#rich-content-transformer)
  - [In Docker](#in-docker)
  - [Without Docker](#without-docker)
    - [Installation](#installation)
    - [Customization](#customization)
    - [Build](#build)
    - [Local run](#local-run)
  - [Call samples](#call-samples)
  - [Load testing](#load-testing)
  - [Description](#description)
  - [Execution](#execution)
  - [Extension](#extension)



## In Docker


```shell
docker-compose up -d content-transformer --build
```

## Without Docker
### Installation

```shell
pnpm i
```

### Customization

Execute the following command :

```shell
cp .env.template .env
```

Then modify `.env` file to suit your needs.

### Build

```shell
pnpm run build
```

### Local run

```shell
pnpm run dev
```

## Call samples

To transform an HTML to JSON:

```shell
curl -L -X POST 'http://localhost:3000/transform' -H 'Content-Type: application/json' -d '{
    "requestedFormats": ["json"],
    "contentVersion": 0,
    "htmlContent": "<span>Le lorem ipsum est, en imprimerie, une suite de mots sans signification utilisée à titre provisoire pour calibrer une mise en page<br><h1>Hello world</h1><br><a href=\"https://www.website.com\">Website</a>"
}'
```

To transform an HTML to PLAIN TEXT:

```shell
curl -L -X POST 'http://localhost:3000/transform' -H 'Content-Type: application/json' -d '{
    "requestedFormats": ["plainText"],
    "contentVersion": 0,
    "htmlContent": "<span>Le lorem ipsum est, en imprimerie, une suite de mots sans signification utilisée à titre provisoire pour calibrer une mise en page<br><h1>Hello world</h1><br><a href=\"https://www.website.com\">Website</a>"
}'
```

To clean an HTML:

```shell
curl -L -X POST 'http://localhost:3000/transform' -H 'Content-Type: application/json' -d '{
    "requestedFormats": ["html"],
    "contentVersion": 0,
    "htmlContent": "<span>Le lorem ipsum est, en imprimerie, une suite de mots sans signification utilisée à titre provisoire pour calibrer une mise en page<br><h1>Hello world</h1><br><a href=\"https://www.website.com\">Website"
}'
```

To transform a JSON to HTML and PLAIN TEXT:

```shell
curl -L -X POST 'http://localhost:3000/transform' -H 'Content-Type: application/json' -d '{
    "requestedFormats": [
        "html",
        "plainText"
    ],
    "contentVersion": 0,
    "jsonContent": {
        "type": "doc",
        "content": [
            {
                "type": "paragraph",
                "attrs": {
                    "textAlign": "left"
                },
                "content": [
                    {
                        "type": "text",
                        "text": "Le lorem ipsum est, en imprimerie, une suite de mots sans signification utilisée à titre provisoire pour calibrer une mise en page"
                    }
                ]
            }
        ]
    }
}'
```

To access metrics:

```shell
curl -L -X GET 'http://localhost:3000/metrics'
```

## Load testing

## Description

Load testing are run by launching load-tester container. This container can be customized by changing its environment variables :
- `ROOT_URL`, the root URL of the content transformer to load test (shoul be kept to `http://content-transformer:3000` to test your local service)
- `DATA_ROOT_PATH`, should not be changed
- `DURATION`, the duration of the test
- `VUS`, the number of virtual users (see [official documentation](https://k6.io/docs/get-started/running-k6/) for more information)


## Execution

```shell
docker-compose up -d content-transformer --build # To start your container with the latest version of your local source
docker-compose run --rm load-tester run src/index.js # Starts the tests
```

## Extension

To add more files to the test set, add html files in `test/data/big` or `test/data/small` (depending on its size) and make sure to change the way `smallHtmls` and `bigHtmls` are loaded in [index.ts](./index.ts).