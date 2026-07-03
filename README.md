# AWS Cloud Foundations (Hardening & Infrastructure)

Repositório dedicado aos meus estudos de infraestrutura em nuvem utilizando AWS.
O objetivo deste laboratório é aprender os principais serviços da AWS enquanto aplico boas práticas de arquitetura, segurança e hardening em ambientes reais de laboratório.

Ao longo dos estudos este repositório será atualizado com novos serviços, documentações, troubleshooting e aprendizados obtidos durante a construção da infraestrutura.

## Stack Tecnológica

- **Computação:** Amazon EC2 *(Até o momento)*
- **Armazenamento:** Amazon S3 *(em breve)*
- **Rede:** Amazon VPC, Subnets e Internet Gateway
- **Segurança:** Security Groups e IAM Roles
- **Sistema Operacional:** Amazon Linux 2023
- **Governança:** Lifecycle Policies e AWS Trusted Advisor

## 📁 1. Provisionamento de Perímetro e Hardening de Rede

### Contexto

Primeiro laboratório voltado para criação da infraestrutura base na AWS.
O foco foi aprender a criar uma instância EC2, configurar a rede utilizando VPC e Security Groups e entender como expor apenas os serviços necessários para acesso externo.

### Troubleshooting — Governança de Recursos

**Problema:** Durante o encerramento do ambiente não foi possível excluir uma instância EC2 pela API da AWS.

**Causa:** A proteção contra encerramento (*Termination Protection*) estava habilitada.

**Solução:** Identifiquei o atributo `disableApiTermination`, removi a proteção pelo console da AWS e validei novamente o processo de encerramento da instância.

<details>
<summary>📂 Evidências do laboratório</summary>

- Regras do Security Group: ![ec2-sg-inbound-http.png](docs/assets/ec2-sg-inbound-http.png)
- Resumo da Instância EC2: ![ec2-instance-summary-details.png](docs/assets/ec2-instance-summary-details.png)
- Console do Boot da EC2: ![ec2-instance-boot-console.png](docs/assets/ec2-instance-boot-console.png)
- Configuração da Proteção contra Encerramento: ![ec2-instance-termination-lifecycle.png](docs/assets/ec2-instance-termination-lifecycle.png)

</details>

## Observações

Este repositório marca o início da minha jornada em Cloud Computing.

Meu objetivo é documentar toda a evolução dos estudos, registrando laboratórios, dificuldades encontradas, troubleshooting e boas práticas aprendidas ao longo do caminho.

A ideia é transformar este repositório em uma documentação da minha evolução em AWS, Infraestrutura e Segurança.
