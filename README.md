# Serverless Local Schedule

[![serverless](http://public.serverless.com/badges/v3.svg)](http://www.serverless.com)
[![npm](https://img.shields.io/npm/v/@distinction-dev/serverless-local-schedule.svg)](https://www.npmjs.com/package/serverless-local-schedule)

This plugin allows you to specify a timezone on your lambdas and step functions triggered by AWS CloudWatch Events.

## Install

```bash
npm i -D @distinction-dev/serverless-local-schedule
```

or

```bash
yarn add -D @distinction-dev/serverless-local-schedule
```

Add the plugin to your serverless.yml file

```yaml
plugins:
  - "@distinction-dev/serverless-local-schedule"
```

## How it works

When you specify a schedule as an event in your serverless config, this plugin generates 6 different crons to deal with DST changes during that year like so:-

```yaml
functions:
  hello:
    handler: handler.hello
    events:
      - schedule:
          rate: cron(0 10 * * ? *)
          timezone: America/New_York
```

```yaml
functions:
  hello:
    handler: handler.hello
    events:
      - schedule:
          rate: cron(0 15 * 1-2,12 ? *) # full non-DST months
      - schedule:
          rate: cron(0 15 1-10 3 ? *) # non-DST portion of March
      - schedule:
          rate: cron(0 14 11-31 3 ? *) # DST portion of March
      - schedule:
          rate: cron(0 14 * 4-10 ? *) # full DST months
      - schedule:
          rate: cron(0 14 1-3 11 ? *) # DST portion of November
      - schedule:
          rate: cron(0 15 4-31 11 ? *) # non-DST portion of November
```

## Usage

### Lambda Functions

```yaml
functions:
  hello:
    handler: handler.hello
    events:
      - schedule:
          rate: cron(0 10 * * ? *)
          timezone: America/New_York
```

It works by converting that into 6 different schedules, effectively the same as having the following
configuration:

```yaml
functions:
  hello:
    handler: handler.hello
    events:
      - schedule:
          rate: cron(0 15 * 1-2,12 ? *) # full non-DST months
      - schedule:
          rate: cron(0 15 1-10 3 ? *) # non-DST portion of March
      - schedule:
          rate: cron(0 14 11-31 3 ? *) # DST portion of March
      - schedule:
          rate: cron(0 14 * 4-10 ? *) # full DST months
      - schedule:
          rate: cron(0 14 1-3 11 ? *) # DST portion of November
      - schedule:
          rate: cron(0 15 4-31 11 ? *) # non-DST portion of November
```

### Step Functions

```yaml
stepFunctions:
  stateMachines:
    helloStepFunc:
      events:
        - schedule:
            rate: cron(0 10 * * ? *)
            timezone: America/New_York
```

It works by converting that into 6 different schedules, effectively the same as having the following
configuration:

```yaml
stepFunctions:
  stateMachines:
    helloStepFunc:
      events:
        - schedule:
            rate: cron(0 15 * 1-2,12 ? *) # full non-DST months
        - schedule:
            rate: cron(0 15 1-10 3 ? *) # non-DST portion of March
        - schedule:
            rate: cron(0 14 11-31 3 ? *) # DST portion of March
        - schedule:
            rate: cron(0 14 * 4-10 ? *) # full DST months
        - schedule:
            rate: cron(0 14 1-3 11 ? *) # DST portion of November
        - schedule:
            rate: cron(0 15 4-31 11 ? *) # non-DST portion of November
```

## Extra points to consider

* To see what crontabs you'll get for your timezone, try <https://distinction-dev.github.io/local-crontab/>

* The `- schedule: cron(* * * * ? *)` short syntax isn't supported.

* Unfortunately you cannot specify day of the week in the cron expression i.e. `cron(0 7 ? * MON-FRI *)`. This is because to support the split months (March & November in the US), the plugin has to specify a day of month (EG: November 1-3 in 2018), so you cannot specify a DOW other than `?` unfortunately. Recommended workaround for this is to move the day of week check into your code so it's just a no-op on non weekdays for instance.

## Debugging

A `launch.json` file for vscode is provided which will run `sls package` in the test service.

It will be useful if you would like to test and see how the plugin works or debug with your crontab or event configuration

## History

---

_Originally developed by [**Capital One**](https://www.capitalone.com/tech/open-source/), now maintained by Distinction Dev_

_This was maintained by Serverless Inc for some time and then forked over by Distinction Dev_

_Capital One considers itself the bank a technology company would build. It's delivering best-in-class innovation so that its millions of customers can manage their finances with ease. Capital One is all-in on the cloud and is a leader in the adoption of open source, RESTful APIs, microservices and containers. We build our own products and release them with a speed and agility that allows us to get new customer experiences to market quickly. Our engineers use artificial intelligence and machine learning to transform real-time data, software and algorithms into the future of finance, reimagined._

---
