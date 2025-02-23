Кластеры Kubernetes версии 1.23 и выше тесно интегрированы с платформой VK Cloud:

- Используется технология единого входа (Single Sign-On, SSO).

  Пользователь авторизуется в кластере Kubernetes с теми те же реквизитами, что и при входе в [личный кабинет](../../../../base/account) VK Cloud.

  Функциональность SSO нельзя отключить.

- Роли пользователя в личном кабинете влияют на:

  - [Доступные операции с кластерами в личном кабинете](../../../../base/account/concepts/rolesandpermissions#razresheniya_roley_servisa_cloud_containers).

  - [Доступные действия в кластере](#vzaimosvyaz_roley_lichnogo_kabineta_i_kubernetes).

    Пользователю с определенной ролью личного кабинета назначается соответствующая [роль Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles). Роль Kubernetes определяет, какие объекты кластера доступны пользователю, а также какие действия разрешено выполнять над этими объектами.

- Aдминистратор Kubernetes управляет доступом к кластерам, назначая пользователям роли в личном кабинете.

  Нет необходимости конфигурировать права пользователей отдельно для личного кабинета и для кластеров Kubernetes. Например, отключение учетной записи пользователя или отзыв роли в личном кабинете приводит к отзыву прав на доступ к кластерам Kubernetes.

<info>

Чтобы получить такие же возможности для более старых кластеров, [обновите](../../operations/update) их до версии 1.23 или выше.

</info>

## Взаимосвязь ролей личного кабинета и Kubernetes

<tabs>
<tablist>
<tab>Аудитор Kubernetes</tab>
<tab>Оператор Kubernetes</tab>
<tab>Администратор Kubernetes</tab>
</tablist>
<tabpanel>

**Роль Kubernetes:** `view`.

Роль предоставляет доступ на чтение к большинству объектов в пространстве имен.

Роль не предоставляет:

- Доступ на просмотр или изменение ролей и связывания ролей.
- Доступ к секретам.
  
  Пользователь с доступом к секретам может получить доступ к учетным данным любого сервисного аккаунта в пространстве имен. Это позволит получить доступ к API от имени любого сервисного аккаунта в пространстве имен. Для роли с правами «только чтение» это будет расцениваться, как превышение привилегий (privilege escalation).

</tabpanel>
<tabpanel>

**Роль Kubernetes:** `edit`.

Роль предоставляет:

- Все привилегии, доступные роли `view`.
- Доступ на чтение и запись к большинству объектов в пространстве имен.
- Доступ к секретам, что позволяет запускать поды от имени любого сервисного аккаунта в пространстве имен. Роль может быть использована, например, чтобы получить доступ к API.

Роль не предоставляет:

- Доступ на просмотр или изменение ролей и связывания ролей.
- [Доступ на запись к эндпоинтам](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#write-access-for-endpoints) кластеров Kubernetes версии 1.22 и выше.

</tabpanel>
<tabpanel>

**Роль Kubernetes:** `admin`.

Рекомендуется назначать роль `admin` в пределах пространства имен с помощью связывания ролей.

Роль предоставляет:

- Все привилегии, доступные роли `edit`.
- Доступ на чтение и запись к большинству объектов в пространстве имен, включая возможность создавать другие роли и связывания ролей (role binding).

Роль не предоставляет:

- Доступ на запись к ресурсной квоте (resource quota) или к самому пространству имен.
- [Доступ на запись к эндпоинтам](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#write-access-for-endpoints) кластеров Kubernetes версии 1.22 и выше.

<info>

Кроме администратора Kubernetes, привилегии роли `admin` доступны владельцу проекта, суперадминистратору и администратору проекта.

</info>

</tabpanel>
</tabs>

Чтобы просмотреть список доступных ресурсов для роли, подключитесь к кластеру и выполните команду:

```bash
kubectl describe clusterrole <роль в Kubernetes>
```
