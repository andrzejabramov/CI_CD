# Гайд: 

### Используем ссылку: 
https://habr.com/ru/articles/764568/

1. Запускаем (проверяем, что запущен сервер, на который будет деплоиться приложение) 
2. Заходим на сервер:  
```commandline
ssh <alias_server>
```
3. Копируем из habr (ссылка) в терминал с сервером код, который запускает на сервере  
CI/CD runner в контейнере (программа, которая принимает запросы на деплой от GitLab: там она должна быть зарегистрирована). 
```commandline
docker run -d --name gitlab-runner --restart always \
  -v /srv/gitlab-runner/config:/etc/gitlab-runner \
  -v /var/run/docker.sock:/var/run/docker.sock \
  gitlab/gitlab-runner:alpine
```
4. Получаем (если первый раз - будет сначала скачен образ):
```commandline
root@annika:~/pr_booking# docker run -d --name gitlab-runner --restart always \
  -v /srv/gitlab-runner/config:/etc/gitlab-runner \
  -v /var/run/docker.sock:/var/run/docker.sock \
  gitlab/gitlab-runner:alpine
e34aaa922ffbabd20a91c654d9070b67799841e5af97b22174a97d0a47797d81
root@annika:~/pr_booking#
```
5. Заходим в репо GitLab - settings - CI/CD - RUNNERS:
![GitLab_repo.png](https://github.com/andrzejabramov/CI_CD/blob/master/images/GitLab_repo.png)  
6. Выключаем предустановленные runners  GitLab (будем регистрировать свой):  
![OffRunnersGitLab.png](https://github.com/andrzejabramov/CI_CD/blob/master/images/OffRunnersGitLab.png)  
7. Жмем Create project runner
![OffRunnersGitLab.png](https://github.com/andrzejabramov/CI_CD/blob/master/images/CreateRunner.png)
8. Выбираем Linux
9. В терминале на сервере вводим команду для регистрации runner:
```commandline
docker run --rm -it \
    -v /srv/gitlab-runner/config:/etc/gitlab-runner \
    gitlab/gitlab-runner:alpine register
```
10. На предложение указать URL: 
https://gitlab.com/
11. На запрос токена вставляем токен, выданный GitLab
12. На предложение ввести Имя runner - указываем  
13. Далее выбираем executor:
docker
14. Docker image указываем докер в докере:
 docker:dind
15. Заходим в волюм докера runner (создан при первом создании runner):
cd /srv/gitlab-runner/config  
16. Здесь через nano или vim открываем файл config.toml  
17. Находим строку внизу:
volums = ["/cache""]  
и меняем ее на:  
volumes = ["/var/run/docker.sock:/var/run/docker.sock", "/cache"]
Чтобы выйти с сохранением: esc - wq! - enter
18. В GitLab видим, что runner зарегистрирован:
![RunnerRegistered.png](https://github.com/andrzejabramov/CI_CD/blob/master/images/RunnerRegistered.png)  
19. В меню находим Build - runner:
![BuildRunners.png](https://github.com/andrzejabramov/CI_CD/blob/master/images/BuildPipeline.png)
20. На открывшейся странице выбираем: Try test pipline:
![TestPipelineGitLab](https://github.com/andrzejabramov/CI_CD/blob/master/images/TestPipelineGitLab.png)
