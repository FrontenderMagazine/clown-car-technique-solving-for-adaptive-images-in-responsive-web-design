# Приём «машинка с клоунами»<a
href="#note-1" id="ref-1" class="reference">1</a>: решение для адаптации изображений в отзывчивом веб-дизайне

Адаптивные изображения в последнее время стали острой темой для обсуждений в мире адаптивного и отзывчивого веб-дизайна. Почему? Потому что никого не устраивают варианты решений, существующие на данный момент. Разработчики пытаются применить новые элементы и атрибуты для решения задачи, которая стала для нас головной болью: разработка метода, реализованного на стороне клиента, который дал бы каждому пользователю возможность загружать одно-единственное изображение, оптимизированное под его разрешение и размер монитора, без потери времени, ресурсов устройства и интернет-трафика. 

Есть обычные и фоновые изображения. Большие и маленькие экраны. Экраны с обычным и большим разрешением. Медленные и быстрые интернет-подключения. Вертикальная и горизонтальная ориентация.

Некоторые предпочитают пожертвовать трафиком (и ресурсами) и загружать изображения с высоким разрешением для всех устройств. В некоторых случаях для всех устройств загружаются изображения со средним разрешением, которые на мониторах с высоким разрешением выглядят недостаточно чёткими. 

Нам бы очень хотелось найти святой Грааль: единое решение, которое позволит загружать именно то изображение, которое имеет наиболее подходящий размер и разрешение для конкретного браузера и устройства, с использованием приемлемого запроса.

Из всего что нам доступно на сегодняшний день, приём «машинка с клоунами» наиболее близок к этому святому Граалю. Он предусматривает реализацию отзывчивых изображений с использованием обладающих хорошей поддержкой медиазапросов, формата SVG и элемента `<object>` при всего одном запросе. Это решение пока нельзя назвать безупречным, но оно близко к этому.

## Фоновые изображения и медиазапросы

С фоновыми изображениями всё просто и понятно. Медиазапросы позволяют без проблем регулировать их размер и разрешение исходя из таких параметров устройства как соотношение логических и физических пикселей, размер области просмотра и даже ориентация экрана. 

Использование медиазапросов для фоновых изображений позволяет загружать с сервера только нужные изображения. Количество загрузок можно сократить до подходящего и таким образом сэкономить трафик, ресурсы и HTTP-запросы.

К сожалению, до этого времени решение для обычных (нефоновых) изображений пока предложено не было. Технологии, которые могут стать основой для такого решения, существуют уже довольно давно. Приём «машинка с клоунами» - это всего лишь новый приём, для которого используются существующие технологии.

## Решения с использованием новых технологий

### Новые элементы и атрибуты

Когда речь идёт об изображениях в теле страницы (которые являются частью информационного содержимого страницы), сделать так чтобы браузер загружал и отображал только наиболее подходящее изображение намного сложнее. Большинство считает что с помощью тэга `<img>` нельзя заставить грузиться только изображение с подходящим размером и разрешением. Для этого были созданы [полифилы][1] и специальные [сервисы][2]. 

Был предложен [элемент `<picture>`][3], который использует семантику элемента `<video>` и поддерживает медиазапросы для загрузки разных файлов:

    <picture alt="отзывчивое изображение"> 
         <source src="large.jpg" media="(min-width:1600px),
         (min-resolution: 136dpi) and (min-width:800px)">
         <source src="medium.jpg" media="(min-width:800px),
         (min-resolution: 136dpi) and (min-width:400px)">
         <source src="small.jpg">
      <!-- резервное решение -->
      <img src="small.jpg" alt="отзывчивое изображение">
    </picture>

Также был предложен метод с использованием элемента `<img>` с [атрибутом `srcset`][4]. Пример с элементом `<picture>`, приведенный выше, будет тогда выглядеть так:

    <img
        alt="отзывчивое изображение"
        src="small.jpg" 
        srcset="large.jpg 1600w, 
              large.jpg 800w 1.95x, 
              medium.jpg 800w, 
              medium.jpg 400w 1.95x">

Оба решения имеют преимущества и недостатки. Выбрать лучшее из них трудно, но сейчас в этом и нет необходимости. Эти решения были объединены в так называемый «[Компромисс Флориана][5]». Однако это [ещё не совсем то что нужно][6].

Команда Google предложила добавить в HTTP-заголовки [подсказки для клиента][7], которые позволяли бы определять наиболее подходящее изображение на стороне сервера.

### SVG в качестве нестандартного решения

Многие не понимают что у нас уже есть технология для создания и обслуживания отзывчивых изображений.

SVG уже давно поддерживает медиазапросы, да и [браузеры поддерживают SVG][8] в течении… в общем можно сказать достаточно давно. Большинство браузеров поддерживает медиазапросы для SVG (можете [протестировать свой браузер][9]). Когда дело касается отзывчивых изображений, единственный браузер для мобильных устройств, который не поддерживает SVG - это старые версии браузера Android (поддержка SVG была введена в Android 3.0). 

То что браузеры поддерживают SVG *и* SVG поддерживает медиазапросы и растровые изображения, можно использовать для создания отзывчивых изображений; наиболее подходящее изображение, которое должно быть загружено, может быть определено с помощью медиазапросов для SVG. 

Мой первый эксперимент теоретически должен работать, и он таки работает в Internet Explorer (IE) 10 и Opera. При написании разметки HTML, нужно добавить один-единственный запрос на файл SVG.

    <img src="awesomefile.svg" alt="отзывчивое изображение">

Ну разве этот код не простой?

SVG может быть растровым изображением и прописываться с помощью элемента `<image>` и свойства CSS `background-image`. Мы добавим все изображения, которые нам могут понадобиться, в наш отзывчивый SVG и с помощью медиазапросов будем отображать только наиболее подходящее изображение.

### Загрузка одного растрового изображения

В своем первом опыте с SVG я использовала `<image>` с медиазапросами, спрятанные с помощью `display: none`.

Хотя с точки зрения отзывчивости SVG работает безупречно, несколько проблем все-таки имеют место. К сожалению, `display: none` для `<image>` в SVG, так же как и для `<img>` в HTML, не предотвращает загрузку исходного файла. Если вы откроете SVG с элементами `<image>` в своём браузере, все четыре файла PNG будут скачаны с сервера, следовательно получается четыре HTTP-запроса, перерасход трафика и ресурсов. 

На примере с фоновыми изображениями в CSS мы убедились, что сделать так чтобы загружались только нужные изображения всё-таки можно. Таким образом, в нашем SVG-файле вместо обычных изображений мы будем использовать фоновые чтобы предотвратить скачивание всех изображений помещённых в SVG:

    <svg xmlns="http://www.w3.org/2000/svg" 
       viewBox="0 0 300 329" preserveAspectRatio="xMidYMid meet">

    <title>Приём «машинка с клоунами»</title>

    <style>
    svg {
    	background-size: 100% 100%;
    	background-repeat: no-repeat;
    }

    @media screen and (max-width: 400px) {
    	svg {
    		background-image: url(images/small.png");
    	}
    }

    @media screen and (min-width: 401px) and (max-width: 700px) {
    	svg {
    		background-image: url(images/medium.png);
    	}
    }

    @media screen and (min-width: 701px) and (max-width: 1000px) {
    	svg {
    		background-image: url(images/big.png);
    	}
    }

    @media screen and (min-width: 1001px) {
    	svg {
    		background-image: url(images/huge.png);
    	}
    }
    </style>
    </svg>

Код, приведенный выше, может быть добавлен прямо в виде инлайнового `<svg>` или встроен через атрибуты `<img> src` или `<object> data`. 

Если вы знакомы с медиазапросами и CSS, большая часть кода выше вам должна быть понятна. Для приёма «машинка с клоунами» используются такие медазапросы, обычные для адаптивных сайтов.

Чтобы сохранить соотношение сторон элемента-контейнера и обеспечить пропорциональное масштабирование, были добавлены атрибуты `viewbox` и `preserveAspectRatio`.

Значение атрибута `viewbox` - это список из четырех чисел разделённых пробелами или запятыми: `min-x`, `min-y`, `width` и `height`. Задавая ширину и высоту нашей площади просмотра, мы устанавливаем соотношение сторон изображения SVG. Значения указанные для атрибута `preserveAspectRatio` - 300 × 329 - позволяют сохранить соотношение сторон указанное в `viewbox`.

Недостатки такого решения: 1) В Chrome и Safari соотношение сторон [`<svg>` не сохраняется][11], вместо этого для высоты и ширины устанавливается значение 100%. Я [написала в службу поддержки об этой ошибке][12]. 2) Webkit и Firefox не поддерживают добавление изображений или скриптов в формате SVG через элемент `<img>` и 3) IE <=8 и Android <=2.3.3 не поддерживают SVG.

Когда вы [откроете файл SVG, в котором указаны только фоновые изображения][13], растровое изображение будет занимать всю область просмотра. Версия с `<image>` лучше выглядит когда использован отдельный файл, так как он сохраняет соотношение сторон, тогда как версия с `background-image` заполняет всю область просмотра. Однако если добавить SVG как отдельный документ, который закачивается после запроса из HTML, соотношение сторон сохраняется по умолчанию. Каждое из таких значений свойства `background-size` как `contain`, `cover` и `100%` работает, выбирайте то какое вам больше подходит.

Свойство CSS `background-image` решает проблему с HTTP-запросами. Откройте файл SVG который содержит только фоновые изображения PNG (или версию с изображениями JPEG) и взгляните на вкладку «Сеть» («Network») в панели разработчика, вы увидите что для SVG было сделано только два HTTP-запроса, а не пять. Если у вас большой монитор, браузер должен загрузить маленький файл SVG (на 676 байт) и большой файл `huge.png` или `huge.jpg`.

Наша первая проблема, которая состояла в загрузке всех изображений разных размеров, даже тех, которые не нужны, решена. Версия с `background-image` позволяет загружать только нужное изображение, таким образом решая вопрос с множественными HTTP-запросами и перерасходом трафика.

Чудеса начинаются когда мы добавляем SVG в гибкий макет. Когда в первый раз попробуете изменить размер изображения, вы заметите, что окно браузера может на мгновение стать белым пока запрашивает следующее PNG-изображение, так как он не загружает все файлы автоматически. Вместо этого он загружает только те файлы, которые нужны в данный момент. Укажите ширину или высоту контейнера (`<img>`, `<svg>` или `<object>`) в CSS медизапросах и SVG вызовет только одно растровое изображение. 

У нас остаётся SVG-файл, который создаёт HTTP-запрос, если он не прописан в разметке внутри контейнера `<svg>`. Эту проблему мы решим немного позже.

## Безопасность контента

Откройте в Opera или в IE 9/10 [файл HTML, содержащий растровое изображение SVG][16] в тэге `<img>`. В вкладке «Ресурсы» («Resources») панели инструментов разработчика обратите внимание на то что загружается только один файл JPEG или PNG. Изменить размер окна браузера. Обратите внимание: элемент `<img>` является отзывчивым. Дополнительные файлы JPEG или PNG (также можно использовать GIF или WebP) загружаются только когда в них есть необходимость.

Если вы открыли [файл HTML, содержащий растровое изображение SVG][17] в Firefox или WebKit, скорее всего изображение вы не увидели совсем. SVG работает во всех современных браузерах, но `<img>` с SVG, который вызывает растровые изображения, работает только в Opera и IE 9+. Сначала рассмотрим ситуацию с IE и Opera, а затем разберемся с проблемами WebKit и Firefox.

Код простой:

    <img src="awesomefile.svg" alt="responsive image">

Если добавить SVG в тэг `<img>` с гибкой шириной, например 70% от ширины области просмотра, при увеличении или уменьшении контейнера вследствие изменения размера окна или изменений в CSS, изображений будет подстраиваться соответственно новым обстоятельствам.

Медиазапрос `width` для SVG связан с родительским элементом, в который помещён SVG (в нашем случае с `<img>`), а не с шириной области просмотра.

По мере увеличения и уменьшения размера окна, изображение, воспроизводимое SVG меняется. Размер изображений, помещённых в SVG, рассчитывается как 100% от высоты и ширины родительского элемента, который в случае приведённом выше (когда мы открыли сам SVG-файл) - это область просмотра. Теперь мы имеем в качестве контейнера элемент `<img>`. При добавлении атрибутов `viewbox` и `preserveAspectRatio` если задан хотя бы одно значение длины, SVG увеличится или уменьшится подстраиваясь под это значение и сохраняя соотношение сторон вне зависимости от размера самого изображения. 

Такое решение для изображений работает безупречно в Opera и IE 9+ (в версиях для мобильных устройств). В Chrome и Safari если вы сначала откроете файл SVG и он будет добавлен в кеш, при открытии самого документа HTML, в котором он использован, соответствующее изображение вероятно будет отображено.

Хоть ранее мы и убедились что браузеры понимают SVG если он добавлен в документ через тэг `<img>`, в этом конкретном случае SVG отрисован не будет.

Почему? Чтобы предотвратить междоменные атаки некоторые браузеры поддерживают политику обеспечения безопасности контента, согласно которой SVG не может импортировать медиафайлы и скрипты, если есть вероятность что они являются вредоносными.

В блокировании загрузки скриптов и изображений SVG-элементами есть смысл: чтобы предотвратить междоменную атаку вы вероятно предпочтёте запретить вызов потенциально вредоносного содержимого. Таким образом, SVG поддерживается, но не может загрузить внешние растровые изображения когда речь идёт о WebKit и FireFox. Я обратилась в службу поддержки Chrome с просьбой снять запрет на импорт растровых изображений в SVG.

В Firefox отзывчивые SVG также работают своеобразно. Firefox полностью поддерживает SVG. Однако из соображений безопасности Firefox блокирует импорт внешних растровых изображений, даже если они размещены на том же домене. Аргументировать это можно тем что разрешение на загрузку изображений пользователями и последующее отображение этих изображений и скриптом как части SVG несёт в себе угрозу безопасности. Я могу возразить что если на сайте используется ненадёжный контент, предоставленный пользователями, его разработчикам уже есть над чем задуматься.

На данный момент эта простая строка кода…

    <img src="awesomefile.svg" alt="responsive image">

… блокируется в некоторых браузерах и следовательно такое решение нельзя считать приемлемым.

Все браузеры поддерживают медиазапросы для SVG. Все они поддерживают SVG в качестве обычных изображений, которые помещены в тело страницы. Все они поддерживают SVG в качестве фоновых изображений. Но эта поддержка не одинакова вследствие политики обеспечения безопасности, которой придерживаются браузеры.

Все браузеры поддерживают тэг `<object>`. Тэг `<img>` не будет работать пока не будет пересмотрена политика безопасности, которая соблюдается браузерами. Мы можем использовать тэг `<object>`.

### С тэгом <object>

Элемент `<object>` позволяет подключить внешний файл как изображение. Он может помочь обойти недостатки системы безопасности браузеров, которые мы видим в отношении `<img>`, а именно запрет на импорт изображений или скриптов в файл `<img>`. Элемент `<object>` позволяет импортировать и те и другие. 

Его добавление не делает код намного сложнее:

    <object data="awesomefile.svg" type="image/svg+xml"></object>

По умолчанию ширина `<object>` равна ширине родительского элемента. Однако, также ка и для изображений, ему можно задать ширину и высоту с помощью атрибутов `width` и `height` или свойств CSS `width` и `height`. При использовании приёма «машинка с клоунами» для сохранения соотношения сторон достаточно указать значение для одного из параметров. 

Благодаря `viewbox` и `preserveAspectRatio`, объявленным в SVG-файле, `<object>` по умолчанию сохраняет указанное соотношение сторон. Это можно изменить с помощью атрибутов CSS или HTML. 

Как упоминалось ранее, медиазапросы SVG привязаны к контейнеру SVG, а не области просмотра. Медиазапросы в SVG-файле сравниваются с параметрами родительского элемента для тэга `<object>`, а не области просмотра.

Если протестировать [вызов SVG в качестве содержимого `<object>`][19], можно увидеть что такое решение работает во всех браузерах, поддерживающих SVG.

### С тэгом <svg>

Вместо того чтобы подключать внешний SVG-файл, можно [добавить его прямо в разметку с помощью тэга <svg>][20]. Преимуществом является избавление от дополнительного http-запроса на внешний файл .svg. 

К сожалению, Chrome и Safari понимают SVG как полноэкранный блочный элемент изображения и по всей видимости не поддерживают атрибут `preserveAspectRatio` при таком подключении (хотя они поддерживают `preserveAspectRatio` когда SVG подключён через тэг `<object>`).

Ещё один недостаток - это то такое решение не даёт возможность добавить резервный контент для браузеров, которые не поддерживают SVG, в отличии от варианта с `<object>`. Вместо этого для браузеров, которые не поддерживают SVG, следует использовать `background-image` с `height` и `width` для `<svg>`.

### Резервное решение для IE

Элемент `<object>` поддерживают все браузеры, даже мобильные. Но этот приём работает только если браузер поддерживает и SVG тоже. Таким образом, это решение не будет работать в IE 8 и старше или Android 2.3 и старше. Для этих старых браузеров есть резервное решение. Кроме того, нам приходится делать два запроса, на файл SVG и одно из растровых изображений, которые он содержит и которое мы хотим отобразить, для этого также есть решение.

У `<object>` есть интересная особенность, которая отличает его от `<img>` и `<svg>`: в него может быть помещён резервный контент на случай когда браузер не поддерживает тип данных в `<object>`. При желании для браузеров, которые не поддерживают SVG можно в `<object>` вложить тэг `<img>`.

Для IE 8 и старше мы добавим растровое изображение среднего размера, так как они в большинстве случаев используются на мониторах с нормальным разрешением: 

    <object data="awesomefile.svg" type="image/svg+xml"> 
      <img src="medium.png" alt="отзывчивое изображение">
    </object>

К сожалению, вложенный в `<object>` контент, скачивается даже когда содержимое `<object>` отображается нормально и вложенный контент не нужен/не используется. Это добавляет необходимость загрузки изображения среднего размера вне зависимости от того есть в нём необходимость или нет.

Чтобы этого избежать, можно использовать условные комментарии для IE.

    <object data="awesomefile.svg" type="image/svg+xml">
      <!--[if lte IE 8]>
      <img src="medium.png" alt="резервный контент для IE">
      <![endif]-->
    </object>

## Один HTTP-запрос

Мы добились того чтобы SVG загружал только одно растровое изображение. Метод с использованием `<object>` приводит к загрузке и растрового изображения, и SVG-файла. Вместо одного HTTP-запроса мы получаем два. Чтобы избавиться от дополнительных запросов, можно преобразовать SVG в data URI, вместо того чтобы запрашивать внешний файл SVG.

    <object data="data:image/svg+xml,<svg viewBox='0 0 300 329' preserveAspectRatio='xMidYMid meet' xmlns='http://www.w3.org/2000/svg'><title>Приём «Машинка с клоунами»</title><style>svg{background-size:100% 100%;background-repeat:no-repeat;}@media screen and (max-width: 400px){svg{background-image:url(images/small.png);}}@media screen and (min-width: 401px) and (max-width:700px){svg{ background-image:url(images/medium.png);}}@media screen and (min-width: 701px) and (max-width:1000px){svg{background-image:url(images/big.png);}}@media screen and (min-width:1001px){svg{background-image:url(images/huge.png);}}</style></svg>" type="image/svg+xml">
      <!--[if lte IE 8]>
          <img src="images/medium.png" alt="Резервный контент для IE">
      <![endif]-->
    </object>

Такой код выглядит довольно неопрятно, но это всего лишь `data:image/svg+xml`, за которым следует сокращённая запись содержимого SVG-файла. Это тот же код, который мы бы добавили если бы использовали `<svg>`, но этот метод поддерживает атрибут `preserveAspectRatio` для SVG.

Он работает во всех браузерах, которые поддерживают SVG, кроме IE. Это может показаться странным, но легко объясняется тем что в этом случае Microsoft попытался следовать спецификации буква в букву. Спецификация говорит что data URI нужно избегать. Потому чтобы заставить все браузеры, в том числе IE 9 и 10, поддерживать data URI, мы его упоминать не будем: 

    <object data="data:image/svg+xml,%3Csvg%20viewBox='0%200%20300%20329'%20preserveAspectRatio='xMidYMid%20meet'%20xmlns='http://www.w3.org/2000/svg'%3E%3Ctitle%3EClown%20Car%20Technique%3C/title%3E%3Cstyle%3Esvg%7Bbackground-size:100%25%20100%25;background-repeat:no-repeat;%7D@media%20screen%20and%20(max-width:400px)%7Bsvg%7Bbackground-image:url(images/small.png);%7D%7D@media%20screen%20and%20(min-width:401px)%7Bsvg%7Bbackground-image:url(images/medium.png);%7D%7D@media%20screen%20and%20(min-width:701px)%7Bsvg%7Bbackground-image:url(images/big.png);%7D%7D@media%20screen%20and%20(min-width:1001px)%7Bsvg%7Bbackground-image:url(images/huge.png);%7D%7D%3C/style%3E%3C/svg%3E"
    type="image/svg+xml">
      <!--[if lte IE 8]>
        <img src="images/medium.png" alt="Резервный контент для IE">
      <![endif]-->
    </object>

Разметка уродлива, зато работает!

Откройте [нашу первую страницу][21] и [вторую страницу][22], затем зайдите в панель инструментов разработчика и проверьте HTTP-запросы. Вы увидите два HTTP-запроса: на файл HTML и на PNG, вызванное из SVG. В вкладке «Network» также есть строчка для файла SVG. Но обратите внимание что HTTP-запроса на него нет: в качестве статуса для SVG стоит «успешно» и размер скачанного файла равен 0 байт, в то время как запись data URI для SVG весит немного меньше 600 байт.

## Горизонтальная или вертикальная ориентация

В общем говоря, у изображений, помещённых в тело страницы, может быть или вертикальная или горизонтальная ориентация: лица отдельных людей обычно представлены в вертикальной ориентации, тогда как группы людей, товары и пейзажи - в горизонтальной. Некоторые возражают против применения приёма «машинка с клоунами», аргументируя это тем что изображения не меняются согласно ориентации. Это не обязательно так. 

Волшебная особенность этого приёма состоит в том что изображение изменяется когда изменяется размер его контейнера. Можете установить для изображения с горизонтальной ориентацией значение 33% или 240 пикселей или какое-то ещё, а для `<object>` с вертикальной ориентацией - значение ширины 25% или 180 пикселей или что-то в этом роде. Размер `<object>` устанавливается в CSS для вашего HTML. Растровое изображение зависит от медизапроса, который соответствует размеру `<object>`.

Соотношение сторон остается тем же, но вы получаете возможность контролировать то какое изображение отображается изменяя пропорции SVG-контейнера, совмещая медиазапросы из CSS для HTML и CSS для SVG.

Если вы хотите чтобы в режиме горизонтальной ориентации загружались изображения с горизонтальной ориентацией, а в режиме вертикальной ориентации - изображения с вертикальной ориентацией, не используйте атрибут `preserveAspectRatio`. Вместо этого для каждой контрольной точки дизайна задайте абсолютную высоту и ширину с помощью CSS.

## Другие преимущества

Еще одним преимуществом приёма «машинка с клоунами» является то, что все алгоритмы хранятся в SVG-файле. Подобно тому как мы стремимся к разделению контента, представления и поведения, этот метод позволяет разделить алгоритмы отображения изображения и его содержание. Элемент `<img>` или `<object>` - это часть содержания, но алгоритмы которые заставляют их работать можно отделить от слоя содержания: алгоритмы помещены в SVG-изображение чтобы не засорять CSS и HTML. Это преимущество может кого-то подтолкнуть к использованию варианта метода с `<object>` без data URI, несмотря на дополнительный http-запрос, просто потому что этот метод даёт более лёгкий и чистый код. 

Этот приём даёт нам возможность аккуратно организовать изображения, разделив поведение, представление, контент и изображения. Я примерно так вижу структуру отзывчивых изображений:

    изображения/
      клоуны/
        маленький.png
        средний.png
        большой.png
        svg.svg
      машинки/
        маленький.png
        средний.png
        большой.png
        svg.svg
      приёмы/
        маленький.png
        средний.png
        большой.png
        svg.svg

Все ресурсы для одного изображения размещены в одной отдельной папке. Названия файлов с изображениями соответствуют их содержанию, названия папок различаются. Не обязательно засорять HTML дополнительным неиспользуемым кодом чтобы получить отзывчивые изображения, организация и обновление изображений могут быть лёгкими и приятными. 

## Недостатки приёма «Машинка с клоунами»

Мы рассмотрели преимущества приёма. Он всё еще на стадии формирования, потому я ещё не определила все проблемные моменты. Я работаю над решением для тех, которые были обнаружены и предполагаю что возникнут и новые. 

Больше всего меня волнует почему в некоторых случаях изображения в приёме «машинка с клоунами» не ведут себя как обычные PNG, JPEG и GIF. Вот главные проблемные моменты которые я обнаружила: порядок загрузки, резервное решение для Android 2.3.3 и старше, доступность, возможность правого клика мышкой по изображению.

### Макет страницы

Как утверждает Джон Уилкинс (John Wilkins), приём «машинка с клоунами» требует чтобы шаблон CSS был полностью загружен до начала загрузки изображений. У меня не было возможности сравнить загрузку обычных изображений и элемента `<object>` с SVG, который вызывает растровые изображения, потому этот момент я комментировать не могу. 

### Android 2.3 и старше

Android 2.3 и старше не поддерживает SVG. Я нашла три возможных решения, которые еще нужно довести до ума. 

#### `<SVG>` с `background-image`

Вместо `<object>` можно использовать встроенный `<svg>`. Так как IE 8 и старше и Android 2.3 и старше не поддерживают SVG, с помощью CSS эти браузеры могут выделить под `<svg>` пространство с `height` и `width`, а затем объявить растровое изображение как значение для `background-image`.

Так как наша задача состоит в создании отзывчивого изображения без использования фоновых изображений в CSS, такой хак нам не подходит. Если принять такое решение, почему бы вместо приёма «машинка с клоунами» просто не использовать фоновые изображения в CSS для всех браузеров и устройств? 

#### Условные комментарии

Первым добавляем условный комментарий с резервным контентом среднего размера для IE 8 и старше, затем резервный контент меньшего размера для всех браузеров которые игнорируют условные комментарии (в том числе IE 10):

    <!--[if lte IE 8]>
          <img src="images/medium.png" alt="Резервный контент для IE">
        <![endif]-->
        <!--[if !IE]> -->
          <img src="images/small.png" alt="Резервный контент"/>
        <!-- <![endif]-->

Такой код отобразит меньший PNG на телефонах с Android. К сожалению, для всех браузеров кроме IE 9 и старше будет загружаться `small.png`, хотя оно и не будет отображаться. Пытаясь найти решение для старых Android, мы загружаем ненужный маленький PNG на большинство устройств.

#### Проверка с помощью JavaScript

Еще одним решением является проверка признаков с помощью JavaScript, т.е. проверка на поддержку SVG. Добавьте в элемент `<html>` класс `.no-svg` на случай когда браузер не поддерживает SVG. Используя медиазапрос с префиксом WebKit чтобы исключить браузеры, не использующие движок WebKit, настроенный таким образом:

    .no-svg object[type*="svg"] {
        width: 300px; 
        height: 329px; 
        background-image: url(small.png);
    }

Свойства в примере выше задают размер и фоновое изображение для объекта `<object>`. Опять таки, я этот вариант пока не тестировала, к тому же он не обладает доступностью, что позволяет нам перейти к следующей теме.

### Доступность

Преимуществом `<img>` является то, что простой атрибут `alt` может сделать его доступным. Элемент `<object>` не принимает атрибут `alt`. Вот идеи как сделать изображения в приёме «машинка с клоунами» доступными:

* Добавление `<title>` и `<desc>` в SVG-файл
* Добавление `role="img"` и других атрибутов ARIA, таких как `aria-label` и `aria-labeled-by`
* Добавление `tab-index="0"` чтобы `<object>` получил фокусирование без смены порядка табуляции
* Добавление атрибутов `alt` до резервных изображений
* Добавление альтернативного текста между открывающим и закрывающим тэгами `<object>`

Функция VoiceOver компонента Universal Access на Mac OS X читает содержимое тэга `<title>` для SVG и значение атрибута `aria-label` для `<object>` когда `<object>` содержит `tabindex="0"`. Нужно провести проверку используя настоящие скринридеры на [странице для тестирования доступности][23]. 

### Клик правой клавишей мышки и сохранение

Кликнув правой клавишей мышки по изображении на настольном компьютере, вы увидите меню, которое позволяет сохранить изображение. На некоторых телефонах продолжительное касание в области изображения вызовет предложение сохранить его. Это на работает для `<object>` или фоновых изображений, из которых состоит наш SVG.

Этот «недостаток» может стать находкой для того, кто беспокоится что его изображения могут украсть. Мне еще предстоят поиски нормального решения для этой проблемы. Вполне возможно что это удастся с применением JavaScript.

Если отсутствие возможности сохранения после правого клика мышкой - это для вас главная причина не использовать предложенный приём, вспомните что хоть пользователи и могут применить правый клик по изображениям WebP в поддерживающих их браузерах (только Chrome и Opera), дальше у них выбор действий невелик, так как нативные приложения не поддерживают этот новый формат. Но это еще не значит что нужно отказаться от движения вперед с такими экономными в плане трафика приёмами и функциями. 

## Почему «Машинка с клоунами»?

С помощью [Кристофера Шмитта (Christopher Schmitt)][24] и [Эми Грегори (Amie Gregory)][25], я придумала название «машинка с клоунами», так как в этом приёме используется много больших изображений (клоуны) и один маленький файл-изображение (машинка). 

Нам приходится использовать несемантический элемент `<object>`, пока мы пытаемся убедить разработчиков браузеров ввести поддержку растровых изображений в SVG прописанный через `<img>`.

Приём «машинка с клоунами» - это решение которое у нас есть на данный момент. Противники моего варианта настаивают что `<picture>` и/или `srcset` являются лучшим решением, не приводя убедительных аргументов почему приём «машинка с клоунами» хуже. Некоторые говорят что его слабое место - это отсутствие поддержки Android, забывая что Android 2.3.3 и IE 8 тэги `<picture>` или `srcset` не поддерживают точно так же.

Я верю в то что элемент `<object>` можно сделать доступным. В то время как несемантичность - это существенный минус, меня устраивает этот приём при условии что он гарантирует максимальную доступность. Следующей моей задачей является проверка на доступность. Хоть мне бы и хотелось чтобы этот элемент работал с более простым и семантичным `<img>`, как только вопрос доступности будет решён, приём «машинка с клоунами» можно считать готовым к широкому применению.

---

### Примечания

<a href="#ref-1" id="note-1" class="note">1.</a> Машинка с клоунами *(англ. Clown car)* – это распространенный в США комический трюк, при котором из крохотной машинки выбирается немыслимое количество клоунов. Часто применяется в комедийной мультипликации и кино, просмотреть пример можно [здесь][26]. *(примечание переводчика)*


[1]: https://github.com/scottjehl/picturefill
[2]: http://www.sencha.com/products.io
[3]: http://www.w3.org/TR/html-picture-element/#the-picture-element
[4]: http://www.w3.org/html/wg/drafts/srcset/w3c-srcset/
[5]: http://www.w3.org/community/respimg/2012/06/18/florians-compromise/
[6]: http://www.brucelawson.co.uk/2013/responsive-images-intrerim-report/
[7]: https://docs.google.com/presentation/d/1y_A6VOZy9bD2i0VLHv9ZWr0W3hZJvlTNCDA0itjI0yM/edit?pli=1#slide=id.p19
[8]: http://caniuse.com/#search=svg
[9]: http://jeremie.patonnier.net/experiences/svg/media-queries/test.html
[10]: http://estelle.github.io/clowncar/local.svg
[11]: http://estelle.github.io/clowncar/inlinesvg.html
[12]: https://code.google.com/p/chromium/issues/detail?id=231622
[13]: http://estelle.github.io/clowncar/jpeg/jpeg/svg.svg
[14]: http://estelle.github.io/clowncar/object/bgonly.svg
[15]: http://estelle.github.io/clowncar/jpeg/jpeg/svg.svg
[16]: http://estelle.github.io/clowncar/imagetag/
[17]: http://estelle.github.io/clowncar/imagetag/
[18]: https://code.google.com/p/chromium/issues/detail?id=234082
[19]: http://estelle.github.io/clowncar/object/bgonly.html
[20]: http://estelle.github.io/clowncar/inlinesvg.html
[21]: http://estelle.github.io/clowncar/singlerequest.html
[22]: http://estelle.github.io/clowncar/index2.html
[23]: http://estelle.github.io/clowncar/accessibiltytest.html
[24]: http://dwmgbook.com/
[25]: http://www.precisemoves.com/
[26]: http://www.youtube.com/watch?feature=player_detailpage&v=gqjB2Yu87D0#t=50s