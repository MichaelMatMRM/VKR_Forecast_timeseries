# VKR_Forecast_timeseries
Применение алгоритмов машинного обучения для построения прогноза по временным рядам

Качественный прогноз позволяет изменять стратегию ведения бизнеса, оптимизировать расходы для создания продукции, осуществлять их контроль.

Целью работы является построение прогноза экономического временного ряда и оценка значимости прогноза на реальных данных.

Задачи, реашемые в рамках работы:

•	Выбор модели прогнозирования;

•	Разработка алгоритма автоматического подбора параметров модели на основе алгоритмов машинного обучения на данных сети мастерских города Санкт-Петербурга (продажи рамок для картин);

•	Создание программы для апробации решения задачи учета продаж, правила прогнозирования и визуализации прогноза;

Временной ряд – последовательность наблюдений некоторого признака случайной величины в последовательные моменты времени. 
Для успешного решения задач прогнозирования временной ряд должен удовлетворять требованиям слабой стационарности.
Временной ряд называется стационарным (в широком смысле) если выполняется:
	Постоянство математического ожидания во времени 
M(y_1 )=M(y_2 )=M(y_3 )=⋯
	Постоянство дисперсии во времени 
D(y_1 )=D(y_2 )=D(y_3 )=⋯
	Постоянство коэффициента корреляции во времени 
Cov(y_1, y_2) = Cov(y_2,y_3) = Cov(y_3,y_4) = …


Исследуемые временные ряды представляют из себя данные по продажам рамок для картин из разного материала, размером в 100 наблюдений, с января 2014 года по апрель 2022 года. 
Анализ разложения компонентов исследуемых временных рядов показал, что во всех трех исследуемых временных рядах есть четко выраженный тренд и сезонность, что говорит о том, что ни а каком постоянстве математического ожидания и дисперсии  речи быть не может. Ряды не стационарны.


Выбор модели происходит исходя из предметной области и поставленной задачи прогнозирования.Необходимый прогноз находиться в диапазоне от 1 до 12 месяцев. Наилучшим решением будет использование авторегрессионных моделей, так как они показывают хороший результат прогноза на короткие дистанции.

Среди авторегрессионных моделей для исследуемых временных рядов наилучшим решением будет использование модели SARIMA, так как она содержит в себе механизмы позволяющие сделать ряд стационарным.Она совмещает в себе слагаемые модели AR, которая описывает ВР в виде линейной комбинации этого же ряда и случайной ошибки и модели MA, которая описывает ВР в виде линейной функции зависимости исследуемой величины от разности фактического наблюдения и наблюдений, рассчитанных в прошлом.

Параметры p и q – число слагаемых моделей AR и MA соответственно.

Параметр d – число взятия разностей между соседними значениями. При достаточном количестве операций у ВР пропадает тренд.

Сезонные параметры P D Q находятся аналогичным образом, только с отставанием в размер сезона. Благодаря сезонной модификации ликвидируется сезонная составляющая временного ряда.

Параметр m – тип сезонности, известен изначально при анализе ВР. Он равен 12 так как, сезон равен 1 году.


Для нахождения параметров модели SARIMA мы будем использовать алгоритм пошагового поиска параметров с оптимизацией информационного критерия Акаике.
	Критерий Акаике основан на компромиссе между точностью и сложностью модели.
	Точность модели – это то насколько близко модель описывает поведение исследуемого признака. Показатель точности – RSS (сумма квадратов ошибок)
	Сложность модели – это число сезонных и несезонных коэффицентов модели - k
	Найти значение AIC значит рассчитать штраф, накладываемый на RSS и k. Лучшей будет та модель, у которой значение AIC будет меньше.
	В результате поиска среди возможных комбинаций мы получим наилучшую модель по критерию AIC.

![image](https://user-images.githubusercontent.com/72271693/178759646-341d8263-9c9c-4c1e-9751-072ae13f1ffe.png)


  
  
  Если мы используем алгоритм пошагового поиска параметров на всех данных для тренировки, то возникает проблема переобучения модели.
 Это явление, при котором модель показывает хорошие результаты на данных для тренировки, но плохо описывает поведение наблюдений, не участвующих в обучении.
Эту проблему можно решить при помощи перекрестной проверки (скользящий контроль). 

Суть метода заключается в том, чтобы разбивать тренировочные данные на два подмножества: обучающее (синий цвет окна) и контрольное(разовый цвет окна). Для каждого разбиения осуществлять поиск параметров модели по обучающему подмножеству и проверки работы на контрольном подмножестве.
Метод перекрестной проверки проводиться в два этапа:

На первом этапе (метод раскрывающегося окна), размер обучающего подмножества будет увеличиваться на каждой итерации на одно наблюдение. На текущем размере окна выполняется алгоритм пошагового поиска параметров. Затем делается прогноз от полученной модели на фиксированное число наблюдений вперед и вычисляется СКО между прогнозом и реальными данными. 
В конце первого этапа мы получим размер окна, который будет использован на втором этапе, при котором СКО минимально и соответствующие параметры модели.
![image](https://user-images.githubusercontent.com/72271693/178754533-85e00a86-392e-4829-8eb1-fa7741a10df6.png)

На втором этапе (метод скользящего окна), происходят те же действия что и на первом, но в отличие от него, размер обучающего подмножества остаётся неизменным и при каждой итерации изменяется положение окна обучающего подмножества на одно наблюдение вперед.
В конце алгоритма мы получим наилучшую модель, которая может претендовать на использование в реальной задаче.

![image](https://user-images.githubusercontent.com/72271693/178758248-2c3bc8f4-5668-4527-b0d4-f2fbe019cfe0.png)

Прежде чем использовать полученные модели необходимо посмотреть графики диагностики остатков чтобы убедиться в том, что они некоррелированные и их распределение похоже на нормальное. Т.е. убедимся в то что мы вытащили всю информацию из временного ряда. 
Для всех типов рамок:
-	левые верхние графики похожи на белый шум;
-	правые нижние графики показывают, что остатки некоррелированные, подтверждая, что левые верхние графики являются белым шумом;
-	на правых верхних графиках гистограмм видно, что для всех типов временных рамок линия KDE находиться достаточно близко к линии нормального распределения N(0,1);
-	нижние левые графики qq показывают, что упорядоченное распределение остатков следует линейному тренду выборок из нормального распределения.


![image](https://user-images.githubusercontent.com/72271693/178759368-656de6a4-7a27-48eb-bb7e-0d95b83b2260.png)

Проанализировав графики можно сказать, что модели, построенные от параметров, полученных путем выполнения разработанного алгоритма можно использовать для получения прогнозов.


![image](https://user-images.githubusercontent.com/72271693/178759959-8aabf8fa-e21b-49df-8616-9826c70caee6.png)
Проверим способность к предсказанию полученных моделей. Для этого сравним прогноз с данными для финального теста.
На графиках хорошо видно, что прогнозные значения и значения для финального теста находятся очень близко. Прогноз попадает в 95% доверительный интервал.
По анализу остатков и по тестированию моделей на финальных выборках можно заключить, что разработанный алгоритм автоматического подбора параметров модели позволяет подобрать модели готовые к использованию, которые показывают не плохой результат прогноза, попадающий в 95 процентный доверительный интервал и находящийся близко от реальных значений финальных выборок.

Программная реализация этого алгоритм будет использована, как основная функциональная составляющая разработанного программного обеспечения.

![image](https://user-images.githubusercontent.com/72271693/178760256-fd73b5ba-cdf5-4ce0-9b3b-15323550b140.png)
Обобщенная структурная схема разработанного ПО состоит из 2 основных блоков:
-	Внутренние алгоритмы работы 
-	Графический интерфейс


Функции интерфейса:
-	Инициация обработки действий пользователя
-	Визуализация данных, прогноза.


Функции внутренних алгоритмов работы программы:
-  Обработка сигналов пользователя
-  Изменение данных
-  Построение модели и прогноза

Данные продаж представлены в виде 3 файлов формата .csv. В каждом содержится информация о продажах рамок из соответствующих материалов: дерево, алюминий, пластик.
Изменение данных пользователем через взаимодействие с графическим интерфейсом, внесёт соответствующие изменения в текущий файл.
При построении модели и прогноза по ней, используются актуальные данные из текущего файла. Полученный прогноз будет отображаться пользователю при помощи графического интерфейса.


![image](https://user-images.githubusercontent.com/72271693/178760658-e40cf4cf-ff44-4b2f-b836-953dd67cf680.png)

При запуске приложения по умолчанию открывается вкладка «Управления данными», реализующая:
1)	Выбор типа рамки
2)	Изменение данных по продажам
3)	Просмотр актуальной информации

Вкладка «Построение прогноза» служит для сборки модели для построения прогноза на выбранное число месяцев. Просмотра качества модели.  Построения и визуализации прогноза.
