# Plataforma de Cursos Online

Sistema de gestão de cursos online com arquitetura de microserviços, incluindo funcionalidades de matrícula, pagamento e gestão de leads de marketing.

## 📋 Descrição Geral

Esta aplicação é uma plataforma completa para cursos online que permite:
- Compra de cursos com processamento de pagamento
- Gestão de matrículas e estudantes
- Autenticação e acesso a cursos
- Gestão de leads de marketing
- Comunicação assíncrona entre serviços via mensageria

## 🏗️ Arquitetura

O sistema segue uma arquitetura de **microserviços** com os seguintes componentes:

### Front-end
- **`front-end/`** - Aplicação Angular (SPA) que consome as APIs dos serviços

### Microserviços Back-end
- **`mkt-node/`** - Serviço de Marketing (Node.js/TypeScript)
  - Gestão de leads
  - Conversão de leads em clientes
  
- **`financeiro-php/`** - Serviço Financeiro (PHP/Swoole)
  - Processamento de pagamentos
  - Cadastro de clientes
  
- **`academico-php-web/`** - API Acadêmica (Laravel/Lumen)
  - Autenticação (JWT)
  - Gestão de cursos e estudantes
  
- **`academico-php/`** - Consumer de Eventos Acadêmicos (PHP)
  - Processamento assíncrono de matrículas
  - Envio de e-mails de boas-vindas

### Infraestrutura
- **`servicos-nginx/`** - API Gateway (Nginx)
  - Roteamento e proxy reverso
  - Ponto único de entrada para as APIs

### Comunicação
- **HTTP/REST** - Comunicação síncrona entre front-end e APIs
- **RabbitMQ** - Mensageria para comunicação assíncrona entre serviços

### Bancos de Dados
- **PostgreSQL** - Dados acadêmicos e financeiros
- **MongoDB** - Dados de marketing (leads)

## 🚀 Como Executar Localmente

### Pré-requisitos
- Docker e Docker Compose instalados
- Git

### Execução Completa

1. **Clone o repositório** (se ainda não tiver feito):
   ```bash
   git clone <url-do-repositorio>
   cd alura-ms
   ```

2. **Execute todos os serviços**:
   ```bash
   docker-compose up
   ```

   Isso irá iniciar:
   - Front-end na porta **4200**
   - API Gateway na porta **80**
   - Serviços de back-end nas portas **3000**, **8080**, **9501**
   - RabbitMQ na porta **5672**
   - Bancos de dados (PostgreSQL e MongoDB)

3. **Acesse a aplicação**:
   - Front-end: http://localhost:4200
   - API Gateway: http://localhost:80

### Execução Individual de Serviços

Cada serviço possui um script de inicialização (`.sh`) na raiz do projeto. Para executar um serviço isoladamente, você precisará:

1. Ter as dependências instaladas (Node.js, PHP, Composer)
2. Ter os serviços de infraestrutura rodando (RabbitMQ, bancos de dados)
3. Executar o script correspondente:
   ```bash
   ./front-end.sh
   ./mkt-node.sh
   ./financeiro-php.sh
   ./academico-php-web.sh
   ./academico-php.sh
   ```

### Configuração

As variáveis de ambiente estão definidas no `docker-compose.yml`. Para desenvolvimento local, você pode:

- Criar um arquivo `.env` na raiz (não versionado)
- Sobrescrever variáveis específicas conforme necessário
- Ajustar credenciais de banco de dados e serviços externos (ex: Gmail)

**⚠️ Atenção**: Algumas credenciais estão hardcoded no `docker-compose.yml`. Em produção, mova-as para variáveis de ambiente ou sistema de secrets.

## 📖 Por Onde Começar a Leitura do Código

### 1. Visão Geral da Arquitetura
- **`docker-compose.yml`** - Entenda como os serviços se relacionam e dependem uns dos outros
- **`servicos-nginx/api-gateway.conf`** - Veja como as rotas são distribuídas

### 2. Fluxo do Usuário (Front-end)
- **`front-end/src/app/login/`** - Autenticação
- **`front-end/src/app/compra/`** - Fluxo de compra de cursos
- **`front-end/src/app/area-logada/cursos/`** - Visualização e gestão de cursos

### 3. Microserviços (Ordem sugerida)

#### Marketing (mais simples, boa estrutura)
- **`mkt-node/leads/domain/`** - Entidades de domínio
- **`mkt-node/leads/application/`** - Casos de uso
- **`mkt-node/leads/ui/express-leads-router.ts`** - Endpoints HTTP

#### Financeiro (processamento de pagamento)
- **`financeiro-php/src/Client/Domain/`** - Entidades de domínio
- **`financeiro-php/src/Client/App/`** - Casos de uso (EnrollClient, ProcessPayment)
- **`financeiro-php/index.php`** - Ponto de entrada HTTP

#### Acadêmico Web (API REST)
- **`academico-php-web/routes/web.php`** - Definição de rotas
- **`academico-php-web/app/Http/Controllers/`** - Lógica de controle
- **`academico-php-web/app/Models/`** - Modelos de dados

#### Acadêmico Consumer (processamento assíncrono)
- **`academico-php/receive.php`** - Consumer de eventos RabbitMQ
- **`academico-php/functions.php`** - Funções auxiliares

### 4. Fluxo Principal de Negócio

Para entender o fluxo completo de compra e matrícula:

1. Front-end cria lead → `mkt-node`
2. Front-end envia dados de pagamento → `financeiro-php`
3. Financeiro publica evento → RabbitMQ
4. Consumer acadêmico processa evento → `academico-php`
5. Consumer marketing converte lead → `mkt-node`
6. Usuário faz login → `academico-php-web`
7. Usuário acessa cursos → `academico-php-web`

## 🔧 Tecnologias Utilizadas

- **Front-end**: Angular 12, TypeScript
- **Back-end**: Node.js, PHP 8, Laravel/Lumen
- **Bancos de Dados**: PostgreSQL, MongoDB
- **Mensageria**: RabbitMQ
- **Servidor Web**: Nginx, Swoole
- **Orquestração**: Docker, Docker Compose

## 📁 Estrutura de Diretórios

```
alura-ms/
├── front-end/              # Aplicação Angular
├── mkt-node/               # Microserviço de Marketing
├── financeiro-php/         # Microserviço Financeiro
├── academico-php-web/      # API Acadêmica (Laravel/Lumen)
├── academico-php/          # Consumer de Eventos Acadêmicos
├── servicos-nginx/         # API Gateway
├── docker-compose.yml      # Orquestração de serviços
└── *.sh                    # Scripts de inicialização
```

## 📝 Notas Importantes

- O sistema utiliza comunicação **síncrona (HTTP)** para operações que precisam de resposta imediata
- A comunicação **assíncrona (RabbitMQ)** é usada para processamento de eventos (matrículas, conversão de leads)
- Cada microserviço possui seu próprio banco de dados
- O API Gateway centraliza o acesso às APIs dos serviços

## 🤝 Contribuindo

1. Faça fork do projeto
2. Crie uma branch para sua feature (`git checkout -b feature/AmazingFeature`)
3. Commit suas mudanças (`git commit -m 'Add some AmazingFeature'`)
4. Push para a branch (`git push origin feature/AmazingFeature`)
5. Abra um Pull Request

## 📄 Licença

Este projeto é parte de um curso de microserviços.

# alura-microservicos
