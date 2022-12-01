# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #5 выполнил(а):
- Худорожкова Екатерина Дмитриевна
- РИ-210933
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | * | 20 |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

Структура отчета

- Данные о работе: название работы, фио, группа, выполненные задания.
- Цель работы.
- Задание 1.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 2.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 3.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Выводы.
- ✨Magic ✨

## Цель работы
Интеграция экономической системы в проект Unity и обучение ML-Agent.

## Задание 1
### Интегрировать экономическую систему в проект Unity и обучить ML-Agent.
Откроем файл в Unity
![1](https://user-images.githubusercontent.com/112847807/205122925-78584ca0-a85d-4fe4-9150-dd3e6863a04e.png)

Создадим виртуальное пространство и установим все требуемые библиотеки.
![2](https://user-images.githubusercontent.com/112847807/205123172-084f2c43-9b81-4637-b7ee-82972732bfad.png)

![3](https://user-images.githubusercontent.com/112847807/205123236-bb5f302a-9a28-401a-81a8-a1b906db6dce.png)

![4](https://user-images.githubusercontent.com/112847807/205123279-19dabbad-7887-4bbd-aadb-cdfb6993f1ed.png)

![5](https://user-images.githubusercontent.com/112847807/205123353-85e254e2-4e82-4487-b44f-117b3f91b0d0.png)

Увеличим количество TargetAreaEconomic до 16 и запустим проект
![6](https://user-images.githubusercontent.com/112847807/205123590-e174aa6b-cefb-4fb9-b2ca-57b4537355fe.png)

Работу модели можно посмотреть в прикрепленном файле lab5 - Economic-ML-Agent.mp4

По ссылке из консоли перейдем на TensorBoard и посмотрим графики.

![7 1](https://user-images.githubusercontent.com/112847807/205134087-4968709d-684e-4d56-bb6a-7dc44bd61224.png)

### Задание 2
### Изменить параметры файла yaml-агента, определить какие параметры и как влияют на обучение модели. Описать результаты, выведенные в TensorBoard.

- Изменяем параметр num_layers с 2 на 3 и 
![8](https://user-images.githubusercontent.com/112847807/205127659-1232e4dc-a805-4315-b958-dff62fad6b73.png)



- Изменяем параметр batch_size с 1024 на 2048
![10](https://user-images.githubusercontent.com/112847807/205136703-712a7e47-4b54-4766-a76e-3bbe2a58c718.png)

![2048](https://user-images.githubusercontent.com/112847807/205136163-20342717-0675-4c18-9c35-db36209d0e92.png)

- Изменяем параметр buffer_size с 10240 до 100 
![2022-12-02_00-19-17](https://user-images.githubusercontent.com/112847807/205140998-eb5a1606-a5e2-49fd-bad3-92111095249c.png)


- Изменяем параметр epsilon с 0.2 на 0.3 и параметр lambd с 0.95 на 0.8
![2022-12-01_23-21-00](https://user-images.githubusercontent.com/112847807/205130439-c06ccf61-c9d6-4cd3-a306-35d8c717e599.png)
![предпоследний](https://user-images.githubusercontent.com/112847807/205149170-cb5175f4-6526-4697-b563-bf5761d76e40.png)

При изменении значений график entropy остается прежним либо возрастает. Когда мы изменяли lambd, практически все значения понижались на графике. Заметно при изменении buffer_size много графиков поменяло направление.
Если изменять данные, модель объективно обучается быстрее.


## Выводы
В процессе выполнения этой лабораторной работы мы попробовали интегрировать экономическую систему в проект Unity в связке с MLAgent. А также проследили на графиках как влияют изменения файла yaml-агента на обучение модели.

| Plugin | README |
| ------ | ------ |
| Dropbox | [plugins/dropbox/README.md][PlDb] |
| GitHub | [plugins/github/README.md][PlGh] |
| Google Drive | [plugins/googledrive/README.md][PlGd] |
| OneDrive | [plugins/onedrive/README.md][PlOd] |
| Medium | [plugins/medium/README.md][PlMe] |
| Google Analytics | [plugins/googleanalytics/README.md][PlGa] |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
