## Домашнее задание к занятию «Управление доступом»
### Задание 1. Создайте конфигурацию для подключения пользователя
1. Создайте и подпишите SSL-сертификат для подключения к кластеру.
2. Настройте конфигурационный файл kubectl для подключения.
3. Создайте роли и все необходимые настройки для пользователя.
4. Предусмотрите права пользователя. Пользователь может просматривать логи подов и их конфигурацию (kubectl logs pod <pod_id>, kubectl describe pod <pod_id>).
5. Предоставьте манифесты и скриншоты и/или вывод необходимых команд.

### Выполнение задания 1. Создайте конфигурацию для подключения пользователя
Используя OpenSSL создаю файл ключа:
  Создаю запрос на подписание сертификата (CSR):
  - Генерирую файл сертификата (CRT). Поскольку я использую Microk8s, я буду использовать ключи кластера по пути /var/snap/microk8s/current/certs/:
  - Настраиваю конфигурационный файл kubectl для подключения.

![img1_1](https://github.com/user-attachments/assets/df82c4a4-81ac-45a3-8a5b-3861f3c06685)

Создаю пользователя staff и настраиваю его на использование созданного выше ключа:

![img1_4](https://github.com/user-attachments/assets/0e9b30c7-c386-415f-ae86-2e57511f563a)

Создаю новый контекст с именем staff-context и подключаю его к пользователю staff, созданному ранее:

![img1_5](https://github.com/user-attachments/assets/8188e264-9b7c-459f-8f12-ebc047e96ebe)

Проверю, создался ли контекст:

![img1_6](https://github.com/user-attachments/assets/405d8145-236f-4f67-a3d3-f7576d60e2e4)

Контекст создался.

Для выполнения задания создам отдельный Namespace:

![img1_7](https://github.com/user-attachments/assets/adb8fc5b-29c0-4c59-a54a-987dc7257b90)

Также потребуется включение встроенного в Microk8s RBAC контроллера:

![img1_8](https://github.com/user-attachments/assets/34b7eb6e-0590-494e-9513-0d8cee0ddbe7)

Применю манифест создания роли (Role) и манифест привязки роли к Namespace (RoleBinding):

![img1_9](https://github.com/user-attachments/assets/2f247e4f-cdda-4d9e-b4e8-c1b0794a294b)

Для проверки прав пользователя переключусь в его контекст:

![img1_10](https://github.com/user-attachments/assets/19a75cc6-f9c8-48bd-a730-87e9d7072dbe)

Разверну Deployment в разрешенном для пользователя Namespace:

![img1_11](https://github.com/user-attachments/assets/aaaa8718-d381-4c2b-87d8-82e8176c5e51)

Проверю какие развернуты поды в Namespace с именем default:

![img1_12](https://github.com/user-attachments/assets/afb49f8d-1b70-4ed4-a79f-0b250f63f866)

Видно, что в Namespace с именем default нет доступа, так как он не был указан в манифесте Role.

Но если я проверю поды в Namespace с именем access-control, то список подов отобразится, т.к. на него есть разрешения в Role:

![img1_13](https://github.com/user-attachments/assets/40cc94b4-7d41-45c2-9b83-15340133a6f4)

Также проверю логи пода:

![img1_14](https://github.com/user-attachments/assets/b9c37b9e-a443-41bb-a63f-2fcc4cc11015)

Проверю вывод описания пода:

![img1_15](https://github.com/user-attachments/assets/79968f2c-57cb-4c22-819f-35291f07b743)

Согласно манифесту роли и ее привязке к Namespace, у пользователя staff есть доступ подам, их логам и описанию.

Ссылка на манифест Deployment - https://github.com/slava1005/2_4/blob/main/manifest/deployment.yaml

Ссылка на манифест Role - https://github.com/slava1005/2_4/blob/main/manifest/role.yaml

Ссылка на манифест RoleBinding - https://github.com/slava1005/2_4/blob/main/manifest/rolebinding.yaml
