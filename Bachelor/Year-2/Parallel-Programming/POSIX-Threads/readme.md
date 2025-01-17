# Программирование многопоточных приложений. POSIX Threads

## Задание

Есть список неделимых заданий, каждое из которых может быть выполнено независимо от другого. Задания могут иметь различный вычислительный вес, т.е. требовать при одних и тех же вычислительных ресурсах различного времени для выполнения. Считается, что этот вес нельзя узнать, пока задание не выполнено. После того, как все задания из списка выполнены, появляется новый список заданий. Необходимо организовать параллельную обработку заданий на нескольких компьютерах. Количество заданий существенно превосходит количество процессоров. Программа не должна зависеть от числа компьютеров.

Так как задания имеют различный вычислительный вес, а список обрабатывается итеративно, и требуется синхронизация перед каждой итерацией, то могут возникать ситуации, когда некоторые процессоры выполнили свою работу, а другие — еще нет. Если ничего не предпринять, первые будут простаивать в ожидании последних. Так возникает задача динамического распределения работы. Для ее решения на каждом процессоре заведем несколько потоков. Как минимум, потоков должно два:

1. Поток, который обрабатывает задания и, когда задания закончились, обращается к другим компьютерам за добавкой к работе.
2. Поток, ожидающий запросов о работе от других компьютеров.

Полезно может быть завести третий поток, который возьмет на себя задачу подкачки работ на компьютер, при этом первый поток будет только обрабатывать задания. В таком случае третий поток, до того как кончатся задания (соответствующий момент времени определить самостоятельно), на фоне счета будет отсылать запросы о работе и добавлять к локальному списку пришедшие задания.

### Формирование списка заданий

Задание в данном случае пусть будет иметь совершенно модельный характер. Например, оно может быть таким: выполнить некоторые тратящие время процессора действия `repeatNum` раз. Различие в вычислительном весе заданий будет заключаться в том, что у каждого задания количество повторений `repeatNum` свое:

```C
TaskList tl;
double globalRes = 0;
int iterCounter = 0;
...
while(true) //итерации обработки списков
{
   iterCounter++; // счетчик глобальных итераций
   for(всех task = номер задания из списка) // выборка заданий из списка
       for(int i = 0; i<tl[task].repeatNum; i++)
                globalRes += sin(i);
 
   ...
}
```

Вес задачи можно назначить случайным образом с использованием функции `rand()`, однако для экспериментов лучше задать некоторые осмысленные правила изменения загрузки на процессорах. Например, пусть в одном списке (100 $\cdot$ `size`) заданий, тогда на каждой глобальной итерации `iterCounter` веса заданий на процессоре с номером `rank` установить

```C
Tl[i].repeatNum = abs(50 - i % 100) * abs(rank - (iterCounter % size)) * L;
```

где `size` — количество процессоров. Это создаст "волну" загрузки, смещающуюся с ходом итераций от процессора с меньшим номером к процессору с большим номером (с перескакиванием на начало). Задания генерируются для каждой глобальной итерации заново!

Синхронизация процессоров между итерациями должна заключаться в том, что никто не начинает новую итерацию, пока не обработаны все задания с предыдущей.

## Требования

1. Программа не должна зависеть от числа процессов.
2. Использование коммуникаций *MPI* между процессами.
3. Использование *POSIX-Threads* для порождения нитей в рамках процессов. Минимум в каждом процессе по 2 нити.
4. Вес заданий считать заранее неизвестным для процессов.
5. Количество поочередно обрабатываемых списков (`iterCounter`) должен быть таким, чтобы программа выполнялась не менее 30 секунд и списков должно быть не менее 3.
6. После каждой итерации `iterCounter` (после каждого списка задач) каждый *MPI*-процесс должен выводить: 
   * кол-во заданий, выполненных данным процессом за итерацию
   * значение `globalRes`
   * совокупное время выполнения заданий на этой итерации  
```math  
T_k^p = T_k^p(2) - T_k^p(1)
```  
   где $k$ — `iterCounter`, $T_k^p(1)$ – засечка времени, сделанная процессом $p$  в самом начале выполнения итерации $k$, $T_k^p(2)$ – засечка времени, сделанная процессом $p$ в конце выполнения итерации $k$ сразу после того, как он закончил работать над заданиями и перестал брать задания у других

7. После каждой итерации нужно посчитать
   * время дисбаланса
   ```math
   \Delta k = \max_{m, n} \\; |T_k^m - T_k^n|
   ```
   * долю дисбаланса
   ```math
   100\% \cdot \frac{\Delta k}{M_k}
   ```
   где
   ```math
   M_k = \max_{p} \\; {T_k^p}
   ```

## Дополнительные требования

1. Использование по 3 потока в каждом процессе.
2. Использование коллективных операций для взаимодействия между процессами.
3. Использование одного процесса в качестве "менеджера" заданий.
