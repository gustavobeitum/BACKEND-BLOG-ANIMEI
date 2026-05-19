# 📱 Social Blog API — Laravel

> Esta foi minha **segunda API** desenvolvida de forma independente, dando continuidade ao processo de verificação e consolidação dos meus conhecimentos em desenvolvimento Back-end. Em relação ao projeto anterior, esta versão evoluiu com funcionalidades mais sofisticadas: sistema de curtidas com cache, verificação de e-mail por código, reset de senha por código via cache e busca de posts por categoria.

---

## 📌 Sobre o projeto

API RESTful inspirada em uma rede social/blog, onde usuários autenticados podem criar posts, comentar, responder comentários, curtir posts e comentários, além de buscar conteúdos por tipo. O sistema conta com verificação de e-mail por código numérico e um fluxo completo de recuperação de senha também por código, ambos com expiração via cache.

---

## 🚀 Tecnologias utilizadas

- **PHP** `^7.3 | ^8.0`
- **Laravel** `^8.75`
- **Laravel Sanctum** — autenticação via tokens
- **Laravel Cache** — armazenamento temporário de códigos de verificação
- **Laravel Mail** — envio de e-mails (verificação e recuperação de senha)
- **SQLite** (padrão) / compatível com MySQL

---

## ⚙️ Requisitos

- PHP >= 7.3
- Composer
- SQLite ou MySQL
- Servidor de e-mail (SMTP) configurado para envio de códigos

---

## 🛠️ Instalação e configuração

```bash
# 1. Clone o repositório
git clone https://github.com/gustavobeitum/BACKEND-BLOG-ANIMEI.git
cd BACKEND-BLOG-ANIMEI

# 2. Instale as dependências
composer install

# 3. Configure o arquivo de ambiente
cp .env.example .env

# 4. Gere a chave da aplicação
php artisan key:generate

# 5. Execute as migrations
php artisan migrate

# 6. Crie o link simbólico para o storage (upload de imagens)
php artisan storage:link

# 7. Inicie o servidor
php artisan serve
```

> **Atenção:** configure as variáveis `MAIL_*` no `.env` para que o envio de códigos por e-mail funcione corretamente.

---

## 🗄️ Banco de dados

O projeto utiliza **SQLite** por padrão. Para usar MySQL, basta descomentar e preencher as variáveis `DB_HOST`, `DB_PORT`, `DB_DATABASE`, `DB_USERNAME` e `DB_PASSWORD` no `.env`.

---

## 📂 Estrutura principal

```
app/
├── Http/
│   ├── Controllers/
│   │   ├── Auth/
│   │   │   ├── AuthenticatedSessionController.php       # Login / Logout
│   │   │   ├── EmailVerificationWithCodeController.php  # Envio de código por e-mail
│   │   │   ├── EmailVerificatedController.php           # Validação do código
│   │   │   ├── PasswordResetLinkController.php          # Solicitar reset de senha
│   │   │   └── NewPasswordController.php               # Validar código e redefinir senha
│   │   ├── PostController.php
│   │   ├── CommentController.php
│   │   ├── AnswerController.php
│   │   ├── LikeController.php
│   │   ├── LikeCommentController.php
│   │   ├── SearchPostController.php
│   │   └── UserController.php
│   └── Middleware/
│       └── CheckUserVerifiedEmail.php   # Verifica se e-mail está confirmado
├── Models/
│   ├── User.php
│   ├── Post.php
│   ├── Comment.php
│   ├── Answer.php
│   ├── Like.php
│   └── LikeComment.php
```

---

## 🔗 Endpoints da API

### 🔓 Rotas públicas (sem autenticação)

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| `POST` | `/api/user` | Cadastrar novo usuário |
| `GET` | `/api/users` | Listar todos os usuários |
| `GET` | `/api/user/{user_id}` | Visualizar usuário específico |
| `POST` | `/api/login` | Login |
| `POST` | `/api/forgot-password` | Solicitar código de reset de senha |
| `POST` | `/api/reset-code-of-password` | Validar código de reset de senha |
| `POST` | `/api/reset-password` | Redefinir senha com novo valor |

---

### 🔒 Rotas autenticadas (requer token Sanctum)

#### Usuário

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| `GET` | `/api/user` | Ver perfil do usuário autenticado |
| `PUT` | `/api/user/{user_id}` | Atualizar perfil |
| `DELETE` | `/api/logout` | Logout |
| `DELETE` | `/api/user/{user_id}` | Deletar conta *(requer `check`)* |

#### Verificação de e-mail

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| `POST` | `/api/request-code-email` | Solicitar código de verificação por e-mail |
| `POST` | `/api/verification-code-email` | Confirmar e-mail com o código recebido |

#### Posts *(requer middleware `check`)*

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| `GET` | `/api/post` | Listar posts |
| `GET` | `/api/post/{post_id}` | Visualizar post específico |
| `GET` | `/api/post/{post}/comments` | Listar comentários de um post |
| `GET` | `/api/search-post?type={tipo}` | Buscar posts por tipo/categoria |
| `POST` | `/api/post` | Criar post |
| `PUT` | `/api/post/{post_id}` | Atualizar post |
| `DELETE` | `/api/post/{post_id}` | Deletar post |

> Tipos disponíveis para busca: `news`, `cancellations`, `curiosities`, `updates`

#### Curtidas em posts *(requer middleware `check`)*

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| `POST` | `/api/like/{post_id}` | Curtir / descurtir post (toggle) |
| `GET` | `/api/countLikes-post/{post_id}` | Contar curtidas de um post |

#### Curtidas em comentários *(requer middleware `check`)*

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| `POST` | `/api/like-comment/{comment_id}` | Curtir / descurtir comentário (toggle) |
| `GET` | `/api/countLikes-comment/{comment_id}` | Contar curtidas de um comentário |

#### Comentários *(requer middleware `check`)*

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| `GET` | `/api/comment` | Listar comentários |
| `GET` | `/api/comment/{comment_id}` | Visualizar comentário |
| `POST` | `/api/comment` | Criar comentário |
| `PUT` | `/api/comment/{comment_id}` | Atualizar comentário |
| `DELETE` | `/api/comment/{comment_id}` | Deletar comentário |

#### Respostas de comentários *(requer middleware `check`)*

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| `GET` | `/api/answer` | Listar respostas |
| `GET` | `/api/answer/{answer_id}` | Visualizar resposta |
| `POST` | `/api/answer` | Criar resposta |
| `PUT` | `/api/answer/{answer_id}` | Atualizar resposta |
| `DELETE` | `/api/answer/{answer_id}` | Deletar resposta |

---

## 🔐 Autenticação

A API utiliza **Laravel Sanctum**. Após o login, inclua o token no header de todas as requisições autenticadas (se for realizado testes no Postman por exemplo):

```
Authorization: Bearer {seu_token}
```

---

## ✉️ Verificação de e-mail por código

O fluxo de verificação de e-mail funciona da seguinte forma:

1. Envie uma requisição para `/api/request-code-email` com o e-mail desejado.
2. Um código numérico de **6 dígitos** é gerado e armazenado em cache por **15 minutos**.
3. O código é enviado ao e-mail informado.
4. Confirme o e-mail enviando o código para `/api/verification-code-email`.

---

## 🔑 Recuperação de senha por código

O fluxo de reset de senha também utiliza código via cache:

1. `POST /api/forgot-password` — solicita o envio do código de recuperação ao e-mail.
2. `POST /api/reset-code-of-password` — valida o código recebido (expira automaticamente).
3. `POST /api/reset-password` — define a nova senha após o código validado.

---

## ❤️ Sistema de curtidas com cache

As curtidas em posts e comentários funcionam como **toggle** — a mesma rota serve para curtir e descurtir. A contagem é armazenada em **cache por 60 minutos** e atualizada automaticamente a cada interação, reduzindo consultas ao banco de dados.

---

## 👤 Model: User

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `username` | string (único) | Nome de usuário |
| `name` | string | Primeiro nome |
| `surname` | string | Sobrenome |
| `image` | string | Foto de perfil |
| `email` | string (único) | E-mail |
| `password` | string | Senha (hash) |

---

## 📄 Model: Post

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `user_id` | integer | Autor do post |
| `text` | string | Conteúdo do post |
| `image` | string | Imagem do post |
| `type` | string | Categoria (`news`, `cancellations`, `curiosities`, `updates`) |

---

## 🧪 Testes

```bash
php artisan test
```

---

## 📝 Licença

Este projeto está sob a licença **MIT**.
