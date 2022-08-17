# Домашнее задание к занятию "8.2 Работа с Playbook" 
_______________________________________________________________________________
# 8.2.1 оcнованая часть
______________________________________________________________________________


0. Сделали первый play 



```

ivan@ubt-tst:~/netology-ansible2$ ansible-playbook -i inventory/prod.yml site.yml
[WARNING]: Found both group and host with same name: elasticsearch

PLAY [Install Java] *****************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************
ok: [elasticsearch]

TASK [Set facts for Java 11 vars] ***************************************************************************************
ok: [elasticsearch]

TASK [Upload .tar.gz file containing binaries from local storage] *******************************************************
changed: [elasticsearch]

TASK [Ensure installation dir exists] ***********************************************************************************
changed: [elasticsearch]

TASK [Extract java in the installation directory] ***********************************************************************
changed: [elasticsearch]

TASK [Export environment variables] *************************************************************************************
changed: [elasticsearch]

PLAY [Install Elasticsearch] ********************************************************************************************

TASK [Gathering Facts] **************************************************************************************************
ok: [elasticsearch]

TASK [Upload tar.gz Elasticsearch from local storage] *******************************************************************
changed: [elasticsearch]

TASK [Create directrory for Elasticsearch] ******************************************************************************
changed: [elasticsearch]

TASK [Extract Elasticsearch in the installation directory] **************************************************************
changed: [elasticsearch]

TASK [Set environment Elastic] ******************************************************************************************
changed: [elasticsearch]

PLAY RECAP **************************************************************************************************************
elasticsearch              : ok=11   changed=8    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```

<img width="700" alt="2" src="https://github.com/Darkpunks/netology-ansible2/blob/main/8.2%20image/8.2.0.jpg">

1. Приготовьте свой собственный inventory файл prod.yml.
```
 ---
      elasticsearch:
        hosts:
          elasticsearch:
            ansible_connection: docker
      kibana:
        hosts:
          kibana:
            ansible_connection: docker
 ```     
 
 <img width="700" alt="2" src="https://github.com/Darkpunks/netology-ansible2/blob/main/8.2%20image/8.2.1.jpg">
            
2. Допишите playbook: нужно сделать ещё один play, который устанавливает и настраивает kibana.

Так как запускаем в докере, где по умолчанию действия производятся под root, убрал become 

<img width="700" alt="2" src="https://github.com/Darkpunks/netology-ansible2/blob/main/8.2%20image/8.2.2.jpg">

3. При создании tasks рекомендую использовать модули: get_url, template, unarchive, file.

get_url не можем использовать по причине санкций, приходится использовать "copy"

4. Tasks должны: скачать нужной версии дистрибутив, выполнить распаковку в выбранную директорию, сгенерировать конфигурацию с параметрами.

Опять же скачать не получится - сделовательно, брать из папки playbook/files/ куда заранее положил файлы, где какой-то добрый человек из чата телеграм добавил в "закрепленные".  .

```
- name: Install kibana
  hosts: kibana
  tasks:
    - name: Upload tar.gz kibana from local storage
      copy:
        src: "{{ kibana_package }}"
        dest: "/tmp/kibana-{{ kibana_version }}-linux-x86_64.tar.gz"
        mode: 0755
      register: get_kibana
      until: get_kibana is succeeded
      tags: kibana
    - name: Create directrory for kibana
      file:
        state: directory
        path: "{{ kibana_home }}"
      tags: kibana
    - name: Extract Kibana in the installation directory
      unarchive:
        copy: false
        src: "/tmp/kibana-{{ kibana_version }}-linux-x86_64.tar.gz"
        dest: "{{ kibana_home }}"
        extra_opts: [--strip-components=1]
        creates: "{{ kibana_home }}/bin/kibana"
      tags:
        - skip_ansible_lint
        - kibana
    - name: Set environment Kibana
      template:
        src: templates/kib.sh.j2
        dest: /etc/profile.d/kib.sh
      tags: kibana
```


5. Запустите ansible-lint site.yml и исправьте ошибки, если они есть.

Ошибок нет


6. Попробуйте запустить playbook на этом окружении с флагом --check.
Появилась ошибка. Потому что это просто симуляция, которая не создает никакие папки. 

```
ivan@ubt-tst:~/netology-ansible2$ ansible-playbook -i inventory/prod.yml site.yml --check
[WARNING]: Found both group and host with same name: elasticsearch

PLAY [Install Java] *****************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************
ok: [elasticsearch]

TASK [Set facts for Java 11 vars] ***************************************************************************************
ok: [elasticsearch]

TASK [Upload .tar.gz file containing binaries from local storage] *******************************************************
changed: [elasticsearch]

TASK [Ensure installation dir exists] ***********************************************************************************
changed: [elasticsearch]

TASK [Extract java in the installation directory] ***********************************************************************
An exception occurred during task execution. To see the full traceback, use -vvv. The error was: NoneType: None
fatal: [elasticsearch]: FAILED! => {"changed": false, "msg": "dest '/opt/jdk/11.0.11' must be an existing dir"}

PLAY RECAP **************************************************************************************************************
elasticsearch              : ok=4    changed=2    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0

```


<img width="700" alt="2" src="https://github.com/Darkpunks/netology-ansible2/blob/main/8.2%20image/8.2.6.jpg">

Запустил в обычном режиме, playbook создал все необходимые папки и теперь  
запуск происходит без ошибок:
```
ivan@ubt-tst:~/netology-ansible2$ ansible-playbook -i inventory/prod.yml site.yml
[WARNING]: Found both group and host with same name: kibana
[WARNING]: Found both group and host with same name: elasticsearch

PLAY [Install Java] *****************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************
ok: [elasticsearch]
ok: [kibana]

TASK [Set facts for Java 11 vars] ***************************************************************************************
ok: [elasticsearch]
ok: [kibana]

TASK [Upload .tar.gz file containing binaries from local storage] *******************************************************
ok: [elasticsearch]
changed: [kibana]

TASK [Ensure installation dir exists] ***********************************************************************************
changed: [kibana]
ok: [elasticsearch]

TASK [Extract java in the installation directory] ***********************************************************************
skipping: [elasticsearch]
changed: [kibana]

TASK [Export environment variables] *************************************************************************************
ok: [elasticsearch]
changed: [kibana]

PLAY [Install Elasticsearch] ********************************************************************************************

TASK [Gathering Facts] **************************************************************************************************
ok: [elasticsearch]

TASK [Upload tar.gz Elasticsearch from local storage] *******************************************************************
ok: [elasticsearch]

TASK [Create directrory for Elasticsearch] ******************************************************************************
ok: [elasticsearch]

TASK [Extract Elasticsearch in the installation directory] **************************************************************
skipping: [elasticsearch]

TASK [Set environment Elastic] ******************************************************************************************
ok: [elasticsearch]

PLAY [Install kibana] ***************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************
ok: [kibana]

TASK [Upload tar.gz kibana from local storage] **************************************************************************
changed: [kibana]

TASK [Create directrory for kibana] *************************************************************************************
changed: [kibana]

TASK [Extract Kibana in the installation directory] *********************************************************************
changed: [kibana]

TASK [Set environment Kibana] *******************************************************************************************
changed: [kibana]

PLAY RECAP **************************************************************************************************************
elasticsearch              : ok=9    changed=0    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0
kibana                     : ok=11   changed=8    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```


7. Запустите playbook на prod.yml окружении с флагом --diff. Убедитесь, что изменения на системе произведены.

Сделал. 

8. Повторно запустите playbook с флагом --diff и убедитесь, что playbook идемпотентен.

--diff выдал, что хосты находятся в акутуальном состоянии

```
ivan@ubt-tst:~/netology-ansible2$ ansible-playbook -i inventory/prod.yml site.yml --diff
[WARNING]: Found both group and host with same name: kibana
[WARNING]: Found both group and host with same name: elasticsearch

PLAY [Install Java] *****************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************
ok: [kibana]
ok: [elasticsearch]

TASK [Set facts for Java 11 vars] ***************************************************************************************
ok: [elasticsearch]
ok: [kibana]

TASK [Upload .tar.gz file containing binaries from local storage] *******************************************************
ok: [elasticsearch]
ok: [kibana]

TASK [Ensure installation dir exists] ***********************************************************************************
ok: [elasticsearch]
ok: [kibana]

TASK [Extract java in the installation directory] ***********************************************************************
skipping: [elasticsearch]
skipping: [kibana]

TASK [Export environment variables] *************************************************************************************
ok: [kibana]
ok: [elasticsearch]

PLAY [Install Elasticsearch] ********************************************************************************************

TASK [Gathering Facts] **************************************************************************************************
ok: [elasticsearch]

TASK [Upload tar.gz Elasticsearch from local storage] *******************************************************************
ok: [elasticsearch]

TASK [Create directrory for Elasticsearch] ******************************************************************************
ok: [elasticsearch]

TASK [Extract Elasticsearch in the installation directory] **************************************************************
skipping: [elasticsearch]

TASK [Set environment Elastic] ******************************************************************************************
ok: [elasticsearch]

PLAY [Install kibana] ***************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************
ok: [kibana]

TASK [Upload tar.gz kibana from local storage] **************************************************************************
ok: [kibana]

TASK [Create directrory for kibana] *************************************************************************************
ok: [kibana]

TASK [Extract Kibana in the installation directory] *********************************************************************
skipping: [kibana]

TASK [Set environment Kibana] *******************************************************************************************
ok: [kibana]

PLAY RECAP **************************************************************************************************************
elasticsearch              : ok=9    changed=0    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0
kibana                     : ok=9    changed=0    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0
```

<img width="700" alt="2" src="https://github.com/Darkpunks/netology-ansible2/blob/main/8.2%20image/8.2.8.jpg">

9. Подготовьте README.md файл по своему playbook. В нём должно быть описано: что делает playbook, какие у него есть параметры и теги.

https://github.com/Darkpunks/netology-ansible2/blob/main/README.md

10. Готовый playbook выложите в свой репозиторий, в ответ предоставьте ссылку на него.

https://github.com/Darkpunks/netology-ansible2
