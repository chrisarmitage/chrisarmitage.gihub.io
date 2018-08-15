---
layout: post
title: "M is for Monolog"
comments: false
category: php
tags:
  - A to Z 2013
  - logging
published: false
---
Following on from [Saturdays post about logging]({% post_url 2013-04-13-l-is-for-logging %}), I’ll now delve a bit more in to the technical aspects using [Monolog](https://github.com/Seldaek/monolog).

The package has docs on its basic usage, so let’s jump straight in with some code and I’ll step through it as I trigger various events

```php?start_inline=1
use Monolog\Logger;
use Monolog\Handler\StreamHandler;
use Monolog\Handler\PushoverHandler;
use Monolog\Formatter\LogstashFormatter;
use Monolog\Handler\FingersCrossedHandler;

$key = md5(microtime(true));

// Set up our logs
$appLog = new Logger('app');
$managementLog = new Logger('mgt');
$securityLog = new Logger('sec');

// Set up our formatters
$formatterLogstash = new LogstashFormatter(
        'app-name',
        'zoostorm.vm.web-test'
        );

// Set our mode. Normally set from the server config
$serverType = 'dev';
if ($serverType == 'dev') {
    $logLevel = Logger::DEBUG;
} else {
    $logLevel = Logger::INFO;
}

// Set up our handlers
$handlerStreamApp = new StreamHandler(
        '/logs/app.log',
        $logLevel
        );
$handlerStreamAppLogstash = new StreamHandler(
        '/logs/app.logstash.log',
        $logLevel
        );
$handlerStreamMgt = new StreamHandler(
        '/logs/mgt.log',
        $logLevel
        );
$handlerStreamSec = new StreamHandler(
        '/logs/sec.log',
        $logLevel
        );
$handlerFC = new FingersCrossedHandler(
        $handlerStreamAppLogstash,
        Logger::ERROR);

// Attach our formatters
$handlerStreamAppLogstash->setFormatter($formatterLogstash);

// Attach our handlers to the loggers
$appLog->pushHandler($handlerStreamApp);
$appLog->pushHandler($handlerFC);
$managementLog->pushHandler($handlerStreamMgt);
$securityLog->pushHandler($handlerStreamSec);
```

This gives us three logs, a general one for the application, one for security and one for management.

Starting with the simple two, I can do the following in my code

```php?start_inline=1
$managementLog->addInfo('Record processing completed', array('key' => $key));
```

I use this for events that may not be of direct interest to the dev team, but that management need for their reporting. This will insert the following line into the mgt.log file.

```
[2013-04-15 09:00:00] mgt.INFO: Record processing complete {"key":"31ada9ceaa16559b7c1228789f5facf6"} []
```

This can be parsed and displayed wherever suitable for the users.

Now if instead I wanted to have this in my code

```php?start_inline=1
$managementLog->addDebug("Record processing took {$time}ms for {$count} records", array('key' => $key));
$managementLog->addInfo('Record processing completed', array('key' => $key));
```

And it ran on the production server, the output would be the same. This is because the handler would be set to INFO, therefore ignoring the DEBUG entries. Run the exact same code on a dev server and both lines would be outputted. Using this technique you can leave debugging lines all round your code and they won’t litter your production logs.

The application log is a little more complicated as it has two handlers attached. One works in the same way as the security and management logs. We also have a Logstash handler with attached formatter. This turns the data into a JSON object which makes it far easier to parse in Logstash. The Logstash handler is then wrapped in the magical FingersCrossed handler.

> Logstash is a separate app for viewing and searching logs. It can pair with rsyslog to send logs to a remote server. This means four servers collating their logs into one location.

The way the FingersCrossed handler works is it stores up all messages but only starts to log them if the given log level is reached, here it’s Logger::ERROR.

The outcome of this is if the script only outputs DEBUGs and INFOs, it is logged locally as normal. If at any point an ERROR or worse is triggered, the entire log list is outputted. Rsyslog then watches the app.logstash.log file and fires it off to the remote Logstash server. This way I’m not wasting bandwidth on normal operation, but if an error does occur I have the full trace of DEBUG and INFO messages to help me trace what went wrong. This is where the $key comes in, every log entry from a single script execution will carry the same key, making it a breeze to reconstruct exactly what your script was up to.
