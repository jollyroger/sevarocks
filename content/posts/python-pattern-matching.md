+++
title = "Python 3.10 и pattern matching"
date = "2021-10-08"
+++

Смотрю на реализацию паттерн матчинга в питоне и прям неплохо сделали. 

На всякий случай, если кто-то путает паттерн матчинг и switch/case это немного разные штуки. 

Паттерн матчинг, подразумевает разворачивание значений из контейнера. 

Например, вот как это классно сделано в хаскеле 

```hs
someF :: [Int] -> String
someF [] = "This list is empty"
someF [x:xs] = "This list contain an element: " ++ x
```

У нас есть функция, которая принимает на вход список чисел. Если список пустой — выводит сообщение что список пустой, а если не пустой, то выводит его первый элемент.

Но можно пойти дальше и добавить ещё вот такую функцию, если мы хотим выводить отдельное сообщение для списков, которые начинаются с единицы: 

```
someD [1:xs] = "This lists starts with one” 
```

Вот в питоне тоже сделали что-то подобное. Правда система типов в питоне не такая гибкая как в хаскеле, как мне кажется (наверное, там и не нужно, впрочем, я не очень хорошо знаю питон)

А еще в 3.10 питоне обавили оператор или для типов

И можно применять вполне себе функциональные конструкторы: 

если у вас есть два Datatype  (например Just(value) и Nothing) то вы можете определить тип Maybe так же как и в хаскеле 

```py3
Maybe = Just | Nothing
```


и потом применять его в паттерн матчинге 

```py3
def func(value: Maybe)
    match value: 
        case Just(a):
             print(a) 
        case Nothing:
             print(“Nothing to do here”)
```

Это очень полезно и здорово и позволит избегать исключений в будущем (если они вам не нравятся также сильно как и мне)

{{ tg(id="devops_tricks/197") }}