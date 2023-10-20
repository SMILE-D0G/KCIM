## Комп'ютерні системи імітаційного моделювання
## СПм-22-3, **Близнюк Олександр Валерiйович**
### Лабораторна робота №**2**. Редагування імітаційних моделей у середовищі NetLogo

<br>

### Варіант 3, модель у середовищі NetLogo:
[Virus]
(https://www.netlogoweb.org/launch#https://www.netlogoweb.org/assets/modelslib/Sample%20Models/Biology/Virus.nlogo)

<br>

### Внесені зміни у вихідну логіку моделі, за варіантом:

**Додавання впливу віку на інфікування та результат захворювання**   
Додано вплив вiку на iнфiкування:
<pre>
if ((random-float 100) - (age / 10)) < infectiousness  
</pre>
Додано вплив вiку на результат захворювання:
<pre>
ifelse ((random-float 100) + (age / 10)) < chance-recover
</pre>

**Додавання впливу ступеня поширення захворювання (поточного відсотка інфікованих) на вірогідність появи нових агентів**
, а не однаково для всіх машин:
<pre>
;; set speed-limit 1
set speed-limit get-random-float 1.1 0.9
</pre>
Використовується кольорова диференціація машин залежно від їхньої швидкісного ліміту:
<pre>
  ;; set label speed-limit
  if(speed-limit > 1.2) [
      set color green
  ]
   if(speed-limit > 1.5) [
     set color red
  ]

  ;; ask sample-car [ set color red ]
</pre>
Сині найповільніші, зелені швидше, червоні найшвидші.  
Забарвлення обраної для відстеження машини скасовано.

**Зміна логіки гальмування та набору швидкості** залежно від наявності перешкоди перед машиною:
<pre>
  let car-ahead-1 one-of turtles-on patch-ahead 1
    let car-ahead-2 one-of turtles-on patch-ahead 2
    ifelse car-ahead-1 = nobody and car-ahead-2 = nobody
      [ speed-up-car ] ;; otherwise, speed up
      [ slow-down-car car-ahead-1 car-ahead-2 ]
    ;; don't slow down below speed minimum or speed up beyond speed limit
    if speed < speed-min [ set speed speed-min ]
    if speed > speed-limit [ set speed speed-limit ]
    fd speed
</pre>
Для цього також були внесені зміни до процедури slow-down-car, яка спрацьовує при гальмуванні:
<pre>
to slow-down-car [ car-ahead-1 car-ahead-2 ] ;; turtle procedure
  if (car-ahead-1 != nobody) 
  [
      let speed-car-ahead-1 [ speed ] of car-ahead-1
      ;; slow down so you are driving more slowly than the car ahead of you
      set speed speed-car-ahead-1 - deceleration
      stop
  ]
  ]  
  if (car-ahead-2 != nobody) [
    let speed-car-ahead-2 [ speed ] of car-ahead-2
    let speed-difference-ahead-2 abs speed - speed-car-ahead-2
    set speed speed - speed-difference-ahead-2 / 2
  ]
end
</pre>

<br>

### Внесені зміни у вихідну логіку моделі, на власний розсуд:

**Змiнено тривалiсть життя та вплив вiку на можливiсть розмноження**.
Тривалiсть життя змiнена з 50 до 70 рокiв:
<pre>
set lifespan 70 * 52
</pre>

Додано вплив вiку на вiрогiднiсть розмноження:
<pre>
if count turtles < carrying-capacity and random-float 100 < chance-reproduce and age > 17 and age < 50
</pre>
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
Пеервірка, чи знаходимось на дорозі:
<pre>
to-report is-on-road
    report pycor = 0
end
</pre>

Додано узбіччя, дорога зведена до повноційної односмугової:
<pre>
to setup-road ;; patch procedure
  if pycor < 1 and pycor > -2 [ set pcolor yellow ]
  if pycor < 1 and pycor > -1 [ set pcolor white ]
end
</pre>

На кожному ході виконується перевірка, чи знаходиться машина на дорозі. Якщо машина їде узбіччям, то намагатиметься повернутися на дорогу:
<pre>
  if(not is-on-road) [
      if (road-main-free-check) [
        set heading 0
        fd 1
        set heading 90
        set car-roadside-amount car-roadside-amount - 1
      ]
    ]
</pre>
Функція перевірки, чи вільна дорога поблизу машини, щоб можна було повернутися з узбіччя:
<pre>
to-report road-main-free-check
  set heading 0
  let car-road one-of turtles-on patch-ahead 1
  set heading 90
  ;ifelse(car-roadside = nobody) [
    report car-road = nobody
  ;;]
  ;;[
  ;;  report false
  ;;]
end
</pre>

![Скріншот моделі в процесі симуляції](example-model.png)

Фінальний код моделі та її інтерфейс доступні за [посиланням](example-model.nlogo). *// якщо вносили зміни до інтерфейсу середовища моделювання - то експорт потрібен у форматі nlogo, як тут. Інакше, якщо змінювався лише код логіки моделі, достатньо викласти лише його, як [тут](example-model-code.html),якщо експортовано з десктопної версії NetLogo, або окремим текстовим файлом, шляхом копіпасту з веб-версії*.
<br>

## Обчислювальні експерименти
*// тут повинен бути наведений опис одного експерименту, за аналогією з першої л/р.* 
### 1. Вплив дисциплінованості водіів на середню швидкість переміщення
