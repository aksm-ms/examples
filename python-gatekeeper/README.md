# apiary-python

## Overview

This is an example app used to explore Honeycomb. It implements the /1/events endpoint of our public API: see the docs for that on our [main documentation site](https://docs.honeycomb.io/api/events/).

## Download or Build

First, create and activate a virtual environment. Then, install the requirements:

```bash
$ pip install -r requirements.txt
```

Run the application:

```bash
$ export FLASK_APP=app.py
$ flask run
```

The server will start listening on port 5000.

## Run the server

Before you launch the server, set an environment variable `HONEYCOMB_WRITEKEY` to your team api key, available from [your account page](https://ui.honeycomb.io/account). This will configure the server to send instrumentation events to Honeycomb in a dataset called `apiary-python`

Running the server will start it listening on port 5000. Send HTTP POSTs to the `/1/events/<dataset-name>` endpoint with an `X-Honeycomb-Team` header containing one of the hardcoded api keys and with a body containing a JSON object. The server will process the POST and if everything looks good, eventually write it to `/tmp/api#.log`, where # is a partition number (between 1-5).

example of sending an event to the apiary server:

```bash
➜  curl -v localhost:5000/1/events/wade \
    -H "X-Honeycomb-Team: abcd123EFGH" \
    -H "X-Honeycomb-Event-Time: 2018-07-03T20:59:08.832016791Z" \
    -d '{"foo":3}'
```

Hard-coded values for `X-Honeycomb-Team` (required header):

- abcd123EFGH
- ijkl456MNOP
- qrst789UVWX

Hard-coded values for dataset names (required in the URL):

- wade
- james
- helen
- peter
- valentine
- andrew

Expected values for `X-Honeycomb-Samplerate` (optional): positive integers. If absent, defaults to 1

Expected values for `X-Honeycomb-Event-Time` (optional): RFC3339 formatted timestamps. If absent, defaults to the current time

Any values other than those listed above will cause a HTTP status 401 (for api keys) or 400 (for dataset names) to be returned by the server along with an error message. Errors can also be generated by sending malformed JSON or broken expected headers

Example output instrumentation events you'll see in Honeycomb:

```json
{
  "data": {
    "chosen_partition": 1,
    "dataset": {
      "id": 1,
      "name": "wade",
      "partition_list": [1, 2, 3]
    },
    "error_time_parsing": "no timestamp for event",
    "event_columns": 1,
    "hitSchemaCache": "True",
    "meta.beeline_version": "1.0.0",
    "meta.local_hostname": "Alainas-MBP-2.local",
    "name": "flask_http_post",
    "request.content_length": "12",
    "request.host": "localhost:5000",
    "request.method": "POST",
    "request.path": "/1/events/wade",
    "request.query": "",
    "request.remote_addr": "127.0.0.1",
    "request.scheme": "http",
    "request.user_agent": "curl/7.54.0",
    "response.status_code": 200,
    "service_name": "sample_app",
    "team": {
      "id": 1,
      "name": "RPO",
      "write_key": "abcd123EFGH"
    },
    "timer.get_headers_dur_ms": 0.0476837158203125,
    "timer.get_partition_dur_ms": 0.010013580322265625,
    "timer.get_schema_dur_ms": 0.009298324584960938,
    "timer.resolve_dataset_dur_ms": 0.0011920928955078125,
    "timer.validate_write_key_dur_ms": 0.005245208740234375,
    "trace.parent_id": "None",
    "trace.span_id": "1dafc981-7320-414b-aade-4b1542dc3573",
    "trace.trace_id": "64d9f8a3-b261-4e23-93de-0c123db9df4b",
    "type": "http_server",
    "X-Honeycomb-Event-Time": "None",
    "X-Honeycomb-Samplerate": "1",
    "X-Honeycomb-Team": "abcd123EFGH"
  },
  "time": "2018-07-03T14:00:20.023732191-07:00"
}
```