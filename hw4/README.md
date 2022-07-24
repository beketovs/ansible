# Домашнее задание к занятию "8.4 Работа с Roles"

## Подготовка к выполнению
1. (Необязательно) Познакомтесь с [lighthouse](https://youtu.be/ymlrNlaHzIY?t=929)
2. Создайте два пустых публичных репозитория в любом своём проекте: vector-role и lighthouse-role.
3. Добавьте публичную часть своего ключа к своему профилю в github.

## Основная часть

Наша основная цель - разбить наш playbook на отдельные roles. Задача: сделать roles для clickhouse, vector и lighthouse и написать playbook для использования этих ролей. Ожидаемый результат: существуют три ваших репозитория: два с roles и один с playbook.

1. Создать в старой версии playbook файл `requirements.yml` и заполнить его следующим содержимым:

   ```yaml
   ---
     - src: git@github.com:AlexeySetevoi/ansible-clickhouse.git
       scm: git
       version: "1.11.0"
       name: clickhouse 
   ```

2. При помощи `ansible-galaxy` скачать себе эту роль.
3. Создать новый каталог с ролью при помощи `ansible-galaxy role init vector-role`.
4. На основе tasks из старого playbook заполните новую role. Разнесите переменные между `vars` и `default`. 
5. Перенести нужные шаблоны конфигов в `templates`.
6. Описать в `README.md` обе роли и их параметры.
7. Повторите шаги 3-6 для lighthouse. Помните, что одна роль должна настраивать один продукт.
8. Выложите все roles в репозитории. Проставьте тэги, используя семантическую нумерацию Добавьте roles в `requirements.yml` в playbook.
9. Переработайте playbook на использование roles. Не забудьте про зависимости lighthouse и возможности совмещения `roles` с `tasks`.
10. Выложите playbook в репозиторий.
11. В ответ приведите ссылки на оба репозитория с roles и одну ссылку на репозиторий с playbook.

---
```
beketov@beketovs-MacBook-Pro hw4 % ansible-playbook -i inventory/prod.yml site.yml 

PLAY [Install Clickhouse] *************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************
ok: [clickhouse-01]

TASK [clickhouse-role : Get clickhouse distrib] ***************************************************************************
ok: [clickhouse-01] => (item=clickhouse-client)
ok: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static-22.3.3.44.rpm", "elapsed": 0, "gid": 1000, "group": "beketov", "item": "clickhouse-common-static", "mode": "0644", "msg": "Request failed", "owner": "beketov", "response": "HTTP Error 404: Not Found", "secontext": "unconfined_u:object_r:user_home_t:s0", "size": 246310036, "state": "file", "status_code": 404, "uid": 1000, "url": "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-22.3.3.44.noarch.rpm"}

TASK [clickhouse-role : Get clickhouse distrib] ***************************************************************************
ok: [clickhouse-01]

TASK [clickhouse-role : Install clickhouse packages] **********************************************************************
ok: [clickhouse-01]

TASK [clickhouse-role : Flush handlers] ***********************************************************************************

TASK [clickhouse-role : Create database] **********************************************************************************
ok: [clickhouse-01]

PLAY [Install vector  packages] *******************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************
ok: [vector-01]

TASK [vector-role : Get vector distrib] ***********************************************************************************
ok: [vector-01]

TASK [vector-role : Install package] **************************************************************************************
ok: [vector-01]

PLAY [Install lighthouse] *************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************
ok: [lighthouse-01]

TASK [lighthouse-role : Install dependencies for lighthouse] **************************************************************
ok: [lighthouse-01]

TASK [lighthouse-role : Add epel-release for nginx] ***********************************************************************
ok: [lighthouse-01]

TASK [lighthouse-role : Install nginx] ************************************************************************************
ok: [lighthouse-01]

TASK [lighthouse-role : Create config for nginx] **************************************************************************
ok: [lighthouse-01]

TASK [lighthouse-role : Install lighthouse] *******************************************************************************
ok: [lighthouse-01]

TASK [lighthouse-role : Config for lighthouse] ****************************************************************************
ok: [lighthouse-01]

PLAY RECAP ****************************************************************************************************************
clickhouse-01              : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=1    ignored=0   
lighthouse-01              : ok=7    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
vector-01                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
```
---
