# cs2servers-cfg

**Command line example:** 
```
-dedicated -usercon -console -secure +game_type 0 +game_mode 1 +map de_dust2 +mapgroup mg_cs2 +exec server.cfg -ip 123.123.123.123 -port 27015 -maxplayers 10
```

**⚠️WARNING**: If you're using a Docker image please, set your IP in startup commands to ___-ip 0.0.0.0___ or simply remove the -ip parameter. Thanks asdfxD for the fix.

№№ Рекомендую для запуска: 
```
-dedicated -usercon -console -secure +game_type 0 +game_mode 1 +map de_dust2 +mapgroup mg_cs2 +exec server.cfg -ip 0.0.0.0 -port 27015 -maxplayers 10 +tv_port 28015
```

№№ Остальные cfg файлы будут запускаться по запросу, либо согласно установленным для них правилам

| exec ***.cfg  | Параметры запуска | Условия | Провал условий |
| -------- | -------- |-------- |-------- |
| warmup.cfg | Запускается после того, как игровой сервер получит от АГРЕГАТОРА все данные матча и отправит. После отправки всех команд, отправляется *exec warmup*. | С момента выполнения запускается таймер — 10 минут, если все заявленные игроки на сервере и прописали  ___!ready___ или ___!rdy___, проверяется значение "knife round" в матче: **если TRUE**, то отправляется *exec knife* и передается АГРЕГАТОРУ статус начала ножевого раунда, **если FALSE**, то отправляется *exec live* и передается статус АГРЕГАТОРУ о начале матча. | Если по истечению 10 минут, не присоеденились какие-то игроки и не написали команду в чат, то матч отменяется, а присоединившиеся игроки возвращаются в поиск в премиальном статусе для наибыстрейшего поиска недостающих игроков. АГРЕГАТОРУ отправляется результат и данные не подключившыхся игроков. | 
| knife.cfg | Запускается только в матчах, относящихся к турнирам: если у матча включена данная опция. |  Выполняется, если все 10 игроков на сервере и написали в чат ___!ready___ или ___!rdy___ | Если 10 игроков на сервере, запускается принудительно. |
| live.cfg | Запускается игровой конфиг матча, который сам сообщает игрокам о начале игры. | Запускается если 10 игроков на сервере и прописали ___!ready___ или ___!rdy___ в чат. Если по истечению таймера (после запуска warmup), 10 игроков на сервере, но кто-то не сообщил о готовности в чат, команда отправляется и матч начинается принудительно. Сообщается АГРЕГАТОРУ статус. | Если на сервере <10 игроков по истечению таймера, неподключенные игроки исключаются из поиска, а остальные возвращаются в поиск в премиальном статусе. АГРЕГАТОРУ сообщается об отмене матча. |
| clear.cfg | Если сервера не выключаются и используются дальше без перезагрузки/создания, то выполняется команда **exec clear**, которая отправляет обнуленные данные серверу и осуществляет смену карты для очищения скопившихся не нужных данных. |Только если сервера для competitive игр работаю без выключений/перезагрузок для новых матчей. | Если после каждого завершения матча (в т.ч. ОТМЕНЫ), сервер каким-то образом выключается или перезагружается в серверной станции, то команда не выполняется, так как каждый раз сервер запускается "чистый" и устанавливает актуальные требуемые параметры для предстоящей игры. |
| warmup.cfg | Запускается после того, как игровой сервер получит от агрегатора все данные матча. | 123123312213| 1231212|
