

# Практическое задание 7

Анализ данных сетевого трафика при помощи библиотеки Arrow

## Цель

1.  Изучить возможности технологии Apache Arrow для обработки и анализа
    больших данных
2.  Получить навыки применения Arrow совместно с языком программирования
    R
3.  Получить навыки анализа метаинформации о сетевом трафике
4.  Получить навыки применения облачных технологий хранения, подготовки
    и анализа данных: Yandex Object Storage, Rstudio Server

## Исходные даннные

1.  Персональный компьютер

2.  Браузер

3.  R studio

4.  Библиотека Arrow

## Общий план выполнения

1.  Импорт данных
2.  Выполнение заданий
3.  Подготовить отчёт

## Содержание ПР

### Шаг 1

**На данном шаге производится импорт данных**

Скачивание файла с данными

``` r
#download.file('https://storage.yandexcloud.net/arrow-datasets/tm_data.pqt', destfile = "tm_data.pqt")
```

Применение функции read_parquet пакета arrow

``` r
library(arrow)
```

    Warning: пакет 'arrow' был собран под R версии 4.4.2


    Присоединяю пакет: 'arrow'

    Следующий объект скрыт от 'package:utils':

        timestamp

``` r
df <- read_parquet("tm_data.pqt", use_threads=False)
```

### Шаг 2

**На данном шаге производится выполнение заданий**

#### Задание 1. Найдите утечку данных из Вашей сети

Важнейшие документы с результатами нашей исследовательской деятельности
в области создания вакцин скачиваются в виде больших заархивированных
дампов. Один из хостов в нашей сети используется для пересылки этой
информации – он пересылает гораздо больше информации на внешние ресурсы
в Интернете, чем остальные компьютеры нашей сети. Определите его
IP-адрес.

Из условия:

-   12-14 - ip-адреса внутренней сети

-   Все остальные - ip-адреса внешней сети

``` r
library(dplyr)
```


    Присоединяю пакет: 'dplyr'

    Следующие объекты скрыты от 'package:stats':

        filter, lag

    Следующие объекты скрыты от 'package:base':

        intersect, setdiff, setequal, union

``` r
library(tidyverse)
```

    ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ✔ forcats   1.0.0     ✔ readr     2.1.5
    ✔ ggplot2   3.5.1     ✔ stringr   1.5.1
    ✔ lubridate 1.9.3     ✔ tibble    3.2.1
    ✔ purrr     1.0.2     ✔ tidyr     1.3.1
    ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ✖ lubridate::duration() masks arrow::duration()
    ✖ dplyr::filter()       masks stats::filter()
    ✖ dplyr::lag()          masks stats::lag()
    ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

Фильтрация по внутренней сети (ip-адреса начинаются с 12, 13 или 14)

``` r
internal_traffic <- df %>%
  filter(grepl("^12\\.|^13\\.|^14\\.", src))
```

Группировка данных, суммирование объема переданных данных, сортировка по
убыванию

``` r
summary_traffic <- internal_traffic %>%
  group_by(src) %>%
  summarise(total_bytes_sent = sum(bytes, na.rm = TRUE)) %>%
  arrange(desc(total_bytes_sent))
```

Выбор самого первого ip-адреса (по трафику наибольший)

``` r
top_ip <- head(summary_traffic, 1)
```

Вывод

``` r
top_ip
```

    # A tibble: 1 × 2
      src          total_bytes_sent
      <chr>                   <dbl>
    1 13.37.84.125      11152202376

Ответ: 13.37.84.125

#### Задание 2. Найдите утечку данных 2

Другой атакующий установил автоматическую задачу в системном
планировщике сron для экспорта содержимого внутренней wiki системы. Эта
система генерирует большое количество трафика в нерабочие часы, больше
чем остальные хосты. Определите IP этой системы. Известно, что ее IP
адрес отличается от нарушителя из предыдущей задачи.

``` r
hourly_traffic <- df%>%select(timestamp, src, dst, bytes)%>%mutate(trafic=grepl("^12.|^13.|^14.", src) & !grepl("^12.|^13.|^14.",dst),time=hour(as_datetime(timestamp/1000))) %>%filter(trafic==TRUE,time>=0&time<=24)%>% group_by(time)%>%summarise(trafictime=n())%>%arrange(desc(time))
```

``` r
print(hourly_traffic)
```

    # A tibble: 24 × 2
        time trafictime
       <int>      <int>
     1    23    4488093
     2    22    4489703
     3    21    4487109
     4    20    4482712
     5    19    4487345
     6    18    4489386
     7    17    4483578
     8    16    4490576
     9    15     168355
    10    14     169028
    # ℹ 14 more rows

Из таблицы выше - предполагаемые рабочие часы: 16 - 23, нерабочие: 1-15

``` r
traffic_noWork <- df %>% mutate(
   time=hour(as_datetime(timestamp/1000))
  ) %>%
  filter(
    time >= 1 & time <= 15,
    grepl("^(12|13|14)\\.", src),
    src != '13.37.84.125'
  ) %>%
  group_by(src) %>%
  summarise(
    total_bytes = sum(bytes)
  ) %>%
  arrange(desc(total_bytes))
```

Вывод ip-адреса системы

``` r
print(head(traffic_noWork, 1))
```

    # A tibble: 1 × 2
      src         total_bytes
      <chr>             <int>
    1 12.55.77.96   298669501

Ответ: 12.55.77.96

#### Задание 3. Найдите утечку данных из Вашей сети 3

Еще один нарушитель собирает содержимое электронной почты и отправляет в
Интернет используя порт, который обычно используется для другого типа
трафика. Атакующий пересылает большое количество информации используя
этот порт, которое нехарактерно для других хостов, использующих этот
номер порта. Определите IP этой системы. Известно, что ее IP адрес
отличается от нарушителей из предыдущих задач.

``` r
result <- df %>%
  filter(grepl("^(12|13|14)\\.", src), src != "13.37.84.125", src != "12.55.77.96") %>%
  group_by(port) %>%
  # Средний объем трафика для каждого порта
  mutate(port_avg_bytes = mean(bytes)) %>%
  group_by(port, src) %>%
  summarise(
    total_bytes = sum(bytes),
    port_avg = first(port_avg_bytes),
    # Во сколько раз трафик превышает средний по порту
    ratio = total_bytes / port_avg,
    .groups = 'drop'
  ) %>%
  arrange(desc(ratio))
```

``` r
print(head(result, 1))
```

    # A tibble: 1 × 5
       port src         total_bytes port_avg ratio
      <int> <chr>             <int>    <dbl> <dbl>
    1    83 13.39.46.94     9077865    1000. 9082.

Ответ: 13.39.46.94

## Оценка результатов

Был произведен анализ данных сетевого трафика при помощи библиотеки
Arrow

## Вывод

1.  Были изучены возможности технологии Apache Arrow для обработки и
    анализа больших данных
2.  Получены навыки применения Arrow совместно с языком программирования
    R
3.  Получены навыки анализа метаинформации о сетевом трафике
4.  Получены навыки применения облачных технологий хранения, подготовки
    и анализа данных: Yandex Object Storage, Rstudio Server
