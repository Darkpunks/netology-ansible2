# netology-ansible2

# 8.2 �������� Playbook

## GROUP VARS
java_oracle_jdk_package - ����� ��������� Java
kibana_package - ����� ��������� kibana
elasticsearch_package - ����� ��������� elasticsearch

java_home - �������� ������� java
elastic_home - �������� ������� Elasticsearch
kibana_home - �������� ������� Kibana

java_jdk_version - ������ Java
elastic_version - ������ Elasticsearch
kibana_version - ������ Kibana

## �������� Play 

### Install Java
 ����������� ���� *java* 
 - ��������� ���������� java_home
 - ����������� ������������� ������
 - ������� �������� java_home
 - ���������� ������ jdk � java_home
 - �������� jdk.sh �� ������� jdk.sh.j2 

### Install Elastic
 ����������� ���� *elastic*  
 - ����������� ������������� ������
 - ������� �������� elastic_home
 - ���������� ������ elasticsearch � elastic_home
 - �������� elk.sh �� ������� elk.sh.j2 

### install Kibana
 ����������� ���� *kibana*  
 - ����������� ������������� ������
 - ������� �������� kibana_home
 - ���������� ������ kibana � kibana_home
 - �������� kib.sh �� ������� kib.sh.j2 