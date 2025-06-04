# API de Tarefas com Flask 📝🔐

Uma API RESTful simples e segura para gerenciamento de tarefas, construída com **Flask**. Inclui autenticação básica para geração de tokens JWT, protegendo as rotas de modificação de dados.

---

## 🔧 Funcionalidades

- Operações CRUD para lista de tarefas (Criar, Listar, Atualizar, Remover)
- Login com autenticação básica (Basic Auth) para geração de token
- Proteção de rotas com token JWT
- Status de tarefas: `pendente` ou `concluída`
- Identificação única para cada tarefa (`id` gerado com `secrets`)

---

## Como Executar

1. **Clone o repositório**  
   ```bash
   git clone <URL_DO_REPOSITORIO>
   cd <NOME_DA_PASTA>
   ```

2. **Crie o ambiente virtual**  
   ```bash
   python -m venv venv
   source venv/bin/activate  # Linux/Mac
   venv\Scripts\activate     # Windows
   ```

3. **Instale as dependências**  
   ```bash
   pip install flask pyjwt
   ```

4. **Execute o servidor**  
   ```bash
   python app.py
   ```

---

## 🔐 Autenticação

- **Usuário:** `admin`  
- **Senha:** `123_base64`

Para obter o token JWT, envie uma requisição `POST` para `/login` com **autenticação básica**.  

---

## 🛠️ Endpoints

### `POST /login`
Gera um token JWT com autenticação básica.
- **Requer:** Basic Auth

---

### `GET /tarefas`
Lista todas as tarefas.
- **Requer:** Nenhuma autenticação

---

### `POST /tarefas`
Cria uma nova tarefa.
- **Body JSON:**
  ```json
  {
    "descricao": "Texto da tarefa"
  }
  ```
- **Requer:** Nenhuma autenticação

---

### `PUT /tarefas/<id>`
Atualiza descrição ou status de uma tarefa.
- **Body JSON:**
  ```json
  {
    "descricao": "Nova descrição",
    "status": "pendente ou concluída"
  }
  ```
- **Requer:** Token JWT

---

### `PATCH /tarefas/<id>/pendente`
Marca a tarefa como pendente.
- **Requer:** Token JWT

---

### `PATCH /tarefas/<id>/concluida`
Marca a tarefa como concluída.
- **Requer:** Token JWT

---

### `DELETE /tarefas/<id>`
Remove a tarefa com o ID especificado.
- **Requer:** Token JWT



