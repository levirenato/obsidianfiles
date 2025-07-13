Vamos usar React Native. para deixar o software escalável.

pastas:
```
/src
  /app
    /auth        → Login, Cadastro, Recuperar senha
    /patient     → Telas exclusivas do paciente
    /professional→ Telas exclusivas do profissional
    /shared      → Telas em comum
  /components    → Componentes genéricos reutilizáveis
  /hooks         → Custom Hooks (ex: useAuth, useProfile)
  /services      → API Clients, WebSocket, WebRTC
  /stores        → Zustand Stores (State Global)
  /utils         → Helpers
  /config        → Configurações gerais
/assets         → Imagens, ícones

```

### fluxo comum:
SplashScreen → LoginScreen → CadastroScreen → Escolha Perfil (Paciente ou Profissional)

### fluxo paciente:
Tabs:
- HomeScreen (Buscar profissionais)
- AgendamentosScreen (Histórico e futuros)
- ChatScreen (Mensagens)
- PerfilScreen (Dados pessoais, pagamentos)

Fluxos:
- Buscar → Ver Profissional → Agendar → Pagar → Consultar
- Chat → Consultar histórico

### fluxo profissional:
Tabs:
- DashboardScreen (Agenda do dia)
- ConsultasScreen (Histórico de atendimentos)
- ChatScreen (Mensagens)
- PerfilScreen (Configurações de agenda, preço, bio)

Fluxos:
- Ver Agenda → Iniciar Consulta Online (WebRTC)
- Chat com paciente
- Editar disponibilidade / preço
