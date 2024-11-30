

# Практическое задание 5

Исследование информации о состоянии бепроводных сетей

## Цель

1.  Получить знания о методах исследования радиоэлектронной обстановки
2.  Составить прдставление о механизмах работы Wi-Fi сетей на канальном
    и сетевом уровне модели OSI
3.  Зекрепить практические навыки использования языка программирования R
    для обработки данных
4.  Закрепить знания основных функций обработки данных экосистемы
    tidyverse языка R

## Исходные даннные

1.  Персональный компьютер

2.  Браузер

3.  R studio

4.  Программный пакет dplyr

5.  tidyverse

6.  CSV-файл с данными для анализа

## Общий план выполнения

1.  Подготовка данных
    -   Импорт данных
    -   Преобразование датасетов в вид “аккуратных данных”
    -   Просмотр общей структуры данных
2.  Анализ данных
    -   Точки доступа
    -   Данных клиентов
3.  Подготовить отчёт

## Содержание ПР

### Шаг 1

**На данном шаге производится подготовка данных для дальнейшего
анализа**

Использование пакета tidyverse

``` r
library(tidyverse)
```

    Warning: package 'tidyverse' was built under R version 4.4.2

    Warning: package 'ggplot2' was built under R version 4.4.2

    Warning: package 'tidyr' was built under R version 4.4.2

    Warning: package 'readr' was built under R version 4.4.2

    Warning: package 'purrr' was built under R version 4.4.2

    Warning: package 'forcats' was built under R version 4.4.2

    Warning: package 'lubridate' was built under R version 4.4.2

    ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ✔ ggplot2   3.5.1     ✔ tibble    3.2.1
    ✔ lubridate 1.9.3     ✔ tidyr     1.3.1
    ✔ purrr     1.0.2     
    ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ✖ dplyr::filter() masks stats::filter()
    ✖ dplyr::lag()    masks stats::lag()
    ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

Для импорта csv-файла понадобится библиотека readr

``` r
library(readr)
```

Импортирование данных из данного csv файла

``` r
allDataCsv <- read.csv('./P2_wifi_data.csv')
head(allDataCsv)
```

                  BSSID      First.time.seen       Last.time.seen channel Speed
    1 BE:F1:71:D5:17:8B  2023-07-28 09:13:03  2023-07-28 11:50:50       1   195
    2 6E:C7:EC:16:DA:1A  2023-07-28 09:13:03  2023-07-28 11:55:12       1   130
    3 9A:75:A8:B9:04:1E  2023-07-28 09:13:03  2023-07-28 11:53:31       1   360
    4 4A:EC:1E:DB:BF:95  2023-07-28 09:13:03  2023-07-28 11:04:01       7   360
    5 D2:6D:52:61:51:5D  2023-07-28 09:13:03  2023-07-28 10:30:19       6   130
    6 E8:28:C1:DC:B2:52  2023-07-28 09:13:03  2023-07-28 11:55:38       6   130
      Privacy Cipher Authentication Power X..beacons     X..IV           LAN.IP
    1    WPA2   CCMP            PSK   -30        846       504    0.  0.  0.  0
    2    WPA2   CCMP            PSK   -30        750       116    0.  0.  0.  0
    3    WPA2   CCMP            PSK   -68        694        26    0.  0.  0.  0
    4    WPA2   CCMP            PSK   -37        510        21    0.  0.  0.  0
    5    WPA2   CCMP            PSK   -57        647         6    0.  0.  0.  0
    6     OPN                         -63        251      3430  172. 17.203.197
      ID.length           ESSID Key
    1        12    C322U13 3965  NA
    2         4            Cnet  NA
    3         2              KC  NA
    4        14  POCO X5 Pro 5G  NA
    5        25                  NA
    6        13   MIREA_HOTSPOT  NA

Так как формат CSV лога меняется внутри файла, то необходимо его
разделить на два датасета (до 167 строки включительно - первый датасет,
далее - второй датасет)

Получение данных об анонсах бепроводных точек доступа

``` r
wireNetData <- read.csv('./P2_wifi_data.csv', nrows = 167)
head(wireNetData)
```

                  BSSID      First.time.seen       Last.time.seen channel Speed
    1 BE:F1:71:D5:17:8B  2023-07-28 09:13:03  2023-07-28 11:50:50       1   195
    2 6E:C7:EC:16:DA:1A  2023-07-28 09:13:03  2023-07-28 11:55:12       1   130
    3 9A:75:A8:B9:04:1E  2023-07-28 09:13:03  2023-07-28 11:53:31       1   360
    4 4A:EC:1E:DB:BF:95  2023-07-28 09:13:03  2023-07-28 11:04:01       7   360
    5 D2:6D:52:61:51:5D  2023-07-28 09:13:03  2023-07-28 10:30:19       6   130
    6 E8:28:C1:DC:B2:52  2023-07-28 09:13:03  2023-07-28 11:55:38       6   130
      Privacy Cipher Authentication Power X..beacons X..IV           LAN.IP
    1    WPA2   CCMP            PSK   -30        846   504    0.  0.  0.  0
    2    WPA2   CCMP            PSK   -30        750   116    0.  0.  0.  0
    3    WPA2   CCMP            PSK   -68        694    26    0.  0.  0.  0
    4    WPA2   CCMP            PSK   -37        510    21    0.  0.  0.  0
    5    WPA2   CCMP            PSK   -57        647     6    0.  0.  0.  0
    6     OPN                         -63        251  3430  172. 17.203.197
      ID.length           ESSID Key
    1        12    C322U13 3965  NA
    2         4            Cnet  NA
    3         2              KC  NA
    4        14  POCO X5 Pro 5G  NA
    5        25                  NA
    6        13   MIREA_HOTSPOT  NA

Получение данных о запросах на подключение клиентов к известным их
точкам доступа

``` r
requestData <- read.csv('./P2_wifi_data.csv', skip = 169)
head(requestData)
```

            Station.MAC      First.time.seen       Last.time.seen Power X..packets
    1 CA:66:3B:8F:56:DD  2023-07-28 09:13:03  2023-07-28 10:59:44   -33        858
    2 96:35:2D:3D:85:E6  2023-07-28 09:13:03  2023-07-28 09:13:03   -65          4
    3 5C:3A:45:9E:1A:7B  2023-07-28 09:13:03  2023-07-28 11:51:54   -39        432
    4 C0:E4:34:D8:E7:E5  2023-07-28 09:13:03  2023-07-28 11:53:16   -61        958
    5 5E:8E:A6:5E:34:81  2023-07-28 09:13:04  2023-07-28 09:13:04   -53          1
    6 10:51:07:CB:33:E7  2023-07-28 09:13:05  2023-07-28 11:56:06   -43        344
                   BSSID Probed.ESSIDs
    1  BE:F1:71:D5:17:8B  C322U13 3965
    2  (not associated)   IT2 Wireless
    3  BE:F1:71:D6:10:D7  C322U21 0566
    4  BE:F1:71:D5:17:8B  C322U13 3965
    5  (not associated)               
    6  (not associated)               

Далее производится преобразование датасетов в вид “аккуратных данных”

Для анонсов

``` r
wireNetData <- wireNetData %>% 
  mutate_at(vars(BSSID, Privacy, Cipher, Authentication, LAN.IP, ESSID), trimws) %>%
  mutate_at(vars(BSSID, Privacy, Cipher, Authentication, LAN.IP, ESSID), na_if, "")

wireNetData$First.time.seen <- as.POSIXct(wireNetData$First.time.seen, format = "%Y-%m-%d %H:%M:%S")
wireNetData$Last.time.seen <- as.POSIXct(wireNetData$Last.time.seen, format = "%Y-%m-%d %H:%M:%S")
```

Для запросов

``` r
requestData <- requestData %>% 
  mutate_at(vars(Station.MAC, BSSID, Probed.ESSIDs), trimws) %>%
  mutate_at(vars(Station.MAC, BSSID, Probed.ESSIDs), na_if, "")

requestData$First.time.seen <- as.POSIXct(requestData$First.time.seen, format = "%Y-%m-%d %H:%M:%S")
requestData$Last.time.seen <- as.POSIXct(requestData$Last.time.seen, format = "%Y-%m-%d %H:%M:%S")
```

Просмотр общей структуры данных

``` r
glimpse(wireNetData)
```

    Rows: 167
    Columns: 15
    $ BSSID           <chr> "BE:F1:71:D5:17:8B", "6E:C7:EC:16:DA:1A", "9A:75:A8:B9…
    $ First.time.seen <dttm> 2023-07-28 09:13:03, 2023-07-28 09:13:03, 2023-07-28 …
    $ Last.time.seen  <dttm> 2023-07-28 11:50:50, 2023-07-28 11:55:12, 2023-07-28 …
    $ channel         <int> 1, 1, 1, 7, 6, 6, 11, 11, 11, 1, 6, 14, 11, 11, 6, 6, …
    $ Speed           <int> 195, 130, 360, 360, 130, 130, 195, 130, 130, 195, 180,…
    $ Privacy         <chr> "WPA2", "WPA2", "WPA2", "WPA2", "WPA2", "OPN", "WPA2",…
    $ Cipher          <chr> "CCMP", "CCMP", "CCMP", "CCMP", "CCMP", NA, "CCMP", "C…
    $ Authentication  <chr> "PSK", "PSK", "PSK", "PSK", "PSK", NA, "PSK", "PSK", "…
    $ Power           <int> -30, -30, -68, -37, -57, -63, -27, -38, -38, -66, -42,…
    $ X..beacons      <int> 846, 750, 694, 510, 647, 251, 1647, 1251, 704, 617, 13…
    $ X..IV           <int> 504, 116, 26, 21, 6, 3430, 80, 11, 0, 0, 86, 0, 0, 0, …
    $ LAN.IP          <chr> "0.  0.  0.  0", "0.  0.  0.  0", "0.  0.  0.  0", "0.…
    $ ID.length       <int> 12, 4, 2, 14, 25, 13, 12, 13, 24, 12, 10, 0, 24, 24, 1…
    $ ESSID           <chr> "C322U13 3965", "Cnet", "KC", "POCO X5 Pro 5G", NA, "M…
    $ Key             <lgl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA…

``` r
glimpse(requestData)
```

    Rows: 12,269
    Columns: 7
    $ Station.MAC     <chr> "CA:66:3B:8F:56:DD", "96:35:2D:3D:85:E6", "5C:3A:45:9E…
    $ First.time.seen <dttm> 2023-07-28 09:13:03, 2023-07-28 09:13:03, 2023-07-28 …
    $ Last.time.seen  <dttm> 2023-07-28 10:59:44, 2023-07-28 09:13:03, 2023-07-28 …
    $ Power           <chr> " -33", " -65", " -39", " -61", " -53", " -43", " -31"…
    $ X..packets      <chr> "      858", "        4", "      432", "      958", " …
    $ BSSID           <chr> "BE:F1:71:D5:17:8B", "(not associated)", "BE:F1:71:D6:…
    $ Probed.ESSIDs   <chr> "C322U13 3965", "IT2 Wireless", "C322U21 0566", "C322U…

### Шаг 2

#### Точки доступа

**На этом шаге производится выполнение заданий по анализу данных**

1\. Определить небезопасные точки доступа (без шифрования – OPN)

``` r
allOpnId <- wireNetData %>% filter(Privacy == 'OPN') %>% select(BSSID)
head(unique(allOpnId))
```

                  BSSID
    1 E8:28:C1:DC:B2:52
    2 E8:28:C1:DC:B2:50
    3 E8:28:C1:DC:B2:51
    4 E8:28:C1:DC:FF:F2
    5 00:25:00:FF:94:73
    6 E8:28:C1:DD:04:52

2\. Определить производителя для каждого обнаруженного устройства

Для определния будет использоваться база данных производителей из
состава Wireshark

-   E8:28:C1 - Eltex Enterprise Ltd.

-   00:25:00 - Apple, Inc

-   E0:D9:E3 - Eltex Enterprise Ltd.

-   00:26:99 - Cisco Systems, Inc

-   00:03:7A - Taiyo Yuden Co., Ltd.

-   00:03:7F6 - Atheros Communications, Inc.

3\. Выявить устройства, использующие последнюю версию протокола
шифрования WPA3, и названия точек доступа, реализованных на этих
устройствах

``` r
wireNetData %>% filter(str_detect(wireNetData$Privacy, 'WPA3') == TRUE) %>% select(BSSID, ESSID, Privacy)
```

                  BSSID              ESSID   Privacy
    1 26:20:53:0C:98:E8               <NA> WPA3 WPA2
    2 A2:FE:FF:B8:9B:C9         Christie’s WPA3 WPA2
    3 96:FF:FC:91:EF:64               <NA> WPA3 WPA2
    4 CE:48:E7:86:4E:33 iPhone (Анастасия) WPA3 WPA2
    5 8E:1F:94:96:DA:FD iPhone (Анастасия) WPA3 WPA2
    6 BE:FD:EF:18:92:44            Димасик WPA3 WPA2
    7 3A:DA:00:F9:0C:02  iPhone XS Max 🦊🐱🦊 WPA3 WPA2
    8 76:C5:A0:70:08:96               <NA> WPA3 WPA2

4\. Отсортировать точки доступа по интрвалу времени, в течении которого
они находились на связи, по убыванию.

``` r
wireNetData %>% mutate(Time = difftime(Last.time.seen, First.time.seen, units = "mins")) %>% arrange(desc(Time)) %>% select(BSSID, Time)
```

                    BSSID              Time
    1   00:25:00:FF:94:73 163.25000000 mins
    2   E8:28:C1:DD:04:52 162.93333333 mins
    3   E8:28:C1:DC:B2:52 162.58333333 mins
    4   08:3A:2F:56:35:FE 162.43333333 mins
    5   6E:C7:EC:16:DA:1A 162.15000000 mins
    6   E8:28:C1:DC:B2:50 162.10000000 mins
    7   E8:28:C1:DC:B2:51 162.08333333 mins
    8   48:5B:39:F9:7A:48 162.08333333 mins
    9   E8:28:C1:DC:FF:F2 162.06666667 mins
    10  8E:55:4A:85:5B:01 162.05000000 mins
    11  00:26:99:BA:75:80 161.83333333 mins
    12  00:26:99:F2:7A:E2 161.78333333 mins
    13  1E:93:E3:1B:3C:F4 160.55000000 mins
    14  9A:75:A8:B9:04:1E 160.46666667 mins
    15  0C:80:63:A9:6E:EE 160.46666667 mins
    16  00:23:EB:E3:81:F2 159.91666667 mins
    17  9E:A3:A9:DB:7E:01 159.25000000 mins
    18  E8:28:C1:DC:C8:32 159.25000000 mins
    19  1C:7E:E5:8E:B7:DE 158.73333333 mins
    20  00:26:99:F2:7A:E1 158.20000000 mins
    21  BE:F1:71:D5:17:8B 157.78333333 mins
    22  BE:F1:71:D6:10:D7 157.68333333 mins
    23  9E:A3:A9:D6:28:3C 157.51666667 mins
    24  E8:28:C1:DD:04:40 156.66666667 mins
    25  E8:28:C1:DD:04:41 156.66666667 mins
    26  00:23:EB:E3:81:F1 155.80000000 mins
    27  00:23:EB:E3:81:FE 155.08333333 mins
    28  00:23:EB:E3:81:FD 155.08333333 mins
    29  9E:A3:A9:BF:12:C0 154.50000000 mins
    30  E8:28:C1:DC:B2:40 153.53333333 mins
    31  AA:F4:3F:EE:49:0B 150.75000000 mins
    32  E8:28:C1:DE:47:D2 150.68333333 mins
    33  E8:28:C1:DD:04:50 149.81666667 mins
    34  14:EB:B6:6A:76:37 148.58333333 mins
    35  56:99:98:EE:5A:4E 146.85000000 mins
    36  E8:28:C1:DC:B2:42 144.88333333 mins
    37  38:1A:52:0D:90:A1 144.35000000 mins
    38  0A:C5:E1:DB:17:7B 143.46666667 mins
    39  E8:28:C1:DC:C8:30 140.75000000 mins
    40  E8:28:C1:DC:C6:B1 139.83333333 mins
    41  E8:28:C1:DD:04:42 138.63333333 mins
    42  E8:28:C1:DC:B2:41 138.45000000 mins
    43  12:51:07:FF:29:D6 124.71666667 mins
    44  CE:B3:FF:84:45:FC 121.18333333 mins
    45  E8:28:C1:DC:C8:31 119.98333333 mins
    46  E8:28:C1:DC:C6:B2 113.65000000 mins
    47  4A:EC:1E:DB:BF:95 110.96666667 mins
    48  00:26:99:F2:7A:E0 103.63333333 mins
    49  E8:28:C1:DD:04:51  94.05000000 mins
    50  E0:D9:E3:48:FF:D2  93.73333333 mins
    51  00:AB:0A:00:10:10  89.26666667 mins
    52  E8:28:C1:DE:74:32  86.50000000 mins
    53  10:50:72:00:11:08  83.28333333 mins
    54  EA:D8:D1:77:C8:08  83.25000000 mins
    55  D2:6D:52:61:51:5D  77.26666667 mins
    56  E0:D9:E3:49:04:52  76.90000000 mins
    57  7E:3A:10:A7:59:4E  76.85000000 mins
    58  BE:F1:71:D5:0E:53  76.30000000 mins
    59  A6:02:B9:73:83:18  76.28333333 mins
    60  9A:9F:06:44:24:5B  76.20000000 mins
    61  E8:28:C1:DE:74:31  73.88333333 mins
    62  92:F5:7B:43:0B:69  73.20000000 mins
    63  E8:28:C1:DC:3C:92  72.18333333 mins
    64  38:1A:52:0D:84:D7  71.98333333 mins
    65  38:1A:52:0D:90:5D  70.91666667 mins
    66  A2:64:E8:97:58:EE  70.86666667 mins
    67  A6:02:B9:73:81:47  70.40000000 mins
    68  56:C5:2B:9F:84:90  69.55000000 mins
    69  A6:02:B9:73:2F:76  69.06666667 mins
    70  38:1A:52:0D:97:60  68.10000000 mins
    71  0A:24:D8:D9:24:70  67.85000000 mins
    72  E8:28:C1:DC:C6:B0  64.65000000 mins
    73  8A:A3:03:73:52:08  57.51666667 mins
    74  5E:C7:C0:E4:D7:D4  54.41666667 mins
    75  E8:28:C1:DC:54:72  51.23333333 mins
    76  4A:86:77:04:B7:28  50.13333333 mins
    77  B6:C4:55:B5:53:24  49.78333333 mins
    78  E8:28:C1:DC:BD:50  45.71666667 mins
    79  76:70:AF:A4:D2:AF  45.55000000 mins
    80  86:DF:BF:E4:2F:23  44.80000000 mins
    81  38:1A:52:0D:8F:EC  43.91666667 mins
    82  EA:7B:9B:D8:56:34  37.35000000 mins
    83  38:1A:52:0D:85:1D  34.70000000 mins
    84  00:26:CB:AA:62:71  32.81666667 mins
    85  96:FF:FC:91:EF:64  32.13333333 mins
    86  E8:28:C1:DC:33:12  22.98333333 mins
    87  E8:28:C1:DC:F0:90  21.86666667 mins
    88  3A:70:96:C6:30:2C  21.66666667 mins
    89  36:46:53:81:12:A0  20.80000000 mins
    90  CE:C3:F7:A4:7E:B3  20.40000000 mins
    91  26:20:53:0C:98:E8  17.41666667 mins
    92  92:12:38:E5:7E:1E  14.46666667 mins
    93  E8:28:C1:DC:33:10  14.10000000 mins
    94  E8:28:C1:DB:F5:F0  14.03333333 mins
    95  E8:28:C1:DC:0B:B0  13.86666667 mins
    96  E8:28:C1:DB:F5:F2  13.03333333 mins
    97  02:67:F1:B0:6C:98  10.85000000 mins
    98  E8:28:C1:DE:74:30   8.46666667 mins
    99  1E:C2:8E:D8:30:91   8.30000000 mins
    100 8E:1F:94:96:DA:FD   6.91666667 mins
    101 E0:D9:E3:49:04:50   6.68333333 mins
    102 CE:48:E7:86:4E:33   4.91666667 mins
    103 00:26:99:BA:75:8F   4.80000000 mins
    104 2A:E8:A2:02:01:73   3.66666667 mins
    105 2E:FE:13:D0:96:51   0.96666667 mins
    106 9C:A5:13:28:D5:89   0.71666667 mins
    107 22:C9:7F:A9:BA:9C   0.68333333 mins
    108 E8:28:C1:DC:54:B0   0.60000000 mins
    109 D2:25:91:F6:6C:D8   0.21666667 mins
    110 3A:DA:00:F9:0C:02   0.15000000 mins
    111 E8:28:C1:DB:FC:F2   0.15000000 mins
    112 DC:09:4C:32:34:9B   0.13333333 mins
    113 F2:30:AB:E9:03:ED   0.11666667 mins
    114 E0:D9:E3:49:04:40   0.11666667 mins
    115 00:03:7A:1A:03:56   0.10000000 mins
    116 B2:CF:C0:00:4A:60   0.08333333 mins
    117 BE:FD:EF:18:92:44   0.06666667 mins
    118 02:BC:15:7E:D5:DC   0.03333333 mins
    119 00:23:EB:E3:49:31   0.03333333 mins
    120 00:3E:1A:5D:14:45   0.03333333 mins
    121 76:C5:A0:70:08:96   0.03333333 mins
    122 82:CD:7D:04:17:3B   0.03333333 mins
    123 E0:D9:E3:49:00:B0   0.01666667 mins
    124 E8:28:C1:DC:54:B2   0.01666667 mins
    125 C6:BC:37:7A:67:0D   0.00000000 mins
    126 12:48:F9:CF:58:8E   0.00000000 mins
    127 76:E4:ED:B0:5C:9A   0.00000000 mins
    128 E0:D9:E3:48:FF:D0   0.00000000 mins
    129 E2:37:BF:8F:6A:7B   0.00000000 mins
    130 C2:B5:D7:7F:07:A8   0.00000000 mins
    131 8A:4E:75:44:5A:F6   0.00000000 mins
    132 00:03:7A:1A:18:56   0.00000000 mins
    133 E8:28:C1:DE:47:D1   0.00000000 mins
    134 A2:FE:FF:B8:9B:C9   0.00000000 mins
    135 00:09:9A:12:55:04   0.00000000 mins
    136 E8:28:C1:DC:3A:B0   0.00000000 mins
    137 E8:28:C1:DC:0B:B2   0.00000000 mins
    138 E8:28:C1:DC:3C:80   0.00000000 mins
    139 00:23:EB:E3:44:31   0.00000000 mins
    140 A6:F7:05:31:E8:EE   0.00000000 mins
    141 BA:2A:7A:DD:38:3E   0.00000000 mins
    142 12:54:1A:C6:FF:71   0.00000000 mins
    143 76:5E:F3:F9:A5:1C   0.00000000 mins
    144 00:03:7F:12:34:56   0.00000000 mins
    145 E8:28:C1:DC:03:30   0.00000000 mins
    146 B2:1B:0C:67:0A:BD   0.00000000 mins
    147 E0:D9:E3:49:00:B1   0.00000000 mins
    148 E8:28:C1:DC:BD:52   0.00000000 mins
    149 E8:28:C1:DE:72:D0   0.00000000 mins
    150 E0:D9:E3:49:04:41   0.00000000 mins
    151 00:26:99:F1:1A:E1   0.00000000 mins
    152 00:23:EB:E3:44:32   0.00000000 mins
    153 00:26:CB:AA:62:72   0.00000000 mins
    154 E0:D9:E3:48:B4:D2   0.00000000 mins
    155 AE:3E:7F:C8:BC:8E   0.00000000 mins
    156 02:B3:45:5A:05:93   0.00000000 mins
    157 00:00:00:00:00:00   0.00000000 mins
    158 6A:B0:1A:C2:DF:49   0.00000000 mins
    159 E8:28:C1:DC:3C:90   0.00000000 mins
    160 30:B4:B8:11:C0:90   0.00000000 mins
    161 00:26:99:F2:7A:EF   0.00000000 mins
    162 02:CF:8B:87:B4:F9   0.00000000 mins
    163 E8:28:C1:DC:03:32   0.00000000 mins
    164 00:53:7A:99:98:56   0.00000000 mins
    165 00:03:7F:10:17:56   0.00000000 mins
    166 00:0D:97:6B:93:DF   0.00000000 mins
    167 E8:28:C1:DE:47:D0   0.00000000 mins

``` r
head(wireNetData)
```

                  BSSID     First.time.seen      Last.time.seen channel Speed
    1 BE:F1:71:D5:17:8B 2023-07-28 09:13:03 2023-07-28 11:50:50       1   195
    2 6E:C7:EC:16:DA:1A 2023-07-28 09:13:03 2023-07-28 11:55:12       1   130
    3 9A:75:A8:B9:04:1E 2023-07-28 09:13:03 2023-07-28 11:53:31       1   360
    4 4A:EC:1E:DB:BF:95 2023-07-28 09:13:03 2023-07-28 11:04:01       7   360
    5 D2:6D:52:61:51:5D 2023-07-28 09:13:03 2023-07-28 10:30:19       6   130
    6 E8:28:C1:DC:B2:52 2023-07-28 09:13:03 2023-07-28 11:55:38       6   130
      Privacy Cipher Authentication Power X..beacons X..IV          LAN.IP
    1    WPA2   CCMP            PSK   -30        846   504   0.  0.  0.  0
    2    WPA2   CCMP            PSK   -30        750   116   0.  0.  0.  0
    3    WPA2   CCMP            PSK   -68        694    26   0.  0.  0.  0
    4    WPA2   CCMP            PSK   -37        510    21   0.  0.  0.  0
    5    WPA2   CCMP            PSK   -57        647     6   0.  0.  0.  0
    6     OPN   <NA>           <NA>   -63        251  3430 172. 17.203.197
      ID.length          ESSID Key
    1        12   C322U13 3965  NA
    2         4           Cnet  NA
    3         2             KC  NA
    4        14 POCO X5 Pro 5G  NA
    5        25           <NA>  NA
    6        13  MIREA_HOTSPOT  NA

5\. Обнаружить топ-10 самых быстрых точек доступа

``` r
wireNetData %>% arrange(desc(Speed)) %>% head(10) %>% select(BSSID, Speed)
```

                   BSSID Speed
    1  26:20:53:0C:98:E8   866
    2  96:FF:FC:91:EF:64   866
    3  CE:48:E7:86:4E:33   866
    4  8E:1F:94:96:DA:FD   866
    5  9A:75:A8:B9:04:1E   360
    6  4A:EC:1E:DB:BF:95   360
    7  56:C5:2B:9F:84:90   360
    8  E8:28:C1:DC:B2:41   360
    9  E8:28:C1:DC:B2:40   360
    10 E8:28:C1:DC:B2:42   360

6\. Отсортировать точки доступа по частоте отправки запросов в единицу
времени по их убыванию

``` r
wireNetData %>% mutate(Time = difftime(Last.time.seen, First.time.seen, units = "sec")) %>% filter(!is.na(Time)) %>% filter(Time != 0) %>% filter(X..beacons != 0) %>% select(BSSID, X..beacons, Time) %>% mutate(Beacon_sec = X..beacons / as.integer(Time)) %>% arrange(desc(Beacon_sec))
```

                   BSSID X..beacons      Time   Beacon_sec
    1  F2:30:AB:E9:03:ED          6    7 secs 0.8571428571
    2  B2:CF:C0:00:4A:60          4    5 secs 0.8000000000
    3  3A:DA:00:F9:0C:02          5    9 secs 0.5555555556
    4  02:BC:15:7E:D5:DC          1    2 secs 0.5000000000
    5  00:3E:1A:5D:14:45          1    2 secs 0.5000000000
    6  76:C5:A0:70:08:96          1    2 secs 0.5000000000
    7  D2:25:91:F6:6C:D8          5   13 secs 0.3846153846
    8  BE:F1:71:D6:10:D7       1647 9461 secs 0.1740830779
    9  00:03:7A:1A:03:56          1    6 secs 0.1666666667
    10 38:1A:52:0D:84:D7        704 4319 secs 0.1630006946
    11 0A:C5:E1:DB:17:7B       1251 8608 secs 0.1453299257
    12 1E:93:E3:1B:3C:F4       1390 9633 secs 0.1442956504
    13 D2:6D:52:61:51:5D        647 4636 secs 0.1395599655
    14 BE:F1:71:D5:0E:53        617 4578 secs 0.1347750109
    15 4A:86:77:04:B7:28        361 3008 secs 0.1200132979
    16 3A:70:96:C6:30:2C        145 1300 secs 0.1115384615
    17 76:70:AF:A4:D2:AF        253 2733 secs 0.0925722649
    18 BE:F1:71:D5:17:8B        846 9467 secs 0.0893630506
    19 AA:F4:3F:EE:49:0B        738 9045 secs 0.0815920398
    20 6E:C7:EC:16:DA:1A        750 9729 secs 0.0770891150
    21 4A:EC:1E:DB:BF:95        510 6658 secs 0.0765995795
    22 56:C5:2B:9F:84:90        317 4173 secs 0.0759645339
    23 9A:75:A8:B9:04:1E        694 9628 secs 0.0720814292
    24 9C:A5:13:28:D5:89          3   43 secs 0.0697674419
    25 36:46:53:81:12:A0         82 1248 secs 0.0657051282
    26 38:1A:52:0D:85:1D        130 2082 secs 0.0624399616
    27 38:1A:52:0D:8F:EC        107 2635 secs 0.0406072106
    28 2E:FE:13:D0:96:51          2   58 secs 0.0344827586
    29 CE:48:E7:86:4E:33          9  295 secs 0.0305084746
    30 8E:1F:94:96:DA:FD         12  415 secs 0.0289156627
    31 E8:28:C1:DC:B2:51        279 9725 secs 0.0286889460
    32 E8:28:C1:DC:B2:50        260 9726 secs 0.0267324697
    33 5E:C7:C0:E4:D7:D4         85 3265 secs 0.0260336907
    34 E8:28:C1:DC:B2:52        251 9755 secs 0.0257303947
    35 8E:55:4A:85:5B:01        248 9723 secs 0.0255065309
    36 38:1A:52:0D:90:5D         90 4255 secs 0.0211515864
    37 1C:7E:E5:8E:B7:DE        142 9524 secs 0.0149097018
    38 38:1A:52:0D:90:A1        112 8661 secs 0.0129315322
    39 A2:64:E8:97:58:EE         52 4252 secs 0.0122295390
    40 1E:C2:8E:D8:30:91          6  498 secs 0.0120481928
    41 48:5B:39:F9:7A:48        109 9725 secs 0.0112082262
    42 00:26:99:F2:7A:E2         84 9707 secs 0.0086535490
    43 38:1A:52:0D:97:60         28 4086 secs 0.0068526676
    44 00:26:99:F2:7A:E1         65 9492 secs 0.0068478719
    45 00:26:99:BA:75:80         61 9710 secs 0.0062821833
    46 A6:02:B9:73:2F:76         26 4144 secs 0.0062741313
    47 9E:A3:A9:D6:28:3C         51 9451 secs 0.0053962544
    48 00:23:EB:E3:81:FE         47 9305 secs 0.0050510478
    49 00:23:EB:E3:81:FD         46 9305 secs 0.0049435787
    50 9A:9F:06:44:24:5B         22 4572 secs 0.0048118985
    51 96:FF:FC:91:EF:64          9 1928 secs 0.0046680498
    52 A6:02:B9:73:81:47         19 4224 secs 0.0044981061
    53 0C:80:63:A9:6E:EE         42 9628 secs 0.0043622767
    54 12:51:07:FF:29:D6         32 7483 secs 0.0042763597
    55 9E:A3:A9:DB:7E:01         40 9555 secs 0.0041862899
    56 92:F5:7B:43:0B:69         18 4392 secs 0.0040983607
    57 86:DF:BF:E4:2F:23         11 2688 secs 0.0040922619
    58 A6:02:B9:73:83:18         17 4577 secs 0.0037142233
    59 E8:28:C1:DD:04:40         30 9400 secs 0.0031914894
    60 26:20:53:0C:98:E8          3 1045 secs 0.0028708134
    61 E8:28:C1:DD:04:42         23 8318 secs 0.0027650878
    62 E8:28:C1:DD:04:41         25 9400 secs 0.0026595745
    63 B6:C4:55:B5:53:24          7 2987 secs 0.0023434884
    64 E8:28:C1:DD:04:50         20 8989 secs 0.0022249416
    65 00:23:EB:E3:81:F1         19 9348 secs 0.0020325203
    66 E8:28:C1:DC:BD:50          5 2743 secs 0.0018228217
    67 E8:28:C1:DD:04:51          9 5643 secs 0.0015948963
    68 02:67:F1:B0:6C:98          1  651 secs 0.0015360983
    69 E8:28:C1:DC:C8:32         12 9555 secs 0.0012558870
    70 E8:28:C1:DC:C8:31          8 7199 secs 0.0011112655
    71 E8:28:C1:DC:C6:B0          4 3879 secs 0.0010311936
    72 00:26:CB:AA:62:71          2 1969 secs 0.0010157440
    73 9E:A3:A9:BF:12:C0          9 9270 secs 0.0009708738
    74 E8:28:C1:DC:C8:30          7 8445 secs 0.0008288928
    75 00:23:EB:E3:81:F2          7 9595 secs 0.0007295466
    76 7E:3A:10:A7:59:4E          3 4611 secs 0.0006506181
    77 E8:28:C1:DC:B2:41          5 8307 secs 0.0006019020
    78 E8:28:C1:DC:C6:B1          5 8390 secs 0.0005959476
    79 E8:28:C1:DC:B2:42          5 8693 secs 0.0005751754
    80 E8:28:C1:DC:B2:40          5 9212 secs 0.0005427703
    81 0A:24:D8:D9:24:70          2 4071 secs 0.0004912798
    82 E8:28:C1:DE:74:31          2 4433 secs 0.0004511617
    83 EA:7B:9B:D8:56:34          1 2241 secs 0.0004462294
    84 E8:28:C1:DD:04:52          4 9776 secs 0.0004091653
    85 10:50:72:00:11:08          2 4997 secs 0.0004002401
    86 E8:28:C1:DE:47:D2          3 9041 secs 0.0003318217
    87 EA:D8:D1:77:C8:08          1 4995 secs 0.0002002002
    88 E8:28:C1:DE:74:32          1 5190 secs 0.0001926782
    89 56:99:98:EE:5A:4E          1 8811 secs 0.0001134945

``` r
head(wireNetData)
```

                  BSSID     First.time.seen      Last.time.seen channel Speed
    1 BE:F1:71:D5:17:8B 2023-07-28 09:13:03 2023-07-28 11:50:50       1   195
    2 6E:C7:EC:16:DA:1A 2023-07-28 09:13:03 2023-07-28 11:55:12       1   130
    3 9A:75:A8:B9:04:1E 2023-07-28 09:13:03 2023-07-28 11:53:31       1   360
    4 4A:EC:1E:DB:BF:95 2023-07-28 09:13:03 2023-07-28 11:04:01       7   360
    5 D2:6D:52:61:51:5D 2023-07-28 09:13:03 2023-07-28 10:30:19       6   130
    6 E8:28:C1:DC:B2:52 2023-07-28 09:13:03 2023-07-28 11:55:38       6   130
      Privacy Cipher Authentication Power X..beacons X..IV          LAN.IP
    1    WPA2   CCMP            PSK   -30        846   504   0.  0.  0.  0
    2    WPA2   CCMP            PSK   -30        750   116   0.  0.  0.  0
    3    WPA2   CCMP            PSK   -68        694    26   0.  0.  0.  0
    4    WPA2   CCMP            PSK   -37        510    21   0.  0.  0.  0
    5    WPA2   CCMP            PSK   -57        647     6   0.  0.  0.  0
    6     OPN   <NA>           <NA>   -63        251  3430 172. 17.203.197
      ID.length          ESSID Key
    1        12   C322U13 3965  NA
    2         4           Cnet  NA
    3         2             KC  NA
    4        14 POCO X5 Pro 5G  NA
    5        25           <NA>  NA
    6        13  MIREA_HOTSPOT  NA

#### Данные клиентов

1\. Определить производителя для каждого обнаруженного устройства

Используется база данных производителей из состава Wireshark

``` r
allDecices <- requestData %>% filter(BSSID != '(not associated)') %>% select(BSSID)
unique(allDecices)
```

                    BSSID
    1   BE:F1:71:D5:17:8B
    2   BE:F1:71:D6:10:D7
    4   1E:93:E3:1B:3C:F4
    5   E8:28:C1:DC:FF:F2
    6   00:25:00:FF:94:73
    7   00:26:99:F2:7A:E2
    8   0C:80:63:A9:6E:EE
    9   E8:28:C1:DD:04:52
    10  0A:C5:E1:DB:17:7B
    12  9A:75:A8:B9:04:1E
    13  8A:A3:03:73:52:08
    14  4A:EC:1E:DB:BF:95
    15  BE:F1:71:D5:0E:53
    16  08:3A:2F:56:35:FE
    17  6E:C7:EC:16:DA:1A
    21  2A:E8:A2:02:01:73
    22  E8:28:C1:DC:B2:52
    23  E8:28:C1:DC:C6:B2
    27  E8:28:C1:DC:C8:32
    28  56:C5:2B:9F:84:90
    30  9A:9F:06:44:24:5B
    31  12:48:F9:CF:58:8E
    33  E8:28:C1:DD:04:50
    35  AA:F4:3F:EE:49:0B
    37  3A:70:96:C6:30:2C
    39  E8:28:C1:DC:3C:92
    42  8E:55:4A:85:5B:01
    43  5E:C7:C0:E4:D7:D4
    44  E2:37:BF:8F:6A:7B
    48  96:FF:FC:91:EF:64
    50  CE:B3:FF:84:45:FC
    55  00:26:99:BA:75:80
    58  76:70:AF:A4:D2:AF
    59  E8:28:C1:DC:B2:50
    60  00:AB:0A:00:10:10
    61  E8:28:C1:DC:C8:30
    64      MIREA_HOTSPOT
    66  8E:1F:94:96:DA:FD
    70  E8:28:C1:DB:F5:F2
    76  E8:28:C1:DD:04:40
    78  EA:7B:9B:D8:56:34
    79  BE:FD:EF:18:92:44
    81  7E:3A:10:A7:59:4E
    82  00:26:99:F2:7A:E1
    83  00:23:EB:E3:49:31
    86  E8:28:C1:DC:B2:40
    87  E0:D9:E3:49:04:40
    88  3A:DA:00:F9:0C:02
    91  E8:28:C1:DC:B2:41
    92  E8:28:C1:DE:74:32
    97  E8:28:C1:DC:33:12
    100 92:F5:7B:43:0B:69
    103 AndroidShare_6329
    104 DC:09:4C:32:34:9B
    105 E8:28:C1:DC:F0:90
    107 E0:D9:E3:49:04:52
    110 22:C9:7F:A9:BA:9C
    111 E8:28:C1:DD:04:41
    112   TP-Link_9872_5G
    117 92:12:38:E5:7E:1E
    120 B2:1B:0C:67:0A:BD
    126 E8:28:C1:DC:33:10
    129 E0:D9:E3:49:04:41
    134 1E:C2:8E:D8:30:91
    136 A2:64:E8:97:58:EE
    138 A6:02:B9:73:2F:76
    140 A6:02:B9:73:81:47
    150 AE:3E:7F:C8:BC:8E
    158 B6:C4:55:B5:53:24
    161 86:DF:BF:E4:2F:23
    163 02:67:F1:B0:6C:98
    169 36:46:53:81:12:A0
    175 E8:28:C1:DC:0B:B0
    176 82:CD:7D:04:17:3B
    178 E8:28:C1:DC:54:B2
    182 00:03:7F:10:17:56
    183 00:0D:97:6B:93:DF

-   00:25:00 Apple, Inc.

-   00:03:7F Atheros Communications, Inc.

-   00:23:EB Cisco Systems, Inc

-   00:0D:97 Hitachi Energy USA Inc.

-   08:3A:2F Guangzhou Juan Intelligent Tech Joint Stock Co.,Ltd

-   E0:D9:E3 Eltex Enterprise Ltd.

-   DC:09:4C Huawei Technologies Co.,Ltd

-   E8:28:C1 Eltex Enterprise Ltd.

-   0C:80:63 Tp-Link Technologies Co.,Ltd.

-   00:26:99 Cisco Systems, Inc

2\. Обнаружить устройства, которые НЕ рандомизируют свой MAC адрес

``` r
result <- requestData %>% filter(grepl("(.2:..:..:)(..:..:..)", Station.MAC)!=TRUE & grepl("(.6:..:..:)(..:..:..)", Station.MAC)!=TRUE & grepl("(.A:..:..:)(..:..:..)", Station.MAC)!=TRUE & grepl("(.E:..:..:)(..:..:..)", Station.MAC)!=TRUE) %>%  select(Station.MAC)

head(result)
```

            Station.MAC
    1 5C:3A:45:9E:1A:7B
    2 C0:E4:34:D8:E7:E5
    3 10:51:07:CB:33:E7
    4 68:54:5A:40:35:9E
    5        Galaxy A71
    6 74:4C:A1:70:CE:F7

3\. Кластеризовать запросы от устройств к точкам доступа по их именам.
Определить время появления устройства в зоне радиовидимости и время
выхода его из нее.

``` r
result1 <- requestData %>% filter(!is.na(Probed.ESSIDs)) %>% group_by(Probed.ESSIDs) %>%  summarise("Появление" = min(First.time.seen), "Выход" = max(Last.time.seen))

head(result1)
```

    # A tibble: 6 × 3
      Probed.ESSIDs            Появление           Выход              
      <chr>                    <dttm>              <dttm>             
    1 -D-13-                   2023-07-28 09:14:42 2023-07-28 10:26:42
    2 1                        2023-07-28 10:36:12 2023-07-28 11:56:13
    3 107                      2023-07-28 10:29:43 2023-07-28 10:29:43
    4 531                      2023-07-28 10:57:04 2023-07-28 10:57:04
    5 AAAAAOB/CC0ADwGkRedmi 3S 2023-07-28 09:34:20 2023-07-28 11:44:40
    6 AKADO-D967               2023-07-28 10:31:55 2023-07-28 10:31:55

4\. Оценить стабильность уровня сигнала внури кластера во времени.
Выявить наиболее стабильный кластер

``` r
result2 <- requestData %>% mutate(Time = difftime(Last.time.seen, First.time.seen, units = "sec")) %>% filter(as.integer(Time) != 0) %>% arrange(desc(as.integer(Time))) %>% filter(!is.na(Probed.ESSIDs)) %>% group_by(Probed.ESSIDs) %>% summarise(Mean = mean(as.integer(Time)), Sd = sd(as.integer(Time))) %>% filter(!is.na(Sd)) %>% filter(Sd != 0) %>% arrange(Sd) %>% head(1)

result2
```

    # A tibble: 1 × 3
      Probed.ESSIDs  Mean    Sd
      <chr>         <dbl> <dbl>
    1 nvripcsuite    9780  3.46

## Оценка результатов

1.  Были успешно получены данные из csv-файла и преобразованы в вид
    “аккуратных данных”
2.  Был произведен анализ полученных данных, используя функционал
    tidyverse

## Вывод

В результате выполнения работы были получены знания о методах
исследования радиоэлектронной обстановки и о механизмах работы Wi-Fi
сетей на канальном и сетевом уровне модели OSI. Так же закреплены
практические навыки использования языка R для обработки данных и
основные функции обработки данных экосистемы tidyverse
