## Подготовка
1) В качестве основы использовал схему DC-DC step-down преобразователя. На роль ключа выбрал pnp транзистор BC-327-40, так как на нём доступен весь диапазон напряжений, в отличии от транзистора npn(0...Vmax-0.7В).

_На самом деле можно использовать и npn с тем же успехом. В любом случае около 0,7 вольта будет падать на открытом PN переходе._

[Схема](./Images/Scheme.png)

2) Для индикации использовал 7сегментный индикатор. С ним понятно как работать + есть готовая библиотеку(правда пришлось её переделать для работы с дробными числами)

_Отлично. К сожалению, у нас не было времени его пощупать плотнее, так что я очень рад, что вы с ним разобрались._

## Реализация 

1) Конфигурация пинов представлена ниже. Для генерации шим использовал таймер 3. Этот же таймер служит для запуска АЦП. Тем самым экономим таймеры и само регулирование синхронно с ШИМ. Ещё 1 таймер служит для динамической индикации. Регулирование идёт по завершению работы АЦП(но тут не знаю, возможно лучше сделать чтобы по апдейту таймера)

_Хорошо. На самом деле имеется смысл делать именно так, чтобы регулятор имел дело со свежими данными._


2) Сам таймер 3 использовал без делителя, так как нужна одновременно высокая частота для уменьшения пульсаций, и большое число возможных значений CNT для более точной подстройки.

_Агась. Более точная подстройка — это верно. Частота же выбирается обычно исходя из возможностей транзисторов и прочих деталей._

3) Реализовать PID регулятор можно было 2мя путями: статические переменные и структуры. Вариант со статическими перменными более красивый,
однако данным регулятором можно регулировать лишь что-то одно. Вариант со структурами менее красивый и требует отдельного типа. Однако данным 
вариантом в одном проекте можно регулировать 2 разные вещи. нужны лишь 2 структуры. Выбрал вариант 2.

_Ну, на мой взгляд как раз вариант со структурами более элегантен, это дальнейшее развитие примера, показанного на уроке._

4) Так же был сделан отдельный модуль фильтрации. По похожему принципу. Коэффициент выбрал не слишком большим, так как питание не имеет тенденции быстро меняться

_+_

5) Сама регуляция происходит по завершению работы АЦП. Сначала значение со входа фильтруется, потом уже используется как обратная связь. 
Использовал только пропорциональную составляющую. Для решения основной задачи этого оказалось достаточно.

_Хорошо. Хотя и остаются лёгкие сомнения в том смысле, что при тонкой настройке, скорее всего, вылезла бы и необходимость интегральной части.__

6) Для испытания в качестве нагрузки использовал подстроечный резистор на 1(кОм) и последовательно подключённый на 100(Ом) в качестве защиты.
(Там последовательно стоит линейный преобразователь на 3,3В. МК пока нужен и потому рисковать не хочется).

7) Результат: получился регулируемый источник питания, способный держать напряжение при разной нагрузке. Видео прилагается в папке с домашними работами

## Замечания и предложения

1) Данная схема из за некоторых ограничений МК может регулитировать только в пределах 0...3.3В. Транзистор не позволит.
 Однако есть вариант расширения диапазона: Использовать не просто транзистор, а комбинацию "силовой транзистор + оптопара". 
 Сам МК открывает оптопару, оптопара открывает уже силовой транзистор. При этом на МК не попадает напряжение из вне, которое выше 3.3В,
  а соотвественно он в безопасности. Другое дело что и АЦП обезопасить надо, но это решается с помощью делителя напряжения.
   Но данный делитель нужно сделать так, чтобы на АЦП не пришло напряжение больше 3.3В, при этом чтобы диапазон был максимальный.
   (но пока МК нужен, а соотвественно рисковать не стал, однако схему приложу).

[2й вариант](./Images/Scheme%20variant%202.png)


_Немного странный выбор оптопары с открытым корпусом, а сама идея верная. Для этих целей лучше подошла бы классическая PC817. Кстати, на приложенных схемах было бы хорошо узреть
и реализацию той самой обратной связи_