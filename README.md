### **AWS S3 Static Website Pipeline**

This repository contains a **static website** created by [Designmodo Slides](https://designmodo.com/slides/) | [html-website-templates](https://github.com/designmodo/html-website-templates)

#### **Objective**
Study and automate the deployment of a static website to **Amazon S3** using **AWS CodePipeline**, which automatically deploys the site whenever there is a **push** to the GitHub repository.

#### **Architecture Pipeline**

```plaintext
[GitHub Repository] 
       |
       v
[AWS CodePipeline] ----> [AWS CodeBuild] ----> [Amazon S3 Bucket]
```

#### **How It Works**
- **AWS CodePipeline** monitors the GitHub repository for any changes (pushes).
- When a **push** is detected, **AWS CodePipeline** triggers **AWS CodeBuild** to build the website. The build process involves:
  - Copying the static website files (HTML, CSS, and any other assets) from the repository.
- After the build is successful, **AWS CodePipeline** automatically deploys the static website to **Amazon S3** for hosting.
- [Guide to Creating the Pipeline](setting-up-pipeline.md)

#### **Technologies Used**
- **AWS CodePipeline**
- **AWS CodeBuild**
- **Amazon S3**

#### **Website on S3**

You can view the deployed website at the following link:
[Website no S3](http://joaonolasco.s3-website-us-east-1.amazonaws.com/)

#### Build Logs in **AWS CodeBuild:**

<div align="center">
    <img src="https://github.com/user-attachments/assets/c1b47469-7609-4795-a0f4-9eed3d116e59" />
</div>

#### Execution History in **AWS CodePipeline:**

<div align="center">
    <img src="https://github.com/user-attachments/assets/bc05746a-9dbc-475b-a697-ee8ed601f7e1" />
</div>

<div align="center">
    <img src="https://github.com/user-attachments/assets/ac686ea6-f09b-4b24-8fce-6be69209c226" />
</div>

---
#### pt-br

Este repositório contém um **site estático** criado por [Designmodo Slides](https://designmodo.com/slides/) | [html-website-templates](https://github.com/designmodo/html-website-templates)

#### **Objetivo**
Estudar e automatizar o deploy de um site estático no **Amazon S3** usando **AWS CodePipeline**, que realiza o deploy automaticamente toda vez que há um **push** no repositório GitHub.

#### **Arquitetura do Pipeline**

```plaintext
[GitHub Repository] 
       |
       v
[AWS CodePipeline] ----> [AWS CodeBuild] ----> [Amazon S3 Bucket]
```

#### **Como Funciona**
- **AWS CodePipeline** monitora o repositório GitHub em busca de alterações (push).
- Quando um **push** é detectado, o **AWS CodePipeline** aciona o **AWS CodeBuild** para construir o site. O processo de construção envolve:
  - Copiar os arquivos estáticos do site (HTML, CSS e quaisquer outros assets) do repositório.
- Após a construção bem-sucedida, o **AWS CodePipeline** faz o **deploy automático** para o **Amazon S3** para hospedagem.
- [Guia para criação do pipeline](configurando-pipeline.md)

#### **Website no S3**

Você pode visualizar o site hospedado no seguinte link:
[Website no S3](http://joaonolasco.s3-website-us-east-1.amazonaws.com/)
