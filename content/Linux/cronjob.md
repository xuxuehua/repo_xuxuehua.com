---
title: "cronjob"
date: 2018-07-26 19:11
---



[TOC]



# cronjob



## `*` 

任何时间



## `,`

代表不连续时间

```
0 8,12,16 * * *
```

> 每天8点，12点，16点0分执行



## `-`

连续时间范围

```
0 5 * * 1-6
```

> 周一至周六凌晨5点执行



## `*/n`

每隔多久执行

```
*/10 * * * *
```

> 每隔10s执行一次



# example 

- [every minute](https://crontab.guru/every-minute)
- [every 1 minute](https://crontab.guru/every-1-minute)
- [every 2 minutes](https://crontab.guru/every-2-minutes)
- [every even minute](https://crontab.guru/every-even-minute)
- [every uneven minute](https://crontab.guru/every-uneven-minute)
- [every 3 minutes](https://crontab.guru/every-3-minutes)
- [every 4 minutes](https://crontab.guru/every-4-minutes)
- [every 5 minutes](https://crontab.guru/every-5-minutes)
- [every five minutes](https://crontab.guru/every-five-minutes)
- [every 6 minutes](https://crontab.guru/every-6-minutes)
- [every 10 minutes](https://crontab.guru/every-10-minutes)
- [every ten minutes](https://crontab.guru/every-ten-minutes)
- [every quarter hour](https://crontab.guru/every-quarter-hour)
- [every 20 minutes](https://crontab.guru/every-20-minutes)
- [every 30 minutes](https://crontab.guru/every-30-minutes)
- [every hour at 30 minutes](https://crontab.guru/every-hour-at-30-minutes)
- [every half hour](https://crontab.guru/every-half-hour)
- [every 60 minutes](https://crontab.guru/every-60-minutes)
- [every hour](https://crontab.guru/every-hour)
- [every 1 hour](https://crontab.guru/every-1-hour)
- [every 2 hours](https://crontab.guru/every-2-hours)
- [every two hours](https://crontab.guru/every-two-hours)
- [every even hour](https://crontab.guru/every-even-hour)
- [every other hour](https://crontab.guru/every-other-hour)
- [every 3 hours](https://crontab.guru/every-3-hours)
- [every three hours](https://crontab.guru/every-three-hours)
- [every 4 hours](https://crontab.guru/every-4-hours)
- [every 6 hours](https://crontab.guru/every-6-hours)
- [every six hours](https://crontab.guru/every-six-hours)
- [every 8 hours](https://crontab.guru/every-8-hours)
- [every 12 hours](https://crontab.guru/every-12-hours)
- [every day](https://crontab.guru/every-day)
- [every night](https://crontab.guru/every-night)
- [every day at 1am](https://crontab.guru/every-day-at-1am)
- [every day at 2am](https://crontab.guru/every-day-at-2am)
- [every day 8am](https://crontab.guru/every-day-8am)
- [every morning](https://crontab.guru/every-morning)
- [every midnight](https://crontab.guru/every-midnight)
- [every day at midnight](https://crontab.guru/every-day-at-midnight)
- [every night at midnight](https://crontab.guru/every-night-at-midnight)
- [every sunday](https://crontab.guru/every-sunday)
- [every monday](https://crontab.guru/every-monday)
- [every tuesday](https://crontab.guru/every-tuesday)
- [every wednesday](https://crontab.guru/every-wednesday)
- [every thursday](https://crontab.guru/every-thursday)
- [every friday](https://crontab.guru/every-friday)
- [every friday at midnight](https://crontab.guru/every-friday-at-midnight)
- [every saturday](https://crontab.guru/every-saturday)
- [every weekday](https://crontab.guru/every-weekday)
- [every 7 days](https://crontab.guru/every-7-days)
- [every week](https://crontab.guru/every-week)
- [every month](https://crontab.guru/every-month)
- [every other month](https://crontab.guru/every-other-month)
- [every quarter](https://crontab.guru/every-quarter)
- [every 6 months](https://crontab.guru/every-6-months)
- [every year](https://crontab.guru/every-year)

# 在线测试语法

https://crontab.guru/

http://cron.schlitt.info/

http://www.cronmaker.com/

https://www.freeformatter.com/

