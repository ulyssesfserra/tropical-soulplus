# Tropical Multi Loja API

**Versão:** 1.0.0  
**Desenvolvido por:** [Soul Plus Digital](https://soulplus.digital)  
**Cliente:** Tropical Multi Loja  
**Data:** Janeiro de 2026

---

## 📋 Descrição

API de geração de orçamentos para a Tropical Multi Loja. Esta aplicação permite processar listas de produtos e gerar orçamentos formatados em texto ou PDF.

### Funcionalidades

- ✅ Geração de orçamentos em formato texto (para WhatsApp/Chat)
- ✅ Geração de orçamentos em PDF com upload automático
- ✅ Consulta de descrições e preços de produtos na API CIM2
- ✅ Formatação automática de valores e datas
- ✅ Health check para monitoramento

---

## 📁 Estrutura do Projeto

```
tropical-multiloja-api/
├── config/
│   └── index.js            # Configurações centralizadas
├── src/
│   ├── controllers/
│   │   └── tropical.controller.js
│   ├── routes/
│   │   └── tropical.routes.js
│   ├── services/
│   │   └── tropical.service.js
│   └── app.js              # Arquivo principal
├── .env.example            # Exemplo de variáveis de ambiente
├── .gitignore
├── package.json
└── README.md
```

---

## 🚀 Guia de Instalação

### Pré-requisitos

- **Sistema Operacional:** Ubuntu 20.04+ ou similar
- **Node.js:** versão 18.x ou superior
- **npm:** versão 9.x ou superior
- **Acesso à internet:** para comunicação com APIs externas

### Passo 1: Preparar o Servidor

Conecte-se ao servidor via SSH:

```bash
ssh usuario@seu-servidor.com
```

Atualize o sistema:

```bash
sudo apt update && sudo apt upgrade -y
```

### Passo 2: Instalar o Node.js

Instale o Node.js via NodeSource:

```bash
# Instala o curl se necessário
sudo apt install -y curl

# Adiciona o repositório do Node.js 20.x
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -

# Instala o Node.js
sudo apt install -y nodejs

# Verifica a instalação
node --version
npm --version
```

### Passo 3: Criar Diretório da Aplicação

```bash
# Cria o diretório para a aplicação
sudo mkdir -p /opt/tropical-api

# Define as permissões
sudo chown -R $USER:$USER /opt/tropical-api

# Acessa o diretório
cd /opt/tropical-api
```

### Passo 4: Transferir os Arquivos

Transfira os arquivos da aplicação para o servidor. Você pode usar `scp`, `rsync` ou `git`:

**Opção A - Via SCP (do seu computador local):**
```bash
scp -r ./tropical-multiloja-api/* usuario@seu-servidor.com:/opt/tropical-api/
```

**Opção B - Via Git (se usar repositório):**
```bash
git clone https://seu-repositorio.git /opt/tropical-api
```

### Passo 5: Configurar Variáveis de Ambiente

```bash
# Cria o arquivo .env baseado no exemplo
cp .env.example .env

# Edita o arquivo de configuração
nano .env
```

Ajuste as configurações conforme necessário:

```env
# Porta do servidor
PORT=3000

# URL base da API Tropical (CIM2)
API_BASE_URL=https://cim2-app-tropical-multiloja.guidetti.com.br

# Credenciais de autenticação da API
AUTH_CLIENT_ID=cisspoder-oauth
AUTH_GRANT_TYPE=password
AUTH_CLIENT_SECRET=poder7547
AUTH_USERNAME=SOULPLUS
AUTH_PASSWORD=S0uL$25

# URL para upload de arquivos
UPLOAD_URL=https://tropical.soulplus.chat/int/uploadFile

# URL do logo da empresa
LOGO_URL=https://storage-files-api.soulplus.digital/logo-39-tropical.png

# ID da empresa para busca de preços
EMPRESA_ID=3

# ID da fila para upload
QUEUE_ID=32

# API Key para upload
UPLOAD_API_KEY=@soulplus-api

# Informações da empresa para o PDF
EMPRESA_NOME=Comercio de Miudezas Bandeira Ltda
EMPRESA_CNPJ=84.450.212/0001-60
EMPRESA_ENDERECO=Rua Saldanha Marinho 530 - Centro
EMPRESA_CIDADE=Manaus - AM
EMPRESA_CEP=CEP 69010-040
```

Salve e feche o arquivo (`Ctrl+X`, depois `Y`, depois `Enter`).

### Passo 6: Instalar Dependências

```bash
npm install
```

### Passo 7: Testar a Aplicação

```bash
# Executa em modo de desenvolvimento
npm run dev

# Ou executa em modo de produção
npm start
```

Você deve ver a seguinte mensagem:

```
╔═══════════════════════════════════════════════════════════╗
║                                                           ║
║          TROPICAL MULTI LOJA API v1.0.0                   ║
║          Desenvolvido por Soul Plus Digital               ║
║                                                           ║
╠═══════════════════════════════════════════════════════════╣
║  Servidor rodando em: http://localhost:3000              ║
║                                                           ║
║  Endpoints:                                               ║
║  - GET  /api/tropical/health                              ║
║  - POST /api/tropical/products                            ║
║  - POST /api/tropical/productsToPDF                       ║
║                                                           ║
╚═══════════════════════════════════════════════════════════╝
```

---

## ⚙️ Configurar como Serviço (Produção)

Para manter a aplicação rodando em segundo plano e iniciar automaticamente com o servidor:

### Passo 1: Instalar o PM2

```bash
sudo npm install -g pm2
```

### Passo 2: Iniciar a Aplicação com PM2

```bash
cd /opt/tropical-api
pm2 start src/app.js --name "tropical-api"
```

### Passo 3: Configurar Inicialização Automática

```bash
# Salva a configuração atual
pm2 save

# Configura para iniciar com o sistema
pm2 startup

# Execute o comando que aparecer na tela (começa com sudo)
```

### Comandos Úteis do PM2

```bash
# Ver status
pm2 status

# Ver logs
pm2 logs tropical-api

# Reiniciar
pm2 restart tropical-api

# Parar
pm2 stop tropical-api

# Remover
pm2 delete tropical-api
```

---

## 🌐 Configurar Nginx (Proxy Reverso)

Para expor a API através do Nginx:

### Passo 1: Instalar o Nginx

```bash
sudo apt install -y nginx
```

### Passo 2: Criar Configuração

```bash
sudo nano /etc/nginx/sites-available/tropical-api
```

Adicione o conteúdo:

```nginx
server {
    listen 80;
    server_name api.tropical.seudominio.com.br;

    location / {
        proxy_pass http://127.0.0.1:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }
}
```

### Passo 3: Ativar a Configuração

```bash
# Cria link simbólico
sudo ln -s /etc/nginx/sites-available/tropical-api /etc/nginx/sites-enabled/

# Testa a configuração
sudo nginx -t

# Reinicia o Nginx
sudo systemctl restart nginx
```

### Passo 4: Configurar SSL (Opcional, mas recomendado)

```bash
# Instala o Certbot
sudo apt install -y certbot python3-certbot-nginx

# Obtém certificado SSL
sudo certbot --nginx -d api.tropical.seudominio.com.br
```

---

## 📡 Endpoints da API

### Health Check

Verifica se a API está funcionando.

```
GET /api/tropical/health
```

**Resposta:**
```json
{
    "status": "ok",
    "service": "Tropical Multi Loja API",
    "version": "1.0.0",
    "timestamp": "2026-01-20T12:00:00.000Z"
}
```

---

### Gerar Orçamento em Texto

Processa a lista de produtos e retorna o orçamento formatado em texto.

```
POST /api/tropical/products
Content-Type: application/json
```

**Corpo da Requisição:**
```json
{
    "produtos": {
        "encontrados": [
            {
                "codigo": "123456",
                "quantidade": 2,
                "descricao": "Lápis Preto"
            },
            {
                "codigo": "789012",
                "quantidade": 5,
                "descricao": "Caderno 100 folhas"
            }
        ],
        "nao_encontrados": [
            {
                "descricao": "Borracha especial"
            }
        ]
    }
}
```

**Resposta:**
```json
{
    "message": "Perfeito! 🌺 Segue o orçamento:\n\nITENS ENCONTRADOS:\n1) Lápis Preto - Qtde 2\nCódigo: 123456 - LÁPIS GRAFITE HB - Preço: R$ 1,50 - Subtotal: R$ 3,00\n\n2) Caderno 100 folhas - Qtde 5\nCódigo: 789012 - CADERNO BROCHURA 100FLS - Preço: R$ 12,90 - Subtotal: R$ 64,50\n\nITENS NÃO ENCONTRADOS:\n- Borracha especial\n\nValor total: R$ 67,50\n\n*Data do orçamento: 20/01/2026*\n*Observação: esse orçamento tem validade de 2 dias.*\n\nSe deseja mais informações, nosso time de vendas está à disposição para concluir seu orçamento ou alterar itens.\n"
}
```

---

### Gerar Orçamento em PDF

Processa a lista de produtos, gera um PDF e retorna o ID do arquivo.

```
POST /api/tropical/productsToPDF
Content-Type: application/json
```

**Corpo da Requisição:**
```json
{
    "produtos": {
        "encontrados": [
            {
                "codigo": "123456",
                "quantidade": 2,
                "descricao": "Lápis Preto"
            }
        ],
        "nao_encontrados": []
    }
}
```

**Resposta:**
```json
{
    "fileId": "abc123xyz789"
}
```

---

## 🔧 Solução de Problemas

### A aplicação não inicia

1. Verifique se o Node.js está instalado corretamente:
   ```bash
   node --version
   ```

2. Verifique se as dependências foram instaladas:
   ```bash
   npm install
   ```

3. Verifique se o arquivo `.env` existe e está configurado:
   ```bash
   cat .env
   ```

### Erro de autenticação na API

Verifique se as credenciais no arquivo `.env` estão corretas:
- `AUTH_USERNAME`
- `AUTH_PASSWORD`
- `AUTH_CLIENT_SECRET`

### Erro ao gerar PDF

Verifique se a URL do logo está acessível:
```bash
curl -I https://storage-files-api.soulplus.digital/logo-39-tropical.png
```

### Erro ao fazer upload do PDF

Verifique se a URL de upload está correta e acessível:
- `UPLOAD_URL`
- `UPLOAD_API_KEY`
- `QUEUE_ID`

---

## 📞 Suporte

Em caso de dúvidas ou problemas, entre em contato com a Soul Plus Digital:

- **Website:** [https://soulplus.digital](https://soulplus.digital)
- **Email:** suporte@soulplus.digital

---

## 📜 Licença

Este software é proprietário e foi desenvolvido exclusivamente para a Tropical Multi Loja.

© 2026 Soul Plus Digital. Todos os direitos reservados.
