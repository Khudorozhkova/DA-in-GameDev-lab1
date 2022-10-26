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
### В разделе "ход работы" пошагово выполнить каждый пункт с описанием и примером реализации задачи по теме лабораторной работы

Ход работы:

- Произвести подготовку данных для работы с алгоритмом линейной регрессии. 10 видов данных были установлены случайным образом, и данные находились в линейной зависимости. Данные преобразуются в формат массива, чтобы их можно было вычислить напрямую при использовании умножения и сложения.

Первичная визуализация данных. 

![1](https://user-images.githubusercontent.com/112847807/192759490-4436061a-91b8-4ad1-b7f7-a0a27aa64dfa.png)


Добавим в код функции. 

![2022-09-28_15-33-48](https://user-images.githubusercontent.com/112847807/192757895-210bb761-bbd1-4efa-a544-8c15481df855.png)

- Определите связанные функции. Функция модели: определяет модель линейной регрессии wx+b. Функция потерь: функция потерь среднеквадратичной ошибки. Функция оптимизации: метод градиентного спуска для нахождения частных производных w и b.

Есть несколько функций, которые связаны между собой. Это optimize и iterate, optimize и model.
Функции optimize и model связаны, так как для каждого случая создается модель линейной регрессии.
На каждой итерации нужно находить частные произведения, поэтому optimize и iterate зависят друг от друга.

Первая итерация 

![Итерация 1](https://user-images.githubusercontent.com/112847807/192758909-4b286bd8-fdff-4f6a-9bd9-9a8470e798c2.png)

Вторая итерация 

![Итерация 2](https://user-images.githubusercontent.com/112847807/192758966-57c11dca-fc99-49a9-828a-f0dd18a394f5.png)

Третья итерация 

![Итерация 3](https://user-images.githubusercontent.com/112847807/192759012-ed424b1c-6b50-4049-b9e8-7c65a0cd7ceb.png)

Четвертая итерация

![Итерация 4](https://user-images.githubusercontent.com/112847807/192759070-fbbf8596-2581-4e94-a0a3-23729b5b6478.png)

Пятая итерация 

![Итерация 5](https://user-images.githubusercontent.com/112847807/192759098-a50dff00-da21-45cb-888c-6f8fc67bae8f.png)

Шестая итерация 

![Итерация 6](https://user-images.githubusercontent.com/112847807/192759149-c4836013-1863-43ae-80b3-3995f1837881.png)


## Задание 3
### Должна ли величина loss стремиться к нулю при изменении исходных данных? Ответьте на вопрос, приведите пример выполнения кода, который подтверждает ваш ответ.
Величина стремится к нулю. В этом можно убедиться запустив несколько итераций. 

На 1 итерации среднеквадратичная ошибка больше 2000

![Итерация 1](https://user-images.githubusercontent.com/112847807/192760263-72d300af-fdd6-4111-b5db-34a6a87669fb.png)

На 1000 итерации среднеквадратичная ошибка меньше 200

![Итерация 6](https://user-images.githubusercontent.com/112847807/192760422-a65ba05a-3e01-436e-aabd-249f66b81204.png)


### Какова роль параметра Lr? Ответьте на вопрос, приведите пример выполнения кода, который подтверждает ваш ответ. В качестве эксперимента можете изменить значение параметра.
Изменение параметра Lr пиводит к уменьшению количества итераций, которые требуются для достижения результата. Другими словами, параметр Lr изменяет быстроту обучения.
На прикрепленных скриншотах одно и то же количество итераций, но разный параметр Lr.

Lr = 0.00000001

![2022-09-28_15-54-13](https://user-images.githubusercontent.com/112847807/192761721-ac9f2ad0-9276-4eea-8764-3af97d3dc060.png)

Lr = 0.0001

![2022-09-28_15-55-15](https://user-images.githubusercontent.com/112847807/192761824-e3d75975-1610-418c-8759-485557d2d879.png)



## Выводы
В первой лабораторной работе мы написали самые простые программы вывода на языках  Python и С#, попробовали поработать в программе Unity, изучили метод реализации линейной регрессии

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
