# Analyse Logs
I'll desctibe my workflow for logs analitics its not perfect but in most cases it can do the job. Basically its for internal use.
## Prerequisites
I assume that you logs store in papertrail but it can be anywhere

1. fetch logs from parpertrail example:
   ```
    papertrail  --min-time '2019-05-13 00:00:00' --max-time '2019-05-13 23:59:59'  evt_type > 2019-05-13_events.log
   ```
   you need to have papertrail and setup it https://github.com/papertrail/papertrail-cli
2. logs on your machine you need to do docker-compose up -d (with kibanan and elasticsearch) 
3. Use logcli (https://github.com/gingray/logcli) to extract json object form you logs.
   ```bash
   logcli extract_json --filenames=example1.log example2.log
   ```
   assume that you have log lines looks like this:
   ```text
   May 10 01:00:02 0f04a46f7fd2 production-evt: I, [2019-05-10T08:00:02.090892 #1]  INFO -- : {"evt_type":"EvtClass","status":"info","time":"2019-05-10T08:00:02+0000","payload":{"time_elapsed_human":"00:00:00.323","time_elapsed":0.323117733001709},"trace_id":"9d35ca3a-b52f-462d-b5f1-f55ccc781e5e"}
   ```
   its not a valid json object but logcli able to extract valid from each line after this command you'll get
   ```json
   {"evt_type":"EvtClass","status":"info","time":"2019-05-10T08:00:02+0000","payload":{"time_elapsed_human":"00:00:00.323","time_elapsed":0.323117733001709},"trace_id":"9d35ca3a-b52f-462d-b5f1-f55ccc781e5e"}
   ```
   on each line
4. than push these logs to ealsicsearch
   ```bash
   logcli elasticsearch --elasticsearch-url=http://localhost:9201 --filenames=example1.json example2.json
   ```
5. Enjoy

NOTE:
before push data to elasticsearch make jusre that eac line is valid json object. Data pushed in ES by batches 100 per request its not optimize in terms of backpressure to a ES but its was able to process ~400mb logs in a seconds but objects that was used it was relatively small.

Why ES on 2001 port?
Because I've ES already on port 9200 for my dev env thats why to avoid conflicts its on 9201


