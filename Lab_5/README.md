## Lab_5: Робота з Docker.

Makefile
---
1. Прочитав про `docker-compose`.
2. Ознайомився з бібліотекою `Flask`.
3. Створив папку `my_app`, `tests`. Скопіював файли з репозиторію `devops_course`, ознайомився з файлами.
4. Перевірив проект на працездатність , виконуючи наступні команди:
````
pipenv --python 3.6
pipenv install -r requirements.txt
pipenv run python app.py
````
+ Ініціалізував середовище та запустив тест
````
pipenv run pytest test_app.py --url http://localhost:5000
````
   - При запуску додатку виникла помилка з'єднання з  `redis-server`. Установив `redis-server` та налаштував його на вискористання.
   - Для того, щоб тести працювали створив папку `logs` і лог-файл `app.log` у папці з додатком. Після цього тести проходять успішно:
![image](img/1.jpg)
   
   - Видаляю всі файли, що створились в процесі запуску (Pipfile, Pipfile.lock);
    
   - Перевіряю роботу сайту, перейшовши на кожну із сторінок:
   
   ![image](img/2.jpg)
   
   ![image](img/3.jpg)
   
   ![image](img/4.jpg)

5. Створив два Dockerfile з іменами як у репозиторі `devops_course` та Makefile, який допоможе автоматизувати процес розгортання.
6. Ознайомлююся із вмістом Dockerfile та Makefile та його директивами:
    - `STATES` і `REPO` - змінні які містять назви тегів та назву Docker Hub репозиторію відповідно;
    - `.PHONY` - утиліта `make`, яка вказує файлу, що переліченні нище цілі не є файлами;
    - `$(STATES)` - ціль, призначення для білду контейнера;
    - `run` - ціль, призначення для створення мережі, у якій буде працювати додаток; запуску додатку і сховища `redis`;
    - `test-app` - запуск контейнера тестової програми;
    - `docker-prune` -очистка ресурсів, що бути використанні при роботі `Docker`.
7. Створюю Docker імеджі для додатку та для тестів виконавши команду:
    ```
    make .PHONY
    ```
8. Запускаю додаток та відкривши новий термінал, запускаю тести:
    ```
    make run
    make test-app
    ```
    - Переконуюся, що тести пройшли успішно:
       
    ![image](img/5.png)
    - Перевіряю роботу кожної сторінки веб-сайту:
    
    ![image](img/6.png)
    
    ![image](img/7.png)
    
    ![image](img/8.png)
    
9. Зупиняю проект, натиснувши `Ctrl+C`, та очищаю всі ресурси Docker за допомогою команди:
    ```
    make docker-prune
    ```
10. Створюю директиву в `Makefile` для завантаження створених імеджів у Docker Hub репозиторій. Завантажую імеджі до репозиторію `andee1/lab5` командою:
    ```
    make push
    ```
    - [Посилання на Docker Hub репозиторій](https://hub.docker.com/repository/docker/andee1/lab5);

11. Видаляю створені та завантаженні імеджі. Створюю директиву `delete-images` в `Makefile`, яка автоматизує процес видалення імеджів. Команда `docker images` виводить наступне:
    
    ![image](img/9.png)

Docker-compose
---
1. Створюю файл `docker-compose.yaml` у кореновій папці проекту та заповнюю вмістом згідно прикладу. Проект за цим варіантом трохи відрізняється від першого тим, що у нього з'являється дві мережі: `secret` і `public`:
    - У мережі `secret` знаходяться `app` і `redis`. Отстанній не має доступу до зовнішніх ресурсів;
    - До мережі `public` відносяться `app` і `tests`. Таким чином `tests` i `redis` не мають доступу один до одного.

2. Перевіряю чи Docker-compose встановлений та працює у моїй системі, а далі просто запускаю `docker-compose`:
    ```
    docker-compose version
    docker-compose -p lab5 up
    ```
3. Перевіряю чи працює веб-сайт. Для цього переходжу за адресою `http://0.0.0.0:80.`:

    ![image](img/10.png)
    
    ![image](img/11.png)
    
    ![image](img/12.png)
    
4. Перевіряю чи компоуз створив докер імеджі . Імеджі мають `compose-tests` і `compose-app`. Змінюю їх на власний репозиторій `andee1/lab5` і перезапускаю `docker-compose`, очистивши імеджі без тегів за допомогою директиви в `Makefile`:
    
    ![image](img/13.png)
    
5. Зупиняю проект, натиснувши `Ctrl+C`, і очищаю ресурси створені компоуз `docker-compose down`.

6. Завантажую створені імеджі до `Docker Hub` репозиторію за допомогою команди:
    ```
    docker-compose push
    ```
Завдання
 ---
1. На мою думку, як `docker-compose.yaml`, так і `Makefile` мають свої сфери використання і цільових користувачів:
   - `Makefiles` є інструментом збирання, принцип якого простий: для побудови цілі необхідно вказати залежності та команди для її побудови. Однак він має дещо важчу реалізацію і при роботі з багатьма `docker` контейнерами можуть виникнути складнощі;
   - За допомогою `docker-compose`  можна визначити багатоконтейнерну структуру в одному файлі, потім працювати з своєю програмою виконуючи лише одну команду, яка робить усе необхідне для її запуску, зупинки.
       
2. Створюю `docker-compose.yaml` для лабораторної №4:
   - `docker-compose.yaml` створює два імеджі для Django сайту та моніторингу ([docker-compose.yaml](https://github.com/Andrii464/labs/blob/main/Lab_4/docker-compose.yaml) файл знаходиться тут);     
   - Запускаю `docker-compose.yaml` командою
        ```
        docker-compose -p lab4 up
        ```
   - Перевіряю доступність головної сторінки та вкладки `health`:
     ![image](img/14.png)
           
     ![image](img/15.png)
 
    - Зупиняю проект, натиснувши `Ctrl+C`, і очищаю ресурси створені компоуз `docker-compose down`.
           
    - Завантажую створені імеджі до `Docker Hub` репозиторію за допомого команди ([Посилання на Docker Hub репозиторій](https://hub.docker.com/repository/docker/andee1/lab4_django)):
       ```
       docker-compose push
       ```
   
