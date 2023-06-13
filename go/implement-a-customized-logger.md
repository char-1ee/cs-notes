---
description: A self designed logger file
---

# Logger

Open interfaces:

* SetFile(): configure the log destination file.
* SetLevel(): to which level of logs should be output, like in production env.
* Debug(), Info(), Warn(), Error()

```go
package logger

import (
	"fmt"
	"log"
	"os"
	"runtime"
	"strconv"
	"strings"
	"sync"
	"time"
)

var (
	debugLogger *log.Logger
	infoLogger *log.Logger
	warnLogger *log.Logger
	errorLogger *log.Logger

	logFile *os.File
	logLevel  = 0 // default 0
	logFileName string
	lock sync.RWMutex
	day int
)

const (
	DebugLevel = iota // 0
	InfoLevel // 1
	WarnLevel // 2
	ErrorLevel // 3
)

func SetLogLevel(level int) {
	logLevel = level
}

func SetFile(file string) {
	var err error
	logFile, err = os.OpenFile(file, os.O_CREATE|os.O_APPEND|os.O_WRONLY, 0664)
	if err != nil {
		panic(err)
	} else {
		day = time.Now().YearDay()
		infoLogger = log.New(logFile, "[INFO] ", log.LstdFlags)
		debugLogger = log.New(logFile, "[DEBUG] ", log.LstdFlags)
		warnLogger = log.New(logFile, "[WARN] ", log.LstdFlags)
		errorLogger = log.New(logFile, "[ERROR] ", log.LstdFlags)
		logFileName = file
		lock = sync.RWMutex{}
	}
}

func checkAndChangeLogFile() {
	lock.Lock()
	defer lock.Unlock()
	
	currDay := time.Now().YearDay()
	if currDay == day {
		return
	} else {
		logFile.close()
		postFix := time.Now().Sub(24 * time.Hour).Format("20011017")
		os.Rename(logFileName,  logFileName + "." + postFix)
		logFile, _ = os.OpenFile(file, os.O_CREATE|os.O_APPEND|os.O_WRONLY, 0664)

		infoLogger = log.New(logFile, "[INFO] ", log.LstdFlags)
		debugLogger = log.New(logFile, "[DEBUG] ", log.LstdFlags)
		warnLogger = log.New(logFile, "[WARN] ", log.LstdFlags)
		errorLogger = log.New(logFile, "[ERROR] ", log.LstdFlags)
		logFileName = file
		day = currentDay
	}
}

func getCallTrace() (string, string, int) {
	funcName, file, line, ok = runtime.Caller(3)
	if ok {
		return 
	} else {
		return "", 0
	}
}

func getPrefix() string {
	file, line := getCallTrace()
	path := strings.Split(file, "/")
	if len(path) > 3 {
		path = path[len(path) - 3:]
	}
	return strings.Join(path, "/") + ":" + strconv.Itoa(line) + " "
}

func Debug(format string, v ...any) {
	if logLevel <= DebugLevel {
		checkAndChangeLogfile()
		debugLogger.Printf(setPrefix() + format, v...)
	}
}

func Info(format string, v ...any) {
	if logLevel <= InfoLevel {
		checkAndChangeLogfile()
		infoLogger.Printf(setPrefix() + format, v...)
	}
}

func Warn(format string, v ...any) {
	if logLevel <= WarnLevel {
		checkAndChangeLogfile()
		warnLogger.Printf(setPrefix() + format, v...)
	}
}

func Error(format string, v ...any) {
	if logLevel <= ErrorLevel {
		checkAndChangeLogfile()
		errorLogger.Printf(setPrefix() + format, v...)
	}
}
```
