******
DApp
******

Децентрализованные приложения в IZZZIO позволяют создавать добавочный функционал для ноды. Такие приложения имеют широкий функционал:
1. Взаимодействие с цепочкой блоков, предобработка, реакция на изменения цепочки
2. Взаимодействие со смарт контрактами, в том числе запуск новых
3. Взаимодействие с другими децентрализованными приложениями посредством встроенных протколов. См. :ref:`StarWave`, :ref:`StarWaveCrypto`
4. Интеграция централизованных приложений с децентрализованным.

Для написания децентрализованных приложений в IZZZIO используется класс DApp. Класс предоставляет все необходимые методы для работы с функционалом сети, и обеспечивает совместимость приложений с разными версиями узлов блокчейна IZZZIO.

.. _DApp:

DApp
====================
При создании класса наследуемого от DApp рекомендуется не объявлять конструктор, и использовать вместо него встроенный метод ``init``
Для обработки состояния завершения используется метод ``terminate(cb)``

Глобальные переменные
======================

Для использования в DApp доступны следующие глобальные переменные:

1. **global.PATH.mainDir** - путь до корневой директори файлов блокчейна IZZZIO
2. **global.PATH.configDir** - путь до директории конфигурационного файла

Эти переменные обычно используются для включения файлов модулей ядра в проект.

.. code-block:: javascript

    const DApp = require(global.PATH.mainDir + '/app/DApp'); //Поиск DApp будет выполнен в корневой директории блокчейна

    class TestDapp extends DApp {
        init(){
	    console.log('DApp ready!');
        }

        terminate(cb){
	    console.log('DApp terminated');
	}
  
    }

Доступные объекты
==================

logger
------

Внутренний путь: ``modules/logger``

logger обеспечивает стандартизацию вывода сообщений в консоль. 

Свойство: ``disable`` - true - отключение вывода сообщений; false - вывод включения

.. js:method:: new Logger([prefix])

    :param string prefix: Префикс, с которым будет выводится сообщение

.. js:method:: getPrefix()

    :returns: Текущий установленный префикс

.. js:method:: log(type, data)

    :param string type: Тип сообщения для вывода
    :param string data: Сообщение

.. js:method:: info(type, data)

    :param string data: Сообщение информационного типа

.. js:method:: init(type, data)

    :param string data: Сообщение инициализации

.. js:method:: error(type, data)

    :param string data: Сообщение об ошибке

.. js:method:: fatal(type, data)

    :param string data: Сообщение о критической ошибке

.. js:method:: warning(type, data)

    :param string data: Предупреждение


.. code-block:: javascript

    
    const DApp = require(global.PATH.mainDir + '/app/DApp');
    const logger = new (require(global.PATH.mainDir + '/modules/logger'))("TestDApp");

    class TestDApp extends DApp {
        init(){
	    logger.info('Test DApp ready!'); //Выведет "Fri, 01 Feb 2019 14:12:48 GMT Info:  ECMAContract: Test DApp ready"
        }
  
    }

assert
------

Внутренний путь: ``modules/testing/assert``

Предоставляет функционал проверки каких - либо условий. Невыполнение этих условий ведёт к бросу исключения, и завершению вызова контракта с ошибкой. Состояние контракта откатывается до состояния до вызова.

Реализация идентична реализации для EcmaContract. 

Перейти на описание :ref:`assert`


instanceStorage
----------------

Внутренний путь: ``modules/instanceStorage``

Предоставляет доступ к созданным экземплярам объектов на основе ключей.

.. js:method:: instanceStorage.get(objName)

    :param string name: Название сохранённой сущности
    :returns: Сохранённая сущность или **null** при отсутствии

.. js:method:: instanceStorage.put(objName, value)

    :param string name: Название сохраняемой сущности
    :param value: Объект сущности

По умолчанию instanceStorage содержит следующие сущности:

1. **Blockchain** - главный экземпляр объекта сети
2. **config** - экземпляр объекта конфигурации
3. **wallet** - текущий кошёлёк
4. **blocks** - хранилище данных блоков
5. **blockHandler** - экземпляр объекта обработчика блоков
6. **frontend** - экземпляр объекта RPC интерфейса и оболочки
7. **transactor** - экземпляр объекта контроля траназкций
8. **cryptography** - экземпляр объекта криптографии
9. **blockchainObject** - алиас объекта Blockchain

Опциональные объекты и значения:

1. **ecmaContract** - экземпляр объекта смарт контрактов (если включены)
2. **dapp** - экземпляр объекта децентролизованного приложения (если включено)
3. **terminating** - флаг завершения работы

.. code-block:: javascript

    
    const DApp = require(global.PATH.mainDir + '/app/DApp');
    const storj = require(global.PATH.mainDir + '/modules/instanceStorage');

    class TestDApp extends DApp {
        init(){
	    console.log(storj.get('config').p2pPort); //6015
        }
  
    }

StarWaveCrypto
---------------
См. раздел :ref:`StarWaveCrypto`

Коннекторы
============

Коннекторы DApp - классы-обёртки, реализующие упрощенный интерфейс взаимодействия с контрактами определённых типов.

DApp-ContractConnector
----------------------

Внутренний путь: ``modules/smartContracts/connectors/ContractConnector``

Базовый абстрактный класс для коннекторов. Позволяет регестрировать методы и их алиасы.

.. _DApp-TokenContractConnector:

DApp-TokenContractConnector
---------------------------

Внутренний путь: ``modules/smartContracts/connectors/TokenContractConnector``

Класс коннектора, реализующий взаимодействие с контрактом-токеном для DApp


.. js:method:: new  TokenContractConnector(EcmaContractsInstance, contractAddress)

    :param EcmaContractsInstance: Экзмпляр объекта EcmaContracts
    :param string contractAddress: Адрес контракта, к которому происходит подключение

.. js:method:: async balanceOf(address)

    :param string address: Адрес владельца токенов
    :returns string: Баланс токенов

.. js:method:: async totalSupply()

    :returns string: Количество всего выпущеных токенов

.. js:method:: async transfer(to, amount)

    :param string to: Получатель токенов
    :param string amount: Количество токенов

.. js:method:: async burn(amount)

    :param string amount: Количество сжигаемых токенов

.. js:method:: async mint(amount)

    :param string amount: Количество выпускаемых токенов

.. js:method:: async contract

    :returns string: Свойство contract из контракта. Данные о контракте

Выполнение метода из другого контракта с оплатой

.. js:method:: async pay(address, method, txAmount, args)

    :param string address: Адрес контракта для вызова метода
    :param string method: Метод
    :param string txAmount: Сумма перевода
    :param array args: Аргументы вызова
    :returns: Новый созданный блок


.. code-block:: javascript

    const DApp = require(global.PATH.mainDir + '/app/DApp');
    const TokenContractConnector = require(global.PATH.mainDir + '/modules/smartContracts/connectors/TokenContractConnector');

    class TestDApp extends DApp {
        async init(){
	    let mainToken = new TokenContractConnector(this.ecmaContract, this.getMasterContractAddress());

            console.log(await mainToken.balanceOf(this.getCurrentWallet().id)); //Выведет баланс текущего кошелька
       
            //Вызовет метод processPayment из контракта SOME_CONTRACT_ADDRESS с параметром 'Hello', передав состояние проведения оплаты на 1 токен с текущего кошелька
            await mainToken.pay(SOME_CONTRACT_ADDRESS, "processPayment", '1', ['Hello']);
        }
  
    }



Внутренние объекты DApp
=======================

DApp.network
-------------
Предоставляет базовый функционал взаимодействия с сетевым интерфейсом, а также регистрацию RPC и обрбаотчиков интерфейса.

1. Возвращает массив текущих пиров в формате ws://address:port

.. js:function:: getCurrentPeers()

    :returns: пиры

2. Получение сокета по адресу шины (адрес внешней ноды)

.. js:function:: getSocketByBusAdress(address)

    :param string address: адрес шины
    :returns: объект сокета или false, если адрес не был найден.

3. Прямая отправка сообщения в сокет

.. js:function:: socketSend(ws, message)

    :param ws: сокет.
    :param string message: сообщение.

4. Регистрация метода обратного вызова для get запроса

.. js:function:: rpc.registerGetHandler(url, callback)

    :param string url: Обрабочтик ссылки
    :param function callback: Метод обратного вызова

5. Регистрация метода обратного вызова для post запроса

.. js:function:: rpc.registerPostHandler(url, callback)

    :param string url: Обрабочтик ссылки
    :param function callback: Метод обратного вызова

Методы ``rpc.registerGetHandler`` и ``rpc.registerPostHandler`` основаны на использовании фреймворка Express.js. Подробнее об обработчиках в  `документации Express.js <https://expressjs.com/ru/4x/api.html>`_ 


DApp.messaging
---------------
**Устаревший функционал**
Предоставляет базовый функционал обмена собщениями между узлами сети.

1.Регистрация обработчика сообщений

.. js:function:: registerMessageHandler(message, handler)

    :param string message: идентификатор сообщения
    :param function handler: функция - обработчик сообщений

2. Метод широковещательной передачи сообщений по сети

.. js:function:: broadcastMessage(data, message, receiver)

    :param data: передаваемый объект
    :param string message: идентификатор сообщения
    :param string receiver: получатель сообщения

3. Метод передачи сообщения близжайшему получателю

.. js:function:: sendMessage(data, message, receiver)

    :param data: объект, содержащий само сообщение.
    :param string message: идентификатор сообщения.
    :param string receiver: получатель сообщения.

Методы DApp.messaging.starwave описаны в разделе :ref:`StarWave`

DApp.blocks
---------------
Предоставляет доступ к функционалу генерации и добавления блоков, обработчику событий.

Генерирует блок без добавления в цепочку блоков

.. js:function:: generateBlock(blockData, cb, cancelCondition)

    :param Block blockData: Подписанный блок Block
    :param function cb: функция обратного вызова function(newBlock)
    :param cancelCondition: Функция условия отмены генерации блока

Генерация блока с добавлением в цепочку блоков и уведомлением узлов о новом блоке

.. js:function:: generateAndAddBlock(blockData, cb, cancelCondition)

    :param Block blockData: Подписанный блок Block
    :param function cb: функция обратного вызова function(newBlock)
    :param cancelCondition: Функция условия отмены генерации блока

Добавление сгенерированного блока

.. js:function:: addBlock(newBlock, cb)

    :param newBlock: Готовый блок
    :param cb: callback - обратного вызова


**Методы обработчика блоков**

Возвращает экземпляр текущего обработчика блоков

.. js:function:: handler.get()

    :returns: объект обработчика (ловца) блоков.

Регистрация обработчика блока по типу

.. js:function:: handler.registerHandler(type, handler)

    :param string type: Тип блока
    :param function handler: функция - обработчик блоков function(blockData, blockSource, callback)

.. warning::
    Вызов  callback в обработчике блоков обязателен. Отсутствие обратного вызова приведёт к зависанию обработчика блоков.



DApp.contracts
---------------
Предоставляет функционал доступа к модулю EcmaContracts.

``contracts.ecma`` - предоставляет прямой доступ к объекту EcmaContracts. См. EcmaContracts

``contracts.ecmaPromise`` - предоставляет основные методы EcmaContracts для использования с асинхронным режимом (async-await)

Выполняет запуск контракта в сети

.. js:function:: ecmaPromise.deployContract(source, resourceRent)

    :param string source: Исходнй код контракта
    :param number resourceRent: Количество токенов, выделяемых на аренду ресурсов контракта
    :returns: объект нового блока

Выполняет запуск метода из контракта

.. js:function:: ecmaPromise.deployMethod(address, method, args, state)

    :param string address: Адрес контракта
    :param string method: Метод контракта
    :param array args: Массив с аргументами вызова
    :param object state: Объект передаваемого состояния
    :returns: объект нового блока

Выполняет запуск метода из контракта с откатом состояния

.. js:function:: ecmaPromise.callMethodRollback(address, method, args, state)

    :param string address: Адрес контракта
    :param string method: Метод контракта
    :param array args: Массив с аргументами вызова
    :param object state: Объект передаваемого состояния
    :returns: Результат выполнения метода контракта


Внутренние методы DApp
======================

.. js:function:: DApp.getConfig()

    :returns: объект конфигурации

.. js:function:: DApp.getBlockHandler()

    :returns: объект обработчика блоков

.. js:function:: DApp.getCurrentWallet()

    :returns: объект текущего кошелька

.. js:function:: DApp.getMasterContractAddress()

    :returns: адрес мастер-контракта или false если не существует



.. _StarWave:

StarWave
==================

StarWave - протокол высокоскоростной передачи данных внутри децентрализованной сети IZZZIO. Протокол автоматически составляет кратчайший маршрут доставки сообщения до получателя. В случае нарушения маршрута, происходит его перестроение по такому-же принципу.

Методы StarWave доступны в DApp ``DApp.messaging.starwave``

Методы messaging.starwave
-------------------------

Регистрация обработчика сообщений

.. js:function:: starwave.registerMessageHandler(message, handler)

    :param string message: идентификатор сообщения
    :param function handler: функция - обработчик сообщений


Метод передачи сообщения близжайшему получателю

.. js:function:: starwave.sendMessage(data, message, receiver)

    :param data: объект, содержащий само сообщение.
    :param string message: идентификатор сообщения.
    :param string receiver: получатель сообщения.


Метод создания структуры сообщения

.. js:function:: starwave.createMessage(data, reciver, sender = undefined, id, timestamp = undefined, TTL = undefined, relevancyTime = undefined, route = undefined, type  = undefined, timestampOfStart)

    :param data: объект, содержащий само сообщение.
    :param string reciver: получатель сообщения
    :param string sender: отправитель сообщения; undefined - используются системное инфо об отправителе
    :param string id: Идентификатор сообщения
    :param number timestamp: Метка времени сообщения; undefined - автоматически
    :param number TTL: Возможное количество скачков сообщения; undefined - автоматически
    :param number relevancyTime: время актуальности сообщения; undefined - автоматически
    :param number timestamp: Метка времени сообщения; undefined - автоматически
    :param array route: Маршрут сообщения; undefined - автоматически
    :param string type: Тип сообщения; undefined - автоматически
    :param number timestampOfStart: Метка времени отправки сообщения; undefined - автоматически
    :returns: Объект структуры сообщения

.. _StarWaveCrypto:
 
StarWaveCrypto
==================

Внутренний путь: ``modules/starwaveCrypto``


StarWaveCrypto - модуль, реализующий создание шифрованного канала для обмена данными между узлами сети. При создании подключения, между узлами просходит генерация и обмен ключами по протоколу Диффи - Хеллмана (DH). 

Для cоздания шифрованного канала используется метод, осуществляющий "рукопожатие".
Таким образом, принимающая сторона, используя метод обработки получаемых сообщений, сразу создаст новое защищеное соъединение, или использует существующее.
Пример установки соединения:


.. code-block:: javascript

    const DApp = require(global.PATH.mainDir + '/app/DApp');
    const StarwaveCrypto = require(global.PATH.mainDir + '/modules/starwaveCrypto');
    
    class TestDApp extends DApp {

        init() {

	    let that = this;

	    starwave.registerMessageHandler('SW_TEST', function (message) {
		console.log('New message', message);
	    });

            let crypto =  new StarwaveCrypto(this.starwave, this.blockchain.secretKeys).makeConnection(RECIVER_ADDRESS, function(who, secretKey){
	    	console.log('Connected!');
                let message = that.starwave.createMessage('Hello Node Two' + Math.random(), 'RECIVER_ADDRESS', undefined, 'SW_TEST');
                crypto.sendMessage(m);
            });
        };
    }