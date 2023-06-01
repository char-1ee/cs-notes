# Cronjob

```go
type RegularSchedulerImpl struct {
	DBRepo               model.CommonDBRepo                         `inject:""`
	RedisService         redis.CacheManager                         `inject:"Redis"`
	AbtestMetaServiceMap map[string]*meta_rpc.Client                `inject:"AbtestMetaServiceMap"`
	Config               container.DynamicConfig[*config.TCCConfig] `inject:""`
	ExecuteEngineService feature.ExecuteEngineService               `inject:""`
	ProcessorFactory     post_processor.Factory                     `inject:""`
	PerformanceProducer  kafka.Producer                             `inject:"PerformanceProducer"`
}

func (scheduler *RegularSchedulerImpl) StartCronJob() {
	go func() {
		ticker := time.NewTicker(time.Duration(scheduler.Config.Get().SchedulerConf.TickerTime) * time.Second)
		for {
			<-ticker.C
			func() {
				defer func() {
					if e := recover(); e != nil {
						logs.Error("[StartCronJob] panic: %s, stack: %s", e, string(debug.Stack()))
						logs.Flush()
						_ = metrics.EmitCounter("cron_job.err", 1, "", nil)
					}
				}()
				scheduler.DistributeRules() // main logic
			}()
		}
	}()
}
```
