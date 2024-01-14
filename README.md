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

| exec ***.cfg  | Параметры команды: когда отправляется, какие события учитываются и т.д. |
| -------- | -------- |
| warmup.cfg | Запускается после того, как игровой сервер получит от агрегатора все данные матча. С момента выполнения запускается таймер 10 минут, если все заявленные игроки на сервере и прописали  ___!ready___ или ___!rdy___, проверяется значение запуска *knife.cfg*, если 0, то запускается *live.cfg* |
| knife.cfg | Запускается только в матчах, относящихся к турнирам и если у матча включена данная опция. Выполняется, если все 10 игроков на сервере и написали в чат ___!ready___ или ___!rdy___ |
| live.cfg | Если "warmup |
| clear.cfg | Запускается после того, как игровой сервер получит от агрегатора все данные матча. |
| warmup.cfg | Запускается после того, как игровой сервер получит от агрегатора все данные матча. |
