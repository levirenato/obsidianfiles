
### **Esquema Resumido do Sistema de PIT com gRPC**

---

#### **1. Funcionalidades Principais**

- **CRUD de PITs**:
    - **Cadastrar**: Registrar um novo PIT com semestre, docente e atividades.
    - **Editar**: Atualizar informações de um PIT existente.
    - **Excluir**: Remover um PIT.
- **Listagem em Tempo Real**:
    - Atualizar a lista de PITs sempre que houver alterações.
- **Tela de Informações da Equipe**:
    - Mostrar dados da equipe com base na base de dados do RAD.

---

#### **2. Arquitetura**

**Banco de Dados**

- Tabelas principais:
    - **Docente**: ID, nome, e-mail.
    - **Semestre**: ID, ano, período (ex.: 2024.1).
    - **PIT**: ID, docente_id, semestre_id, atividade, descrição.

**Protocolo gRPC (`pit.proto`)**

- Serviços:
    - **CreatePIT**: Criar novo PIT.
    - **UpdatePIT**: Editar um PIT.
    - **DeletePIT**: Remover um PIT.
    - **ListPITs**: Listar PITs do semestre com streaming contínuo.
    - **GetTeamInfo**: Obter dados da equipe.

**Backend**

- **Python** com **gRPC** para comunicação.
- **SQLAlchemy** para gerenciar o banco de dados.

**Frontend** (Opcional)

- **FastAPI** com WebSocket para exibir atualizações em tempo real.
- Alternativamente, um CLI simples pode consumir os serviços gRPC.

---

#### **3. Comunicação com gRPC**

- **Unary RPC**
    - Cadastro, edição e exclusão de PITs.
    - Requisição de dados da equipe.
- **Server-side Streaming**
    - Atualização contínua da lista de PITs.

---

#### **4. Exemplo de Fluxo**

1. **Cadastro de PIT**
    - Cliente → Servidor: Dados do PIT (docente, semestre, tipo de atividade).
    - Servidor: Salva no banco e retorna o ID.
2. **Listagem em Tempo Real**
    - Cliente: Abre uma conexão de streaming.
    - Servidor: Envia a lista inicial e atualizações em tempo real.
3. **Consultar Dados da Equipe**
    - Cliente → Servidor: Solicita dados da equipe.
    - Servidor: Retorna informações baseadas no banco do RAD.

---

#### **5. Próximos Passos**

1. Criar o **arquivo `.proto`** para os serviços e mensagens.
2. Configurar o **banco de dados** com as tabelas descritas.
3. Desenvolver o **servidor gRPC** com Python.
4. Implementar o **cliente** para interagir com o servidor.
5. Adicionar **streaming** para a listagem em real-time.