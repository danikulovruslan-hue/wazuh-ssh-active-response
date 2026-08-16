# wazuh-ssh-active-response
SSH brute-force detection

Все что я буду делать, я буду делать на виртуальной машине где установлен Kali linux с Wazuh в Docker в роли атакующего и так же виртуальный сервер Ubuntu server в роли жертвы.

Как настроить activy-response(детект брут-форс атак)

Cначала нам нужно зайти в файл конфигурации wazuh по команде  "sudo nano ~/wazuh-docker/single-node/config/wazuh_cluster/wazuh_manager.conf". Далее листаем в самый конец(стрелки up down или колёсико мышки) и вставляем следующий текст в конфигурацию 
```
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
```
Что бы было всё как у меня на скрине <img width="1884" height="593" alt="Settings config" src="https://github.com/user-attachments/assets/6d85cd58-40c6-4e80-aba4-451b0cb6d15f" />
После чего нажимаем CTRL+O Enter CTRL+X. Эта конфигурация выполняет функцию блокировки IP адресса от которого идёт очень много попыток входа с неправильным логином и паролем(<command>firewall-drop</command> <location>all</location> <rules_id>5710,5712,5758,2502</rules_id>), после чего этот адресс с которого шла атака разблокируется после прохода 120 секунд(<timeout>120</timeout>).
После редактирования конфигурации ОБЯЗАТЕЛЬНО надо перезапустить менеджер и агент wazuh командой
```
  sudo docker restart single-node-wazuh.manager-1
```
 (перезапуск менеджара на kali)
  ```
  sudo systemctl restart wazuh-agent
```
(перезапуск агента на сервере).
Направляем атаку на наш сервер через Hydra <img width="1912" height="375" alt="Hydra brute-force" src="https://github.com/user-attachments/assets/c07341c4-bcbf-4644-951d-b8db0f0a1f70" />
Как мы видим атака зависла на 3-х запросах изза работы нашего скрипта так же мы можем видеть на этом скрине пинг сервера после прохода 120 секунд.
На панели wazuh можно увидеть что всё успешно отработанно <img width="1920" height="818" alt="Wazuh logs" src="https://github.com/user-attachments/assets/8faaef4d-c0ca-405c-92fa-df6dab6a8307" />
Так же в логах сервера отчетливо видим что наш скрипт отработал успешно <img width="1279" height="799" alt="Activy reponse logs" src="https://github.com/user-attachments/assets/1fcc7cb3-70c1-4c96-a534-86b552e30a2f" />

На этом всё!
