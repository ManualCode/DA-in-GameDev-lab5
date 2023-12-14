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
Меняя это значение, можно понять, что коэффициент корреляции определяет на каком расстоянии должны находиться объект и цель, чтобы получить награду. Чем он больше, тем быстрее обучается модель, но при этом менее точно будет достигаться цель, чем меньше, тем более точное и медленое обучение.

## Задание 2
### Изменить параметры файла yaml-агента и определить какие параметры и как влияют на обучение модели. Привести описание не менее трех параметров.

Ход работы:
Будем менять параметры в файле rollerball_config.yaml:
![image](https://github.com/ManualCode/DA-in-GameDev5/assets/120582775/bb61e022-fdd3-4763-9a98-02b273177b03)
1. learning_rate - параметр, который регулирует скорость, с которой модель адаптируется к данным в процессе обучения. При слишком маленьком значение обучение становится стабильным, но его темп замедляется. При слишком большом learning_rate процесс обучение стал неравномерным.
2. num_epoch - параметр, отвечающий за количество эпох обучения. Увеличение значения приводит к повышению точности обучения, но значительно увеличивается времемя, необходимое для обучения ML-агента.
3. buffer_size - параметр, который определяет кол-во опыта, который необходимо собрать перед обновлением модели политики. При увеличении параметра обновления становятся более стабильным.

## Задание 3
### Приведите примеры, для каких игровых задачи и ситуаций могут использоваться примеры 1 и 2 с ML-Agent’ом. В каких случаях проще использовать ML-агент, а не писать программную реализацию решения?

Ход работы: 
1. ML-Agent может использоваться для NPC в игре. Например, их передвижение по игровому миру, чтобы они шли ровно по нужной дороге, не врезались в объекты и знали как обойти препятствия.
   К прмеру любой моб из того же Minecraft'a
2. ML-Agent может быть полезен для разработки интерактивных NPC, который следует от одной точки к другой по определённому маршруту и важно чтобы он искал способ обойти его. Также можно подметить, что такой агент был бы полезен для создания противников в гонках со строго заданным маршрутом
3. Программная реализация совсем не адаптивна, в отличие от ML Агента, поэтому ML Агент стоит использовать в тех случаях, где игровая ситуация постоянно меняется. Программная реализация слишком ненадежна, и может неадекватно реагировать на изменения игровой ситуации.

## Выводы
В этой лабораторной работе мы взаимодействовали с ML-Agent в Unity, познавая процесс обучения объектов. Экспериментировали с различными значениями параметров, стремясь понять, как они влияют на обучение. В ходе лабораторной работы успешно интегрировали ML-Agent

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
