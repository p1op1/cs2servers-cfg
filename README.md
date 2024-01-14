# cs2servers-cfg

**Command line example:** 
```
-dedicated -usercon -console -secure +game_type 0 +game_mode 1 +map de_dust2 +mapgroup mg_cs2 +exec server.cfg -ip 123.123.123.123 -port 27015 -maxplayers 10
```

**⚠️WARNING**: If you're using a Docker image please, set your IP in startup commands to ___-ip 0.0.0.0___ or simply remove the -ip parameter. Thanks asdfxD for the fix.

№№ Рекомендую для запуска: 
```
-dedicated -usercon -console -secure +game_type 0 +game_mode 1 +map de_dust2 +mapgroup mg_cs2 +exec server.cfg -ip 0.0.0.0 -port 27015 -maxplayers 10 +tv_port 28015 +hostname "BAKS.GG: &Team1 vs &Team2"
```

№№ Остальные cfg файлы будут запускаться по запросу, либо согласно установленным для них правилам

| exec ***.cfg  | Параметры запуска | Условия | Провал условий |
| -------- | -------- |-------- |-------- |
| warmup.cfg | Запускается после того, как игровой сервер получит от АГРЕГАТОРА все данные матча. | С момента выполнения запускается таймер — 10 минут, если все заявленные игроки на сервере и прописали  ___!ready___ или ___!rdy___, проверяется значение "knife round" в матче: **если TRUE**, то отправляется *exec knife.cfg* и передается АГРЕГАТОРУ статус начала ножевого раунда, **если FALSE**, то отправляется *exec live.cfg* и передается статус АГРЕГАТОРУ о начале матча. | Если по истечению 10 минут, не присоеденились какие-то игроки и не написали команду в чат, то матч отменяется, а присоединившиеся игроки возвращаются в поиск в премиальном статусе для наибыстрейшего поиска недостающих игроков. АГРЕГАТОРУ отправляется результат и данные не подключившыхся игроков. | 
| knife.cfg | Запускается только в матчах, относящихся к турнирам и если у матча включена данная опция. Выполняется, если все 10 игроков на сервере и написали в чат ___!ready___ или ___!rdy___ | 123132 | 123123 |
| live.cfg | Если "warmup |123123|12 123|
| clear.cfg | Запускается после того, как игровой сервер получит от агрегатора все данные матча. |1212312| 1231212312|
| warmup.cfg | Запускается после того, как игровой сервер получит от агрегатора все данные матча. | 123123312213| 1231212|
