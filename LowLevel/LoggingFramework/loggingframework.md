Logging Framework

# Requirements
1. The logging framework should support different log levels, such as DEBUG, INFO, WARNING, ERROR, and FATAL.
2. It should allow logging messages with a timestamp, log level, and message content.
3. The framework should support multiple output destinations, such as console, file, and database.
4. It should provide a configuration mechanism to set the log level and output destination.
5. The logging framework should be thread-safe to handle concurrent logging from multiple threads.
6. It should be extensible to accommodate new log levels and output destinations in the future.


# Entity / Classes


1. LogType Enum for the types of the logs
2. LogMessage that captures the current message, timestamp as well the level in the stacktrace( maybe you can configure the log lines as well here)
3. LogOutput , this is an interfacem which just provides a basic stc to log onto different output
4. ConsoleLogOutput, FileLogOutput can use this interface and do the log, accordingly
6. Logger , singleton, which provides the APIs to interact with the internal classes and entities


# How Will it work,

lets you want to log DEBUG / WARNING / ERROR in you system
create LOGCONFIG_DEBUG(....) / LOGCONFIG_WARNING(....) / LOGCONFIG_ERROR(....)

each type could have different congfigurations, including where do you want the append to maybe debug only needs console, you wanna set error to a file, ans warning to a alert box
(you can also have a default config, if the user forgets)

Then the logger class has the method log(logtype, config), config(logType)
this top level logger class call the internal LOGCONFIG and the LOGMESSAGE function to compute the details, the message user provides, but for the stacktrace maybe it needs to interact with the runtime logs

We can use factory method here based on the logType argument the user passes down,
the user can also pass a custom config just for that message, but doesnt want it to apply globally, here we can use the config argument in the log message.

Otherwise the config method here sets the global config for the given logType,(factory method again)

# Second throught Imrpovement.

Maybe the config for each log type can store a list of oberservers and we can all the observers (in this case the log output we want to notify).
this will help us when we want to interact with the outside world maybe trigger an email or a laptop notification