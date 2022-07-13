# Домашнее задание к занятию "08.02 Работа с Playbook"

## Подготовка к выполнению

1. (Необязательно) Изучите, что такое [clickhouse](https://www.youtube.com/watch?v=fjTNS2zkeBs) и [vector](https://www.youtube.com/watch?v=CgEhyffisLY)
2. Создайте свой собственный (или используйте старый) публичный репозиторий на github с произвольным именем.
3. Скачайте [playbook](./playbook/) из репозитория с домашним заданием и перенесите его в свой репозиторий.
4. Подготовьте хосты в соответствии с группами из предподготовленного playbook.

## Основная часть

1. Приготовьте свой собственный inventory файл `prod.yml`.
```
beketov@beketovs-MacBook-Pro playbook % ansible-playbook site.yml -i inventory/prod.yml

PLAY [Install Clickhouse] *************************************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************************************
ok: [clickhouse-01]

TASK [Get clickhouse distrib] *********************************************************************************************************************
ok: [clickhouse-01] => (item=clickhouse-client)
ok: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static-22.3.3.44.rpm", "elapsed": 0, "gid": 1000, "group": "beketov", "item": "clickhouse-common-static", "mode": "0664", "msg": "Request failed", "owner": "beketov", "response": "HTTP Error 404: Not Found", "secontext": "unconfined_u:object_r:user_home_t:s0", "size": 246310036, "state": "file", "status_code": 404, "uid": 1000, "url": "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-22.3.3.44.noarch.rpm"}

TASK [Get clickhouse distrib] *********************************************************************************************************************
ok: [clickhouse-01]

TASK [Install clickhouse packages] ****************************************************************************************************************
ok: [clickhouse-01]

TASK [Create database] ****************************************************************************************************************************
changed: [clickhouse-01]

PLAY RECAP ****************************************************************************************************************************************
clickhouse-01              : ok=4    changed=1    unreachable=0    failed=0    skipped=0    rescued=1    ignored=0  
```
2. Допишите playbook: нужно сделать ещё один play, который устанавливает и настраивает [vector](https://vector.dev).
3. При создании tasks рекомендую использовать модули: `get_url`, `template`, `unarchive`, `file`.
4. Tasks должны: скачать нужной версии дистрибутив, выполнить распаковку в выбранную директорию, установить vector.
```
beketov@beketovs-MacBook-Pro playbook % ansible-playbook site.yml -i inventory/prod.yml

PLAY [Install Clickhouse] ************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************
ok: [clickhouse-01]

TASK [Get clickhouse distrib] ********************************************************************************************************
ok: [clickhouse-01] => (item=clickhouse-client)
ok: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static-22.3.3.44.rpm", "elapsed": 0, "gid": 1000, "group": "beketov", "item": "clickhouse-common-static", "mode": "0664", "msg": "Request failed", "owner": "beketov", "response": "HTTP Error 404: Not Found", "secontext": "unconfined_u:object_r:user_home_t:s0", "size": 246310036, "state": "file", "status_code": 404, "uid": 1000, "url": "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-22.3.3.44.noarch.rpm"}

TASK [Get clickhouse distrib] ********************************************************************************************************
ok: [clickhouse-01]

TASK [Install clickhouse packages] ***************************************************************************************************
ok: [clickhouse-01]

TASK [Create database] ***************************************************************************************************************
ok: [clickhouse-01]

PLAY [Install vector  packages] ******************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************
ok: [vector-01]

TASK [Get vector distrib] ************************************************************************************************************
changed: [vector-01]

TASK [Install package] ***************************************************************************************************************
changed: [vector-01]

RUNNING HANDLER [Start vector service] ***********************************************************************************************
changed: [vector-01]

PLAY RECAP ***************************************************************************************************************************
clickhouse-01              : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=1    ignored=0   
vector-01                  : ok=4    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

beketov@vector-01:~$ service vector status
● vector.service - Vector
     Loaded: loaded (/lib/systemd/system/vector.service; disabled; vendor preset: enabled)
     Active: active (running) since Wed 2022-07-13 09:38:50 UTC; 39s ago
       Docs: https://vector.dev
    Process: 2439 ExecStartPre=/usr/bin/vector validate (code=exited, status=0/SUCCESS)
   Main PID: 2442 (vector)
      Tasks: 4 (limit: 2310)
     Memory: 5.7M
     CGroup: /system.slice/vector.service
             └─2442 /usr/bin/vector
```
5. Запустите `ansible-lint site.yml` и исправьте ошибки, если они есть.
```
beketov@beketovs-MacBook-Pro playbook % ansible-lint site.yml
WARNING: PATH altered to include /usr/local/Cellar/ansible-lint/6.3.0/libexec/bin
WARNING  Overriding detected file kind 'yaml' with 'playbook' for given positional argument: site.yml
```
6. Попробуйте запустить playbook на этом окружении с флагом `--check`.
7. Запустите playbook на `prod.yml` окружении с флагом `--diff`. Убедитесь, что изменения на системе произведены.
8. Повторно запустите playbook с флагом `--diff` и убедитесь, что playbook идемпотентен.
```
beketov@beketovs-MacBook-Pro playbook % ansible-playbook -i inventory/prod.yml site.yml --diff

PLAY [Install Clickhouse] ******************************************************************************************************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Get clickhouse distrib] **************************************************************************************************************************************************************************************
ok: [clickhouse-01] => (item=clickhouse-client)
ok: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static-22.3.3.44.rpm", "elapsed": 0, "gid": 1000, "group": "beketov", "item": "clickhouse-common-static", "mode": "0664", "msg": "Request failed", "owner": "beketov", "response": "HTTP Error 404: Not Found", "secontext": "unconfined_u:object_r:user_home_t:s0", "size": 246310036, "state": "file", "status_code": 404, "uid": 1000, "url": "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-22.3.3.44.noarch.rpm"}

TASK [Get clickhouse distrib] **************************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Install clickhouse packages] *********************************************************************************************************************************************************************************
ok: [clickhouse-01]

TASK [Create database] *********************************************************************************************************************************************************************************************
ok: [clickhouse-01]

PLAY [Install vector  packages] ************************************************************************************************************************************************************************************

TASK [Gathering Facts] *********************************************************************************************************************************************************************************************
ok: [vector-01]

TASK [Get vector distrib] ******************************************************************************************************************************************************************************************
ok: [vector-01]

TASK [Install package] *********************************************************************************************************************************************************************************************
ok: [vector-01]

PLAY RECAP *********************************************************************************************************************************************************************************************************
clickhouse-01              : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=1    ignored=0   
vector-01                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
9. Подготовьте README.md файл по своему playbook. В нём должно быть описано: что делает playbook, какие у него есть параметры и теги.
10. Готовый playbook выложите в свой репозиторий, поставьте тег `08-ansible-02-playbook` на фиксирующий коммит, в ответ предоставьте ссылку на него.

---

### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
