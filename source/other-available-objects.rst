*************************
Другие доступные объекты
*************************

.. _BigNumber:

BigNumber
=========
Объект, позволяющий безопасно проводить арифметические операции с большими числами. Рекомендуется для любых вычислений связанных с балансами пользователей, и с необходимой точностью.

`Описание BigNumber <https://github.com/MikeMcl/bignumber.js/>`_

Date
=====
Встроенный объект даты, однако ход времени объекта Date заморожен, и установлен в соответствии с состоянием, передаваемым в вызове.

Math
====
Встроенный объект.
Отличием от стандартного ``Math`` является реализация ``random``: использует внешний seed, устанавливаемый в соответствии с состоянием, передаваемым в вызове.
