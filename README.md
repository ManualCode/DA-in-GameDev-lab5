Отчет по лабораторной работе #5 выполнил(а):
- Тимохов Кирилл Александрович
- РИ220910
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

## Цель работы
Познакомиться с программными средствами для создания системы машинного обучения и ее интеграции в Unity.

## Задание 1
Найдите внутри C# скрипта “коэффициент корреляции” и сделать выводы о том, как он влияет на обучение модели.

Ход работы:
Скрипт RollerAgent:
```C#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Unity.MLAgents;
using Unity.MLAgents.Sensors;
using Unity.MLAgents.Actuators;

public class RollerAgent : Agent
{
    Rigidbody rBody;
    // Start is called before the first frame update
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

        Target.localPosition = new Vector3(Random.value * 8 - 4, 0.5f, Random.value * 8 - 4);
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

        if (distanceToTarget < 1.42f)
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
Коэффициент корреляции можно найти в методе OnActionReceived и равен он 1.42f (рис).
![image](https://github.com/ManualCode/DA-in-GameDev5/assets/120582775/f7396d71-f0dd-4ce4-b191-825c4a712d2b)
Меняя это значение, можно понять, что коэффициент корреляции определяет, на каком расстоянии от цели агент будет считаться успешным. Чем он больше, тем быстрее обучается модель, но при этом менее точно будет достигаться цель, чем меньше, тем 
 более точное и медленое обучение.

## Задание 2
### Построить графики зависимости количества эпох от ошибки обучения. Указать от чего зависит необходимое количество эпох обучения.

Ход работы:
- Я заполнил Google-Таблицу данными об эпохах и визуализировал их с помощью графиков.

1. OR (Логическое сложение(ИЛИ)):

![image](https://github.com/ManualCode/DA-in-GameDev-lab4/assets/120582775/69ca3a81-38cb-440b-9e12-c61f3b973d93)

2. AND (Логическое умножение(И)):

![image](https://github.com/ManualCode/DA-in-GameDev-lab4/assets/120582775/a5c541a4-c52d-4ac7-b89d-7016fe2623f9)

3. NAND (Инвертированное Логическое умножение(НЕ И)):

![image](https://github.com/ManualCode/DA-in-GameDev-lab4/assets/120582775/f9057b84-6518-48b5-b757-720dec2f9e0e)

4. XOR (Исключающая Логическая сумма((ИЛИ) и (НЕ И))):

![image](https://github.com/ManualCode/DA-in-GameDev-lab4/assets/120582775/10a39ef5-f540-4f6a-88d8-bfb961f473b3)

## Задание 3
### Визуализировать работу персептрона с помощью физуальной модели на сцене Unity.
Ход работы: 
- Создал сцену в Unity, добавил плоскость и несколько кубов;
- Модефицировал код в Perceptron.cs и накинул на кубы;
- Продублировал сцену на каждую логическую операцию;

Код дописанный в Perceptron.cs:
Для каждой логической операции нужно менять условие (test == ?).
```C#
 private void OnCollisionEnter(Collision collision)
 {
     var test = CalcOutput(0, 0);
     if (test == 0 && collision.gameObject.tag == "Cube2" && collision.gameObject.tag != "Floor")
     {
         collision.gameObject.GetComponent<Renderer>().material.color = Color.green;
         gameObject.GetComponent<Renderer>().material.color = Color.green;
     }
     else if (collision.gameObject.tag == "Cube2" && collision.gameObject.tag != "Floor")
     {
         collision.gameObject.GetComponent<Renderer>().material.color = Color.red;
         gameObject.GetComponent<Renderer>().material.color = Color.red;
     };
     test = CalcOutput(0, 1);
     if (test == 0 && collision.gameObject.tag == "Cube4" && collision.gameObject.tag != "Floor")
     {
         collision.gameObject.GetComponent<Renderer>().material.color = Color.green;
         gameObject.GetComponent<Renderer>().material.color = Color.green;
     }
     else if (collision.gameObject.tag == "Cube4" && collision.gameObject.tag != "Floor")
     {
         collision.gameObject.GetComponent<Renderer>().material.color = Color.red;
         gameObject.GetComponent<Renderer>().material.color = Color.red;
     };
     test = CalcOutput(1, 0);
     if (test == 0 && collision.gameObject.tag == "Cube6" && collision.gameObject.tag != "Floor")
     {
         collision.gameObject.GetComponent<Renderer>().material.color = Color.green;
         gameObject.GetComponent<Renderer>().material.color = Color.green;
     }
     else if (collision.gameObject.tag == "Cube6" && collision.gameObject.tag != "Floor")
     {
         collision.gameObject.GetComponent<Renderer>().material.color = Color.red;
         gameObject.GetComponent<Renderer>().material.color = Color.red;
     };
     test = CalcOutput(1, 1);
     if (test == 1 && collision.gameObject.tag == "Cube8" && collision.gameObject.tag != "Floor")
     {
         collision.gameObject.GetComponent<Renderer>().material.color = Color.green;
         gameObject.GetComponent<Renderer>().material.color = Color.green;
     }
     else if (collision.gameObject.tag == "Cube8" && collision.gameObject.tag != "Floor")
     {
         collision.gameObject.GetComponent<Renderer>().material.color = Color.red;
         gameObject.GetComponent<Renderer>().material.color = Color.red;
     };
 }
```

1, 2, 3. Тест OR, AND, NAND:
Не имеет смысла втавлять отдельные gif-ки на каждую логтческую операцию, т.к результат одинаков

![nteTesRRGt_edited](https://github.com/ManualCode/DA-in-GameDev-lab4/assets/120582775/3697d0cc-16aa-4274-b12c-7007d5b01b7d)
4. Тест XOR:

![XOR](https://github.com/ManualCode/DA-in-GameDev-lab4/assets/120582775/b71e1e83-3ad5-4ab6-b1ad-38363766f873)

## Выводы
В ходе данной лабораторной работы я познакомился с персептроном. Реализовал и визуализировал его в Unity, а так же сделал выводы о корректности его работы на примере функций OR, AND, NAND, XOR.

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
