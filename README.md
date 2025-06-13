# Counter Strike 2 Dedicated Server using DockerProperties

<!-- 
Command line example 
```
-dedicated -usercon -console -secure +game_type 0 +game_mode 1 +map de_dust2 +mapgroup mg_cs2 +exec server.cfg -ip 123.123.123.123 -port 27015 -maxplayers 10
```
-->

# **Техническое задание: Чат-бот для управления соревновательными матчами в CS2**

## **1. Общее описание**
Чат-бот предназначен для автоматизации управления сервером CS2 в режиме 5x5 матчей. Бот:
- Читает логи сервера в реальном времени (или получает данные через RCON/Game State Integration).
- Управляет матчем через чат-команды.
- Взаимодействует с базой данных для получения настроек матча.
- Генерирует конфигурационные файлы (`namesfile.txt`, настройки сервера).
- Ведет статистику и логирует события.

---

## **2. Технические требования**
### **2.1. Входные данные**
- **База данных** (MySQL/PostgreSQL/SQLite) с параметрами матча:
  ```sql
  CREATE TABLE matches (
      match_id INT PRIMARY KEY,
      team1_name VARCHAR(32),
      team2_name VARCHAR(32),
      team1_flag VARCHAR(4),
      team2_flag VARCHAR(4),
      max_rounds INT DEFAULT 30,
      map VARCHAR(32),
      knife_round BOOLEAN DEFAULT TRUE,
      ot_maxrounds INT DEFAULT 3,
      ot_startmoney INT DEFAULT 10000,
      players_steamid JSON,  -- ["STEAM_1:0:12345", ...]
      players_nick JSON      -- ["Player1", "Player2", ...]
  );
  ```
- **Лог-файлы сервера** (`console.log`, `gamestate.log`).

### **2.2. Выходные данные**
- Конфигурационные файлы (`namesfile.txt`, `match_config.cfg`).
- Логи матча (`match_[ID].log`).
- Статистика в БД (`kills`, `deaths`, `assists`, `score`, `ADR`).

---

## **3. Сценарий работы**
### **3.1. Подготовка сервера**
1. Бот получает `match_id` из аргументов запуска или БД.
2. Загружает параметры матча из БД:
   ```python
   hostname = f"BAKS.GG::{team1} vs. {team2} | {match_id}"
   tv_demo = f"BAKS.GG::{team1} vs. {team2} on {map}.{match_id}"
   ```
3. Генерирует `namesfile.txt`:
   ```json
   "Names"
   {
       "7656119798491234" "KennyS"
       "7656119798491234" "ApeX"
   }
   ```
4. Применяет настройки сервера через RCON:
   ```bash
   hostname "$hostname"
   mp_teamname_1 "$team1"
   mp_teamflag_1 "$team1_flag"
   mp_maxrounds "$max_rounds"
   exec match_config.cfg
   ```

---

### **3.2. Фаза готовности**
- Игроки подтверждают готовность командой `!ready`.
- Бот проверяет 10 подтверждений и запускает матч:
  ```python
  if len(ready_players) == 10:
      start_match()
  else:
      send_chat(f"Ожидание игроков: {10 - len(ready_players)}")
  ```

---

### **3.3. Ножевой раунд (если включен)**
1. После `knife_round` победитель выбирает сторону:
   - `!stay` – оставить текущую сторону.
   - `!switch` – поменяться сторонами.
2. Бот обновляет конфиг:
   ```bash
   mp_swapteams 1  # Если !switch
   mp_restartgame 1
   ```

---

### **3.4. Игровой процесс**
- **Пауза** (`!pause`):
  - Доступна только в `mp_freezetime`.
  - Требует `!unpause` от капитана противников.
- **Статистика**:
  - Парсит логи убийств/смертей (`"PlayerX killed PlayerY with AK-47"`).
  - Отправляет уведомления:
    ```
    [BAKS.GG] Счет: 5-3 | Осталось раундов: 12
    ```
- **Овертайм**:
  - Активируется при ничье 15-15:
    ```bash
    mp_overtime_enable 1
    mp_overtime_maxrounds 3
    ```

---

### **3.5. Завершение матча**
1. Бот сохраняет статистику в БД:
   ```sql
   INSERT INTO match_stats VALUES (match_id, steamid, kills, deaths, adr);
   ```
2. Отправляет результаты в чат:
   ```
   [BAKS.GG] Матч завершен! Победила Team1 16-14.
   Статистика: !stats
   ```

---

## **4. Команды чата**
| Команда         | Описание                          | Доступ          |
|-----------------|-----------------------------------|-----------------|
| `!ready`        | Подтвердить готовность            | Все игроки      |
| `!stay`         | Оставить сторону после knife      | Капитаны        |
| `!switch`       | Поменять стороны после knife      | Капитаны        |
| `!pause`        | Поставить паузу                   | Капитаны        |
| `!unpause`      | Снять паузу                       | Капитаны        |
| `!report`       | Пожаловаться на игрока            | Все игроки      |
| `!stats`        | Показать статистику               | Все игроки      |

---

## **5. Логирование и безопасность**
- **Логи чата** сохраняются в `chat_[match_id].log`.
- **Репорты** (`!report`) записываются в БД:
  ```sql
  CREATE TABLE reports (
      match_id INT,
      reporter_steamid VARCHAR(32),
      target_steamid VARCHAR(32),
      reason VARCHAR(64)
  );
  ```
- **Анти-флуд**: ограничение 3 команды/игрок/минуту.

---

## **6. Технологии**
- **Язык**: Python (с библиотеками `asyncio`, `aiohttp`, `valve-rcon`).
- **База данных**: SQLite/MySQL.
- **Парсинг логов**: Регулярные выражения (`re`).
- **Интеграция с CS2**: RCON или Game State Integration.

---

## **7. Пример кода (Python)**
```python
import valve.rcon

async def handle_chat_command(steamid, message):
    if message == "!ready":
        db.execute("INSERT INTO ready_players VALUES (?, ?)", (match_id, steamid))
        send_chat(f"Игрок {get_nick(steamid)} готов.")

    elif message == "!pause" and is_captain(steamid):
        valve.rcon.execute("mp_pause_match")

def generate_namesfile(steamids, nicks):
    with open("namesfile.txt", "w") as f:
        f.write('"Names"\n{\n')
        for steamid, nick in zip(steamids, nicks):
            f.write(f'    "{steamid}" "{nick}"\n')
        f.write("}")
```

---

Готово! Такой бот может работать на любом CS2-сервере с минимальными доработками. Нужна детализация по какому-то модулю?
