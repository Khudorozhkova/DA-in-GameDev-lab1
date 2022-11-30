# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #4 выполнил(а):
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
Познакомиться с работой перцептрона на практике при помощи движка Unity. Реализовать перцептрон который умеет решать логические операции.

## Задание 1
### В проекте Unity реализовать перцептрон, который умеет производить вычисления: OR, AND, NAND, XOR, и дать комментарии о корректности работы.
Ход работы: 
- Для начала подготовим все для работы с перцептроном
1) Создаем проект в Unity
![2022-11-30_17-12-14](https://user-images.githubusercontent.com/112847807/204833953-1e6ce3f0-8dbe-4dc3-8ffa-e435726faaf0.png)

2) Создаем пустой файл для скрипта
![2022-11-30_17-12-53](https://user-images.githubusercontent.com/112847807/204834146-7522361d-df4f-49f0-8a73-eac3c4449449.png)

3) Добавляем в файл скрипт
```py
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

[System.Serializable]
public class TrainingSet
{
	public double[] input;
	public double output;
}

public class Perceptron : MonoBehaviour {

	public TrainingSet[] ts;
	double[] weights = {0,0};
	double bias = 0;
	double totalError = 0;

	double DotProductBias(double[] v1, double[] v2) 
	{
		if (v1 == null || v2 == null)
			return -1;
	 
		if (v1.Length != v2.Length)
			return -1;
	 
		double d = 0;
		for (int x = 0; x < v1.Length; x++)
		{
			d += v1[x] * v2[x];
		}

		d += bias;
	 
		return d;
	}

	double CalcOutput(int i)
	{
		double dp = DotProductBias(weights,ts[i].input);
		if(dp > 0) return(1);
		return (0);
	}

	void InitialiseWeights()
	{
		for(int i = 0; i < weights.Length; i++)
		{
			weights[i] = Random.Range(-1.0f,1.0f);
		}
		bias = Random.Range(-1.0f,1.0f);
	}

	void UpdateWeights(int j)
	{
		double error = ts[j].output - CalcOutput(j);
		totalError += Mathf.Abs((float)error);
		for(int i = 0; i < weights.Length; i++)
		{			
			weights[i] = weights[i] + error*ts[j].input[i]; 
		}
		bias += error;
	}

	double CalcOutput(double i1, double i2)
	{
		double[] inp = new double[] {i1, i2};
		double dp = DotProductBias(weights,inp);
		if(dp > 0) return(1);
		return (0);
	}

	void Train(int epochs)
	{
		InitialiseWeights();
		
		for(int e = 0; e < epochs; e++)
		{
			totalError = 0;
			for(int t = 0; t < ts.Length; t++)
			{
				UpdateWeights(t);
				Debug.Log("W1: " + (weights[0]) + " W2: " + (weights[1]) + " B: " + bias);
			}
			Debug.Log("TOTAL ERROR: " + totalError);
		}
	}

	void Start () {
		Train(8);
		Debug.Log("Test 0 0: " + CalcOutput(0,0));
		Debug.Log("Test 0 1: " + CalcOutput(0,1));
		Debug.Log("Test 1 0: " + CalcOutput(1,0));
		Debug.Log("Test 1 1: " + CalcOutput(1,1));		
	}
	
	void Update () {
		
	}
}
```
- Операция OR (ИЛИ, объединения множеств)
Проставим значения для Ts в соответствии с операцией
![OR](https://user-images.githubusercontent.com/112847807/204835797-35d3d25c-ce98-44f6-8c5b-aec6fcc1c2d3.png)

В скрипте зададим 1 обучающую эпоху. По результатам можно понять, что перцептрон не успел обучиться.
![Train(1)](https://user-images.githubusercontent.com/112847807/204836372-e4648613-b623-429a-a72c-3f982d400adc.png)

После 2 обучающих эпох все еще есть ошибки.
![Train(2)](https://user-images.githubusercontent.com/112847807/204836470-6bd33a54-d265-4c5f-a423-b31d15d297bb.png)

А вот после шести ошибки не появились.
![Train(6)](https://user-images.githubusercontent.com/112847807/204838286-92ddf4e9-b754-4261-8c05-61366244488e.png)


- Операция AND (И, логическое умножение)
Проставим значения для Ts в соответствии с операцией
![AND](https://user-images.githubusercontent.com/112847807/204837548-880836b9-3a62-4be2-91ea-fdc4013c07ce.png)

После 1 обучающей эпохи результаты, как показывает практика, всегда будут с ошибками. Поэтому постепенно пробуем увеличивать обучающие эпохи. На скриншоте результат после команды Train(7)
![AND Train(7)](https://user-images.githubusercontent.com/112847807/204839449-88c851a2-cd20-4ce0-93a9-4032ca1ed152.png)

- Операция NAND (инвертированное И)
Проставим значения для Ts в соответствии с операцией
![NAND](https://user-images.githubusercontent.com/112847807/204839752-23f0e55e-2e7c-4f52-9f39-b86444282c0a.png)

В скрипте зададим 1, а после 2 обучающих эпох. Смотрим результаты.
![NAND Train(1)](https://user-images.githubusercontent.com/112847807/204840041-fbee712a-00d7-41a2-ac4f-55ad74806998.png)
![NAND Train(2)](https://user-images.githubusercontent.com/112847807/204840208-09c8fcd0-47bd-46fb-80dd-3d52503423f8.png)

В консоли есть Total Error. Пробуем поставить 4 обучающих эпохи. После них перцептрон успешно обучился и выполнил все правильно.
![NAND Train(4)](https://user-images.githubusercontent.com/112847807/204840604-ab711ceb-5e90-4ec3-b98e-95e70359522a.png)

- Операция XOR (исключающее ИЛИ)
Проставим значения для Ts в соответствии с операцией.
![XOR](https://user-images.githubusercontent.com/112847807/204841125-8757c5b1-65b6-4405-902e-e86a966fd398.png)

На 4, 8, 10 и 16 обучающих эпохах Total Error везде равен 4. 
![XOR Train(10)](https://user-images.githubusercontent.com/112847807/204841695-11a857e9-5455-473e-bfec-74439de09776.png)
Можно сделать вывод, что перцептрон не может обрабатывать эту операцию. XOR - нелинейная операция.
А OR, AND, NAND - линейные. Перцептрон обрабатывает только задачи линейной классификации. Чтобы в XOR не было ошибок, нужно использовать дополнительные слои перцепторна.

### Задание 2
### Построить графики зависимости количества эпох от ошибки обучения. Указать от чего зависит необходимое количество эпох обучения.
- Построим график для OR
![OR график](https://user-images.githubusercontent.com/112847807/204843927-0648718f-2ae1-41bd-8238-3384eb070c78.png)

- Построим график для AND
![АND график](https://user-images.githubusercontent.com/112847807/204843992-967f64b5-206e-4abf-9909-1e187cd14655.png)

- Построим график для NAND
![NAND график](https://user-images.githubusercontent.com/112847807/204844177-968e19ea-959b-4d3b-be5c-31f5968f4298.png)


## Задание 3
### Построить визуальную модель работы перцептрона на сцене Unity.
- Создаем на сцене 4 шара. черные шары - 0, белые шары - 1.
![2022-11-30_19-45-38](https://user-images.githubusercontent.com/112847807/204845048-f6be5f16-1715-476c-8fa5-c0c33147590c.png)

У верхнего шара добавляем Rigidbody, а у нижнего в разделе Box Collider галочку у Is trigger.
Навешиваем на нижние шары скрипт с методом изменения цвета (с помощью операции ИЛИ)
```py
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class ChangeColor: MonoBehaviour
{
    private void OnTriggerEnter(Collider other)
    {
        if (gameObject.GetComponent<Renderer>().material.color == Color.white || other.gameObject.GetComponent<Renderer>().material.color == Color.white)
        {
            gameObject.GetComponent<Renderer>().material.color = Color.white;
            other.gameObject.GetComponent<Renderer>().material.color = Color.white;

        }
        else
        {
            gameObject.GetComponent<Renderer>().material.color = Color.black;
            other.gameObject.GetComponent<Renderer>().material.color = Color.black;
        }
    }
}
```
Работа модели показана в видео (3 задание), которое приложено в файлах.

## Выводы
В процессе выполнения этой лабораторной работы мы попробовали поработать с перцетроном. Выполнили с помощью него разные логические операции (OR, AND, NAND, XOR).
Узнали, как отличается работа с линейными и нелинейными операциями. Мы построили графики оценки обучаемости моделей разных логических операций.
А также создали модель в Unity с использованием перцептрона.

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
