[![GitHub release (latest SemVer)](https://img.shields.io/github/v/release/thoughtworks/build-your-own-radar?sort=semver)](https://github.com/thoughtworks/build-your-own-radar/releases/latest)
[![Thoughtworks](https://circleci.com/gh/thoughtworks/build-your-own-radar.svg?style=shield)](https://circleci.com/gh/thoughtworks/build-your-own-radar)
[![Stars](https://badgen.net/github/stars/thoughtworks/build-your-own-radar)](https://github.com/thoughtworks/build-your-own-radar)
[![Docker Hub Pulls](https://img.shields.io/docker/pulls/wwwthoughtworks/build-your-own-radar.svg)](https://hub.docker.com/r/wwwthoughtworks/build-your-own-radar)
[![GitHub contributors](https://badgen.net/github/contributors/thoughtworks/build-your-own-radar?color=cyan)](https://github.com/thoughtworks/build-your-own-radar/graphs/contributors)
[![Prettier-Standard Style Guide](https://img.shields.io/badge/code_style-standard-brightgreen.svg)](https://github.com/sheerun/prettier-standard)
[![AGPL License](https://badgen.net/github/license/thoughtworks/build-your-own-radar)](https://github.com/thoughtworks/build-your-own-radar)

A library that generates an interactive radar, inspired by [thoughtworks.com/radar](http://thoughtworks.com/radar).

## Run in Docker for development 
```
$ sudo docker pull wwwthoughtworks/build-your-own-radar:v1.1.4
$ sudo docker run --rm -p 8080:80 -e SERVER_NAMES="localhost 127.0.0.1" -v $PROJECT_FOLDER/:/opt/build-your-own-radar/files --name radar wwwthoughtworks/build-your-own-radar:v1.1.4
```
Provided your radar file is called `Datalan_TR_2024_01.csv` your path to the radar file will be `http://localhost:8080/files/Datalan_TR_2024_01.csv` and you can access the radar directly via `http://localhost:8080/?documentId=http%3A%2F%2Flocalhost%3A8080%2Ffiles%2FDatalan_TR_2024_01.csv`.


## Demo

You can see this in action at https://radar.thoughtworks.com. If you plug in [this data](https://docs.google.com/spreadsheets/d/1GBX3-jzlGkiKpYHF9RvVtu6GxSrco5OYTBv9YsOTXVg/edit#gid=0) you'll see [this visualization](https://radar.thoughtworks.com/?sheetId=https%3A%2F%2Fdocs.google.com%2Fspreadsheets%2Fd%2F1GBX3-jzlGkiKpYHF9RvVtu6GxSrco5OYTBv9YsOTXVg%2Fedit%23gid%3D0).

## How To Use

The easiest way to use the app out of the box is to provide a _public_ Google Sheet ID from which all the data will be fetched. You can enter that ID into the input field and your radar will be generated once you click the submit button. The data must conform to the format below for the radar to be generated correctly.

### Setting up your data

You need to make your data public in a form we can digest.

Create a Google Sheet. Give it at least the below column headers, and put in the content that you want:

| name          | ring   | quadrant               | isNew | description                                             |
| ------------- | ------ | ---------------------- | ----- | ------------------------------------------------------- |
| Composer      | adopt  | tools                  | TRUE  | Although the idea of dependency management ...          |
| Canary builds | trial  | techniques             | FALSE | Many projects have external code dependencies ...       |
| Apache Kylin  | assess | platforms              | TRUE  | Apache Kylin is an open source analytics solution ...   |
| JSF           | hold   | languages & frameworks | FALSE | We continue to see teams run into trouble using JSF ... |

### Sharing the sheet

- In Google Sheets, click on "Share".
- On the pop-up that appears, set the General Access as "Anyone with the link" and add "Viewer" permission.
- Use the URL link of the sheet.

The URL will be similar to [https://docs.google.com/spreadsheets/d/1waDG0_W3-yNiAaUfxcZhTKvl7AUCgXwQw8mdPjCz86U/edit](https://docs.google.com/spreadsheets/d/1waDG0_W3-yNiAaUfxcZhTKvl7AUCgXwQw8mdPjCz86U/edit). In theory we are only interested in the part between '/d/' and '/edit' but you can use the whole URL if you want.

### Using private Google Sheet

When using a private Google Sheet as your input, you will be prompted with a Google One Tap Login popup. Once you have logged in with the appropriate Google Account and authorized our app to access the sheet, the Radar will be generated.

The input data format for the private sheet is the same as a public Google Sheet.

### Using CSV data

The other way to provide your data is using CSV document format.
You can enter a publicly accessible URL (not behind any authentication) of a CSV file into the input field on the first page.
For example, a [raw URL](https://raw.githubusercontent.com/thoughtworks/build-your-own-radar/master/spec/end_to_end_tests/resources/sheet.csv) for a CSV file hosted publicly on GitHub can be used.
The format is just the same as that of the Google Sheet, the example is as follows:

```
name,ring,quadrant,isNew,description
Composer,adopt,tools,TRUE,"Although the idea of dependency management ..."
Canary builds,trial,techniques,FALSE,"Many projects have external code dependencies ..."
Apache Kylin,assess,platforms,TRUE,"Apache Kylin is an open source analytics solution ..."
JSF,hold,languages & frameworks,FALSE,"We continue to see teams run into trouble using JSF ..."
```

If you do not want to host the CSV file publicly, you can follow [these steps](#advanced-option---docker-image-with-a-csvjson-file-from-the-host-machine) to host the file locally on your BYOR docker instance itself.

**_Note:_** The CSV file parsing is using D3 library, so consult the [D3 documentation](https://github.com/d3/d3-request/blob/master/README.md#csv) for the data format details.

### Using JSON data

Another other way to provide your data is using a JSON array.
You can enter a publicly accessible URL (not behind any authentication) of a JSON file into the input field on the first page.
For example, a [raw URL](https://raw.githubusercontent.com/thoughtworks/build-your-own-radar/master/spec/end_to_end_tests/resources/data.json) for a JSON file hosted publicly on GitHub can be used.
The format of the JSON is an array of objects with the the fields: `name`, `ring`, `quadrant`, `isNew`, and `description`.

An example:

```json
[
  {
    "name": "Composer",
    "ring": "adopt",
    "quadrant": "tools",
    "isNew": "TRUE",
    "description": "Although the idea of dependency management ..."
  },
  {
    "name": "Canary builds",
    "ring": "trial",
    "quadrant": "techniques",
    "isNew": "FALSE",
    "description": "Many projects have external code dependencies ..."
  },
  {
    "name": "Apache Kylin",
    "ring": "assess",
    "quadrant": "platforms",
    "isNew": "TRUE",
    "description": "Apache Kylin is an open source analytics solution ..."
  },
  {
    "name": "JSF",
    "ring": "hold",
    "quadrant": "languages & frameworks",
    "isNew": "FALSE",
    "description": "We continue to see teams run into trouble using JSF ..."
  }
]
```

If you do not want to host the JSON file publicly, you can follow [these steps](#advanced-option---docker-image-with-a-csvjson-file-from-the-host-machine) to host the file locally on your BYOR docker instance itself.

**_Note:_** The JSON file parsing is using D3 library, so consult the [D3 documentation](https://github.com/d3/d3-request/blob/master/README.md#json) for the data format details.

### Building the radar

Paste the URL in the input field on the home page.

That's it!

**_Note:_** When using the BYOR app on [radar.thoughtworks.com](https://radar.thoughtworks.com), the ring and quadrant names should be among the values mentioned in the [example above](#setting-up-your-data). This holds good for Google Sheet, CSV or JSON inputs.
For a self hosted BYOR app, there is no such condition on the names. Instructions to specify custom names are in the [next section](#more-complex-usage).

Check [this page](https://www.thoughtworks.com/radar/byor) for step by step guidance.

### More complex usage

To create the data representation, you can use the Google Sheet [factory](/src/util/factory.js) methods or CSV/JSON, or you can also insert all your data straight into the code.

The app uses [Google Sheets APIs](https://developers.google.com/sheets/api/reference/rest) to fetch the data from a Google Sheet or [D3.js](https://d3js.org/) if supplied as CSV/JSON, so refer to their documentation for more advanced interaction. The input data is sanitized by whitelisting HTML tags with [sanitize-html](https://github.com/punkave/sanitize-html).

The application uses [webpack](https://webpack.github.io/) to package dependencies and minify all .js and .scss files.

Google OAuth Client ID and API Key can be obtained from your Google Developer Console. OAuth Client ID is mandatory for private Google Sheets, as it is needed for Google Authentication and Authorization of our app.

```
export CLIENT_ID=[Google Client ID]
```

**_Note:_** Make sure to set the "Authorized JavaScript origins" field for the Client ID to the right origin domain, with port, where the app is hosted. Examples: `http://localhost:8080` or `https://radar.thoughtworks.com`.

Optionally, API Key can be set to bypass Google Authentication for public sheets.

```
export API_KEY=[Google API Key]
```

To enable Google Tag Manager, add the following environment variable.

```
export GTM_ID=[GTM ID]
```

To enable Adobe Launch, add the following environment variable.

```
export ADOBE_LAUNCH_SCRIPT_URL=[Adobe Launch URL]
```

To specify custom ring and/or quadrant names, add the following environment variables with the desired values.

```
export RINGS='["Adopt", "Trial", "Assess", "Hold"]'
export QUADRANTS='["Techniques", "Platforms", "Tools", "Languages & Frameworks"]'
```

## Docker Image

We have released BYOR as a docker image for our users. The image is available in our [DockerHub Repo](https://hub.docker.com/r/wwwthoughtworks/build-your-own-radar/). To pull and run the image, run the following commands.

```
$ docker pull wwwthoughtworks/build-your-own-radar
$ docker run --rm -p 8080:80 -e CLIENT_ID="[Google Client ID]" wwwthoughtworks/build-your-own-radar:latest
$ open http://localhost:8080
```

**_Notes:_**

- The other environment variables mentioned in the previous section can be used with `docker run` as well.
- Docker images for all the [releases](https://github.com/thoughtworks/build-your-own-radar/releases) are available with their respective tags (eg: `wwwthoughtworks/build-your-own-radar:v1.0.0`).

### Advanced option - Docker image with a CSV/JSON file from the host machine

You can check your setup by clicking on "Build my radar" and by loading the `csv`/`json` file from these locations:

- http://localhost:8080/files/radar.csv
- http://localhost:8080/files/radar.json

```
$ docker pull wwwthoughtworks/build-your-own-radar
$ docker run --rm -p 8080:80 -e SERVER_NAMES="localhost 127.0.0.1" -v /mnt/radar/files/:/opt/build-your-own-radar/files wwwthoughtworks/build-your-own-radar:latest
$ open http://localhost:8080
```

This will:

- Spawn a server that will listen locally on port 8080.
- Mount the host volume on `/mnt/radar/files/` into the container on `/opt/build-your-own-radar/files/`.
- Open http://localhost:8080 and for the URL enter: `http://localhost:8080/files/<NAME_OF_YOUR_FILE>.<EXTENSION_OF_YOUR_FILE[csv/json]>`. It needs to be a csv/json file.

You can now work locally on your machine, updating the csv/json file and render the result back on your browser.
There is a sample csv and json file placed in `spec/end_to_end_tests/resources/localfiles/` for reference.

**_Notes:_**

- If API Key is also available, same can be provided to the `docker run` command as `-e API_KEY=[Google API Key]`.
- For setting the `publicPath` in the webpack config while using this image, the path can be passed as an environment variable called `ASSET_PATH`.

## Contribute

All tasks are defined in `package.json`.

Pull requests are welcome; please write tests whenever possible.
Make sure you have nodejs installed. You can run `nvm use` to use the version used by this repo.

- `git clone git@github.com:thoughtworks/build-your-own-radar.git`
- `npm install`
- `npm run quality` - to run the linter and the unit tests
- `npm run dev` - to run application in localhost:8080. This will watch the .js and .css files and rebuild on file changes

## End to End Tests

To run End to End tests, start the dev server and follow the required steps below:

- To run in headless mode:

  - add a new environment variable `TEST_URL` and set it to 'http://localhost:8080'
  - `npm run test:e2e-headless`

- To run in debug mode:
  - add a new environment variable `TEST_URL` and set it to 'http://localhost:8080'
  - `npm run e2e`
  - Select 'E2E Testing' and choose the browser
  - Click on the spec to run it's tests

**_Notes:_**

- Currently, end to end tests are not supported for private Google Sheets, as it requires interacting with the Google One Tap popup.
- To run end to end tests for public Google Sheets, the `CLIENT_ID` and `API_KEY` environment variables need to set as well (steps details [here](#more-complex-usage)), to provide Cypress with an authenticated session (without having to interact with Google's auth popups).

### Don't want to install node? Run with one line docker

     $ docker run -p 8080:8080 -v $PWD:/app -w /app -it node:18 /bin/sh -c 'npm install && npm run dev'

After building it will start on `localhost:8080`.
