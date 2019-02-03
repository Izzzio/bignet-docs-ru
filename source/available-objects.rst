******************
Доступные объекты
******************

########################
Объекты-хранилища
########################

Объекты, реализующие перенос и сохранение состояния между вызовами методов смарт контракта. Необходимы для сохранение любой информации (состояние счетов пользователя, балансы и другие)

.. _KeyValue:

KeyValue
=========
Единственный реальный сохраняемый объект внутри виртуальной машины. Другие сохраняемые объекты зависят от объекта **KeyValue**, и по сути являются обёртками на его базе.
KeyValue - объект, реализующий хранилице ключ-значение. Максимальное количество записей - не ограничено. Максимальная длина ключа и записи - **512 кБ**.

.. js:method:: new KeyValue(название хранилища)

    :param string название хранилища: Название хранилища для загрузки и записи данных

.. js:method:: get(ключ)

    :param string ключ: Ключ по которому будет выполнятся поиск значения в хранилище
    :returns: Значение. Если не найдено - false

.. js:method:: put(ключ, значение)

    :param string ключ: Ключ по которому будет выполнена запись в хранилище
    :param string значение: Строка для записи в хранилище

Пример инициализации и использования методов:

.. code-block:: javascript

    class Test extends Contract {
        init(){
	    super.init();
            //Создаём/загружаем хранилище
            this._db = new KeyValue('name'); 
        }
  
        putKeyValue(){
            this._db.put('key','value');
        }

        getKeyValue(){
            console.log(this._db.get('key')); //Выведет "value"
        }
    }

.. _TypedKeyValue:

TypedKeyValue
==============
Аналог **KeyValue**, сохраняющий типы значений, при условии, что их можно сереализовать.

.. js:method:: new TypedKeyValue(название хранилища)

    :param string название хранилища: Название хранилища для загрузки и записи данных

.. js:method:: get(ключ)

    :param string ключ: Ключ по которому будет выполнятся поиск значения в хранилище
    :returns: Значение. Если не найдено - false

.. js:method:: put(ключ, значение)

    :param string ключ: Ключ по которому будет выполнена запись в хранилище
    :param * значение: Строка для записи в хранилище

Пример инициализации и использования методов:

.. code-block:: javascript

    class Test extends Contract {
        init(){
	    super.init();
            //Создаём/загружаем хранилище
            this._db = new KeyValue('name'); 
        }
  
        putKeyValue(){
            this._db.put('number',1337);
	    this._db.put('string',"Hello");
        }

        getKeyValue(){
            console.log(typeof this._db.get('number')); //Выведет "number"
	    console.log(typeof this._db.get('string')); //Выведет "string"
        }
    }

.. _BlockchainArraySafe:

BlockchainArraySafe
====================
Массивоподобная структура на основе **KeyValue**. Не содержит методы, которые могут привести к проблеме переполнения ОЗУ (join(), slice(), splice() и другие).

.. js:method:: new BlockchainArraySafe(название хранилища)

    :param string название хранилища: Название хранилища для загрузки и записи данных


.. code-block:: javascript

    class Test extends Contract {
        init(){
	    super.init();
            //Создаём/загружаем хранилище
            this._db = new BlockchainArraySafe('name'); 
        }
  
        doSomething(){
            this._db[0]=1;
        }

	doSomethingNext(){
	    this._db[0]++;	
	}

        test(){
	    this.doSomething();
	    this.doSomethingNext();
            console.log(this._db[0]); //Выведет "2"
        }
    }

.. _BlockchainArray:

BlockchainArray
====================
Данный объект является расширением **BlockchainArraySafe**. Содержит полифиллы методов, которые могут привести к переполнению доступной памяти. Рекомендуется использовать только на небольших массивах данных

.. js:method:: new BlockchainArray(название хранилища)

    :param string название хранилища: Название хранилища для загрузки и записи данных

.. code-block:: javascript

    class Test extends Contract {
        init(){
	    super.init();
            //Создаём/загружаем хранилище
            this._db = new BlockchainArraySafe('name'); 
        }
  
        doSomething(){
            this._db.push('Hello');
        }

	doSomethingNext(){
	   this._db.push('world!');
	}

        test(){
	    this.doSomething();
	    this.doSomethingNext();
            console.log(this._db.join(' ')); //Выведет "Hello world!"
        }
    }

.. _BlockchainMap:

BlockchainMap
====================
Объекто-подобная структура. Не сереализуема. Сохраняет типы объектов при условии, что их можно сереализовать.

.. js:method:: new BlockchainMap(название хранилища)

    :param string название хранилища: Название хранилища для загрузки и записи данных

.. code-block:: javascript

    class Test extends Contract {
        init(){
	    super.init();
            //Создаём/загружаем хранилище
            this._db = new BlockchainMap('name'); 
            this._db['hello'] = 'Hello';
	    this._db['world'] = 'world!';
	    console.log(this._db['hello']+' '+this._db['world']); //Выведет "Hello world!"
        }
  
    }

.. _TokenRegister:

TokenRegister
==============
Класс, реализующий безопасную логику работы реестра токенов. Является основой для **TokenContract** и большинства токенов в системе. Содержит основные методы по управлению балансами кошельков, переводами, поддержкой минтинга, сжигания токенов, и учета существующего объема.
Работает с числами до 256 знаков.

.. js:method:: new TokenRegister(название хранилища)

    :param string название хранилища: Название хранилища для загрузки и записи данных

.. js:method:: balanceOf(адрес)

    :param string адрес: Адрес для проверки баланса
    :returns BigNumber: Баланс кошелька

.. js:method:: totalSupply()

    :returns BigNumber: Суммарный объем существующих токенов

.. js:method:: setBalance(адрес, баланс)

    :param адрес: Адрес для установки баланса
    :param баланс: Баланс, который необходимо установить

.. js:method:: withdraw(адрес, сумма)

    :param адрес: Адрес для списания токенов
    :param сумма: Сумма списания

.. js:method:: minus(адрес, сумма)

    :param адрес: Адрес для списания токенов
    :param сумма: Сумма списания

.. js:method:: deposit(адрес, сумма)

    :param адрес: Адрес для начисления токенов
    :param сумма: Сумма начисления

.. js:method:: plus(адрес, сумма)

    :param адрес: Адрес для начисления токенов
    :param сумма: Сумма начисления

.. js:method:: transfer(адрес отправителя, адрес получателя, сумма)

    :param адрес отправителя: Адрес списания токенов
    :param адрес получателя: Адрес начисления токенов
    :param сумма: Сумма перевода

.. js:method:: burn(адрес, сумма)

    :param адрес: Адрес для сжигания токенов
    :param сумма: Сумма сжигания

.. js:method:: mint(адрес, сумма)

    :param адрес: Адрес для выпуска токенов
    :param сумма: Сумма выпуска

.. code-block:: javascript

    class Test extends Contract {
        init(){
	    super.init();
            this._wallets = new TokensRegister('TKN');
	    this._wallets.mint('ME',1000);
	    console.log(this._wallets.balanceOf('ME').toFixed()); //1000

	    this._wallets.transfer('ME','FRIEND',100);
	    console.log(this._wallets.balanceOf('ME').toFixed()); //900
            console.log(this._wallets.balanceOf('FRIEND').toFixed()); //100
        }
  
    }

########################
События
########################

Генерируемые события используются для построения индекса событий контракта. Например, база данных событий позволяет быстро узнать баланс токенов на определенном кошельке на момент появления определенного блока.

Events
===============
**Events** - глобальный объект, реализующий возможность создания (выпуск) события смарт-контракта. Метод  ``emit`` испускает событие.
Внимание: Мы не рекомендуем использовать объект **Events** напрямую. Используйте объект Event для создания экземпляра события и его выпуска.

.. js:method:: emit(событие, аргументы)

    :param string событие: Название события
    :param array аргументы: До 10 аргументов события

.. _Event:

Event
===============
Класс-обёртка, реализующий безопасное и удобное использование объекта **Events**. Event реализует проверку типов входящих параметров, а также корректно приводит данные к верному формату.

.. js:method:: new Event(событие, массив типов аргументов)

    :param string событие: Название события
    :param array массив типов аргументов: До 10 типов аргументов события ('number','string','array','object')

.. js:method:: emit(аргументы)

    :param array аргументы: До 10 аргументов события, соответствующих ранее указаному типу


.. code-block:: javascript

    class Test extends Contract {
        init(){
	    super.init();
            this._helloEvent = new Event('Hello',['string']);
	    this._helloEvent.emit('Hello world!');
        }
  
    }

.. warning::
    Event может принимать не более 10 параметров в качестве входных аргументов. 

########################
Объекты контрактов
########################

.. _Contract:

Contract
=========
Объект, рекомендуемый к наследованию всеми контрактами платформы. Объект **Contract** реализует:

1. Базовый функционал проверки доступа
2. Определение состояния запуска (deploy)
3. Прием и подтверждение оплаты
4. Взаимодействие с системой межконтрактных взаиморасчётов
5. Интерфейс внешнего приложения 

Внутренний метод init инициализирует внутренний функционал контракта, в том числе определение состояния запуска (deploy)

.. js:method:: super.init()



Вызывается единожды в жизни смарт контракта, позволяет определить момент запуска (deploy)

.. js:method:: deploy()


Позволяет получить информацию об оплате. см. :ref:`Pay Object`

.. js:method:: payProcess()

   :returns object: Объект с информацией оплаты

Проверка на то, что вызывающий является владельцем контракта

.. js:method:: assertOwnership([msg])

   :param string msg: Сообщение на случай ошибки проверки

Проверка на то, что вызывающий является мастер-контрактом

.. js:method:: assertMaster([msg])

   :param string msg: Сообщение на случай ошибки проверки

Проверка на то, что метод вызван извне (т.е. не из другого контракта)

.. js:method:: assertExternal([msg])

   :param string msg: Сообщение на случай ошибки проверки

Проверка на то, что метод вызван с передачей информации об оплате

.. js:method:: assertPayment([msg])

   :param string msg: Сообщение на случай ошибки проверки


Добавление инфо-метода для внешнего приложения

.. js:method:: _registerAppInfoMethod(имя метода, типы аргументов)

   :param string имя метода: Название метода
   :param array типы аргументов: Типы принимаемых значений метода

Добавление исполнительного-метода для внешнего приложения

.. js:method:: _registerAppDeployMethod(имя метода, типы аргументов)

   :param string имя метода: Название метода
   :param array типы аргументов: Типы принимаемых значений метода

Добавление исходного кода приложения

.. js:method:: _registerApp(исходный код[,тип = "web"])

   :param string исходный код: Исходный код внешнего приложения или информация для его поиска
   :param array тип: Тип внешнего приложения

Получение данных внешнего приложеия

.. js:method:: getAppData()

   :returns string: Сереализованный объект внешнего приложения


Регистрация обратного вызова для межконтрактных-покупок

.. js:method:: _registerC2CResultCallback(адрес контракта продавца, функция обратного вызова)

   :param адрес контракта продавца: Адрес контракта-продавца, у которого сделан заказ
   :param функция обратного вызова: Функция, которая будет вызвана когда придёт заказ function(объект результата, номер заказа, адрес продавца)


.. _TokenContract:

TokenContract
=============
Объект, рекомендуемый к наследованию всеми контрактами-токенами. Реализует интерфейс контракта стандарта :ref:`IZ3 Token`.

Свойство **_wallets** - экземпляр объекта :ref:`TokenRegister`

Свойство **_TransferEvent** - экземпляр объекта :ref:`Event`

Свойство **_MintEvent** - экземпляр объекта :ref:`Event`

Свойство **_BurnEvent** - экземпляр объекта :ref:`Event`

.. js:method:: super.init(initialEmission,[mintable = false])

   :param initialEmission: Стартовая эмиссия токенов
   :param boolean mintable: Разрешен ли минтинг в будующем

.. js:method:: _getSender()

   :returns: Адрес отправителя вызова

.. js:method:: balanceOf(address)

    :param string address: Адрес владельца токенов
    :returns string: Баланс токенов

.. js:method:: totalSupply()

    :returns string: Количество всего выпущеных токенов

.. js:method:: transfer(to, amount)

    :param string to: Получатель токенов
    :param string amount: Количество токенов

.. js:method:: burn(amount)

    :param string amount: Количество сжигаемых токенов

.. js:method:: mint(amount)

    :param string amount: Количество выпускаемых токенов

.. js:method:: getActionFee(action, args)

    :param string action: Действие
    :param array args: Аргументы
    :returns string: Стоимость действия

.. js:method:: getTransferFee(amount)

    :param string amount: Сумма перевода
    :returns string: Стоимость перевода


########################
Коннекторы
########################

Коннекторы EcmaContracts - классы-обёртки, реализующие упрощенный интерфейс взаимодействия с внешними контрактами определённых типов.

.. _ContractConnector:

ContractConnector
===================
Базовый класс для коннекторов. Позволяет регестрировать методы и их алиасы.

.. _TokenContractConnector:

TokenContractConnector
=======================
Класс коннектора, реализующий взаимодействие с контрактом-токеном из другого контракта

.. js:method:: new TokenContractConnector(address)

    :param string address: Адрес контракта, к которому происходит подключение

.. js:method:: balanceOf(address)

    :param string address: Адрес владельца токенов
    :returns string: Баланс токенов

.. js:method:: totalSupply()

    :returns string: Количество всего выпущеных токенов

.. js:method:: transfer(to, amount)

    :param string to: Получатель токенов
    :param string amount: Количество токенов

.. js:method:: burn(amount)

    :param string amount: Количество сжигаемых токенов

.. js:method:: mint(amount)

    :param string amount: Количество выпускаемых токенов

.. code-block:: javascript

    class Test extends Contract {
        init(){
	    super.init();
            let tokenContract = new TokenContractConnector(1);
            console.log(tokenContract.balanceOf('INVALID_ADDR')); //Выведет 0
        }
  
    }


.. _SellerContractConnector:

SellerContractConnector
=======================
Класс коннектора, реализующий взаимодействие с контрактом-продавцом. Контракты-продавцы - это контракты, продающие данные или услугу внутри сети.

.. js:method:: getPrice(args)

    :param array args: Аргументы заказа
    :returns: Цена заказа

.. js:method:: buy(args)

    :param array args: Аргументы заказа
    :returns: id заказа

.. js:method:: getResult(orderId)

    :param string orderId: id заказа
    :returns: Объект содержащий заказ


Require
=======================
Require - не относится к стандартным коннекторам, однако реализует сходный функционал. Require позволяет создать экземпляр класса внешнего контракта с произвольными методами и свойствами. Наличие методов и свойств во внешнем классе проверяется во время выполнения кода.

.. js:method:: new Require(externalContract)

    :param string externalContract: Адрес подключаемого контракта

.. code-block:: javascript

    class Test extends Contract {
        init(){
	    super.init();
            let tokenContract = new Require(1);
            console.log(tokenContract.balanceOf('INVALID_ADDR')); //Выведет 0
	    console.log(tokenContract.invalidMethod('INVALID_ADDR')); //Бросит исключение 
        }
  
    }


Альтернативный синтаксис c методом-фабрикой:

.. js:method:: require(externalContract)

    :param string externalContract: Адрес подключаемого контракта


########################
Системные классы
########################

.. _assert:

assert
=======
Предоставляет функционал проверки каких - либо условий. Невыполнение этих условий ведёт к бросу исключения, и завершению вызова контракта с ошибкой. Состояние контракта откатывается до состояния до вызова.

Предоставляются следующие методы для проверки условий:

1.  Простая проверка на истину


.. js:method:: assert(условие[, сообщение])

    :param условие: условие, истина
    :param string сообщение: сообщени

2.  Проверка на несоответствие типу undefined


.. js:method:: defined(условие[, сообщение])

    :param условие: простое условие с использованием условных операторов
    :param string сообщение: сообщение при неверности условия

3.  Проверяет верность условия a > b


.. js:method:: gt(a, b, [ сообщение])

    :param a: числовой параметр
    :param b: числовой параметр
    :param string сообщение: сообщение при неверности условия

4.  Проверяет верность условия a < b


.. js:method:: lt(a, b, [ сообщение])

    :param a: числовой параметр
    :param b: числовой параметр
    :param string сообщение: сообщение при неверности условия

5.  Проверяет истинность условия. Алиас assert.assert


.. js:method:: true(условие[, сообщение])

    :param условие: условие
    :param string сообщение: сообщение при неверности условия

6.  Проверка ложности условия (аналогично "Неверно, что...")


.. js:method:: false(условие[, сообщение])

    :param условие: условие; условие
    :param string сообщение: сообщение при неверности условия


.. code-block:: javascript

    class Test extends Contract {
        init(){
	    super.init();
            assert.gt(100,200,'100 не может быть больше 200');
        }
  
    }


contracts
==========
Предоставляет функционал для управления текущим или взаимодействия с внешними контрактами. Доступны следующие методы:

Вызов метода другого контракта с сохранением изменённого состояния.

.. js:function:: callMethodDeploy(адресКонтракта, метод, [аргументы])

    :param string адресКонтракта: адрес вызываемого контракта.
    :param string метод: метод вызываемого контракта.
    :param array аргументы: передаваемый методу аргументы в виде массива.
    :throws Error1: 'You can\'t call method from himself' - вызов изнутри.
    :throws Error2: 'External call failed' - ошибка при вызова метода.
    :returns: результат вызова метода.


Вызов метода другого контракта выполняемый после завершения текущего вызова

.. js:function:: callDelayedMethodDeploy(адресКонтракта, метод, [аргументы])

    :param string адресКонтракта: адрес вызываемого контракта.
    :param string метод: метод вызываемого контракта.
    :param array аргументы: передаваемый методу аргументы в виде массива.
    :throws Error1: 'You can\'t call method from himself' - вызов изнутри.

Запрос свойства другого контракта

.. js:function:: getContractProperty(адресКонтракта, свойство)

    :param string адресКонтракта: адрес вызываемого контракта.
    :param string свойство: свойство контракта
    :throws Error1: 'You can\'t call method from himself' - вызов изнутри.
    :returns: Значение свойства

**Метод устарел.**
Вызов метода другого контракта с откатом изменённого состояния.

.. js:function:: callMethodRollback(адресКонтракта, метод, [аргументы])

    :param string адресКонтракта: адрес вызываемого контракта.
    :param string метод: метод вызываемого контракта.
    :param array аргументы: передаваемый методу аргументы в виде массива.
    :throws Error1: 'You can\'t call method from himself' - вызов изнутри.
    :returns: результат вызова метода.



.. js:function:: getMasterContractAddress()

    :returns: Адрес мастер-контракта, определенный в конфигурации

.. js:function:: caller()

    :returns: адрес вызывающего контракта; false, если вызов внешний


.. js:function:: callingIndex()

    :returns: номер текущего вызова контракта в цепочке вызовов или 0, если вызов внешний


.. js:function:: isChild()

    :returns:: true, если контракт был вызван другим контрактом, в ином случае - false


.. js:function:: isDeploy()

    :returns:: true, если контракт находится в состоянии запуска (deploy), в ином случае - false

.. js:function:: isDelayedCall()

    :returns: true если вызов выполнен с помощью callDelayedMethodDeploy

.. js:function:: getExtendedState()

    :returns: Объект добавочного состояния

crypto
==========
Методы работы с криптографией

.. js:function:: sha256(data)

    :param string data: Данные для хеширования
    :returns: Строковое представление хеш-суммы

.. js:function:: md5(data)

    :param string data: Данные для хеширования
    :returns: Строковое представление хеш-суммы

Хеширование сконфигурированной функцией

.. js:function:: hash(data)

    :param string data: Данные для хеширования
    :returns: Строковое представление хеш-суммы

Проверка цифвровой подписи сконфигурированной функцией

.. js:function:: verifySign(data, sign, publicKey)

    :param string data: Данные для проверки
    :param string sign: Подпись данных
    :param string publicKey: Публичный ключ
    :returns boolean: true если проверка успешна

global
==========
Другие глобальные методы

Передача класса контракта в виртуальную машину 

.. js:function:: registerContract(контракт)

    :param string data: Данные для проверки
    :param string sign: Подпись данных
    :param string publicKey: Публичный ключ
    :returns boolean: true если проверка успешна

Вывод данных на экран (при условии включеного вывода)

.. js:function:: console.log(данные)

    :param string данные: Данные для проверки

.. js:function:: getState()

    :returns object: Текущий объект state. Рекомендуется использовать вместо global.state



