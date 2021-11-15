**1. date**  
date time -> seconds
```
date -d "2021-11-09 08:34:00" +%s
date -d "2021-11-09T08:34:00" +%s
```

seconds -> date time
```
date -d @1636446780 +"%Y-%m-%d %H:%M:%S"
```
