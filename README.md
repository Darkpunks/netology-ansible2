# Домашнее задание к занятию "08.02 Работа с Playbook" 
_______________________________________________________________________________
# 8.2 описание Playbook
______________________________________________________________________________
## GROUP VARS
 - java_oracle_jdk_package - пакет установки Java
 - kibana_package - пакет установки kibana
 - elasticsearch_package - пакет установки elasticsearch

 - java_home - домашний каталог java
 - elastic_home - домашний каталог Elasticsearch
 - kibana_home - домашний каталог Kibana

 - java_jdk_version - версия Java
 - elastic_version - версия Elasticsearch
 - kibana_version - версия Kibana

## Описание Play 

### Install Java
 - установлены тэги *java* 
 - установка переменной java_home
 - копирование установочного пакета
 - создние каталога java_home
 - распаковка пакета jdk в java_home
 - создание jdk.sh по шаблону jdk.sh.j2 

### Install Elastic
 - установлены тэги *elastic*  
 - копирование установочного пакета
 - создние каталога elastic_home
 - распаковка пакета elasticsearch в elastic_home
 - создание elk.sh по шаблону elk.sh.j2 

### install Kibana
 - установлены тэги *kibana*  
 - копирование установочного пакета
 - создние каталога kibana_home
 - распаковка пакета kibana в kibana_home
 - создание kib.sh по шаблону kib.sh.j2 
