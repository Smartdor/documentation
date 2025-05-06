---
stoplight-id: dy9dvy1im7j87
---

# Downlink API

The downlink API is used to configure individual sensors. When a configuration input is sent to the API, this is then formatted into the relevant sensor payload (a hexadecimal string), and then sent to the TTN API, which handles queuing and sending the payload to the actual sensor, and notifying via webhooks of any status updates.

## App Flow

#### In Brief
1. [Create Downlink Task](reference/Smartdor-MVP-API.json/paths/~1api~1sensors~1{sensorId}~1downlink/post)
2. [Fetch Downlink Task](reference/Smartdor-MVP-API.json/paths/~1api~1sensors~1{sensorId}~1downlink~1{taskId}/get)

#### Overview

To configure a sensor, send the relevant input to the [downlink API](reference/Smartdor-MVP-API.json/paths/~1api~1sensors~1{sensorId}~1downlink/post). For example, we may want to configure the reporting interval, so the payload sent to the API might look like:
```json
{
  "key": "reportingInterval",
  "input": {
    "value": 30
  }
}
```

Within the API, this will first validate the input, and then format it into the relevant sensor config payload (a hexadecimal string). Next, it will create a 'task' in the database, then send the config payload to the TTN API, and finally return the task in the request. At this point, the app has a task it can show the user, and start polling the API for task updates.

Within TTN, the payload command is first queued, then sent to the actual sensor, then the sensor sends an acknowledgement that it has received the command. For each of these TTN sends a webhook event, which our API receives, and updates the relevant task with the current status/stage - so as the app polls the task API, it will receive updates to show the current progress.

## Historic Tasks

There is also an API that can be used to list both in-progress tasks, and tasks that have completed within the past week. This could be used to display currently-running tasks on the sensor config page, and show previous tasks and their statuses.

[View Endpoint Docs](reference/Smartdor-MVP-API.json/paths/~1api~1sensors~1{sensorId}~1downlink/get)

## Task Inputs

There are multiple inputs that can be sent to configure different settings within the sensor. These currently include:

> Each input has its own input shape and validation, so check the docs for relevant information.

1. Reporting Interval
2. Measurement Interval
3. Door Open Timeout
4. Reset Acknowledgement
5. Accelerometer Configuration

[View Model Docs](reference/Smartdor-MVP-API.json/components/schemas/SensorConfigureInput)