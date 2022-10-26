# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #3 выполнил(а):
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
Познакомиться с программными средствами для создания системы машинного обучения и ее интеграции в Unity.

##Постановка задачи
В данной лабораторной работе создается ML-агент и тренируется нейросеть, задача которой будет заключаться в управлении шаром. Задача шара заключается в том, чтобы оставаясь на плоскости находить кубик, смещающийся в заданном случайном диапазоне координат.

## Задание 1
### Реализовать систему машинного обучения в связке Python - Google-Sheets – Unity
Ход работы: 
- Для начала нужно было создать пустой проект на Unity. Далее скачать папку со всеми нужными материалами. В проект добавить ML Agent. После добавить файлы
ml-agents-release_19 / com,unity.ml-agents / package.json и ml-agents-release_19 / com,unity.ml-agents.extensions / package.json

![2022-10-26_17-48-46](https://user-images.githubusercontent.com/112847807/198036756-f55bb1f5-0a81-448e-a6a9-94995f75fad5.png)

-Далее запустить Anaconda Prompt(консоль)
- Написать определенные команды для скачивания библиотек, создания и активации  ML-агента

![скрин 2](https://user-images.githubusercontent.com/112847807/198038065-f31193f1-29bd-4ea6-aa12-2b818c125ad3.png)

- Следующий шаг: создание плоскости, куба и сферы. Дальше создать  C# скрипт-файл, подключить его к сфере

![скрин 3](https://user-images.githubusercontent.com/112847807/198038965-d4ae764d-74ff-4146-afde-caecffeeabaf.png)

- В скрипт добавить код
```py
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Unity.MLAgents;
using Unity.MLAgents.Sensors;
using Unity.MLAgents.Actuators;

public class RollerAgent : Agent
{
    Rigidbody rBody;
    void Start()
    {
        rBody = GetComponent<Rigidbody>();
    }

    public Transform Target;
    public override void OnEpisodeBegin()
    {
        if (this.transform.localPosition.y < 0)
        {
            this.rBody.angularVelocity = Vector3.zero;
            this.rBody.velocity = Vector3.zero;
            this.transform.localPosition = new Vector3(0, 0.5f, 0);
        }

        Target.localPosition = new Vector3(Random.value * 8-4, 0.5f, Random.value * 8-4);
    }
    public override void CollectObservations(VectorSensor sensor)
    {
        sensor.AddObservation(Target.localPosition);
        sensor.AddObservation(this.transform.localPosition);
        sensor.AddObservation(rBody.velocity.x);
        sensor.AddObservation(rBody.velocity.z);
    }
    public float forceMultiplier = 10;
    public override void OnActionReceived(ActionBuffers actionBuffers)
    {
        Vector3 controlSignal = Vector3.zero;
        controlSignal.x = actionBuffers.ContinuousActions[0];
        controlSignal.z = actionBuffers.ContinuousActions[1];
        rBody.AddForce(controlSignal * forceMultiplier);

        float distanceToTarget = Vector3.Distance(this.transform.localPosition, Target.localPosition);

        if(distanceToTarget < 1.42f)
        {
            SetReward(1.0f);
            EndEpisode();
        }
        else if (this.transform.localPosition.y < 0)
        {
            EndEpisode();
        }
    }
}
```
![скрин 4](https://user-images.githubusercontent.com/112847807/198039687-1e851a54-8366-4db9-9c8a-db5cc321f3d3.png)

- Сфере добавить компоненты Rigidbody, Decision Requester, Behavior Parameters 

![скрин 5](https://user-images.githubusercontent.com/112847807/198040162-1937f149-00ba-4e7e-a7fc-1a4166a77303.png)

-Добавить файл конфигурации нейронной сети
```py
behaviors:
  RollerBall:
    trainer_type: ppo
    hyperparameters:                 
      batch_size: 10
      buffer_size: 100
      learning_rate: 3.0e-4
      beta: 5.0e-4
      epsilon: 0.2
      lambd: 0.99
      num_epoch: 3
      learning_rate_schedule: linear
    network_settings: 
      normalize: false
      hidden_units: 128
      num_layers: 2
    reward_signals:
      extrinsic:
        gamma: 0.99
        strength: 1.0
    max_steps: 500000
    time_horizon: 64
    summary_freq: 10000
```
- Запустить ML-agent

![скрин 6](https://user-images.githubusercontent.com/112847807/198040790-3f0d60f4-11c3-4b38-b111-a435c8ae9abe.png)

- На скриншотах ниже представлены 3, 9, 27 копий модели «Плоскость-Сфера-Куб»

![скрин 7](https://user-images.githubusercontent.com/112847807/198041883-afd4c137-d8e0-423e-bd68-6d8dbbbee6c5.png)

![скрин 8](https://user-images.githubusercontent.com/112847807/198041985-44de9501-d41f-41c1-aca6-6c51833d23ef.png)

![скрин 9](https://user-images.githubusercontent.com/112847807/198042054-5cc44926-2062-4a65-b185-047b17edb15b.png)

- После обучения движение шарика стало более правильным. Шарик стал быстрее находить кубик

![скрин 10](https://user-images.githubusercontent.com/112847807/198042460-39e04975-6ac2-429e-90fa-82fe2731d56b.png)

### Задание 2
### Подробно опишите каждую строку файла конфигурации нейронной сети, доступного в папке с файлами проекта по ссылке. Самостоятельно найдите информацию о компонентах Decision Requester, Behavior Parameters, добавленных на сфере.
- Decision Requester - запрашивает решение через регулярные промежутки времени и обрабатывает чередование между ними во время обучения.

- Behavior Parameters - определяет принятие объектом решений, в него указывается какой тип поведения будет использоваться: уже обученная модель или удалённый процесс обучения.

```py
behaviors:
  RollerBall:                        # Имя агента
    trainer_type: ppo                # Установка режим обучения (Proximal Policy Optimization).
    hyperparameters:                 # Гиперпараметры.
      batch_size: 10                 # Кол-во опытов на каждой итерации для обновления экстремумов функции.
      buffer_size: 100               # Кол-во опыта, которое нужно набрать перед обновлением модели.
      learning_rate: 3.0e-4          # Установка шага обучения (начальная скорость).
      beta: 5.0e-4                   # Случайность действия, повышение разнообразия и иследованности пространства обучения.
      epsilon: 0.2                   # Порог расхождений между старой и новой политиками при обновлении.
      lambd: 0.99                    # Определение авторитетности оценок значений во времени. Выше значение, более авторитетен набор оценок.
      num_epoch: 3                   # Кол-во проходов через буфер опыта.
      learning_rate_schedule: linear # Как скорость обучения изменяется с течением времени, линейно уменьшает скорость.
    network_settings:                # Сетевые настройки.
      normalize: false               # Отключается нормализация входных данных.
      hidden_units: 128              # Кол-во нейронов в скрытых слоях сети.
      num_layers: 2                  # Кол-во скрытых слоев для размещения нейронов.
    reward_signals:                  # Сигналы о вознаграждении.
      extrinsic:
        gamma: 0.99                  # Коэффициент скидки для будущих вознаграждений.
        strength: 1.0                # Шаг для learning_rate.
    max_steps: 500000                # Общее количество шагов, которые должны быть выполнены до завершения обучения.
    time_horizon: 64                 # Кол-во циклов ML агента, хранящихся в буфере до ввода в модель.
    summary_freq: 10000              # Кол-во опыта, который необходимо собрать перед созданием и отображением статистики.
```


## Задание 3
### Доработайте сцену и обучите ML-Agent таким образом, чтобы шар перемещался между двумя кубами разного цвета. Кубы должны, как и в первом задании, случайно изменять координаты на плоскости.
- Для начала создадим дополнительный куб в среде

![скрин 11](https://user-images.githubusercontent.com/112847807/198045578-f010a70f-5fbe-4d88-86d2-70e858cecc72.png)

- Обновим код под новую цель
```py
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Unity.MLAgents;
using Unity.MLAgents.Sensors;
using Unity.MLAgents.Actuators;

public class RollerAgent : Agent
{
    Rigidbody rBody;
    void Start()
    {
        rBody = GetComponent<Rigidbody>();
    }

    public Transform Target;
    public Transform Target2;
    public override void OnEpisodeBegin()
    {
        if (this.transform.localPosition.y < 0)
        {
            this.rBody.angularVelocity = Vector3.zero;
            this.rBody.velocity = Vector3.zero;
            this.transform.localPosition = new Vector3(0, 0.5f, 0);
        }

        Target.localPosition = new Vector3(Random.value * 8 - 4, 0.5f, Random.value * 8 - 4);
        Target2.localPosition = new Vector3(Random.value * 8 - 4, 0.5f, Random.value * 8 - 4);
    }
    public override void CollectObservations(VectorSensor sensor)
    {
        sensor.AddObservation(Target.localPosition);
        sensor.AddObservation(this.transform.localPosition);
        sensor.AddObservation(rBody.velocity.x);
        sensor.AddObservation(rBody.velocity.z);
    }
    public float forceMultiplier = 10;
    public override void OnActionReceived(ActionBuffers actionBuffers)
    {
        Vector3 controlSignal = Vector3.zero;
        controlSignal.x = actionBuffers.ContinuousActions[0];
        controlSignal.z = actionBuffers.ContinuousActions[1];
        rBody.AddForce(controlSignal * forceMultiplier);

        float distanceToTarget = Vector3.Distance(this.transform.localPosition, Target.localPosition);
        float distanceToTarget2 = Vector3.Distance(this.transform.localPosition, Target2.localPosition);

        if (distanceToTarget < 1.42f || distanceToTarget2 < 1.42f)
        {
            SetReward(1.0f);
            EndEpisode();
        }
        else if (this.transform.localPosition.y < 0)
        {
            EndEpisode();
        }
    }
}
```
- Снова сделать 3, 9 и 27 копий модели и запустить обучение.
![скрин 12](https://user-images.githubusercontent.com/112847807/198094416-6be8b781-a35e-4594-959b-d1036c97154f.png)

![скрин 13](https://user-images.githubusercontent.com/112847807/198094498-664ea0ab-aa42-49d6-8e84-376726527adc.png)

![скрин 14](https://user-images.githubusercontent.com/112847807/198094643-f760e9f5-9db2-4046-977a-8c83c7e6a360.png)

- Проверить везультат. Все работает правильно

## Выводы
Игровой баланс - субъективное «равновесие» между персонажами, командами, тактиками игры и другими игровыми объектами. Благодаря нему игра может быть более или менее играбельной, что достигается цельностью и продуманностью геймплея. Баланс является одним из требований к «честности» правил.
С помощью относительно простой нейронной сети можно достичь высокой эффективности игры.
В этой лабораторной работе мы научились создавать и обучать MlAgent, который позволяет шарику успешно добираться до своей цели.

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
