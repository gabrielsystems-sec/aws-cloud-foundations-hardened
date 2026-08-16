# AWS Cloud Infrastructure Labs ☁️

Repositório dedicado à construção de infraestrutura em nuvem utilizando serviços da AWS, com foco em implementação, troubleshooting, automação, segurança e alta disponibilidade.

Os laboratórios documentam a construção dos ambientes, os problemas encontrados durante a implementação e as soluções aplicadas para validar cada serviço e componente da infraestrutura.

## Objetivo | Cloud Infrastructure

Consolidar conhecimentos práticos em Cloud Computing por meio da implementação e administração de serviços AWS.

O foco está na construção de ambientes, automação de infraestrutura, configuração de redes, gestão de identidades, administração de sistemas, alta disponibilidade e troubleshooting de serviços em nuvem.

---

## Stack Tecnológica

- Compute: Amazon EC2 e Auto Scaling
- Networking: Amazon VPC, Security Groups e Route 53
- Identity: AWS IAM
- Management: AWS Systems Manager e AWS CLI
- Storage: Amazon S3
- Database: Amazon RDS
- Load Balancing: Application Load Balancer
- Monitoring & Notification: Route 53 Health Checks e Amazon SNS
- Operating Systems & Automation: Linux e Bash

---

## Lab 01 — Provisionamento de Perímetro e Hardening de Rede

### Contexto

Provisionamento de uma instância EC2 com configuração de VPC, Security Groups e exposição controlada dos serviços necessários para acesso externo.

### Troubleshooting

**Problema:** Durante o encerramento do ambiente, não foi possível excluir uma instância EC2 pela API da AWS.

**Causa:** A proteção contra encerramento (*Termination Protection*) estava habilitada.

**Resolução:** Identificação do atributo `disableApiTermination`, remoção da proteção e validação do encerramento da instância.

<details>
<summary>Evidências Técnicas</summary>

- Regras do Security Group: ![ec2-sg-inbound-http.png](docs/assets/ec2-sg-inbound-http.png)
- Resumo da Instância EC2: ![ec2-instance-summary-details.png](docs/assets/ec2-instance-summary-details.png)
- Console do Boot da EC2: ![ec2-instance-boot-console.png](docs/assets/ec2-instance-boot-console.png)
- Configuração da Proteção contra Encerramento: ![ec2-instance-termination-lifecycle.png](docs/assets/ec2-instance-termination-lifecycle.png)

</details>

---

## Lab 02 — Arquitetura de Rede e Computação em Nuvem

### Contexto

Construção de um ambiente de rede isolado utilizando VPC, subnets públicas e privadas, Internet Gateway, Route Tables e uma instância EC2 executando um servidor web.

### Troubleshooting

**Problema:** Não foi possível acessar a instância EC2 por meio do IP público.

**Causa:** A Route Table da subnet pública não estava configurada corretamente para encaminhar o tráfego ao Internet Gateway.

**Resolução:** Configuração da rota padrão `0.0.0.0/0` para o Internet Gateway e validação da associação da subnet com a Route Table.

### Aprendizados

- Controle do fluxo de tráfego por meio de Route Tables.
- Relação entre VPC, Subnets e Internet Gateway.
- Configuração necessária para conectividade externa de recursos públicos.

<details>
<summary>Evidências Técnicas</summary>

- Planejamento da Arquitetura de Rede: ![vpc-configuracao-sucesso-aws.png](docs/assets/vpc-configuracao-sucesso-aws.png)
- Workflow de Criação da Infraestrutura: ![vpc-criacao-sucesso-aws.png](docs/assets/vpc-criacao-sucesso-aws.png)
- Mapa Consolidado de Recursos: ![vpc-recursos-verificacao-lab.png](docs/assets/vpc-recursos-verificacao-lab.png)
- Instância EC2 em Operação: ![ec2-instancia-running-websrv.png](docs/assets/ec2-instancia-running-websrv.png)

</details>

---

## Lab 03 — Gestão de Identidade e Acesso com IAM

### Contexto

Implementação de controles de identidade e acesso utilizando usuários, grupos e políticas IAM, com foco em governança e aplicação do princípio do menor privilégio.

### Troubleshooting

**Problema:** Operações de gerenciamento em instâncias EC2 retornavam erros de `Access Denied`.

**Causa:** O usuário estava associado a políticas restritivas que não permitiam operações de escrita e modificação.

**Resolução:** Análise da política JSON associada, ajuste das permissões do grupo e nova validação do acesso aos recursos.

### Aprendizados

- Administração de permissões utilizando grupos.
- Diferença entre autenticação e autorização.
- Controle de acesso por meio de políticas JSON.
- Aplicação do princípio do menor privilégio.

<details>
<summary>Evidências Técnicas</summary>

- Configuração de Grupos IAM: ![1-iam-user-groups.png](docs/assets/1-iam-user-groups.png)
- Política de Senha: ![2-iam-password-policy.png](docs/assets/2-iam-password-policy.png)
- Análise de Política IAM: ![3-iam-policy-ec2-admin-json.png](docs/assets/3-iam-policy-ec2-admin-json.png)
- Erro de Acesso Negado: ![4-ec2-access-denied-error.png](docs/assets/4-ec2-access-denied-error.png)
- Buckets Amazon S3: ![5-s3-buckets-overview.png](docs/assets/5-s3-buckets-overview.png)
- Verificação de MFA: ![6-iam-user-mfa-missing.png](docs/assets/6-iam-user-mfa-missing.png)

</details>

---

## Lab 04 — Banco de Dados Relacional com Amazon RDS

### Contexto

Implementação de um banco de dados gerenciado em ambiente isolado, utilizando DB Subnet Groups, Security Groups e conectividade controlada entre aplicação e banco de dados.

### Troubleshooting

**Problema:** A instância da aplicação não conseguia estabelecer conexão com o banco RDS.

**Causa:** O Security Group do banco não permitia o tráfego na porta `3306` proveniente da camada de aplicação.

**Resolução:** Configuração de regras específicas entre os Security Groups e validação da conectividade utilizando o endpoint do banco.

### Aprendizados

- Função dos DB Subnet Groups.
- Controle de comunicação entre aplicação e banco.
- Aplicação do princípio do menor privilégio em regras de rede.

<details>
<summary>Evidências Técnicas</summary>

- Configuração das Inbound Rules: ![vpc-security-group-inbound-rules-created.png](docs/assets/vpc-security-group-inbound-rules-created.png)
- Criação do Subnet Group: ![rds-subnet-group-complete.png](docs/assets/rds-subnet-group-complete.png)
- Instância RDS Ativa: ![rds-instance-status-active.png](docs/assets/rds-instance-status-active.png)
- Endpoint de Conectividade: ![rds-connectivity-endpoint-details.png](docs/assets/rds-connectivity-endpoint-details.png)
- Teste de Carga: ![rds-load-test-100-cpu-active.png](docs/assets/rds-load-test-100-cpu-active.png)

</details>

---

## Lab 05 — Gerenciamento e Automação com AWS Systems Manager

### Contexto

Administração centralizada de instâncias EC2 utilizando Session Manager, Run Command, Fleet Manager e Parameter Store, reduzindo a dependência de acesso administrativo via SSH.

### Troubleshooting

**Problema:** Era necessário acessar e administrar a instância sem utilizar conexões SSH.

**Causa:** O acesso tradicional exige exposição da porta `22` e gerenciamento de chaves privadas.

**Resolução:** Utilização do Session Manager para estabelecer uma sessão administrativa sem expor portas de acesso remoto.

### Aprendizados

- Acesso administrativo sem SSH.
- Execução remota de comandos com Run Command.
- Gerenciamento centralizado com Fleet Manager.
- Armazenamento de parâmetros com Parameter Store.
- Redução da superfície de exposição administrativa.

<details>
<summary>Evidências Técnicas</summary>

- Inventário de Aplicações: ![fleetmanager-inventory-ec2.png](docs/assets/fleetmanager-inventory-ec2.png)
- Deploy Remoto: ![runcommand-success-deploy.png](docs/assets/runcommand-success-deploy.png)
- Parameter Store: ![parameterstore-created-beta.png](docs/assets/parameterstore-created-beta.png)
- Dashboard com Feature Flag: ![dashboard-active-betafeature.png](docs/assets/dashboard-active-betafeature.png)
- Session Manager e AWS CLI: ![sessionmanager-cli-troubleshooting.png](docs/assets/sessionmanager-cli-troubleshooting.png)

</details>

---

## Lab 06 — AWS CLI, IAM e Amazon S3

### Contexto

Administração de recursos AWS por meio da AWS CLI, incluindo auditoria de políticas IAM, gerenciamento de usuários e hospedagem de aplicações estáticas no Amazon S3.

### Troubleshooting

**Problema:** O script `update-website.sh` retornava um erro indicando que o diretório ou bucket informado não existia.

**Causa:** Referência incorreta ao bucket e caminhos relativos mal configurados na estrutura de diretórios.

**Resolução:** Correção da árvore de diretórios, ajuste do script de deploy e validação do upload recursivo dos arquivos para o bucket.

### Aprendizados

- Administração de recursos com AWS CLI.
- Auditoria e análise de políticas IAM.
- Criação e gerenciamento de usuários.
- Hospedagem de aplicações estáticas no Amazon S3.
- Validação de scripts antes da execução de deploys.

<details>
<summary>Evidências Técnicas</summary>

- Instalação da AWS CLI: ![aws-cli-install-status.png](docs/assets/aws-cli-install-status.png)
- Auditoria de Políticas IAM: ![aws-iam-policy-extraction.png](docs/assets/aws-iam-policy-extraction.png)
- Análise da Política JSON: ![aws-iam-policy-validation.png](docs/assets/aws-iam-policy-validation.png)
- Políticas Gerenciadas para S3: ![list-policies-success.png](docs/assets/list-policies-success.png)
- Configuração do Bucket: ![s3-bucket-overview.png](docs/assets/s3-bucket-overview.png)
- Static Website Hosting: ![static-website-config-success.png](docs/assets/static-website-config-success.png)
- Correção do Script de Deploy: ![update-script-fix-success.png](docs/assets/update-script-fix-success.png)

</details>

---

## Lab 07 — Deploy de Aplicação Web via AWS CLI

### Contexto

Provisionamento de infraestrutura web utilizando VPC, automação de bootstrap com `UserData` e implantação de uma aplicação por meio da AWS CLI.

### Troubleshooting

**Problema:** O provisionamento da instância retornava o erro `InvalidAMIID.Malformed`.

**Causa:** A variável `$AMI` utilizada pelo script estava vazia.

**Resolução:** Mapeamento explícito de uma AMI válida e validação do script de inicialização.

Também foram ajustadas as regras do Security Group para permitir o tráfego HTTP e analisados os logs de boot para validar a execução do `cloud-init`.

### Aprendizados

- Diagnóstico de variáveis e parâmetros na AWS CLI.
- Análise de logs de boot.
- Automação de servidores web com `UserData`.
- Validação de infraestrutura sem dependência exclusiva de SSH.

<details>
<summary>Evidências Técnicas</summary>

- Debug da AMI: ![cli-provisioning-error-debug.png](docs/assets/cli-provisioning-error-debug.png)
- Logs de Boot: ![ec2-system-log-boot-success.png](docs/assets/ec2-system-log-boot-success.png)
- Ajuste do Security Group: ![security-group-inbound-rules-fixed.png](docs/assets/security-group-inbound-rules-fixed.png)
- Aplicação Web Ativa: ![web-browser-custom-page-active.png](docs/assets/web-browser-custom-page-active.png)

</details>

---

## Lab 08 — Alta Disponibilidade com ALB e Auto Scaling

### Contexto

Implementação de uma arquitetura de alta disponibilidade utilizando Application Load Balancer e Auto Scaling Group, distribuindo o tráfego entre instâncias em múltiplas zonas de disponibilidade.

### Troubleshooting

**Problema:** A criação da política de escalabilidade falhava devido a permissões da Service-Linked Role, enquanto o provisionamento via script retornava `InvalidAMIID.NotFound`.

**Causa:** O papel de serviço ainda estava em propagação e o script referenciava uma AMI inválida para a região.

**Resolução:** Validação da propagação da Service-Linked Role, correção da AMI no script e nova execução do provisionamento.

### Aprendizados

- Distribuição de tráfego com Application Load Balancer.
- Health Checks e Target Groups.
- Propagação de Service-Linked Roles.
- Troubleshooting de AMIs e automação via AWS CLI.
- Alta disponibilidade com Auto Scaling.

<details>
<summary>Evidências Técnicas</summary>

- Erro de AMI: ![awscli-troubleshooting-ami-nmap.png](docs/assets/awscli-troubleshooting-ami-nmap.png)
- Erro de Service-Linked Role: ![aws-autoscaling-error-service-linked-role.png](docs/assets/aws-autoscaling-error-service-linked_role.png)
- Target Group Healthy: ![aws-targetgroup-healthy-alb-instances.png](docs/assets/aws-targetgroup-healthy-alb-instances.png)
- Aplicação via ALB: ![aws-alb-success-loadtest-app.png](docs/assets/aws-alb-success-loadtest-app.png)

</details>

---

## Lab 09 — Automação, Multi-AZ e Teste de Carga

### Contexto

Construção de uma infraestrutura elástica utilizando AWS CLI, AMI customizada, Application Load Balancer e Auto Scaling Group distribuído em múltiplas zonas de disponibilidade.

### Troubleshooting

**Problema:** Era necessário padronizar a instalação da stack web sem intervenção manual e validar o comportamento do Auto Scaling diante de aumento de carga.

**Causa:** A criação manual de servidores gera inconsistências, enquanto a validação do escalamento exige uma carga real sobre a infraestrutura.

**Resolução:** Automação da configuração com `UserData`, criação de uma AMI padronizada via AWS CLI e geração de carga para disparar eventos de escalamento.

### Aprendizados

- Criação de AMIs utilizando AWS CLI.
- Automação de configuração com `UserData`.
- Distribuição Multi-AZ.
- Roteamento com ALB.
- Validação do ciclo de escalamento do Auto Scaling Group.

<details>
<summary>Evidências Técnicas</summary>

- Inspeção do UserData: ![aws-cli-userdata-script-inspection.png](docs/assets/aws-cli-userdata-script-inspection.png)
- Criação da AMI: ![aws-cli-create-ami-success.png](docs/assets/aws-cli-create-ami-success.png)
- Distribuição Multi-AZ: ![aws-targetgroup-multi-az-healthy.png](docs/assets/aws-targetgroup-multi-az-healthy.png)
- Listener do ALB: ![aws-alb-listener-routing-success.png](docs/assets/aws-alb-listener-routing-success.png)
- Histórico do Auto Scaling: ![aws-autoscaling-activity-history-success.png](docs/assets/aws-autoscaling-activity-history-success.png)
- Simulação de Carga: ![aws-ec2-application-stress-test.png](docs/assets/aws-ec2-application-stress-test.png)

</details>

---

## Lab 10 — Disaster Recovery com Route 53 DNS Failover

### Contexto

Implementação de uma estratégia de Disaster Recovery baseada em DNS utilizando Route 53, Health Checks e Amazon SNS para realizar a comutação automática entre ambientes primário e secundário.

### Troubleshooting

**Problema:** Era necessário redirecionar automaticamente o tráfego caso a aplicação primária se tornasse indisponível.

**Causa:** Registros DNS estáticos continuavam resolvendo o endereço do servidor primário mesmo após uma falha.

**Resolução:** Configuração de uma política de Failover com Health Check no ambiente primário e um servidor secundário em standby. Durante a simulação da falha, o Route 53 identificou o estado `Unhealthy` e direcionou a resolução DNS para o ambiente secundário.

### Aprendizados

- Failover Ativo/Passivo com Route 53.
- Monitoramento de endpoints com Health Checks.
- Notificações de eventos com Amazon SNS.
- Comportamento e propagação do DNS.
- Estratégias de Disaster Recovery e continuidade de serviços.

<details>
<summary>Evidências Técnicas</summary>

- Aplicação Primária: ![aws-ec2-primary-app-baseline-us-west-2a.png](docs/assets/aws-ec2-primary-app-baseline-us-west-2a.png)
- Registros de Failover: ![aws-route53-hostedzone-failover-records.png](docs/assets/aws-route53-hostedzone-failover-records.png)
- Health Check Unhealthy: ![aws-route53-healthcheck-unhealthy-detected.png](docs/assets/aws-route53-healthcheck-unhealthy-detected.png)
- Failover para Ambiente Secundário: ![aws-route53-dns-failover-active.png](docs/assets/aws-route53-dns-failover-active.png)
- Notificação via SNS: ![aws-sns-subscription-confirmation-email.png](docs/assets/aws-sns-subscription-confirmation-email.png)

</details>

---

## Tecnologias e Conceitos Aplicados

`Amazon EC2` · `Amazon VPC` · `AWS IAM` · `AWS CLI` · `AWS Systems Manager` · `Amazon S3` · `Amazon RDS` · `Application Load Balancer` · `Auto Scaling` · `Route 53` · `Amazon SNS` · `Disaster Recovery` · `Multi-AZ` · `Linux` · `Bash`

---

## Principais Aprendizados

Este repositório foi desenvolvido com foco na implementação prática de infraestrutura em nuvem e na resolução de problemas encontrados durante a construção dos ambientes.

Os laboratórios documentam troubleshooting de redes, permissões IAM, Security Groups, AMIs, automação via AWS CLI, conectividade entre serviços, alta disponibilidade, escalamento automático e estratégias de Disaster Recovery.

O resultado é uma coleção prática de laboratórios que reúne provisionamento, administração, automação, segurança e resiliência de infraestrutura em ambientes AWS.
