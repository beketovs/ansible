# Домашнее задание к занятию "08.03 Использование Yandex Cloud"

## Подготовка к выполнению

1. Подготовьте в Yandex Cloud три хоста: для `clickhouse`, для `vector` и для `lighthouse`.

Ссылка на репозиторий LightHouse: https://github.com/VKCOM/lighthouse

## Основная часть

1. Допишите playbook: нужно сделать ещё один play, который устанавливает и настраивает lighthouse.
2. При создании tasks рекомендую использовать модули: `get_url`, `template`, `yum`, `apt`.
3. Tasks должны: скачать статику lighthouse, установить nginx или любой другой webserver, настроить его конфиг для открытия lighthouse, запустить webserver.
```
beketov@beketovs-MacBook-Pro playbook % ansible-playbook -i inventory/prod.yml site.yml

PLAY [Install Clickhouse] ************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Get clickhouse distrib] ********************************************************************************************************************************************************************
ok: [clickhouse-01] => (item=clickhouse-client)
ok: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static-22.3.3.44.rpm", "elapsed": 0, "gid": 1000, "group": "beketov", "item": "clickhouse-common-static", "mode": "0644", "msg": "Request failed", "owner": "beketov", "response": "HTTP Error 404: Not Found", "secontext": "unconfined_u:object_r:user_home_t:s0", "size": 246310036, "state": "file", "status_code": 404, "uid": 1000, "url": "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-22.3.3.44.noarch.rpm"}

TASK [Get clickhouse distrib] ********************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Install clickhouse packages] ***************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Flush handlers] ****************************************************************************************************************************************************************************

TASK [Create database] ***************************************************************************************************************************************************************************
ok: [clickhouse-01]

PLAY [Install vector  packages] ******************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************
ok: [vector-01]

TASK [Get vector distrib] ************************************************************************************************************************************************************************
ok: [vector-01]

TASK [Install package] ***************************************************************************************************************************************************************************
ok: [vector-01]

PLAY [Install lighthouse] ************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************
ok: [lighthouse-01]

TASK [Install dependencies for lighthouse] *******************************************************************************************************************************************************
ok: [lighthouse-01]

TASK [Add epel-release for nginx] ****************************************************************************************************************************************************************
ok: [lighthouse-01]

TASK [Install nginx] *****************************************************************************************************************************************************************************
ok: [lighthouse-01]

TASK [Create config for nginx] *******************************************************************************************************************************************************************
ok: [lighthouse-01]

TASK [Install lighthouse] ************************************************************************************************************************************************************************
ok: [lighthouse-01]

TASK [Config for lighthouse] *********************************************************************************************************************************************************************
ok: [lighthouse-01]

PLAY RECAP ***************************************************************************************************************************************************************************************
clickhouse-01              : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=1    ignored=0   
lighthouse-01              : ok=7    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
vector-01                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
4. Приготовьте свой собственный inventory файл `prod.yml`.
5. Запустите `ansible-lint site.yml` и исправьте ошибки, если они есть.
```
beketov@beketovs-MacBook-Pro playbook % ansible-lint site.yml
WARNING: PATH altered to include /usr/local/Cellar/ansible-lint/6.3.0/libexec/bin
WARNING  Overriding detected file kind 'yaml' with 'playbook' for given positional argument: site.yml
```
6. Попробуйте запустить playbook на этом окружении с флагом `--check`.
```
beketov@beketovs-MacBook-Pro playbook % ansible-playbook -i inventory/prod.yml site.yml --check

PLAY [Install Clickhouse] ************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Get clickhouse distrib] ********************************************************************************************************************************************************************
ok: [clickhouse-01] => (item=clickhouse-client)
ok: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static-22.3.3.44.rpm", "elapsed": 0, "gid": 1000, "group": "beketov", "item": "clickhouse-common-static", "mode": "0644", "msg": "Request failed", "owner": "beketov", "response": "HTTP Error 404: Not Found", "secontext": "unconfined_u:object_r:user_home_t:s0", "size": 246310036, "state": "file", "status_code": 404, "uid": 1000, "url": "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-22.3.3.44.noarch.rpm"}

TASK [Get clickhouse distrib] ********************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Install clickhouse packages] ***************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Flush handlers] ****************************************************************************************************************************************************************************

TASK [Create database] ***************************************************************************************************************************************************************************
skipping: [clickhouse-01]

PLAY [Install vector  packages] ******************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************
ok: [vector-01]

TASK [Get vector distrib] ************************************************************************************************************************************************************************
ok: [vector-01]

TASK [Install package] ***************************************************************************************************************************************************************************
ok: [vector-01]

PLAY [Install lighthouse] ************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************
ok: [lighthouse-01]

TASK [Install dependencies for lighthouse] *******************************************************************************************************************************************************
ok: [lighthouse-01]

TASK [Add epel-release for nginx] ****************************************************************************************************************************************************************
ok: [lighthouse-01]

TASK [Install nginx] *****************************************************************************************************************************************************************************
ok: [lighthouse-01]

TASK [Create config for nginx] *******************************************************************************************************************************************************************
ok: [lighthouse-01]

TASK [Install lighthouse] ************************************************************************************************************************************************************************
ok: [lighthouse-01]

TASK [Config for lighthouse] *********************************************************************************************************************************************************************
changed: [lighthouse-01]

RUNNING HANDLER [nginx reload] *******************************************************************************************************************************************************************
skipping: [lighthouse-01]

PLAY RECAP ***************************************************************************************************************************************************************************************
clickhouse-01              : ok=3    changed=0    unreachable=0    failed=0    skipped=1    rescued=1    ignored=0   
lighthouse-01              : ok=7    changed=1    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
vector-01                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
```
7. Запустите playbook на `prod.yml` окружении с флагом `--diff`. Убедитесь, что изменения на системе произведены.
8. Повторно запустите playbook с флагом `--diff` и убедитесь, что playbook идемпотентен.
```
beketov@beketovs-MacBook-Pro playbook % ansible-playbook -i inventory/prod.yml site.yml --diff

PLAY [Install Clickhouse] ************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Get clickhouse distrib] ********************************************************************************************************************************************************************
ok: [clickhouse-01] => (item=clickhouse-client)
ok: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static-22.3.3.44.rpm", "elapsed": 0, "gid": 1000, "group": "beketov", "item": "clickhouse-common-static", "mode": "0644", "msg": "Request failed", "owner": "beketov", "response": "HTTP Error 404: Not Found", "secontext": "unconfined_u:object_r:user_home_t:s0", "size": 246310036, "state": "file", "status_code": 404, "uid": 1000, "url": "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-22.3.3.44.noarch.rpm"}

TASK [Get clickhouse distrib] ********************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Install clickhouse packages] ***************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Flush handlers] ****************************************************************************************************************************************************************************

TASK [Create database] ***************************************************************************************************************************************************************************
ok: [clickhouse-01]

PLAY [Install vector  packages] ******************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************
ok: [vector-01]

TASK [Get vector distrib] ************************************************************************************************************************************************************************
ok: [vector-01]

TASK [Install package] ***************************************************************************************************************************************************************************
ok: [vector-01]

PLAY [Install lighthouse] ************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************
ok: [lighthouse-01]

TASK [Install dependencies for lighthouse] *******************************************************************************************************************************************************
ok: [lighthouse-01]

TASK [Add epel-release for nginx] ****************************************************************************************************************************************************************
ok: [lighthouse-01]

TASK [Install nginx] *****************************************************************************************************************************************************************************
ok: [lighthouse-01]

TASK [Create config for nginx] *******************************************************************************************************************************************************************
ok: [lighthouse-01]

TASK [Install lighthouse] ************************************************************************************************************************************************************************
ok: [lighthouse-01]

TASK [Config for lighthouse] *********************************************************************************************************************************************************************
ok: [lighthouse-01]

PLAY RECAP ***************************************************************************************************************************************************************************************
clickhouse-01              : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=1    ignored=0   
lighthouse-01              : ok=7    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
vector-01                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  
```
9. Подготовьте README.md файл по своему playbook. В нём должно быть описано: что делает playbook, какие у него есть параметры и теги.
10. Готовый playbook выложите в свой репозиторий, поставьте тег `08-ansible-03-yandex` на фиксирующий коммит, в ответ предоставьте ссылку на него.

---

### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
