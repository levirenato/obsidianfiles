
| Tipo de Dado                                                   | Banco de Dados  | Motivo                                                                              |
| -------------------------------------------------------------- | --------------- | ----------------------------------------------------------------------------------- |
| Dados transacionais (Usuários, Agendamento, Consultas)         | PostgreSQL      | Consistência forte, integridade relacional, fácil modelagem e relatórios.           |
| Chat e Mensagens em tempo real                                 | MongoDB         | Não relacional, dados não estruturados, flexível, performance para grandes volumes. |
| Sessões, Tokens, Cache                                         | Redis           | Extremamente rápido (in-memory), TTL nativo, pub/sub eficiente.                     |
| Armazenamento de Arquivos (Receitas, Imagens, Vídeos gravados) | AWS S3 ou MinIO | Escalável, redundante, storage barato.                                              |

### Banco Relacional (PostgreSQL)

#### Tabela: users

- id (UUID)
    
- name
    
- email
    
- password_hash
    
- phone
    
- role (enum: 'patient', 'professional', 'admin')
    
- created_at
    
- updated_at
    

#### Tabela: professionals_profile

- id (UUID)
    
- user_id (FK users)
    
- document_number (CRM, CREFITO, etc)
    
- bio
    
- price_per_consult
    
- specialities (array)
    
- available_hours (jsonb ou tabela separada)
    
- status (enum: 'pending', 'active', 'blocked')
    

#### Tabela: appointments

- id (UUID)
    
- patient_id (FK users)
    
- professional_id (FK users)
    
- status (enum: 'scheduled', 'done', 'cancelled', 'in_progress')
    
- scheduled_at (timestamp)
    
- type (enum: 'online', 'presential')
    
- price
    
- created_at
    
- updated_at
    

#### Tabela: evaluations

- id (UUID)
    
- appointment_id (FK appointments)
    
- rating (1-5)
    
- comment
    
- created_at


### Banco Não Relacional (MongoDB)
```
{
  "_id": ObjectId(),
  "room_id": "appointment-uuid",
  "participants": ["patient-uuid", "professional-uuid"],
  "messages": [
    {
      "sender_id": "uuid",
      "message": "texto da mensagem",
      "type": "text|image|file",
      "created_at": ISODate()
    }
  ]
}

```

### Redis

- Controle de sessões JWT / Tokens
    
- Cache de perfis populares
    
- Rate limit (anti-spam chat / login)
    
- Controle de video rooms em tempo real

### AWS S3 / MinIO

- Pasta: `/users/{user_id}/files/*`