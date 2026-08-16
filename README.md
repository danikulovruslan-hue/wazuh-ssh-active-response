# wazuh-ssh-active-response
SSH brute-force detection

Все что я буду делать, я буду делать на виртуальной машине где установлен Kali linux с Wazuh в Docker в роли атакующего и так же виртуальный сервер Ubuntu server в роли жертвы.

Как настроить activy-response(детект брут-форс атак)
Cначала нам нужно зайти в файл конфигурации wazuh по команде  "sudo nano ~/wazuh-docker/single-node/config/wazuh_cluster/wazuh_manager.conf". Далее листаем в самый конец(стрелки up down или колёсико мышки) и вставляем следующий текст в конфигурацию 
  <command>
    <name>firewall-drop</name>
    <executable>firewall-drop</executable>
    <timeout_allowed>yes</timeout_allowed>
  </command>
 
  <active-response>
    <command>firewall-drop</command>
    <location>all</location>
    <rules_id>5710,5712,5758,2502</rules_id>
    <timeout>120</timeout>
  </active-response>
Что бы было всё как у меня на скрине 
