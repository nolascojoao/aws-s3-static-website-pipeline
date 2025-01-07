# Pipeline Automático para Deploy de Site Estático no S3 com AWS

> **Nota**: Este é apenas um guia e, na prática, as opções podem variar dentro do console da AWS. No entanto, ele serve como uma orientação para configurar o pipeline de forma eficiente.

---

## Pré-requisitos

- **Repositório no GitHub** já criado: `aws-s3-static-website-pipeline` com o seu site estático.
- **Conta na AWS** com permissões para criar e usar serviços como CodePipeline, CodeBuild e S3.

---

## Passo 1: Criar o Bucket S3 para Hospedagem do Site

### 1.1. Acessar o Console AWS

- Vá para o **AWS Management Console** e faça login.

### 1.2. Criar o Bucket S3

- No console, pesquise por **S3** e entre no serviço.
- Clique em **Create bucket** e configure:
  - **Bucket Name**: `meu-bucket-estatico` (Escolha um nome único).
  - **Region**: Escolha a região mais próxima a você.
- Desative o **Bloqueio de Acesso Público** para permitir acesso público ao site.
- Clique em **Create**.

### 1.3. Configurar o Bucket para Hospedar um Site Estático

- Dentro do bucket, vá para a aba **Properties**.
- Em **Static website hosting**, clique em **Edit**.
- Marque a opção **Enable** e configure:
  - **Index document**: `index.html`.
  - **Error document**: (deixe vazio ou coloque `404.html`).
- Clique em **Save changes**.

---

## Passo 2: Criar o Projeto no CodeBuild para Validação do Site

### 2.1. Acessar o Console do CodeBuild

- No AWS Management Console, pesquise por **CodeBuild**.

### 2.2. Criar um Novo Projeto de Build

- Clique em **Create build project**.
- Preencha os campos:
  - **Project Name**: `ValidateStaticWebsite`.
  - **Source Provider**: GitHub.
  - Conecte sua conta GitHub (ou já estará conectado se a AWS e GitHub estiverem vinculados).
  - **Repository**: Selecione o repositório `aws-s3-static-website-pipeline`.
  - **Branch**: Escolha a branch principal (geralmente `main`).

### 2.3. Configuração do Ambiente

Em **Environment**, configure:

- **Build Environment**: Ubuntu Standard (Imagem do CodeBuild).
- **Compute Type**: Default.

### 2.4. Buildspec

Insira o seguinte conteúdo para validar a presença do `index.html`:

```yaml
version: 0.2
phases:
  build:
    commands:
      - echo "Validating website structure..."
      - test -f index.html && echo "index.html found."
artifacts:
  files:
    - '**/*'
```

## Passo 3: Criar o Pipeline no CodePipeline

### 3.1. Acessar o Console do CodePipeline

- No AWS Management Console, pesquise por **CodePipeline** e acesse o serviço.

### 3.2. Criar um Novo Pipeline

- Clique em **Create pipeline**.
- Configure o pipeline:
  - **Pipeline Name**: `StaticWebsitePipeline`.
  - **Service Role**: Selecione **New service role** para criar uma nova role automaticamente.
- Clique em **Next**.

---

## Passo 4: Configurar a Etapa de Source (Fonte) no CodePipeline

### 4.1. Configuração da Fonte

- **Source Provider**: GitHub.
- **Repository**: Escolha o repositório `aws-s3-static-website-pipeline`.
- **Branch**: Selecione a branch principal (geralmente `main`).
- **Change detection options**: Selecione **GitHub Webhook**.

Clique em **Next**.

---

## Passo 5: Configurar a Etapa de Build no CodePipeline

- **Build Provider**: Escolha **AWS CodeBuild**.
- **Project Name**: Selecione o projeto `ValidateStaticWebsite` criado no Passo 2.
- Clique em **Next**.

---

## Passo 6: Configurar a Etapa de Deploy no S3

- **Deploy Provider**: Escolha **Amazon S3**.
- **Bucket**: Selecione o bucket `meu-bucket-estatico` (criado no Passo 1).
- **Extract Artifacts**: Marque a opção **Extract artifacts**.
- Clique em **Next**.

---

## Passo 7: Finalizar a Criação do Pipeline

### 7.1. Revisão

- Verifique as configurações: **Fonte** (GitHub), **Build** (CodeBuild) e **Deploy** (S3).
- Clique em **Create pipeline**.

### 7.2. Pipeline em Ação

- O pipeline será criado e disparado automaticamente sempre que houver um novo commit no repositório GitHub.

---

## Passo 8: Testar o Deploy Automático

### 8.1. Fazer um Commit para Teste

Execute os comandos abaixo no seu terminal para fazer um commit de teste:

```bash
git add .
git commit -m "Atualização no site"
git push origin main
```

### 8.2. Verificar o Pipeline

- O CodePipeline será acionado automaticamente e executará o processo.

### 8.3. Verificar o Site

- Acesse o bucket S3 (via URL do S3) ou, se configurado, use o URL do CloudFront.

---

## Troubleshooting: Access Denied

Esse erro geralmente ocorre quando o bucket S3 está configurado para não permitir acesso público. A política de acesso precisa ser ajustada para permitir que os arquivos sejam acessados via URL pública.

### Passo 1: Ajustar as Configurações de Acesso Público no Bucket S3

1. Acesse o **Bucket S3**:
   - No AWS Management Console, vá até o serviço S3.
   - Selecione o bucket que você criou (por exemplo, `meu-bucket-estatico`).

2. **Permitir Acesso Público**:
   - Na aba **Permissions**, clique em **Bucket Policy**.
   - Adicione a seguinte política para garantir que os arquivos do site sejam públicos:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::meu-bucket/*"
    }
  ]
}
```

> **Importante**: Substitua `meu-bucket` pelo nome do seu bucket.

3. **Salvar a Política**.

### Passo 2: Verificar as Configurações de Bloqueio de Acesso Público

1. **Ajustar Configuração de Bloqueio**:
   - Ainda na aba **Permissions** do bucket, clique em **Block public access** (bucket settings).
   - Selecione **Edit** e desmarque as opções que bloqueiam o acesso público (se necessário).
   - Desmarque a opção **Block all public access**, se estiver ativada.

2. **Salvar as Alterações**.

### Passo 3: Testar o Acesso ao Site

Após salvar essas configurações, acesse a **URL pública** do seu bucket para verificar se o site agora está acessível. O link será algo como:

```text
http://meu-bucket.s3-website-us-east-1.amazonaws.com
