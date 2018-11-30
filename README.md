# Лабораторная работа №4. Очереди#

## Введение ##

Лабораторная работа направлена на практическое освоение динамической структуры данных **Очередь**. С этой целью в лабораторной работе изучаются различные варианты структуры хранения очереди и разрабатываются методы и программы решения задач с использованием очередей. В качестве области приложений выбрана тема эффективной организации выполнения потока заданий на вычислительных системах.

Очередь характеризуется таким порядком обработки значений, при котором вставка новых элементов производится в конец очереди, а извлечение – из начала. Подобная организация данных широко встречается в различных приложениях. В качестве примера использования очереди предлагается задача разработки системы имитации однопроцессорной ЭВМ. Рассматриваемая в рамках лабораторной работы схема имитации является одной из наиболее простых моделей обслуживания заданий в вычислительной системе и обеспечивает тем самым лишь начальное ознакомление с проблемами моделирования и анализа эффективности функционирования реальных вычислительных систем.

**Очередь (англ. queue)**, – схема запоминания информации, при которой каждый вновь поступающий ее элемент занимает крайнее положение (*конец очереди*). При выдаче информации из очереди выдается элемент, расположенный в очереди первым (*начало очереди*), а оставшиеся элементы продвигаются к началу; следовательно, элемент, поступивший первым, выдается первым. 

## Требования к лабораторной работе

Для вычислительной системы (ВС) с одним процессором и однопрограммным последовательным режимом выполнения поступающих заданий требуется разработать программную систему для имитации процесса обслуживания заданий в ВС. При построении модели функционирования ВС должны учитываться следующие основные моменты обслуживания заданий:

- генерация нового задания;
- постановка задания в очередь для ожидания момента освобождения процессора;
- выборка задания из очереди при освобождении процессора после обслуживания очередного задания.

По результатам проводимых вычислительных экспериментов система имитации должна выводить информацию об условиях проведения эксперимента (интенсивность потока заданий, размер очереди заданий, производительность процессора, число тактов имитации) и полученные в результате имитации показатели функционирования вычислительной системы, в т.ч.

- количество поступивших в ВС заданий;
- количество отказов в обслуживании заданий из-за переполнения очереди;
- среднее количество тактов выполнения заданий;
- количество тактов простоя процессора из-за отсутствия в очереди заданий для обслуживания.

Показатели функционирования вычислительной системы, получаемые при помощи систем имитации, могут использоваться для оценки эффективности применения ВС; по результатам анализа показателей могут быть приняты рекомендации о целесообразной модернизации характеристик ВС (например, при длительных простоях процессора и при отсутствии отказов от обслуживания заданий желательно повышение интенсивности потока обслуживаемых заданий и т.д.).

## Условия и ограничения

Сделаем следующие основные допущения:
1 При планировании очередности обслуживания заданий возможность задания приоритетов не учитывается.
2 Моменты появления новых заданий и моменты освобождения процессора рассматриваются как случайные события.

## Структуры данных

Напомним, что динамическая структура есть математическая структура, которой соответствует частично-упорядоченное (по включению) базовое множество **М**, операции вставки и удаления элементы которого являются структурами данных. При этом отношения включения индуцируются операциями преобразования структуры данных.

Таким образом, очередь есть динамическая структура, операции вставки и удаления переводят очередь из одного состояния в другое, при этом добавление новых элементов осуществляется в конец очереди, а извлечение – из начала очереди (дисциплина обслуживания «первым пришел – первым обслужен.

Важной задачей при реализации системы обслуживания очереди является выбор структуры хранения, обеспечивающей решение проблемы эффективного использования памяти без перепаковок и без использования связных списков (требующих дополнительных затрат памяти на указатели).

Как и в случае со стеком, в качестве структуры хранения очереди предлагается использовать одномерный (одноиндексный) массив, размещаемый в динамической области памяти. В связи с характером обработки значений, располагаемых в очереди, для указания хранимых в очереди данных необходимо иметь два указателя – на начало и конец очереди. Эти указатели увеличивают свое значение: один при вставке, другой при извлечении элемента. Таким образом, в ходе функционирования очереди может возникнуть ситуация, когда оба указателя достигнут своего наибольшего значения и дальнейшее пополнение очереди станет невозможным, несмотря на наличие свободного пространства в очереди. Одним из решений проблемы «движения» очереди является организация на одномерном массиве кольцевого буфера. Кольцевым буфером называется структура хранения, получаемая из вектора расширением отношения следования парой **p(an,a1)**. 

![Кольцевой буфер](/images/1.PNG)

Структура хранения очереди в виде кольцевого буфера может быть определена как одномерный (одноиндексный) массив, размещаемый в динамической области памяти и расположение данных в котором определяется при помощи следующего набора параметров:

- **pMem** – указатель на память, выделенную для кольцевого буфера,
- **MemSize** – размер выделенной памяти,
- **MaxMemSize** – размер памяти, выделяемый по умолчанию, если при создании кольцевого буфера явно не указано требуемое количество элементов памяти,
 
- **DataCount** – количество запомненных в очереди значений,
- **Hi** – индекс элемента массива, в котором хранится последний элемент очереди,
- **Li** – индекс элемента массива, в котором хранится первый элемент очереди.

![Кольцевой буфер (на массиве)](/images/2.PNG)

В связи с тем, что структура хранения очереди во многом аналогична структуре хранения стек, предлагается класс для реализации очереди построить наследованием от класса стек, описанного в лабораторной работе №3. При наследовании достаточно переопределить методы **Get** и **GetNextIndex**. В методе **Get** изменяется индекс для получения элемента (извлечение значений происходит из начала очереди), метод **GetNextIndex** реализует отношение следования на кольцевом буфере.

## Алгоритмы

Для работы с очередью предлагается реализовать следующие операции:

- Метод **Put**   добавить элемент;
Добавление элемента в очередь аналогично добавлению в стек, используется метод класса стек.
- Метод **Get** удалить элемент;
При удалении элемента из очереди необходимо возвратить значение из динамического массива по индексу начала очереди, переместить указатель начала очереди и уменьшить количество элементов.
- Метод **IsEmpty**   проверить очередь на пустоту;
- Метод **IsFull**   проверить очередь на полноту.

Данные методы наследуются из класса **стек**.

## Моделирование

Для моделирования момента появления нового задания можно использовать значение датчика случайных чисел. Если значение датчика меньше некоторого порогового значения **q1, 0<=q1<=1**, то считается, что на данном такте имитации в вычислительную систему поступает новое задание (тем самым параметр **q1** можно интерпретировать как величину, регулирующую интенсивность потока заданий – новое задание генерируется в среднем один раз за **(1/q1)** тактов).

Моделирование момента завершения обработки очередного задания также может быть выполнено по описанной выше схеме. При помощи датчика случайных чисел формируется еще одно случайное значение, и если это значение меньше порогового значения **q2, 0<=q2<=1**,то принимается, что на данном такте имитации процессор завершил обслуживание очередного задания и готов приступить к обработке задания из очереди ожидания (тем самым параметр **q2** можно интерпретировать как величину, характеризующую производительность процессора вычислительной системы – каждое задание обслуживается в среднем за **(1/q2)** тактов ).

Возможная простая схема имитации процесса поступления и обслуживания заданий в вычислительной системе состоит в следующем.

- Каждое задание в системе представляется некоторым однозначным идентификатором (например, порядковым номером задания).
- Для проведения расчетов фиксируется (или указывается в диалоге) число тактов работы системы.
- На каждом такте опрашивается состояние потока задач и процессор.
- Регистрация нового задания в вычислительной системе может быть сведена к запоминанию идентификатора задания в очередь ожидания процессора. Ситуацию переполнения очереди заданий следует понимать как нехватку ресурсов вычислительной системы для ввода нового задания (отказ от обслуживания).
- Для моделирования процесса обработки заданий следует учитывать, что процессор может быть занят обслуживанием очередного задания, либо же может находиться в состоянии ожидания (простоя).
- В случае освобождения процессора предпринимается попытка его загрузки. Для этого извлекается задание из очереди ожидания.
- Простой процессора возникает в случае, когда при завершении обработки очередного задания очередь ожидающих заданий оказывается пустой.
- После проведения всех тактов имитации производится вывод характеристик вычислительной системы:

 - количество поступивших в вычислительную систему заданий в течение всего процесса имитации;
 - количество отказов в обслуживании заданий из-за переполнения очереди – целесообразно считать процент отказов как отношение количества не поставленных в очередь заданий к общему количеству сгенерированных заданий, умноженное на 100%;
 - среднее количество тактов выполнения задания;
 - количество тактов простоя процессора из-за отсутствия в очереди заданий для обслуживания – целесообразно считать процент простоя процессора как отношение количества тактов простоя процессора к общему количеству тактов имитации, умноженное на 100%.


## Структура проекта

С учетом сформулированных выше предложений к реализации целесообразной представляется следующая модульная структура программы:

- **TStack.h, TStack.cpp** – модуль с классом, реализующим операции над стеком;
- **TQueue.h, TQueue.cpp** – модуль с классом, реализующим операции над очередью;
- **TJobStream.h, TJobStream.cpp** – модуль с классом, реализующим поток задач;
- **TProc.h, TProc.cpp** – модуль с классом, реализующим процессор;
- **QueueTestkit.cpp** – модуль программы тестирования.
- **main.cpp** - запуск системы моделирования
- **test\_main.cpp** - запуск тестов