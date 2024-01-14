# cs2servers-cfg

**Command line example:** 
```
-dedicated -usercon -console -secure +game_type 0 +game_mode 1 +map de_dust2 +mapgroup mg_cs2 +exec server.cfg -ip 123.123.123.123 -port 27015 -maxplayers 10
```

**⚠️WARNING**: If you're using a Docker image please, set your IP in startup commands to ___-ip 0.0.0.0___ or simply remove the -ip parameter. Thanks asdfxD for the fix.

## Рекомендую для запуска: 
```
-dedicated -usercon -console -secure +game_type 0 +game_mode 1 +map de_dust2 +mapgroup mg_cs2 +exec server.cfg -ip 0.0.0.0 -port 27015 -maxplayers 10 +tv_port 28015
```

## Остальные cfg файлы будут запускаться по запросу, либо согласно установленным для них правилам

| exec ***.cfg  | Параметры запуска | Условия | Провал условий |
| -------- | -------- |-------- |-------- |
| warmup.cfg | Запускается после того, как игровой сервер получит от АГРЕГАТОРА все данные матча и отправит. После отправки всех команд, отправляется *exec warmup*. | С момента выполнения запускается таймер — 10 минут, если все заявленные игроки на сервере и прописали  ___!ready___ или ___!rdy___, проверяется значение "knife round" в матче: **если TRUE**, то отправляется *exec knife* и передается АГРЕГАТОРУ статус начала ножевого раунда, **если FALSE**, то отправляется *exec live* и передается статус АГРЕГАТОРУ о начале матча. | Если по истечению 10 минут, не присоеденились какие-то игроки и не написали команду в чат, то матч отменяется, а присоединившиеся игроки возвращаются в поиск в премиальном статусе для наибыстрейшего поиска недостающих игроков. АГРЕГАТОРУ отправляется результат и данные не подключившыхся игроков. | 
| knife.cfg | Запускается только в матчах, относящихся к турнирам: если у матча включена данная опция. |  Выполняется, если все 10 игроков на сервере и написали в чат ___!ready___ или ___!rdy___ | Если 10 игроков на сервере, запускается принудительно. |
| live.cfg | Запускается игровой конфиг матча, который сам сообщает игрокам о начале игры. | Запускается если 10 игроков на сервере и прописали ___!ready___ или ___!rdy___ в чат. Если по истечению таймера (после запуска warmup), 10 игроков на сервере, но кто-то не сообщил о готовности в чат, команда отправляется и матч начинается принудительно. Сообщается АГРЕГАТОРУ статус. | Если на сервере <10 игроков по истечению таймера, неподключенные игроки исключаются из поиска, а остальные возвращаются в поиск в премиальном статусе. АГРЕГАТОРУ сообщается об отмене матча. |
| clear.cfg | Если сервера не выключаются и используются дальше без перезагрузки/создания, то выполняется команда **exec clear**, которая отправляет обнуленные данные серверу и осуществляет смену карты для очищения скопившихся не нужных данных. |Только если сервера для competitive игр работаю без выключений/перезагрузок для новых матчей. Например, только если через смену карты или если сервер получает статус "свободен"  | Если после каждого завершения матча (в т.ч. ОТМЕНЫ), сервер каким-то образом выключается или перезагружается в серверной станции, то команда не выполняется, так как каждый раз сервер запускается "чистый" и устанавливает актуальные требуемые параметры для предстоящей игры. |
| ......cfg | 12313132213123 | 123123312213| 1231212|


# TO-DO LIST 
- [x] Описания и условия запуска файлов
- [ ] Файлы cfg с готовыми параметрами
- [ ] Команды отправляемые отдельно, не "упаковывая" в cfg
- [ ] Состояния и события, вызывающие выполнение (каких) команд незамедлительно
- [ ] Отдельные настройки для warmup серверов
- [ ] Отдельные настройки для НЕ 5v5 матчей

P.S.: Обратите внимание на [данную разработку](https://github.com/deStrO/eBot-CSGO/), в особенности на файлы: [Config.php](https://github.com/deStrO/eBot-CSGO/blob/master/src/eBot/Config/Config.php) и [Match.php](https://github.com/deStrO/eBot-CSGO/blob/master/src/eBot/Match/Match.php).
При необходимости — [домашняя страница программы](https://www.esport-tools.net/ebot/).

