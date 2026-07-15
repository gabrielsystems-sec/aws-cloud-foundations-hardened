# AWS Cloud Foundations (Hardening & Infrastructure)

Repositório dedicado aos meus estudos de infraestrutura em nuvem utilizando AWS.
O objetivo deste laboratório é aprender os principais serviços da AWS enquanto aplico boas práticas de arquitetura, segurança e hardening em ambientes reais de laboratório.

Ao longo dos estudos este repositório será atualizado com novos serviços, documentações, troubleshooting e aprendizados obtidos durante a construção da infraestrutura.

## Stack Tecnológica

### Computação
- Amazon EC2

### Rede
- Amazon VPC
- Public e Private Subnets
- Route Tables
- Internet Gateway

### Segurança
- AWS Identity and Access Management (IAM)
- Security Groups
- AWS Systems Manager (Session Manager, Run Command, Fleet Manager e Parameter Store)

### Banco de Dados
- Amazon RDS

### Sistema Operacional
- Amazon Linux 2023

### Próximos Serviços
- Amazon S3

## Lab 01 - Provisionamento de Perímetro e Hardening de Rede

### Contexto
O foco foi aprender a criar uma instância EC2, configurar a rede utilizando VPC e Security Groups e entender como expor apenas os serviços necessários para acesso externo.

### Troubleshooting | Governança de Recursos
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

## Lab 02 - Arquitetura de Rede e Computação em Nuvem

### Contexto
O objetivo foi criar um ambiente de rede isolado e seguro, provisionando uma VPC, subnets públicas e privadas, e colocando uma instância EC2 para rodar um servidor web. O foco foi entender como os componentes de rede se conversam e garantem o acesso aos recursos.

### Troubleshooting | Validação de Fluxo de Rede
**Problema:** Durante a configuração do ambiente, não foi possível acessar a instância EC2 por meio do seu IP público.

**Causa:** O Route Table da Subnet Pública não estava associado corretamente ao Internet Gateway.

**Solução:** Ajustei a tabela de rotas adicionando a rota padrão `0.0.0.0/0` apontando para o Internet Gateway e verifiquei a associação da Subnet Pública com a tabela corrigida.

### Aprendizados

- Compreendi como Route Tables controlam o fluxo de saída das Subnets.
- Entendi que a existência de um Internet Gateway não é suficiente; a associação correta da Route Table também é necessária.
- Reforcei a relação entre VPC, Subnets e Internet Gateway na comunicação externa.

<details>
<summary>📂 Evidências do laboratório</summary>

- Planejamento da Arquitetura de Rede (VPC e Subnets): ![vpc-configuracao-sucesso-aws.png](docs/assets/vpc-configuracao-sucesso-aws.png)
- Workflow de Criação da Infraestrutura: ![vpc-criacao-sucesso-aws.png](docs/assets/vpc-criacao-sucesso-aws.png)
- Mapa Consolidado de Recursos (Resource Map): ![vpc-recursos-verificacao-lab.png](docs/assets/vpc-recursos-verificacao-lab.png)
- Instância EC2 em Operação (Status Check OK): ![ec2-instancia-running-websrv.png](docs/assets/ec2-instancia-running-websrv.png)

</details>

## Lab 03 - Gestão de Identidade e Acesso (IAM)

### Contexto
O objetivo deste laboratório foi aplicar princípios de Least Privilege (Menor Privilégio) e governança de identidades na AWS. Configurei uma política de senha alinhada às boas práticas da AWS para compreender o impacto das políticas de autenticação na segurança das contas. Também explorei a relação entre grupos, usuários e permissões via políticas JSON.

### Troubleshooting | Validação de Acesso e Permissões
**Problema:** Ao tentar realizar operações de gerenciamento em instâncias EC2, recebi repetidamente um erro de Access Denied.

**Causa:** O usuário estava associado a um grupo com políticas restritivas que não permitiam ações de escrita/modificação (apenas leitura). Além disso, identifiquei a ausência de MFA obrigatório, o que não está alinhado às boas práticas recomendadas pela AWS.

**Solução:** Analisei o JSON da política associada (3-iam-policy-ec2-admin-json.png), ajustei os níveis de permissão dentro do grupo e validei o ambiente novamente.

### Aprendizados

- Entendi como grupos simplificam a administração de permissões.
- Compreendi a diferença entre autenticação e autorização.
- Observei como políticas JSON definem ações permitidas sobre recursos específicos.

<details>
<summary>📂 Evidências do laboratório</summary>

- Configuração de Grupos IAM (EC2 e S3): ![1-iam-user-groups.png](docs/assets/1-iam-user-groups.png)
- Política de Senha Implementada: ![2-iam-password-policy.png](docs/assets/2-iam-password-policy.png)
- Análise da Política EC2 (JSON): ![3-iam-policy-ec2-admin-json.png](docs/assets/3-iam-policy-ec2-admin-json.png)
- Erro de Acesso Negado (Troubleshooting): ![4-ec2-access-denied-error.png](docs/assets/4-ec2-access-denied-error.png)
- Visão Geral dos Buckets S3: ![5-s3-buckets-overview.png](docs/assets/5-s3-buckets-overview.png)
- Verificação de Ausência de MFA: ![6-iam-user-mfa-missing.png](docs/assets/6-iam-user-mfa-missing.png)

</details>

## Lab 04 - Banco de Dados Relacional (RDS)

### Contexto
O objetivo deste laboratório foi implementar um banco de dados gerenciado em ambiente isolado, focando na configuração de subnets, grupos de segurança e conectividade segura para aplicações web.

### Troubleshooting | Validação de Conectividade
**Problema:** Inicialmente, a instância da aplicação não conseguia estabelecer conexão com o banco de dados RDS.

**Causa:** As regras de entrada (Inbound Rules) do Security Group do banco não estavam permitindo o tráfego na porta padrão do MySQL (3306) vindo do security group da aplicação.

**Solução:** Ajustei as regras do Security Group para autorizar especificamente o tráfego da camada de aplicação e validei a conectividade através dos endpoints.

### Aprendizados

- Entendi a função do DB Subnet Group.
- Compreendi como Security Groups controlam a comunicação entre aplicação e banco.
- Reforcei a importância do princípio do menor privilégio também para bancos de dados.

<details>
<summary>📂 Evidências do laboratório</summary>

- Configuração das Inbound Rules: ![vpc-security-group-inbound-rules-created.png](docs/assets/vpc-security-group-inbound-rules-created.png)
- Criação do Subnet Group: ![rds-subnet-group-complete.png](docs/assets/rds-subnet-group-complete.png)
- Instância RDS Ativa: ![rds-instance-status-active.png](docs/assets/rds-instance-status-active.png)
- Detalhes de Conectividade (Endpoint): ![rds-connectivity-endpoint-details.png](docs/assets/rds-connectivity-endpoint-details.png)
- Teste de Carga (CPU 100%): ![rds-load-test-100-cpu-active.png](docs/assets/rds-load-test-100-cpu-active.png)

</details>

## Lab 05 - Gerenciamento e Automação de Instâncias com AWS Systems Manager (SSM)

### Contexto
O objetivo deste laboratório foi conhecer os principais recursos do AWS Systems Manager (SSM) para administrar instâncias EC2 de forma centralizada. Durante o laboratório utilizei o Session Manager para acessar a instância sem SSH, executei comandos remotamente com o Run Command, explorei o Fleet Manager para visualizar informações da instância e utilizei o Parameter Store para armazenar configurações utilizadas pela aplicação.

### Troubleshooting | Acesso Seguro à Instância

**Problema:** Foi necessário acessar a instância EC2 para realizar verificações no sistema operacional sem utilizar conexões SSH.

**Causa:** O acesso tradicional via SSH exige a abertura da porta 22 no Security Group e o gerenciamento de chaves privadas, aumentando a complexidade administrativa.

**Solução:** Utilizei o Session Manager para estabelecer uma sessão diretamente pelo console da AWS. Dessa forma consegui executar comandos no sistema operacional, consultar informações da instância utilizando o IMDS e utilizar a AWS CLI sem necessidade de expor portas administrativas.

### Aprendizados

- Entendi como o Session Manager permite acessar instâncias EC2 sem utilizar conexões SSH.
- Compreendi como o Run Command automatiza a execução de comandos remotamente em instâncias gerenciadas.
- Observei como o Fleet Manager centraliza informações administrativas e facilita o gerenciamento da infraestrutura.
- Aprendi que o Parameter Store permite armazenar parâmetros utilizados pelas aplicações de forma centralizada.
- Reforcei como o AWS Systems Manager contribui para uma administração mais segura e organizada da infraestrutura.

<details>
<summary>📂 Evidências do Laboratório</summary>

- **Inventário de Aplicações (Fleet Manager):** ![fleetmanager-inventory-ec2.png](docs/assets/fleetmanager-inventory-ec2.png)
- **Deploy de Aplicação Remoto (Run Command):** ![runcommand-success-deploy.png](docs/assets/runcommand-success-deploy.png)
- **Criação do Parâmetro de Ambiente (Parameter Store):** ![parameterstore-created-beta.png](docs/assets/parameterstore-created-beta.png)
- **Dashboard Ativo com Feature Flag Habilitada:** ![dashboard-active-betafeature.png](docs/assets/dashboard-active-betafeature.png)
- **Sessão Interativa Segura e Consulta via CLI (Session Manager):** ![sessionmanager-cli-troubleshooting.png](docs/assets/sessionmanager-cli-troubleshooting.png)

</details>

## Observações

Este repositório reúne minha evolução prática em Cloud Computing utilizando AWS. Cada laboratório documenta o contexto da atividade, os problemas encontrados, a investigação realizada, a solução aplicada e os principais aprendizados obtidos durante o processo.

O objetivo é construir uma documentação contínua da minha jornada em infraestrutura, segurança e computação em nuvem, registrando não apenas os resultados, mas também o raciocínio desenvolvido ao longo dos estudos.
