vai ser feito em golang vamos usar o padrão DDD

o padrão de projeto vai ser esse:

/cmd
    main.go          → Start do app
/internal
    /api             → HTTP REST Handlers
    /ws              → WebSocket Handlers
    /webrtc          → Signaling Server + Media logic
    /domain
        /users (handle,service,repository,model)
        /appointments (handle,service,repository,model)
        /chat (handle,service,repository,model)
        /video (handle,service,repository,model)
/pkg
    /db              → Migrations / Conn Pool
    /cache           → Redis Client
    /storage         → S3 Client
    /utils           → Helpers gerais
/config
    config.yaml      → Configuração
/scripts
    Dockerfile, Makefile, Scripts de DevOps
