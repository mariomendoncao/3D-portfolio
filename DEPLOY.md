# Deploy no Coolify

Este projeto está configurado para deploy automático no Coolify usando GitHub Actions.

## Configuração Inicial

### 1. No Coolify

1. Crie uma nova aplicação no Coolify
2. Selecione "Docker" como tipo de aplicação
3. Conecte ao repositório GitHub
4. Configure o domínio (ex: `portfolio.mmendol.com`)
5. Vá em **Webhooks** e copie a URL do webhook
6. Copie também o token de autenticação (se necessário)

### 2. No GitHub

1. Vá em **Settings** → **Secrets and variables** → **Actions**
2. Adicione os seguintes secrets:
   - `COOLIFY_WEBHOOK_URL`: A URL do webhook do Coolify
   - `COOLIFY_TOKEN`: O token de autenticação (se necessário)

### 3. Deploy Automático

Agora, sempre que você fizer push para a branch `main`, o GitHub Actions irá:
1. Triggerar o webhook do Coolify
2. Coolify irá fazer pull do código
3. Buildar a imagem Docker
4. Fazer deploy da nova versão

## Deploy Manual

Se preferir fazer deploy manual:

```bash
# Build da imagem
docker build -t meu-portfolio .

# Testar localmente
docker run -p 8080:80 meu-portfolio

# Acesse http://localhost:8080
```

## Estrutura

- **Dockerfile**: Build multi-stage (Node.js → Nginx)
- **nginx.conf**: Configuração otimizada para SPA
- **.github/workflows/deploy.yml**: Pipeline de CI/CD
- **.dockerignore**: Arquivos excluídos do build

## Otimizações Incluídas

✅ Compressão gzip  
✅ Cache de assets estáticos (1 ano)  
✅ Security headers  
✅ SPA fallback (todas as rotas → index.html)  
✅ Health check endpoint (`/health`)  
✅ Imagem Docker otimizada (~25MB)
