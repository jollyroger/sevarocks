+++
title = "Апликативные функторы в Haskell"
date = "2021-10-12"
+++

Есть такой деруцкий оператор, для апликативных функторов <*>

У него есть такой вот тип: 

```hs
type Applicative :: (* -> *) -> Constraint
class Functor f => Applicative f where
  ...
  (<*>) :: f (a -> b) -> f a -> f b
  ...
```

Что он делает? Он имеет два аргумента. 

Первый — контейнер с функцией (функциями)
Второй — контейнер с данными к которым функция будет применена

На выходе мы получим контейнер с результирующими данными. 

Например:

```hs
>>> Just (+1) <*> Just 2
Just 3

>>> [(+1)] <*> [1,2,3]
[2,3,4]

>>> [(+1), (*0)] <*> [1,2,3]
[2,3,4,0,0,0]

>>> [show] <*> [1,2,3]
["1","2","3"]

```

Но что будет если написать?

```hs
>>> (zip <*> scanl1 max) [2,7,1,5,4,1]
[(2,2),(7,7),(1,7),(5,7),(4,7),(1,7)]
```

Это немного сильно вводит в заблуждение. Почему оно отработало?

По результату, то что мы получили эквивалентно 

```
>>> zip [2,7,1,5,4,1] (scanl1 max [2,7,1,5,4,1]) 
[(2,2),(7,7),(1,7),(5,7),(4,7),(1,7)]
```

Но почему? И как `<*>` смог прочитать функцию zip как контенер? 

Давайте посмотрим что такое zip

```hs
>>> zip [1,2,3] [5,6,7]
[(1,5),(2,6),(3,7)]

>>> :t zip
zip :: [a] -> [b] -> [(a, b)]
```

То есть zip это функция, которая принимает два списка и сшивает их элементы между собой

Но что забавно, у zip оказывается есть тип. У каждой функции есть свой тип, это тип "функция", то есть `(->)`

Надо посмотреть что это такое

```hs
:info (->)
type (->) :: * -> * -> *
data (->) a b
   -- Defined in ‘ghc-prim-0.6.1:GHC.Prim’
infixr -1 ->
instance Applicative ((->) r) -- Defined in ‘GHC.Base’
instance Functor ((->) r) -- Defined in ‘GHC.Base’
instance Monad ((->) r) -- Defined in ‘GHC.Base’
instance Monoid b => Monoid (a -> b) -- Defined in ‘GHC.Base’
instance Semigroup b => Semigroup (a -> b) -- Defined in ‘GHC.Base’
```


Как видите, на самом деле это даже не то чтобы тип, а конструктор типов (это легко сделать в хаскеле)

То есть, мы можем прочитать `zip :: [a] -> [b] -> [(a, b)]` как 


zip это тип `(->)` который первым элементом принимает список `[a]`, а вторым тип2, который первым элементом принимает тип `[b]`, а вторым тип `[(a,b)]`

И у тип `(->)` реализовывает интерфей Applicative

Давайте посмотрим как:

```hs
-- | @since 2.01  
instance Applicative ((->) r) where  
 pure = const  
 (<*>) f g x = f x (g x)  
 liftA2 q f g x = q (f x) (g x)
```


То есть мы видим что `(<*>)` принимает на вход `f g x` и потом применяет `g` к `x` а к результату применяет функцию `f` с первым аргументом `x`

то есть рельутатом `(zip <*> scanl1 max) [2,7,1,5,4,1]` как раз будет `zip [2,7,1,5,4,1] (scanl1 max [2,7,1,5,4,1])`  который мы и определили в самом начале.

И вообще, я всегда думал что доклады на ютубе это какая-то фигня. Но вот из доклада который я посмотрел вчера ночью я получил интерес к тому как такая конструкция `(zip <*> scanl1 max)` может работать и в итоге понял что функции тоже могут реализовывать интерфейсы и быть апликативными функторами или даже монадами. Может это уж и не такая и фигня.

{{ tg(id="devops_tricks/202") }}