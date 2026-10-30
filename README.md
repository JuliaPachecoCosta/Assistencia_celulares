<div align="center">

# 📱💖 Assistência Técnica de Celulares

**Sistema web para cadastrar clientes, aparelhos e agendar reparos, com login seguro e dados protegidos pela LGPD.**

![Python](https://img.shields.io/badge/Python-3.12-ec4899?style=for-the-badge&logo=python&logoColor=white)
![Flask](https://img.shields.io/badge/Flask-3.0.3-db2777?style=for-the-badge&logo=flask&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL-8.0-be185d?style=for-the-badge&logo=mysql&logoColor=white)
![LGPD](https://img.shields.io/badge/LGPD-CPF%20criptografado-f472b6?style=for-the-badge)

</div>

---

## 🌸 Sobre o projeto

Uma assistência técnica de celulares perdia horários, duplicava informações e guardava dados pessoais sem proteção. Este sistema resolve isso organizando **clientes**, **aparelhos** e **agendamentos de reparo** em um só lugar, com autenticação de usuários com tempo de expiração e CPF armazenado de forma criptografada.

## ✨ Funcionalidades

| | Funcionalidade | O que faz |
|---|---|---|
| 🔐 | **Login** | Valida usuário e senha e mostra mensagem clara em caso de falha |
| 🏠 | **Tela principal** | Exibe o nome do usuário logado e atalhos para as demais telas |
| 🚪 | **Logout** | Botão **Sair** em todas as telas encerra a sessão |
| 👤 | **Clientes** | Cadastro, busca (nome, e-mail, telefone ou CPF), edição e exclusão |
| 📱 | **Aparelhos** | Cadastro de marca, modelo, IMEI, cor e defeito, sempre associado a um cliente |
| 📆 | **Agendamentos** | Lista ordenada por data com dados do cliente e do aparelho, e reagendamento |

## 🛠️ Tecnologias

- 🐍 **Python 3.12** (mínimo 3.10) com **Flask 3.0.3**
- 🐬 **MySQL 8** via `mysql-connector-python`
- 🔒 **bcrypt** para senhas e CPF
- 🎨 **HTML, CSS e JavaScript puro** no front-end (tema rosa 💗)

## 📂 Estrutura do projeto

```
entrega/
├── 📄 documentacao.pdf          # Anexo 1: requisitos e infraestrutura
├── 🖼️ der.png                   # Diagrama Entidade-Relacionamento
├── 🗄️ assistencia_db.sql        # Criação e população do banco
└── 📁 sistema/
    ├── app.py                   # Ponto de entrada (python app.py)
    ├── rotas.py                 # Rotas das páginas e da API JSON
    ├── autenticacao.py          # Login, hash bcrypt e expiração da sessão
    ├── clientes.py              # Regras de clientes (CPF criptografado, busca)
    ├── aparelhos.py             # Regras de aparelhos (IMEI, vínculo com cliente)
    ├── agendamento.py           # Regras de agendamentos e reagendamento
    ├── db.py                    # Conexão com o MySQL e funções de consulta
    ├── testar_conexao.py        # Teste rápido da conexão
    ├── requirements.txt         # Dependências
    ├── ROTEIRO.md               # Passo a passo detalhado de testes
    ├── 📁 templates/            # login, principal, clientes, aparelhos, agendamentos
    └── 📁 static/               # app.js e style.css
```

## 🚀 Como rodar

**1. Crie o banco** (na pasta onde está o `assistencia_db.sql`):

```bash
mysql -u root -p < assistencia_db.sql
```

**2. Instale as dependências** (dentro da pasta `sistema`):

```bash
python -m venv venv
venv\Scripts\activate          # Windows
source venv/bin/activate       # Linux/Mac
pip install -r requirements.txt
```

**3. Configure a conexão** com variáveis de ambiente.

PowerShell:

```powershell
$env:DB_USER="root"
$env:DB_PASSWORD="sua_senha_do_mysql"
$env:SECRET_KEY="uma-chave-longa"
```

Linux/Mac:

```bash
export DB_USER=root DB_PASSWORD=sua_senha_do_mysql SECRET_KEY=uma-chave-longa
```

> 💡 Se precisar, também existem `DB_HOST` (padrão `localhost`), `DB_PORT` (padrão `3306`) e `DB_NAME` (padrão `assistencia_db`).

**4. Teste a conexão:**

```bash
python testar_conexao.py
```

Esperado: `Conexão OK: {'banco': 'assistencia_db', ...}`

**5. Suba o sistema:**

```bash
python app.py
```

Abra **http://localhost:5000** no navegador. ✨

## 🔑 Usuários de teste

| Usuário | Senha |
|---|---|
| `admin` | `admin123` |
| `carlos` | `carlos123` |
| `ana` | `ana12345` |

CPFs de teste (válidos): `529.982.247-25`, `111.444.777-35` e `390.533.447-05`.

## 🗃️ Banco de dados

O banco `assistencia_db` tem 5 tabelas, cada uma com 3 registros de exemplo:

```
clientes (1) ──── (N) aparelhos (1) ──── (N) agendamentos (N) ──── (1) servicos

usuarios  → quem acessa o sistema (sem relacionamento)
```

| Tabela | Campos principais |
|---|---|
| `usuarios` | id, nome, login (único), senha_hash |
| `clientes` | id, nome, **cpf_hash**, telefone, e-mail, endereço |
| `aparelhos` | id, **cliente_id (FK)**, imei (único), marca, modelo, cor, defeito |
| `servicos` | id, descrição, valor |
| `agendamentos` | id, **aparelho_id (FK)**, **servico_id (FK)**, data_agendamento, status, observação |

Status possíveis do agendamento: `AGENDADO`, `EM_ANDAMENTO`, `CONCLUIDO` e `CANCELADO`.

## 🛡️ Segurança e regras de negócio

- 🔒 **Senhas e CPF** são guardados com hash **bcrypt**; o CPF nunca é exibido.
- ⏰ A **sessão expira em 30 minutos**; depois disso, o sistema volta ao login.
- 🚧 Páginas e API só funcionam com usuário logado.
- 🧱 Consultas com **parâmetros `%s`**, que evitam SQL Injection.
- ✅ CPF com 11 dígitos e sem duplicidade; IMEI com 15 dígitos e único.
- 🔗 Todo aparelho precisa de um cliente já cadastrado.
- 🚫 Não é possível excluir cliente que tenha aparelhos, nem aparelho que tenha agendamentos.

## 🔌 Rotas da API

| Método | Rota | Descrição |
|---|---|---|
| `POST` | `/api/login` | Entrar |
| `POST` | `/api/logout` | Sair |
| `GET` | `/api/me` | Nome do usuário logado |
| `GET` | `/api/teste-conexao` | Testa a conexão com o banco |
| `GET` | `/api/servicos` | Lista os serviços |
| `GET` `POST` | `/api/clientes` | Lista (aceita `?q=termo`) e cadastra |
| `PUT` `DELETE` | `/api/clientes/<id>` | Edita e exclui |
| `GET` `POST` | `/api/aparelhos` | Lista e cadastra |
| `PUT` `DELETE` | `/api/aparelhos/<id>` | Edita e exclui |
| `GET` `POST` | `/api/agendamentos` | Lista (por data) e cadastra |
| `PUT` `DELETE` | `/api/agendamentos/<id>` | Reagenda e exclui |

## 🧪 Como conferir se funcionou

1. Entre com `admin` / `admin123` e veja **"Usuário: Administrador"** e o botão **Sair** no topo.
2. Em **Clientes**, busque por `maria` e confirme que só a Maria Oliveira aparece.
3. Em **Aparelhos**, cadastre um aparelho escolhendo o cliente na lista.
4. Em **Agendamentos**, confira a ordem por data e use **Reagendar**.
5. Clique em **Sair** e tente abrir `/clientes`: deve voltar ao login.

O passo a passo completo está no `ROTEIRO.md`.

---

<div align="center">

Feito com 💖 para o desafio da **Assistência Técnica de Celulares**

</div>
