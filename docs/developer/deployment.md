
# Процесс развертывания VPN-Service

## 1. Назначение

Данный раздел описывает процесс развертывания системы VPN-Service в локальной среде разработки и в production-среде.

## 2. Подготовка окружения

Перед развертыванием необходимо установить:

- Docker
- Docker Compose
- Git
- Java 17 или выше
- Maven или Gradle
- PostgreSQL
- Redis

## 3. Клонирование репозитория

Клонирование репозитория и переход в директорию проекта.
```bash
git clone https://github.com/username/vpn-service.git
cd vpn-service
```
## 4. Настройка переменных окружения

Создайте файл .env на основе примера.
```bash
cp .env.example .env
```
Пример содержимого:
```
APP_ENV=development
APP_PORT=8080

DB_HOST=localhost
DB_PORT=5432
DB_NAME=vpn_service
DB_USER=postgres
DB_PASSWORD=postgres

REDIS_HOST=localhost
REDIS_PORT=6379

JWT_SECRET=secret_key

XRAY_API_URL=http://localhost:10085
```
## 5. Запуск инфраструктуры

Запуск PostgreSQL и Redis через Docker Compose.
```bash
docker compose up -d postgres redis
```
Проверка работающих контейнеров.
```bash
docker ps
```
## 6. Применение миграций базы данных

Выполнение миграций через скрипт migrate.sh или через Maven с Flyway.
```bash
cd database
./migrate.sh
```
## 7. Запуск микросервисов

Auth Service: запуск через Maven Spring Boot.
```bash
cd backend/auth-service
mvn spring-boot:run
```
Billing Service: запуск через Maven Spring Boot.
```bash
cd backend/billing-service
mvn spring-boot:run
```
VPN Core Service: запуск через Maven Spring Boot.
```bash
cd backend/vpn-core-service
mvn spring-boot:run
```
API Gateway: запуск через Maven Spring Boot.
```bash
cd gateway/api-gateway
mvn spring-boot:run
```
## 8. Проверка работоспособности

Отправка GET-запроса на endpoint /health.
```bash
curl http://localhost:8080/health
```
Ожидаемый ответ: статус UP.
```json
{
  "status": "UP"
}
```
## 9. Запуск через Docker Compose

Полный запуск системы в фоновом режиме.
```bash
docker compose up -d
```
Остановка всех сервисов.
```bash
docker compose down
```
Пересборка образов и запуск.
```bash
docker compose up -d --build
```
Просмотр логов.
```bash
docker compose logs -f
```
## 10. Production-развертывание

Сборка образов для каждого сервиса.
```bash
docker build -t vpn-service/auth-service ./backend/auth-service
docker build -t vpn-service/billing-service ./backend/billing-service
docker build -t vpn-service/vpn-core-service ./backend/vpn-core-service
docker build -t vpn-service/api-gateway ./gateway/api-gateway
```
Публикация образов в реестр.
```bash
docker push vpn-service/auth-service
docker push vpn-service/billing-service
docker push vpn-service/vpn-core-service
docker push vpn-service/api-gateway
```
## 11. Развертывание в Kubernetes

Применение конфигураций.
```bash
kubectl apply -f infrastructure/kubernetes/
```
Проверка состояния подов и сервисов.
```bash
kubectl get pods
kubectl get svc
```
## 12. Настройка Nginx

Проксирование запросов на API Gateway.
```nginx
server {
    listen 80;

    location / {
        proxy_pass http://api-gateway:8080;
    }
}
```
## 13. Настройка HTTPS

Получение SSL-сертификата через Certbot.
```bash
certbot --nginx -d api.vpn-service.com
```
## 14. Резервное копирование

Создание дампа базы данных.
```bash
pg_dump -U postgres vpn_service > backup.sql
```
Восстановление из дампа.
```bash
psql -U postgres vpn_service < backup.sql
```
## 15. Обновление системы

Обновление образов и перезапуск контейнеров через Docker Compose.
```bash
docker compose pull
docker compose up -d
```
Или перезапуск deployment в Kubernetes.
```bash
kubectl rollout restart deployment/api-gateway
```
## 16. Возможные ошибки
Сервис не запускается: проверка переменных окружения и логов.
Нет подключения к БД: проверка DB_HOST и DB_PORT.

VPN не работает: проверка XRAY_API_URL и доступности серверов.
