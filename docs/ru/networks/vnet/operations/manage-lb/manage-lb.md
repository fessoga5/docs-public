Вы можете управлять балансировщиками нагрузки: просматривать, редактировать и удалять их, добавлять и изменять правила балансировки, манипулировать публичными IP-адресами.

## Просмотр списка балансировщиков нагрузки и информации о них

<tabs>
<tablist>
<tab>Личный кабинет</tab>
<tab>OpenStack CLI</tab>
</tablist>
<tabpanel>

1. [Перейдите](https://mcs.mail.ru/app/) в личный кабинет VK Cloud.
1. Выберите проект.
1. Перейдите в раздел **Виртуальные сети** → **Балансировщики нагрузки**.

   Будет отображен список балансировщиков.

1. Нажмите на имя нужного балансировщика.

   Откроется страница с подробной информацией о нем. На этой странице можно также [редактировать](#redaktirovanie_imeni_balansirovshchika_nagruzki) параметры балансировщика.

</tabpanel>
<tabpanel>

1. Убедитесь, что:

   1. OpenStack CLI [установлен](../../../../base/account/project/cli/setup) вместе с [дополнительным пакетом](../../../../base/account/project/cli/packagessetup) `python-octaviaclient`.
   1. Вы можете [авторизоваться](../../../../base/account/project/cli/authorization) в OpenStack CLI.

1. Чтобы посмотреть список балансировщиков нагрузки и их идентификаторы, выполните команду:

   ```bash
   openstack loadbalancer list
   ```

1. Чтобы посмотреть детальную информацию о балансировщике нагрузки, выполните команду:

   ```bash
   openstack loadbalancer show <идентификатор балансировщика>
   ```

   Будет выведена общая информация о балансировщике нагрузки и идентификаторы:

   - `vip_port_id` — идентификатор порта, который используется в качестве Virtual IP на балансировщике нагрузки. На этот порт можно назначить плавающий IP-адрес.

   - `listeners` — список идентификаторов listener-объектов. Эти объекты слушают входящие соединения к балансировщику нагрузки и служат точкой входа для трафика.

   - `pools` — список идентификаторов пулов (pools). Эти объекты служат для группировки конечных потребителей трафика. Потребители выступают участниками (members) пула. Трафик от listener-объекта балансируется между несколькими участниками, входящими в пул, сконфигурированный для listener-объекта.

1. Чтобы посмотреть настройки listener-объектов и их связь с пулами, выполните команду:

   ```bash
   openstack loadbalancer listener show <идентификатор listener-объекта>
   ```

1. Чтобы посмотреть настройки пула и список участников этого пула, выполните команду:

   ```bash
   openstack loadbalancer pool show <идентификатор пула>
   ```

1. Чтобы посмотреть настройки отдельного участника из пула, выполните команду:

   ```bash
   openstack loadbalancer member show <идентификатор пула> <идентификатор участника>
   ```

   Для участника будет выведена информация, в том числе порт назначения трафика.

</tabpanel>
</tabs>

## Добавление балансировщика нагрузки

<tabs>
<tablist>
<tab>Личный кабинет</tab>
<tab>OpenStack CLI</tab>
</tablist>
<tabpanel>

1. [Перейдите](https://mcs.mail.ru/app/) в личный кабинет VK Cloud.
1. Выберите проект.
1. Перейдите в раздел **Виртуальные сети** → **Балансировщики нагрузки**.
1. Нажмите кнопку **Добавить балансировщик** или **Добавить**.
1. Задайте параметры балансировщика:

   - **Название балансировщика**.
   - **Сеть**: сеть и подсеть, в которой будет размещаться балансировщик.

     Балансировщик будет распределять входящий трафик на выбранные сервисы, которые размещены в этой подсети.

     <warn>

     Изменить этот параметр позднее невозможно.

     </warn>

   - **DNS-имя**: (опционально) DNS-имя для балансировщика.
   - **Назначить внешний IP**: если эта опция выбрана, то балансировщику будет назначен публичный IP-адрес, через который он будет доступен из интернета. В противном случае балансировщик будет выступать в качестве внутреннего балансировщика нагрузки. Такой IP-адрес можно [назначить позднее](#upravlenie_publichnymi_ip_adresami).

     Выберите эту опцию, если планируется размещать за балансировщиком сервисы, которые должны быть доступны из интернета.
     Опцию можно выбрать только при условии, что сеть, выбранная ранее, находится за маршрутизатором, который имеет доступ в интернет.

1. Задайте правила балансировки.
1. Нажмите кнопку **Добавить балансировщик**.

</tabpanel>
<tabpanel>

1. Убедитесь, что:

   1. OpenStack CLI [установлен](../../../../base/account/project/cli/setup) вместе с [дополнительным пакетом](../../../../base/account/project/cli/packagessetup) `python-octaviaclient`.
   1. Вы можете [авторизоваться](../../../../base/account/project/cli/authorization) в OpenStack CLI.

1. Выберите сеть и подсеть, в которых будет размещен балансировщик. [Получите идентификатор](../manage-net#prosmotr_spiska_setey_i_podsetey_a_takzhe_informacii_o_nih) подсети.

1. Создайте балансировщик:

   <tabs>
   <tablist>
   <tab>Linux/macOS (bash, zsh)</tab>
   <tab>Windows (PowerShell)</tab>
   </tablist>
   <tabpanel>

   ```bash
   openstack loadbalancer create \
     --name <имя балансировщика> \
     --vip-subnet-id <идентификатор подсети>
   ```

   </tabpanel>
   <tabpanel>

   ```powershell
   openstack loadbalancer create `
     --name <имя балансировщика> `
     --vip-subnet-id <идентификатор подсети>
   ```

   </tabpanel>
   </tabs>

1. (Опционально) [назначьте балансировщику внешний IP-адрес](#upravlenie_publichnymi_ip_adresami). Через этот адрес он будет доступен из интернета. В противном случае балансировщик будет выступать в качестве внутреннего балансировщика нагрузки.

   Адрес требуется назначить, если планируется размещать за балансировщиком сервисы, которые должны быть доступны из интернета. Назначить адрес можно только при условии, что сеть для подсети, выбранной ранее, находится за маршрутизатором, который имеет доступ в интернет.

</tabpanel>
</tabs>

## Редактирование имени балансировщика нагрузки

<tabs>
<tablist>
<tab>Личный кабинет</tab>
<tab>OpenStack CLI</tab>
</tablist>
<tabpanel>

1. [Перейдите](https://mcs.mail.ru/app/) в личный кабинет VK Cloud.
1. Выберите проект.
1. Перейдите в раздел **Виртуальные сети** → **Балансировщики нагрузки**.
1. Выполните одно из действий для балансировщика, который нужно отредактировать:

   - Нажмите на имя балансировщика.
   - Раскройте меню балансировщика и выберите пункт **Редактировать**.

   Откроется страница с подробной информацией о балансировщике.

1. Чтобы изменить имя:

   1. Нажмите на значок карандаша рядом с текущим именем балансирощика.
   1. Задайте новое имя.
   1. Нажмите кнопку **Переименовать**.

</tabpanel>
<tabpanel>

1. Убедитесь, что:

   1. OpenStack CLI [установлен](../../../../base/account/project/cli/setup) вместе с [дополнительным пакетом](../../../../base/account/project/cli/packagessetup) `python-octaviaclient`.
   1. Вы можете [авторизоваться](../../../../base/account/project/cli/authorization) в OpenStack CLI.

1. [Получите идентификатор](#prosmotr_spiska_balansirovshchikov_nagruzki_i_informacii_o_nih) нужного балансировщика нагрузки.

1. Измените имя балансировщика:

   <tabs>
   <tablist>
   <tab>Linux/macOS (bash, zsh)</tab>
   <tab>Windows (PowerShell)</tab>
   </tablist>
   <tabpanel>

   ```bash
   openstack loadbalancer <идентификатор балансировщика> \
     --name <новое имя>
   ```

   </tabpanel>
   <tabpanel>

   ```powershell
   openstack loadbalancer <идентификатор балансировщика> `
     --name <новое имя>
   ```

   </tabpanel>
   </tabs>

</tabpanel>
</tabs>

## Управление публичными IP-адресами

### Назначить публичный IP-адрес

Если сеть балансировщика подключена к маршрутизатору с доступом в интернет, то можно назначить балансировщику публичный (внешний) IP-адрес.

<tabs>
<tablist>
<tab>Личный кабинет</tab>
<tab>OpenStack CLI</tab>
</tablist>
<tabpanel>

1. [Перейдите](https://mcs.mail.ru/app/) в личный кабинет VK Cloud.
1. Выберите проект.
1. Перейдите в раздел **Виртуальные сети** → **Балансировщики нагрузки**.
1. Выполните одно из действий:

   - Нажмите на имя нужного балансировщика.

     На странице с подробной информацией о балансировщике нажмите ссылку **Назначить** в блоке **IP-адреса** → **Внешний**.

   - Раскройте меню нужного балансировщика и выберите пункт **Назначить внешний IP**.

1. Выберите нужный публичный IP-адрес из списка либо создайте новый.
1. Нажмите кнопку **Подтвердить**.

</tabpanel>
<tabpanel>

1. Убедитесь, что:

   1. OpenStack CLI [установлен](../../../../base/account/project/cli/setup) вместе с [дополнительным пакетом](../../../../base/account/project/cli/packagessetup) `python-octaviaclient`.
   1. Вы можете [авторизоваться](../../../../base/account/project/cli/authorization) в OpenStack CLI.

1. [Получите идентификатор порта](../manage-ports#prosmotr_spiska_portov_i_informacii_o_nih) с Virtual IP для нужного балансировщика нагрузки.
1. [Привяжите плавающий IP-адрес](../manage-floating-ip#privyazka_plavayushchego_ip_adresa) к порту с этим идентификатором.

</tabpanel>
</tabs>

### Отвязать публичный IP-адрес

Если сеть балансировщика подключена к маршрутизатору с доступом в интернет, и балансировщику назначен публичный (внешний) IP-адрес, то этот адрес можно отвязать.

<tabs>
<tablist>
<tab>Личный кабинет</tab>
<tab>OpenStack CLI</tab>
</tablist>
<tabpanel>

1. [Перейдите](https://mcs.mail.ru/app/) в личный кабинет VK Cloud.
1. Выберите проект.
1. Перейдите в раздел **Виртуальные сети** → **Балансировщики нагрузки**.
1. Выполните одно из действий:

   - Нажмите на имя нужного балансировщика.

     На странице с подробной информацией о балансировщике нажмите символ **x** рядом с IP-адресом в секции **IP-адреса** → **Внешний**.

   - Раскройте меню нужного балансировщика и выберите пункт **Отвязать внешний IP**.

1. Нажмите кнопку **Подтвердить**.

</tabpanel>
<tabpanel>

1. Убедитесь, что:

   1. OpenStack CLI [установлен](../../../../base/account/project/cli/setup) вместе с [дополнительным пакетом](../../../../base/account/project/cli/packagessetup) `python-octaviaclient`.
   1. Вы можете [авторизоваться](../../../../base/account/project/cli/authorization) в OpenStack CLI.

1. [Получите идентификатор порта](../manage-ports#prosmotr_spiska_portov_i_informacii_o_nih) с Virtual IP для нужного балансировщика нагрузки.
1. [Отвяжите плавающий IP-адрес](../manage-floating-ip#otvyazka_plavayushchego_ip_adresa) от порта с этим идентификатором.

</tabpanel>
</tabs>

## Управление правилами балансировки

<tabs>
<tablist>
<tab>Личный кабинет</tab>
<tab>OpenStack CLI</tab>
</tablist>
<tabpanel>

1. [Перейдите](#prosmotr_spiska_balansirovshchikov_nagruzki_i_informacii_o_nih) на страницу балансировщика для его редактирования.
1. В секции **Правила балансировки** выполните одно из доступных действий:

   1. Добавьте правило балансировки.
   1. Отредактируйте существующее правило балансировки, нажав на значок карандаша рядом с правилом.
   1. Удалите существующее правило балансировки, нажав на значок корзины рядом с правилом.

При добавлении или редактировании правила доступны следующие параметры:

1. Параметры правила:

   - **Протоколы и порты** (только при создании правила):

     - Протокол балансировки и порт, который будет использоваться балансировщиком.
     - Протокол и порт назначения.

     При выборе протокола балансировки `TCP` доступны два протокола назначения: `TCP` или `PROXY`. Proxy-протокол можно использовать, если он поддерживается серверами, которые находятся за балансировщиком.

   - **Метод балансировки**:

     - `LEAST_CONNECTIONS`: использовать бэкенд, к которому установлено наименьшее число соединений.
     - `ROUND_ROBIN`: перебирать последовательно все бэкенды.
     - `SOURCE_IP`: закреплять бэкенд для обработки трафика за конкретным IP-адресом клиента.

   - **Разрешенные CIDR**: IP-адреса или адреса подсетей, с которых разрешены подключения к балансировщику. Этот параметр может использоваться для ограничения доступа к балансировщику только с доверенных адресов.

     Если параметр не задан, то разрешено подключение с любых IP-адресов (что эквивалентно CIDR `0.0.0.0/0`).

   - **Timeout параметры**:

     - `Client data`: таймаут неактивности клиента.
     - `Member connect`: таймаут установки соединения с бэкендом.
     - `Member data`: таймаут неактивности бэкенда.
     - `TCP inspect`: таймаут ожидания дополнительных TCP-сегментов при инспекции контента.

     Значения таймаутов задаются в миллисекундах. Минимальное значение — `0`, максимальное — `2073600000` (576 часов).

   - **Отправлять заголовок X-Forwarded-For** (только для протоколов балансировки HTTP и HTTPS): опция, позволяющая включить отправку на бэкенд соответствующего HTTP-заголовка. По умолчанию опция выключена.

   - **Применить для следующих инстансов**: инструменты для выбора инстансов виртуальных машин, которые будут выступать бэкендами для балансировщика. Можно добавить инстанс либо выбрав его из списка, либо выбрав тег, назначенный инстансу.

     <info>

     Группы безопасности для выбранных виртуальных машин должны быть настроены так, чтобы разрешать трафик на порт и протокол назначения.

     </info>

   - **Сертификат** (только для протокола балансировки HTTPS): сертификат, который будет использоваться балансировщиком для терминирования SSL-соединения.

     Можно выбрать уже существующий сертификат или загрузить новый.

     При загрузке нового сертификата указываются:

     - **Название сертификата**.
     - **Сертификат или цепочка сертификатов**: публичный сертификат или цепочка сертификатов. Можно вставить в поле в текстовом виде или загрузить из файла.
     - **Приватный ключ**: приватный сертификат. Можно вставить в поле в текстовом виде или загрузить из файла.
     - **Пароль**: пароль от приватного сертификата (если используется). Можно вставить в поле в текстовом виде или загрузить из файла.

1. Параметры проверки доступности бэкендов (healthcheck):

   - **Тип проверки**: TCP или HTTP.
   - **Интервал**: интервал проверки в секундах.
   - **Кол-во попыток**: сколько раз попытаться провести проверку, прежде чем признавать бэкенд недоступным.
   - **Таймаут**: таймаут в секундах, после истечения которого считается, что бэкенд не отвечает на проверку.

   Следующие параметры доступны только для типа проверки `HTTP`:

   - **HTTP-метод**: метод, который надо использовать для проверки доступности.
   - **Статус ответа**: [HTTP-статус](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status), при получении которого проверка доступности будет считаться успешной.
   - **Путь запроса**: путь, по которому надо обратиться для проверки доступности.

</tabpanel>
<tabpanel>

<info>

Здесь приведены только основные аргументы команд. Подробнее о командах и их аргументах читайте в справке OpenStack CLI:

```bash
openstack loadbalancer --help
openstack loadbalancer <команда> --help
```

</info>

1. Убедитесь, что:

   1. OpenStack CLI [установлен](../../../../base/account/project/cli/setup) вместе с [дополнительным пакетом](../../../../base/account/project/cli/packagessetup) `python-octaviaclient`.
   1. Вы можете [авторизоваться](../../../../base/account/project/cli/authorization) в OpenStack CLI.

1. Чтобы создать правило балансировки:

   1. [Получите идентификатор балансировщика нагрузки](#prosmotr_spiska_balansirovshchikov_nagruzki_i_informacii_o_nih), для которого нужно создать правило.

   1. Создайте пул, в котором будут размещены потребители трафика:

      <tabs>
      <tablist>
      <tab>Linux/macOS (bash, zsh)</tab>
      <tab>Windows (PowerShell)</tab>
      </tablist>
      <tabpanel>

      ```bash
      openstack loadbalancer pool create \
        --loadbalancer <идентификатор балансировщика> \
        --name <имя пула> \
        --protocol <протокол назначения> \
        --lb-algorithm <алгоритм балансировки>
      ```

      </tabpanel>
      <tabpanel>

      ```powershell
      openstack loadbalancer pool create `
        --loadbalancer <идентификатор балансировщика> `
        --name <имя пула> `
        --protocol <протокол назначения> `
        --lb-algorithm <алгоритм балансировки>
      ```

      </tabpanel>
      </tabs>

      Запишите идентификатор созданного пула (`id`).

   1. Определите IP-адреса виртуальных машин, которые будут участниками (members) пула. Также [определите идентификатор](../manage-net#prosmotr_spiska_setey_i_podsetey_a_takzhe_informacii_o_nih) подсети, в которой находятся виртуальные машины.

      Эти виртуальные машины должны быть либо размещены в подсети, где находится балансировщик, для которого создается правило балансировки, либо доступны из этой подсети.

   1. Для каждой такой виртуальной машины создайте member-объект, который будет участником созданного пула:

      <tabs>
      <tablist>
      <tab>Linux/macOS (bash, zsh)</tab>
      <tab>Windows (PowerShell)</tab>
      </tablist>
      <tabpanel>

      ```bash
      openstack loadbalancer member create <идентификатор пула> \
        --name <имя участника> \
        --address <IP-адрес виртуальной машины> \
        --subnet-id <идентификатор подсети> \
        --protocol-port <номер порта назначения>
      ```

      </tabpanel>
      <tabpanel>

      ```powershell
      openstack loadbalancer member create <идентификатор пула> `
        --name <имя участника> `
        --address <IP-адрес виртуальной машины> `
        --subnet-id <идентификатор подсети> `
        --protocol-port <номер порта назначения>
      ```

      </tabpanel>
      </tabs>

      <warn>

      Все member-объекты в рамках одного пула должны использовать один и тот же порт.

      </warn>

   1. Создайте для пула healthcheck-объект. Он будет проверять состояние и доступность участников (members) пула.

      <tabs>
      <tablist>
      <tab>Linux/macOS (bash, zsh)</tab>
      <tab>Windows (PowerShell)</tab>
      </tablist>
      <tabpanel>

      ```bash
      openstack loadbalancer healthmonitor create <идентификатор пула> \
        --name <имя healthcheck-объекта> \
        --delay <задержка, сек> \
        --timeout <таймаут, сек> \
        --max-retries <количество успешных попыток> \
        --max-retries-down <количество неуспешных попыток> \
        --type <тип проверки>
      ```

      </tabpanel>
      <tabpanel>

      ```powershell
      openstack loadbalancer healthmonitor create <идентификатор пула> `
        --name <имя healthcheck-объекта> `
        --delay <задержка, сек> `
        --timeout <таймаут, сек> `
        --max-retries <количество успешных попыток> `
        --max-retries-down <количество неуспешных попыток> `
        --type <тип проверки>
      ```

      </tabpanel>
      </tabs>

   1. Создайте listener-объект, который будет обрабатывать входящие соединения:

      <tabs>
      <tablist>
      <tab>Linux/macOS (bash, zsh)</tab>
      <tab>Windows (PowerShell)</tab>
      </tablist>
      <tabpanel>

      ```bash
      openstack loadbalancer listener create <идентификатор балансировщика> \
        --name <имя listener-объекта> \
        --protocol <протокол балансировки> \
        --default-pool <идентификатор пула> \
        --protocol-port <номер порта>
      ```

      </tabpanel>
      <tabpanel>

      ```powershell
      openstack loadbalancer listener create <идентификатор балансировщика> `
        --name <имя listener-объекта> `
        --protocol <протокол балансировки> `
        --default-pool <идентификатор пула> `
        --protocol-port <номер порта>
      ```

      </tabpanel>
      </tabs>

1. Чтобы применить (`set`) нужные настройки к объектам, которые входят в правило балансировки, или отменить настройки (`unset`), используйте соответствующие команды (например, `openstack loadbalancer pool set`).

1. Чтобы удалить правило балансировки:

   1. Найдите идентификатор нужного listener-объекта.

   1. Определите идентификатор пула, используемого этим listener-объектом:

      1. Выполните команду:

         ```bash
         openstack loadbalancer listener show <идентификатор listener-объекта>
         ```

      1. Идентификатор пула будет содержаться в поле `default_pool_id`.

   1. Удалите listener-объект:

      ```bash
      openstack loadbalancer listener delete <идентификатор listener-объекта>
      ```

   1. Удалите пул:

      ```bash
      openstack loadbalancer pool delete <идентификатор пула>
      ```

</tabpanel>
</tabs>

## Удаление балансировщика нагрузки

<tabs>
<tablist>
<tab>Личный кабинет</tab>
<tab>OpenStack CLI</tab>
</tablist>
<tabpanel>

Это групповая операция: при необходимости можно удалить сразу несколько балансировщиков нагрузки, выбрав их с помощью флажков.

Для удаления балансировщика:

1. [Перейдите](https://mcs.mail.ru/app/) в личный кабинет VK Cloud.
1. Выберите проект, где находится нужный балансировщик.
1. Перейдите в раздел **Виртуальные сети** → **Балансировщики нагрузки**.
1. Выполните одно из действий для нужного балансировщика:

   - Выберите с помощью флажка балансировщик, затем нажмите кнопку **Удалить**.
   - Раскройте меню балансировщика и выберите пункт **Удалить балансировщик**.

1. Подтвердите удаление балансировщика.

</tabpanel>
<tabpanel>

1. Убедитесь, что:

   1. OpenStack CLI [установлен](../../../../base/account/project/cli/setup) вместе с [дополнительным пакетом](../../../../base/account/project/cli/packagessetup) `python-octaviaclient`.
   1. Вы можете [авторизоваться](../../../../base/account/project/cli/authorization) в OpenStack CLI.

1. [Получите идентификатор](#prosmotr_spiska_balansirovshchikov_nagruzki_i_informacii_o_nih) балансировщика нагрузки.

1. Удалите балансировщик:

   ```bash
   openstack loadbalancer delete <идентификатор балансировщика>
   ```

</tabpanel>
</tabs>
