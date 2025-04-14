# CrediConsulta - Projeto Full Stack com Angular + Spring Boot + Docker

Este projeto é uma aplicação full stack para consulta de créditos, composta por:

- Frontend: Angular 17+ com NGINX
- Backend: Spring Boot 3.4.4
- Banco de Dados: MariaDB 10.5
- Mensageria: Apache Kafka + Zookeeper
- Orquestração: Docker Compose

---

## 📊 Estrutura de Diretórios

```
CredConsulta/
├── CrediConsultaWeb/         # Frontend Angular
│   ├── src/
│   ├── dist/credi-consulta-web/browser/
│   ├── Dockerfile
│   └── nginx.conf
├── credito-consulta-api/    # Backend Spring Boot
│   ├── src/
│   ├── target/app.jar
│   ├── Dockerfile
│   └── wait-for.sh
├── docker-compose.yml
└── .env
```

---

## 🚀 Como Executar o Projeto

### 1. Clone o repositório

```bash
git clone <repo-url>
cd CredConsulta
```

### 2. Configure o arquivo `.env`

```env
# Banco de dados
DB_ROOT_PASSWORD=root
DB_NAME=credconsulta

# Backend
SPRING_DB_URL=jdbc:mariadb://db:3306/credconsulta
SPRING_DB_USER=root
SPRING_DB_PASS=root
KAFKA_SERVERS=kafka:9092

# Frontend
FRONTEND_PORT=4200
BACKEND_PORT=8080
DB_PORT=3306
ZOOKEEPER_PORT=2181
KAFKA_PORT=9092
```

### 3. Build do projeto

```bash
# Angular build local (opcional)
cd CrediConsultaWeb
npm install
npm run build -- --configuration=production
cd ..

# Compilar backend
cd credito-consulta-api
./mvnw clean package -DskipTests
cd ..
```

### 4. Subir os containers

```bash
docker-compose up --build
```

> O script `wait-for.sh` garante que o backend aguarde o banco iniciar.

---

## 🌐 Acessos

| Componente             | URL                                                                                               |
| ---------------------- | ------------------------------------------------------------------------------------------------- |
| Frontend Angular       | [http://localhost:4200](http://localhost:4200)                                                    |
| Rota lista de créditos | [http://localhost:4200/lista-creditos](http://localhost:4200/lista-creditos)                      |
| Backend Spring Boot    | [http://localhost:8080/api/creditos](http://localhost:8080/api/creditos) (ou conforme controller) |
| MariaDB                | localhost:3306                                                                                    |
| Kafka                  | localhost:9092                                                                                    |

---

## 🧪 Testes

### Testes Unitários

- **Frontend (Angular)**:
  - Utiliza o framework de testes **Karma + Jasmine**
  - Execute com:
    ```bash
    npm run test
    ```

- **Backend (Spring Boot)**:
  - Utiliza **JUnit 5** e **Mockito** para testes unitários de serviços e controladores
  - Execute com:
    ```bash
    ./mvnw test
    ```

### Testes de Integração

- O backend inclui testes de integração usando `@SpringBootTest`, conectando a banco H2 e simulando endpoints REST.
- Pode-se estender para testes com containers reais usando Testcontainers se necessário.

---

## 🗂️ Quadro de Tarefas (Kanban)

O planejamento, andamento e acompanhamento das tarefas deste projeto estão organizados no Trello:

👉 [Acessar Quadro Trello - Desafio Técnico Consulta de Crédito](https://trello.com/b/uXMxb9kq/desafio-tecnico-consulta-de-credito)

---

## 🛠️ Dockerfile do Frontend

```dockerfile
FROM node:22.14.0 AS builder
WORKDIR /app
COPY . .
RUN npm install
RUN npm run build -- --configuration=production

FROM nginx:alpine
COPY --from=builder /app/dist/credi-consulta-web/browser /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
```

## 🛠️ nginx.conf

```nginx
server {
  listen 80;
  server_name localhost;

  root /usr/share/nginx/html;
  index index.html;

  location / {
    try_files $uri $uri/ /index.html;
  }

  error_page 404 /index.html;
}
```

---

## 🔧 Comandos úteis

### Ver arquivos servidos pelo nginx

```bash
docker exec -it credconsulta-frontend-1 ls /usr/share/nginx/html
```

### Ver conteúdo do index.html

```bash
docker exec -it credconsulta-frontend-1 cat /usr/share/nginx/html/index.html
```

---

## ✉️ Dicas finais

- Sempre use `docker-compose build --no-cache` ao fazer alterações no build do Angular.
- Verifique a aba **Console/Network** do navegador (F12) para detectar erros de roteamento ou de carregamento de arquivos.
- Se a rota Angular não abrir, certifique-se que está definida em `app-routing.module.ts`.

---

Projeto pronto para ser utilizado em desenvolvimento local e deploy em produção com Docker ✨

