# cs2servers-cfg

**Command line example:** 
```
-dedicated -usercon -console -secure +game_type 0 +game_mode 1 +map de_dust2 +mapgroup mg_cs2 +exec server.cfg -ip 123.123.123.123 -port 27015 -maxplayers 10
```


**⚠️WARNING**: If you're using a Docker image please, set your IP in startup commands to ```-ip 0.0.0.0``` or simply remove the -ip parameter. Thanks asdfxD for the fix.

Рекомендую для запуска: 
```
-dedicated -usercon -console -secure +game_type 0 +game_mode 1 +map de_dust2 +mapgroup mg_cs2 +exec server.cfg -ip 0.0.0.0 -port 27015 -maxplayers 10 +tv_port 28015 +hostname "BAKS.GG: &Team1 vs &Team2"
```
