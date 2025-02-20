^title Карта теори... территории!
^part Welcome

> Добро пожаловать
> У вас должна быть карта, даже если она грубая. Иначе вы будете блуждать без цели.
> В Властелине колец я никогда не заставлял персонажей проходить больше, чем они могли за день.

> <cite>Дж. Р. Р. Толкин</cite>
Мы не хотим бесцельно блуждать, поэтому, прежде чем отправиться в путь, давайте изучим территорию, которую уже исследовали создатели языков программирования. Это поможет нам понять, куда мы идем, и какие альтернативные пути уже были пройдены.

Для начала я установлю сокращения. Значительная часть этой книги посвящена реализации языка, что отличается от самого языка в его некоторой платоновской идеальной форме. Такие вещи, как «стек», «байткод» и «рекурсивный спуск», — это всего лишь технические детали конкретной реализации. С точки зрения пользователя, если результат работы соответствует спецификации языка, то всё остальное — детали реализации.

Мы будем много говорить о таких деталях, поэтому если каждый раз писать «реализация языка», мои пальцы сотрутся до костей. Вместо этого я буду использовать просто «язык» — когда говорю либо о языке, либо о его реализации, либо о том и другом сразу, если различие очевидно из контекста.
Части языка
Инженеры создают языки программирования с тёмных веков вычислительной техники. Как только мы научились «разговаривать» с компьютерами, мы тут же осознали, что это слишком сложно, и начали использовать сами компьютеры, чтобы облегчить процесс. Меня поражает, что, несмотря на то, что современные машины стали буквально в миллион раз быстрее и обладают огромными объёмами памяти, сам процесс создания языков программирования практически не изменился.

Хотя область, охватываемая разработчиками языков, обширна, тропинок, проложенных сквозь неё, <span name="dead">не так уж и много</span>. Не все языки следуют одному и тому же маршруту — некоторые используют сокращённые пути — но в целом они удивительно похожи, начиная от первого компилятора COBOL контр-адмирала Грейс Хоппер и заканчивая каким-нибудь очередным модным языком, транслирующим код в JavaScript, документация по которому состоит из единственного плохо отредактированного README в Git-репозитории.

<aside name="dead">
Конечно, есть и тупики — грустные закоулки научных статей по информатике с нулём цитирований и забытые оптимизации, имевшие смысл только тогда, когда память измерялась в байтах.

</aside>

Я представляю себе сеть возможных реализаций как восхождение на гору. Внизу мы начинаем с исходного кода программы — просто строки символов. Каждый этап анализа преобразует программу в более высокоуровневое представление, в котором семантика — то, что автор хочет, чтобы компьютер сделал — становится более явной.

В конце концов, мы достигаем вершины. У нас появляется возможность взглянуть на программу с высоты и понять её смысл. Затем мы начинаем спуск. Программа последовательно трансформируется в низкоуровневые представления, приближаясь к тому, что процессор действительно может выполнить.

<img src="image/a-map-of-the-territory/mountain.png" alt="The branching paths a language may take over the mountain." class="wide" />

Давайте рассмотрим эти этапы подробнее. Наше путешествие начинается слева, с обычного текста исходного кода:

<img src="image/a-map-of-the-territory/string.png" alt="var average = (min + max) / 2;" />

### Scanning

Первый шаг - **сканирование**, известное также **лексинг**, или (если вы хотите произвести впечатление) 
**лексический анализ**. Они все значат одно и тоже. 
Мне нравится лексинг, потому, что это звучит как что-то, что 
суперзлой суперзлодей хочет сделать, но я буду использовать
"сканирование", оно более распространено.

Сканер (или лексер) получает последовательность символов и группирует их в нечто, более похожее на <span name="word">«слова»</span>. В языке программирования такие «слова» называются токенами. Некоторые токены — это одиночные символы, например ( и ,. Другие состоят из нескольких символов, например числа (123), строковые литералы ("привет!") и идентификаторы (min).

<aside name="word">
Слово «лексический» происходит от греческого корня «lex», означающего «слово».

</aside>
Некоторые символы в исходном коде не имеют значения. Пробелы часто игнорируются, а комментарии, по определению, не оказывают влияния на работу программы. Обычно сканер просто выбрасывает их, оставляя последовательность значимых токенов.

<img src="image/a-map-of-the-territory/tokens.png" alt="[var] [average] [=] [(] [min] [+] [max] [)] [/] [2] [;]" />

### Parsing

The next step is **parsing**. This is where our syntax gets a **grammar** -- the
ability to compose larger expressions and statements out of smaller parts. Did
you ever diagram sentences in English class? If so, you've done what a parser
does, except that English has thousands and thousands of "keywords" and an
overflowing cornucopia of ambiguity. Programming languages are much simpler.

A **parser** takes the flat sequence of tokens and builds a tree structure that
mirrors the nested nature of the grammar. These trees have a couple of different
names -- **"parse tree"** or **"abstract syntax tree"** -- depending on how
close to the bare syntactic structure of the source language they are. In
practice, language hackers usually call them **"syntax trees"**, **"ASTs"**, or
often just **"trees"**.

<img src="image/a-map-of-the-territory/ast.png" alt="An abstract syntax tree." />

Следующий шаг — разбор (парсинг). Здесь наш синтаксис получает грамматику — возможность составлять более сложные выражения и операторы из простых частей. Помните, как на уроках английского языка разбирали предложения? Если да, то вы уже делали то же самое, что делает парсер, только английский язык имеет тысячи «ключевых слов» и бездну двусмысленностей. Языки программирования гораздо проще.

Парсер берет плоскую последовательность токенов и строит древовидную структуру, которая отражает вложенную природу грамматики. Эти деревья могут называться по-разному — "дерево разбора" (parse tree) или "абстрактное синтаксическое дерево" (abstract syntax tree, AST) — в зависимости от того, насколько близко они к исходной структуре синтаксиса языка. На практике разработчики чаще называют их "синтаксическими деревьями", "AST", или просто "деревьями".

<img src="image/a-map-of-the-territory/ast.png" alt="Абстрактное синтаксическое дерево." />

Разбор имеет длинную и богатую историю в информатике и тесно связан с исследованиями в области искусственного интеллекта. Многие современные методы разбора языков программирования изначально разрабатывались для обработки человеческих языков исследователями ИИ, которые пытались научить компьютеры общаться с нами.

Оказалось, что естественные языки слишком хаотичны для жестких грамматик этих парсеров, но они идеально подошли для более простых искусственных грамматик языков программирования. Увы, даже с такими простыми грамматиками мы, несовершенные люди, умудряемся допускать ошибки. Поэтому парсер также сообщает нам о них, выдавая синтаксические ошибки.

**Статический анализ**

Первые два этапа выглядят примерно одинаково во всех реализациях. Теперь начинают проявляться особенности конкретного языка. На этом этапе мы уже знаем синтаксическую структуру кода — такие вещи, как приоритет операторов и вложенность выражений, — но пока не понимаем многого за пределами этого.

Например, в выражении a + b мы знаем, что складываются a и b, но не знаем, на что они ссылаются. Это локальные переменные? Глобальные? Где они объявлены?

Первый вид анализа, который выполняет большинство языков, называется связыванием (binding) или разрешением (resolution). Для каждого идентификатора мы выясняем, где он был объявлен, и соединяем их. Здесь вступает в игру область видимости (scope) — участок кода, в котором можно использовать определенное имя.

Если язык является <span name="type">статически типизированным</span>, то на этом этапе выполняется проверка типов. Когда мы знаем, где объявлены a и b, можно определить их типы. Если их типы не поддерживают операцию сложения, возникает ошибка типов.

<aside name="type">
Язык, который мы будем разрабатывать в этой книге, является динамически типизированным, поэтому проверка типов будет выполняться позже — во время исполнения.

</aside>
Сделайте глубокий вдох. Мы достигли вершины горы и получили панорамный вид на программу пользователя. Вся эта семантическая информация, которую мы выявили в ходе анализа, должна где-то храниться. Есть несколько способов сохранить её:

Чаще всего она записывается обратно в виде атрибутов узлов синтаксического дерева — дополнительных полей, которые не заполнялись во время разбора, но заполняются позже.

Иногда данные хранятся в отдельной таблице для быстрого поиска. Обычно ключами в такой таблице являются идентификаторы — имена переменных и объявлений. В этом случае она называется таблицей символов (symbol table), а значения, привязанные к каждому ключу, указывают, на что ссылается данный идентификатор.

Самый мощный инструмент управления данными — преобразование дерева в совершенно новую структуру, которая напрямую выражает семантику кода. Об этом речь пойдет в следующем разделе.

Все этапы до этого момента считаются фронтендом реализации. Можно было бы подумать, что всё, что идет дальше, — это бэкенд, но нет. В старые времена, когда появились термины «фронтенд» и «бэкенд», компиляторы были проще. Позже исследователи добавили новые промежуточные фазы между этими двумя частями. Вместо того чтобы отказываться от старых терминов, Уильям Вульф и его коллеги объединили их в очаровательное, но пространственно парадоксальное название — "миддлэнд (middle end)".

### Промежуточные представления

Можно представить компилятор как конвейер, где каждая стадия организует код так, чтобы следующую стадию было проще реализовать. Начало конвейера (фронтенд) зависит от языка программирования, на котором пишет пользователь. Конец (бэкенд) ориентирован на конечную архитектуру, на которой будет выполняться код.

Посередине код может храниться в некоем <span name="ir">промежуточном представлении</span> (или "IR"), которое не привязано жестко ни к исходному коду, ни к целевой форме (отсюда и название "промежуточное"). Вместо этого IR служит интерфейсом между этими двумя уровнями.

<aside name="ir">
Существует несколько хорошо известных стилей IR. Попробуйте поискать такие термины, как "граф потока управления" (control flow graph), "статическое одноназначное представление" (static single-assignment), "стиль передачи продолжений" (continuation-passing style) и "трехадресный код" (three-address code).

</aside>
Использование IR позволяет поддерживать сразу несколько языков программирования и платформ с меньшими затратами. Допустим, вы хотите написать компиляторы для Pascal, C и Fortran, а также поддерживать архитектуры x86, ARM и, скажем, SPARC. В обычном случае вам пришлось бы писать девять отдельных компиляторов: Pascal→x86, C→ARM и так далее.

Общее <span name="gcc">промежуточное представление</span> значительно упрощает задачу. Достаточно написать один фронтенд для каждого исходного языка, который преобразует код в IR. Затем написать один бэкенд для каждой целевой архитектуры. Теперь можно комбинировать их как угодно, получая нужные компиляторы.

<aside name="gcc">
Если вы когда-нибудь задавались вопросом, как GCC поддерживает столько разных языков и архитектур, например, Modula-3 на Motorola 68k, то теперь знаете. Фронтенды языков транслируют код в один из нескольких IR, главным образом в GIMPLE. Затем бэкенды, такие как 68k, берут GIMPLE и генерируют нативный код.

</aside>
Есть еще одна важная причина преобразовывать код в форму, которая лучше раскрывает его семантику...

### Оптимизация
Как только мы понимаем смысл программы пользователя, мы можем заменить ее на другую, которая имеет такую же семантику, но работает эффективнее — то есть оптимизировать ее.

Простой пример — свертывание констант (constant folding): если выражение всегда вычисляется в одно и то же значение, мы можем выполнить вычисления на этапе компиляции и заменить выражение его результатом. Если пользователь написал:

```java
pennyArea = 3.14159 * (0.75 / 2) * (0.75 / 2);
```

Мы можем преобразовать это выражение ещё во время компиляции в:

```java
pennyArea = 0.4417860938;
```

Оптимизация — это огромная часть работы над языками программирования. Многие специалисты посвящают всю свою карьеру этому, выжимая максимум производительности из компиляторов, чтобы ускорить выполнение программ хотя бы на доли процента. Это может перерасти в настоящую одержимость.

Но в этой книге мы <span name="rathole">не будем углубляться</span> в эту тему. Многие успешные языки программирования имеют минимальные оптимизации во время компиляции. Например, Lua и CPython генерируют относительно неэффективный код и сосредотачивают усилия по оптимизации на этапе выполнения.

<aside name="rathole">
Если вам все же интересно изучить эту область, начните с таких тем, как "распространение констант" (constant propagation), "устранение общих подвыражений" (common subexpression elimination), "перемещение инвариантного кода за пределы цикла" (loop invariant code motion), "глобальная нумерация значений" (global value numbering), "ослабление вычислений" (strength reduction), "скалярная замена агрегатов" (scalar replacement of aggregates), "удаление мертвого кода" (dead code elimination) и "развертывание циклов" (loop unrolling).

</aside>
### Генерация кода
Мы применили все возможные оптимизации к программе пользователя. Последний шаг — это преобразование её в форму, которую машина действительно сможет выполнить. Другими словами, мы генерируем код, где «код» относится к примитивным инструкциям, похожим на ассемблерные, которые выполняет процессор, а не к тому «исходному коду», который мог бы прочитать человек.

Наконец, мы попадаем в бэкенд, спускаясь с другой стороны горы. С этого момента представление кода становится всё более примитивным, как будто эволюция пошла в обратную сторону, пока мы не дойдём до чего-то, что сможет понять наша простодушная машина.

Нам предстоит сделать выбор. Генерировать инструкции для реального процессора или для виртуального? Если мы генерируем реальный машинный код, то получаем исполняемый файл, который операционная система может загрузить непосредственно в процессор. Нативный код работает молниеносно, но его генерация требует значительных усилий. Современные архитектуры содержат огромное количество инструкций, сложные конвейеры и достаточно <span name="aad">исторического наследия</span>, чтобы заполнить Boeing 747.

Кроме того, использование инструкций конкретного процессора привязывает компилятор к определённой архитектуре. Если ваш компилятор создаёт код для x86, он не сможет выполняться на ARM. В 60-х годах, во времена «кембрийского взрыва» компьютерных архитектур, эта привязка серьёзно мешала портируемости.

<aside name="aad">
Например, инструкция AAD («ASCII Adjust AX Before Division») позволяет выполнять деление, что звучит полезно. Однако она принимает в качестве операндов два двоично-кодированных десятичных числа (BCD), упакованных в 16-битный регистр. Когда в последний раз вам нужна была работа с BCD на 16-битной машине?

</aside>
Чтобы обойти эту проблему, такие программисты, как Мартин Ричардс и Никлаус Вирт (создатели BCPL и Pascal), заставили свои компиляторы генерировать код для виртуальной машины. Вместо инструкций для реального процессора они создавали код для гипотетической, идеализированной машины. Вирт назвал этот код "p-code" (от «portable» — портируемый), но сегодня его чаще называют байт-кодом, поскольку каждая инструкция обычно занимает один байт.

Эти синтетические инструкции спроектированы так, чтобы лучше соответствовать семантике языка и не зависеть от особенностей конкретной архитектуры с её историческим багажом. Можно представить их как плотное бинарное представление низкоуровневых операций языка.

### Виртуальная машина
Если ваш компилятор генерирует байт-код, на этом работа не заканчивается. Так как не существует процессора, который мог бы выполнять этот байт-код напрямую, вам придётся его интерпретировать. Тут есть два варианта. Можно написать небольшой мини-компилятор для каждой целевой архитектуры, который преобразует байт-код в нативный код для этого процессора. Это означает, что вам всё равно нужно адаптировать код под <span name="shared">каждую</span> архитектуру, но этот последний этап довольно прост, а вся остальная часть компилятора остаётся неизменной. Таким образом, ваш байт-код фактически становится промежуточным представлением.

<aside name="shared">
Основной принцип здесь таков: чем дальше по компиляторному конвейеру вы отодвинете архитектурно-зависимые работы, тем больше этапов можно будет использовать повторно для разных архитектур.

Однако тут есть компромисс. Многие оптимизации, такие как распределение регистров и выбор инструкций, работают лучше, если они знают особенности конкретного процессора. Определение того, какие части компилятора можно сделать общими, а какие должны быть специфичными для целевой платформы — это искусство.

</aside>
Другой вариант — написать виртуальную машину (VM), программу, которая эмулирует гипотетический процессор, поддерживающий ваш виртуальный байт-код. Выполнение байт-кода на виртуальной машине медленнее, чем его предварительная компиляция в машинный код, поскольку каждая инструкция интерпретируется при каждом её выполнении. Однако такой подход даёт простоту и портируемость. Реализовав виртуальную машину, скажем, на C, можно запустить ваш язык на любой платформе, где есть C-компилятор. Именно так работает наш второй интерпретатор.

### Среда выполнения
Наконец, мы преобразовали программу пользователя в исполняемую форму. Последний шаг — её выполнение. Если мы скомпилировали её в машинный код, просто передаём исполняемый файл операционной системе, и он запускается. Если же мы скомпилировали в байт-код, то запускаем виртуальную машину и загружаем в неё программу.

В любом случае, практически любой язык, кроме самых низкоуровневых, требует некоторых сервисов во время выполнения. Например, если язык автоматически управляет памятью, потребуется сборщик мусора для очистки неиспользуемых данных. Если он поддерживает проверку типа объекта с помощью instanceof, то необходимо хранить информацию о типе каждого объекта во время выполнения.

Все эти механизмы работают во время выполнения программы, поэтому они называются средой выполнения (runtime). В полностью компилируемых языках код среды выполнения вставляется прямо в исполняемый файл. Например, в Go каждая скомпилированная программа содержит свою собственную копию среды выполнения Go. В языках, работающих через интерпретатор или виртуальную машину, среда выполнения встроена в сам интерпретатор. Так работают большинство реализаций Java, Python и JavaScript.

### Кратчайшие пути и альтернативные маршруты

That's the long path covering every possible phase you might implement. Many
languages do walk the entire route, but there are a few shortcuts and alternate
paths.

### Однопроходные компиляторы

Некоторые простые компиляторы совмещают разбор, анализ и генерацию кода так, что создают выходной код прямо в процессе парсинга, не выделяя никаких синтаксических деревьев или других промежуточных представлений. Такие <span name="sdt">однопроходные компиляторы</span> ограничивают дизайн языка. У вас нет промежуточных структур данных для хранения глобальной информации о программе, и вы не возвращаетесь к уже разобранной части кода. Это означает, что, как только вы видите выражение, вам уже нужно знать достаточно, чтобы правильно его скомпилировать.

<aside name="sdt">

[**Синтаксически управляемый перевод**][pass] — это структурированная техника для построения таких компиляторов, работающих «в один заход». Вы связываете действие с каждой частью грамматики, обычно это действие генерирует выходной код. Затем, когда парсер находит этот фрагмент синтаксиса, он выполняет соответствующее действие, создавая целевой код по одному правилу за раз.

[pass]: https://en.wikipedia.org/wiki/Syntax-directed_translation

</aside>

Pascal и C были спроектированы с учетом этого ограничения. В то время память была настолько ценной, что компилятор мог не вмещать в нее даже исходный файл, не говоря уже о всей программе. Именно поэтому грамматика Pascal требует, чтобы объявления типов появлялись в начале блока. Это также объясняет, почему в C нельзя вызвать функцию, объявленную ниже текущего кода, если только вы не добавили явное предварительное объявление, которое подскажет компилятору, как сгенерировать код для вызова этой функции.

### Интерпретаторы по дереву
Некоторые языки программирования начинают выполнение кода сразу после его разбора в AST (с возможным применением статического анализа). Для выполнения программы интерпретатор проходит по синтаксическому дереву, обрабатывая ветви и листья по очереди, вычисляя каждый узел по мере прохождения.

Этот способ интерпретации часто используется в студенческих проектах и небольших языках, но редко встречается в языках общего назначения, поскольку он медленный. Одним из примечательных исключений был оригинальный интерпретатор <span name="ruby">Ruby</span>, который использовал обход дерева до версии 1.9.

<aside name="ruby">
В версии 1.9 стандартная реализация Ruby сменилась с оригинального MRI ("Matz' Ruby Interpreter") на YARV ("Yet Another Ruby VM"), созданный Коити Сасадой. YARV является виртуальной машиной с байт-кодом.

</aside>
Некоторые считают, что термин «интерпретатор» относится только к таким реализациям, но другие используют его более широко. Чтобы избежать споров, я буду использовать однозначный термин «интерпретатор, выполняющий обход дерева». Наш первый интерпретатор будет работать именно так.

### Трансляторы/транспиляторы

<span name="gary">Разработка</span> полноценного бэкенда для языка может потребовать много работы. Если у вас уже есть какое-то универсальное промежуточное представление (IR), вы можете просто подключить к нему свой фронтенд. Но что, если у вас его нет? В таком случае можно использовать другой исходный язык как промежуточное представление.

Мы пишем фронтенд для своего языка. Затем, вместо того чтобы выполнять все преобразования до низкоуровневого кода, мы просто создаем строку кода на другом языке, который примерно так же высокоуровневый, как наш. Затем мы используем существующие компиляторы для этого языка, чтобы довести код до исполняемой формы.

Ранее это называлось «компилятор из исходного кода в исходный код» или «транскомпилятор». После появления языков, компилируемых в JavaScript для работы в браузере, появилось модное название «транспилер».

<aside name="gary">

The first transcompiler, XLT86, translated 8080 assembly into 8086 assembly.
That might seem straightforward, but keep in mind the 8080 was an 8-bit chip and
the 8086 a 16-bit chip that could use each register as a pair of 8-bit ones.
XLT86 did data flow analysis to track register usage in the source program and
then efficiently map it to the register set of the 8086.

It was written by Gary Kildall, a tragic hero of computer science if there
ever was one. One of the first people to recognize the promise of
microcomputers, he created PL/M and CP/M, the first high level language and OS
for them.

He was a sea captain, business owner, licensed pilot, and motocyclist. A TV host
with the Kris Kristofferson-esque look sported by dashing bearded dudes in the
80s. He took on Bill Gates and, like many, lost, before meeting his end
in a biker bar under mysterious circumstances. He died too young, but sure as
hell lived before he did.

</aside>

While the first transcompiler translated one assembly language to another,
today, almost all transpilers work on higher-level languages. After the viral
spread of UNIX to machines various and sundry, there began a long tradition of
compilers that produced C as their output language. C compilers were available
everywhere UNIX was and produced efficient code, so targetting C was a good way
to get your language running on a lot of architectures.

Web browsers are the "machines" of today, and their "machine code" is
JavaScript, so these days it seems [almost every language out there][js] has a
compiler that targets JS since that's the <span name="js">only</span> way to get
your code running in a browser.

[js]: https://github.com/jashkenas/coffeescript/wiki/list-of-languages-that-compile-to-js

<aside name="js">

JS may not be the only language browsers natively support for much longer. If
[Web Assembly][] takes off, browsers will support another lower-level language
specifically designed to be targeted by compilers.

[web assembly]: https://github.com/webassembly/

</aside>

The front end -- scanner and parser -- of a transpiler looks like other
compilers. Then, if the source language is only a simple syntactic skin over the
target language, it may skip analysis entirely and go straight to outputting the
analogous syntax in the destination language.

If the two languages are more semantically different, then you'll see more of
the typical phases of a full compiler including analysis and possibly even
optimization. Then, when it comes to code generation, instead of outputting some
binary language like machine code, you produce a string of grammatically correct
source (well, destination) code in the target language.

Either way, you then run that resulting code through the output language's
existing compilation pipeline and you're good to go.

### Just-in-time compilation

This last one is less of a shortcut and more a challenging scramble best
reserved for experts. The fastest way to execute code is by compiling it to
machine code, but you might not know what architecture your end user's machine
supports. What to do?

You can do the same thing the HotSpot JVM, Microsoft's CLR and most JavaScript
interpreters do. On the end user's machine, when the program is loaded -- either
from source in the case of JS, or platform-independent bytecode for the JVM and
CLR -- you compile it to native for the architecture their computer supports.
Naturally enough, this is called **just-in-time compilation.** Most hackers just
say "JIT", pronounced like it rhymes with "fit".

The most sophisticated JITs insert profiling hooks into the generated code to
see which regions are most performance critical and what kind of data is flowing
through them. Then, over time, they will automatically recompile those <span
name="hot">hot spots</span> with more advanced optimizations.

<aside name="hot">

This is, of course, exactly where the HotSpot JVM gets its name.

</aside>

## Compilers and Interpreters

Now that I've stuffed your head with a dictionary's worth of programming
language jargon, we can finally address a question that's plagued coders since
time immemorial: "What's the difference between a compiler and an interpreter?"

It turns out this is like asking the difference between a fruit and a vegetable.
That seems like a binary either-or choice, but actually "fruit" is a *botanical*
term and "vegetable" is *culinary*. One does not imply the negation of the
other. There are fruits that aren't vegetables (apples) and vegetables that are
not fruits (carrots), but also edible plants that are both fruits *and*
vegetables, like tomatoes.

<span name="veg"></span></span>

<img src="image/a-map-of-the-territory/plants.png" alt="A Venn diagram of edible plants" />

<aside name="veg">

There are even plant-based foods that are *neither*, like nuts and cereals. (And
peanuts aren't even nuts!)

</aside>

So, back to languages:

* **Compilation** is an *implementation technique* that involves translating a
  source language to some other -- usually lower-level -- form. When you
  generate bytecode or machine code, you are compiling. When you transpile to
  another high-level language you are compiling too. If users run a tool that
  takes a source language and outputs some target language and then stops, we
  call that tool a **compiler**.

* **Interpretation** describes the *user experience of executing a language*. If
  the end user has a single tool that takes in source code and is able to then
  execute it immediately, that tool is an **interpreter**.

Like apples and oranges, some implementations are clearly compilers and *not*
interpreters. GCC and Clang take your C code and compile it to machine code. An
end user runs that executable directly and may never even know which tool was
used to compile it. So those are *compilers* for C.

In older versions of Matz' canonical implementation of Ruby, the user ran Ruby
from source. The implementation parsed it and ran it directly by traversing the
syntax tree. No other translation occurred, either internally or in any
user-visible form. So this was definitely an *interpreter* for Ruby.

But what of CPython? When you run your Python program using it, the code is
parsed and converted to an internal bytecode format, which is then executed
inside the VM. From the user's perspective, this is clearly an interpreter --
they run their program from source. But if you look under CPython's scaly skin,
you'll see that there is definitely some compiling going on.

The answer is that it is <span name="go">both</span>. CPython *is* an
interpreter, and it *has* a compiler. In practice, most scripting languages work
this way, as you can see:

<aside name="go">

The [Go tool][go] is even more of a horticultural curiosity. If you run `go
build`, it compiles your Go source code to machine code and stops. If you type
`go run`, it does that then immediately executes the generated executable.

So `go` *has* a compiler, *is* an interpreter, and *is* also a compiler.

[go tool]: https://golang.org/cmd/go/

</aside>

<img src="image/a-map-of-the-territory/venn.png" alt="A Venn diagram of compilers and interpreters" />

That overlapping region in the center is where our second interpreter lives too,
since it internally compiles to bytecode. So while this book is nominally about
interpreters, we'll cover some compilation too.

## Our Journey

That's a lot to take in all at once. Don't worry. This isn't the chapter where
you're expected to *understand* all of these pieces and parts. I just want you
to know that they are out there and roughly how they fit together.

This map should serve you well as you explore the territory beyond the guided
path we take in this book. I want to leave you yearning to strike out on your
own and wander all over that mountain.

But, for now, it's time for our own journey to begin. Tighten your bootlaces,
cinch up your pack, and come along. From <span name="here">here</span> on out,
all you need to focus on is the path in front of you.

<aside name="here">

Henceforth, I promise to tone down the whole mountain metaphor thing.

</aside>

<div class="challenges">

## Challenges

1. Pick an open source implementation of a language you like. Download the
   source code and poke around in it. Try to find the code that implements the
   scanner and parser. Are they hand-written, or generated using tools like
   Lex and Yacc? (`.l` or `.y` files usually imply the latter.)

1. Just-in-time compilation tends to be the fastest way to implement a
   dynamically-typed language, but not all of them use it. What reasons are
   there to *not* JIT?

1. Most Lisp implementations that compile to C also contain an interpreter that
   lets them execute Lisp code on the fly as well. Why?

</div>
