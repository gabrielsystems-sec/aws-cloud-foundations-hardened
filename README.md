# AWS Cloud Infrastructure Labs ☁️

Laboratórios práticos desenvolvidos durante a formação da Escola da Nuvem, documentando implementações, troubleshooting e boas práticas em serviços da AWS.

Ao longo deste repositório, cada laboratório registra não apenas a execução dos serviços, mas também os problemas encontrados, o processo de investigação e as soluções aplicadas durante a construção da infraestrutura.

## Objetivo

Consolidar conhecimentos em Cloud Computing por meio da implementação de serviços da AWS, documentando decisões técnicas, troubleshooting e boas práticas relacionadas à automação, segurança, alta disponibilidade e administração de infraestrutura.

## Tecnologias

- Amazon EC2
- Amazon VPC
- AWS IAM
- AWS Systems Manager
- Amazon Route 53
- Amazon SNS
- Amazon RDS
- Amazon S3
- AWS CLI
- Linux
- Bash

---

## Lab 01 - Provisionamento de Perímetro e Hardening de Rede

### Contexto
O foco foi aprender a criar uma instância EC2, configurar a rede utilizando VPC e Security Groups e entender como expor apenas os serviços necessários para acesso externo.

### Troubleshooting
**Problema:** Durante o encerramento do ambiente não foi possível excluir uma instância EC2 pela API da AWS.

**Causa:** A proteção contra encerramento (*Termination Protection*) estava habilitada.

**Resolução:** Identifiquei o atributo `disableApiTermination`, removi a proteção pelo console da AWS e validei novamente o processo de encerramento da instância.

<details>
<summary>📂 Evidências Técnicas </summary>

• Regras do Security Group: ![ec2-sg-inbound-http.png](docs/assets/ec2-sg-inbound-http.png)
• Resumo da Instância EC2: ![ec2-instance-summary-details.png](docs/assets/ec2-instance-summary-details.png)
• Console do Boot da EC2: ![ec2-instance-boot-console.png](docs/assets/ec2-instance-boot-console.png)
• Configuração da Proteção contra Encerramento: ![ec2-instance-termination-lifecycle.png](docs/assets/ec2-instance-termination-lifecycle.png)

</details>

---

## Lab 02 - Arquitetura de Rede e Computação em Nuvem

### Contexto
O objetivo foi criar um ambiente de rede isolado e seguro, provisionando uma VPC, subnets públicas e privadas, e colocando uma instância EC2 para rodar um servidor web. O foco foi entender como os componentes de rede se conversam e garantem o acesso aos recursos.

### Troubleshooting
**Problema:** Durante a configuração do ambiente, não foi possível acessar a instância EC2 por meio do seu IP público.

**Causa:** O Route Table da Subnet Pública não estava associado corretamente ao Internet Gateway.

**Resolução:** Ajustei a tabela de rotas adicionando a rota padrão `0.0.0.0/0` apontando para o Internet Gateway e verifiquei a associação da Subnet Pública com a tabela corrigida.

### Aprendizados

- Compreendi como Route Tables controlam o fluxo de saída das Subnets.
- Entendi que a existência de um Internet Gateway não é suficiente; a associação correta da Route Table também é necessária.
- Reforcei a relação entre VPC, Subnets e Internet Gateway na comunicação externa.

<details>
<summary>📂 Evidências Técnicas </summary>

• Planejamento da Arquitetura de Rede (VPC e Subnets): ![vpc-configuracao-sucesso-aws.png](docs/assets/vpc-configuracao-sucesso-aws.png)
• Workflow de Criação da Infraestrutura: ![vpc-criacao-sucesso-aws.png](docs/assets/vpc-criacao-sucesso-aws.png)
• Mapa Consolidado de Recursos (Resource Map): ![vpc-recursos-verificacao-lab.png](docs/assets/vpc-recursos-verificacao-lab.png)
• Instância EC2 em Operação (Status Check OK): ![ec2-instancia-running-websrv.png](docs/assets/ec2-instancia-running-websrv.png)

</details>

---

## Lab 03 - Gestão de Identidade e Acesso (IAM)

### Contexto
O objetivo deste laboratório foi aplicar princípios de Least Privilege (Menor Privilégio) e governança de identidades na AWS. Configurei uma política de senha alinhada às boas práticas da AWS para compreender o impacto das políticas de autenticação na segurança das contas. Também explorei a relação entre grupos, usuários e permissões via políticas JSON.

### Troubleshooting
**Problema:** Ao tentar realizar operações de gerenciamento em instâncias EC2, recebi repetidamente um erro de Access Denied.

**Causa:** O usuário estava associado a um grupo com políticas restritivas que não permitiam ações de escrita/modificação (apenas leitura). Além disso, identifiquei a ausência de MFA obrigatório, o que não está alinhado às boas práticas recomendadas pela AWS.

**Resolução:** Analisei o JSON da política associada (3-iam-policy-ec2-admin-json.png), ajustei os níveis de permissão dentro do grupo e validei o ambiente novamente.

### Aprendizados

- Entendi como grupos simplificam a administração de permissões.
- Compreendi a diferença entre autenticação e autorização.
- Observei como políticas JSON definem ações permitidas sobre recursos específicos.

<details>
<summary>📂 Evidências Técnicas </summary>

• Configuração de Grupos IAM (EC2 e S3): ![1-iam-user-groups.png](docs/assets/1-iam-user-groups.png)
• Política de Senha Implementada: ![2-iam-password-policy.png](docs/assets/2-iam-password-policy.png)
• Análise da Política EC2 (JSON): ![3-iam-policy-ec2-admin-json.png](docs/assets/3-iam-policy-ec2-admin-json.png)
• Erro de Acesso Negado (Troubleshooting): ![4-ec2-access-denied-error.png](docs/assets/4-ec2-access-denied-error.png)
• Visão Geral dos Buckets S3: ![5-s3-buckets-overview.png](docs/assets/5-s3-buckets-overview.png)
• Verificação de Ausência de MFA: ![6-iam-user-mfa-missing.png](docs/assets/6-iam-user-mfa-missing.png)

</details>

---

## Lab 04 - Banco de Dados Relacional (RDS)

### Contexto
O objetivo deste laboratório foi implementar um banco de dados gerenciado em ambiente isolado, focando na configuração de subnets, grupos de segurança e conectividade segura para aplicações web.

### Troubleshooting
**Problema:** Inicialmente, a instância da aplicação não conseguia estabelecer conexão com o banco de dados RDS.

**Causa:** As regras de entrada (Inbound Rules) do Security Group do banco não estavam permitindo o tráfego na porta padrão do MySQL (3306) vindo do security group da aplicação.

**Resolução:** Ajustei as regras do Security Group para autorizar especificamente o tráfego da camada de aplicação e validei a conectividade através dos endpoints.

### Aprendizados

- Entendi a função do DB Subnet Group.
- Compreendi como Security Groups controlam a comunicação entre aplicação e banco.
- Reforcei a importância do princípio do menor privilégio também para bancos de dados.

<details>
<summary>📂 Evidências Técnicas </summary>

• Configuração das Inbound Rules: ![vpc-security-group-inbound-rules-created.png](docs/assets/vpc-security-group-inbound-rules-created.png)
• Criação do Subnet Group: ![rds-subnet-group-complete.png](docs/assets/rds-subnet-group-complete.png)
• Instância RDS Ativa: ![rds-instance-status-active.png](docs/assets/rds-instance-status-active.png)
• Detalhes de Conectividade (Endpoint): ![rds-connectivity-endpoint-details.png](docs/assets/rds-connectivity-endpoint-details.png)
• Teste de Carga (CPU 100%): ![rds-load-test-100-cpu-active.png](docs/assets/rds-load-test-100-cpu-active.png)

</details>

---

## Lab 05 - Gerenciamento e Automação de Instâncias com AWS Systems Manager (SSM)

### Contexto
O objetivo deste laboratório foi conhecer os principais recursos do AWS Systems Manager (SSM) para administrar instâncias EC2 de forma centralizada. Durante o laboratório utilizei o Session Manager para acessar a instância sem SSH, executei comandos remotamente com o Run Command, explorei o Fleet Manager para visualizar informações da instância e utilizei o Parameter Store para armazenar configurações utilizadas pela aplicação.

### Troubleshooting

**Problema:** Foi necessário acessar a instância EC2 para realizar verificações no sistema operacional sem utilizar conexões SSH.

**Causa:** O acesso tradicional via SSH exige a abertura da porta 22 no Security Group e o gerenciamento de chaves privadas, aumentando a complexidade administrativa.

**Resolução:** Utilizei o Session Manager para estabelecer uma sessão diretamente pelo console da AWS. Dessa forma consegui executar comandos no sistema operacional, consultar informações da instância utilizando o IMDS e utilizar a AWS CLI sem necessidade de expor portas administrativas.

### Aprendizados

- Entendi como o Session Manager permite acessar instâncias EC2 sem utilizar conexões SSH.
- Compreendi como o Run Command automatiza a execução de comandos remotamente em instâncias gerenciadas.
- Observei como o Fleet Manager centraliza informações administrativas e facilita o gerenciamento da infraestrutura.
- Aprendi que o Parameter Store permite armazenar parâmetros utilizados pelas aplicações de forma centralizada.
- Reforcei como o AWS Systems Manager contribui para uma administração mais segura e organizada da infraestrutura.

<details>
<summary>📂 Evidências Técnicas </summary>

• **Inventário de Aplicações (Fleet Manager):** ![fleetmanager-inventory-ec2.png](docs/assets/fleetmanager-inventory-ec2.png)
• **Deploy de Aplicação Remoto (Run Command):** ![runcommand-success-deploy.png](docs/assets/runcommand-success-deploy.png)
• **Criação do Parâmetro de Ambiente (Parameter Store):** ![parameterstore-created-beta.png](docs/assets/parameterstore-created-beta.png)
• **Dashboard Ativo com Feature Flag Habilitada:** ![dashboard-active-betafeature.png](docs/assets/dashboard-active-betafeature.png)
• **Sessão Interativa Segura e Consulta via CLI (Session Manager):** ![sessionmanager-cli-troubleshooting.png](docs/assets/sessionmanager-cli-troubleshooting.png)

</details>

---

## Lab 06 - AWS CLI, IAM e Amazon S3

### Contexto
O objetivo deste laboratório foi administrar recursos da AWS utilizando a AWS CLI a partir de uma instância EC2. Durante as atividades, explorei a auditoria de políticas IAM, a criação de usuários, a análise de políticas JSON e a hospedagem de um site estático no Amazon S3.

Além disso, utilizei scripts de automação para realizar o deploy da aplicação, compreendendo como pequenas inconsistências na configuração podem impactar o processo de publicação.

### Troubleshooting
**Problema:** Ao executar o script de automação (`update-website.sh`) para realizar o deploy e upload de arquivos estáticos para o bucket S3, o terminal retornou um erro indicando que o diretório informado não existia. (`my-bucket: No such file or directory`).

**Causa:** O script inicial continha uma referência estática incorreta para o nome do bucket e caminhos relativos mal mapeados na estrutura de diretórios do ambiente (`sysops-activity-files`).

**Resolução:** Ajustei a estrutura de diretórios, criei a árvore de trabalho correta para os arquivos estáticos, ajustei o script de deploy (`update-website.sh`) apontando para o bucket correto (`gabriel-bucket-12345`) e validei o upload recursivo com sucesso.

### Aprendizados

- Compreendi como utilizar a AWS CLI para administrar recursos da AWS diretamente pelo terminal.
- Entendi como políticas IAM podem ser consultadas e analisadas por meio da AWS CLI.
- Pratiquei a criação e o gerenciamento de usuários utilizando comandos como `create-user` e `create-login-profile`.
- Aprendi como configurar um site estático utilizando o Amazon S3.
- Reforcei a importância da validação de scripts de automação antes da execução de deploys.

<details>
<summary>📂 Evidências Técnicas </summary>

• **Instalação e Validação da AWS CLI v2:** ![aws-cli-install-status.png](docs/assets/aws-cli-install-status.png)
• **Listagem e Auditoria de Políticas IAM:** ![aws-iam-policy-extraction.png](docs/assets/aws-iam-policy-extraction.png)
• **Análise da Estrutura JSON da Política IAM:** ![aws-iam-policy-validation.png](docs/assets/aws-iam-policy-validation.png)
• **Consulta de Políticas Gerenciadas da AWS para Amazon S3:** ![list-policies-success.png](docs/assets/list-policies-success.png)
• **Criação e Configuração do Bucket Amazon S3:** ![s3-bucket-overview.png](docs/assets/s3-bucket-overview.png)
• **Configuração do Static Website Hosting:** ![static-website-config-success.png](docs/assets/static-website-config-success.png)
• **Correção e Validação do Script de Deploy (`update-website.sh`):** ![update-script-fix-success.png](docs/assets/update-script-fix-success.png)


</details>

---

## Lab 07 - Deploy de Aplicação Web EC2 via AWS CLI e Troubleshooting de Infraestrutura

### Contexto
Provisionamento de infraestrutura web em ambiente AWS integrando VPC dedicada, automação de bootstrap via `UserData` e implantação via AWS CLI. O laboratório cobriu desde a liberação de regras de tráfego de rede até a depuração de chamadas de API e validação do serviço Apache (`httpd`).

### Troubleshooting

- **Falha de Parâmetro na CLI (`InvalidAMIID.Malformed`):** Ao automatizar o lançamento da instância via script, a chamada retornava erro devido à variável `$AMI` nula.

- **Resolução:** Mapeamento explícito da AMI válida e validação do script de inicialização (`UserData.txt`).

- **Bloqueio de Tráfego e Inicialização:** Ajuste das Inbound Rules do Security Group (`sg_web_access`) para liberar as portas 80 (HTTP) e 22 (SSH), combinado com a análise do *System Log* da instância para confirmar a execução limpa do Kernel e do `cloud-init`.

### Aprendizados
- Diagnóstico e correção de erros de sintaxe e variáveis em comandos da AWS CLI.
- Leitura de logs de diagnóstico de boot do SO sem necessidade de acesso SSH direto.
- Automação de deploy de servidor web via `UserData` e validação de entrega do serviço.

<details>
<summary>📂 Evidências Técnicas </summary>

• **Debug do Erro de AMI Nula na AWS CLI:** ![cli-provisioning-error-debug.png](docs/assets/cli-provisioning-error-debug.png)
• **Análise dos Logs de Boot do Sistema (System Log):** ![ec2-system-log-boot-success.png](docs/assets/ec2-system-log-boot-success.png)
• **Ajuste de Segurança (Inbound Rules do SG):** ![security-group-inbound-rules-fixed.png](docs/assets/security-group-inbound-rules-fixed.png)
• **Validação da Aplicação Web Ativa:** ![web-browser-custom-page-active.png](docs/assets/web-browser-custom-page-active.png)

</details>

---

## Lab 08 - Alta Disponibilidade e Resiliência com ALB, Auto Scaling e Troubleshooting de IAM/CLI

### Contexto
O objetivo deste laboratório foi implantar uma arquitetura de alta disponibilidade e resiliência na AWS utilizando Application Load Balancer (ALB) e Auto Scaling Group (ASG). Durante o laboratório, configurei a distribuição de tráfego entre instâncias EC2 rodando uma stack LAMP em múltiplas zonas de disponibilidade, validei o monitoramento de saúde no Target Group e resolvi falhas de execução via AWS CLI e permissões de papéis do IAM.

### Troubleshooting

**Problema:** Ocorreu uma falha ao criar a política de escala do Auto Scaling Group devido à ausência de permissão da Service-Linked Role, além de um erro de AMI inexistente (`InvalidAMIID.NotFound`) durante o lançamento da instância por script CLI.

**Causa:** O tempo de propagação do papel de serviço recém-criado no IAM impediu a associação imediata com o ASG, enquanto o script `create-lamp-instance-v2.sh` referenciava um ID de AMI inválido ou indisponível na região.

**Resolução:** Aguardei a propagação da Service-Linked Role no IAM para reexecutar a criação da política e utilizei o editor `vim` no terminal para corrigir o parâmetro de AMI no script, liberando o provisionamento correto da instância e a validação do serviço.

### Aprendizados

- Entendi como o Application Load Balancer (ALB) distribui o tráfego de entrada entre instâncias em diferentes zonas de disponibilidade.
- Compreendi como o Target Group realiza *Health Checks* na porta 80 para garantir o roteamento apenas para instâncias saudáveis.
- Observei como o tempo de propagação de papéis de serviço (*Service-Linked Roles*) no IAM pode impactar rotinas de automação.
- Aprendi a diagnosticar e depurar erros de parâmetros e AMIs em scripts de implantação via AWS CLI.
- Reforcei como a infraestrutura elástica com ALB e Auto Scaling garante alta disponibilidade e tolerância a falhas para aplicações web.

<details>
<summary>📂 Evidências Técnicas </summary>

• **Troubleshooting de AMI inexistente na AWS CLI:** ![awscli-troubleshooting-ami-nmap.png](docs/assets/awscli-troubleshooting-ami-nmap.png)
• **Troubleshooting de Service-Linked Role no Auto Scaling:** ![aws-autoscaling-error-service-linked-role.png](docs/assets/aws-autoscaling-error-service-linked_role.png)
• **Target Group com Instâncias em Estado Healthy:** ![aws-targetgroup-healthy-alb-instances.png](docs/assets/aws-targetgroup-healthy-alb-instances.png)
• **Validação de Acesso via DNS do Application Load Balancer (ALB):** ![aws-alb-success-loadtest-app.png](docs/assets/aws-alb-success-loadtest-app.png)

</details>

---

## Lab 09 - Automação via AWS CLI, Alta Disponibilidade Multi-AZ e Teste de Carga com Auto Scaling

### Contexto
O objetivo deste laboratório foi criar uma infraestrutura elástica e resiliente na AWS, combinando automação via AWS CLI para provisionamento de imagem base (AMI), distribuição de tráfego com Application Load Balancer (ALB) e elasticidade automática com Auto Scaling Group (ASG) em múltiplas zonas de disponibilidade (Multi-AZ).

### Troubleshooting

**Problema:** Necessidade de padronizar a instalação da stack web (Apache, PHP e módulo de stress) sem intervenção manual no console e validar se o Auto Scaling reage corretamente ao aumento de tráfego.

**Causa:** A criação manual de instâncias para geração de imagens padronizadas gera inconsistências no ambiente e o teste de escalabilidade exige a simulação real de uso intenso de CPU.

**Resolução:** Inspecionei e executei o script de inicialização (`UserData`) via terminal, criei a imagem base padronizada (`WebServerAMI`) com comandos encadeados da AWS CLI (`run-instances`, `wait` e `create-image`) e utilizei uma aplicação web de geração de carga (`stress=start`) para disparar os eventos de escalamento do Auto Scaling Group.

### Aprendizados

- Aprendi a encadear comandos da AWS CLI para criar instâncias, aguardar a inicialização e gerar AMIs customizadas via terminal.
- Entendi a importância do `UserData` na automação de instalação de dependências (HTTPD, PHP, Stress Test) no primeiro boot.
- Configurei a distribuição de alvos do Target Group entre diferentes Availability Zones (`us-west-2a` e `us-west-2b`) para garantir alta disponibilidade.
- Compreendi como o Listener do Application Load Balancer (ALB) faz o roteamento direto do tráfego de entrada HTTP:80 para os alvos saudáveis.
- Validei o ciclo de vida do Auto Scaling Group acompanhando o registro de eventos no *Activity History* durante o dimensionamento de instâncias.

<details>
<summary>📂 Evidências Técnicas </summary>

• **Inspeção do Script UserData e Metadados via Terminal:** ![aws-cli-userdata-script-inspection.png](docs/assets/aws-cli-userdata-script-inspection.png)
• **Provisionamento de Instância e Criação da AMI via AWS CLI:** ![aws-cli-create-ami-success.png](docs/assets/aws-cli-create-ami-success.png)
• **Distribuição Multi-AZ e Status Healthy no Target Group:** ![aws-targetgroup-multi-az-healthy.png](docs/assets/aws-targetgroup-multi-az-healthy.png)
• **Roteamento de Tráfego no Listener HTTP:80 do ALB:** ![aws-alb-listener-routing-success.png](docs/assets/aws-alb-listener-routing-success.png)
• **Histórico de Atividade e Escalamento Automático do ASG:** ![aws-autoscaling-activity-history-success.png](docs/assets/aws-autoscaling-activity-history-success.png)
• **Aplicação de Simulação de Carga (Stress Test):** ![aws-ec2-application-stress-test.png](docs/assets/aws-ec2-application-stress-test.png)

</details>

---

## Lab 10 - Disaster Recovery e Alta Disponibilidade com Amazon Route 53 DNS Failover e Amazon SNS

### Contexto
O objetivo deste laboratório foi implementar uma arquitetura de Disaster Recovery (DR) baseada em DNS utilizando o Amazon Route 53 com política de roteamento de *Failover* (Ativo/Passivo). Durante o laboratório, configurei registros DNS apontando para instâncias em diferentes Zonas de Disponibilidade, estabeleci monitoramento contínuo com Route 53 Health Checks e integrei alertas via Amazon SNS para redirecionar automaticamente o tráfego de um servidor primário indisponível para um servidor secundário de *standby*.

### Troubleshooting

**Problema:** Foi necessário garantir que o tráfego dos usuários fosse automaticamente redirecionado para um servidor de backup caso a aplicação primária sofresse uma queda de serviço ou indisponibilidade de rede.

**Causa:** Registros DNS estáticos do tipo `A` sem verificação de saúde continuam resolvendo o endereço IP do servidor primário mesmo quando ele para de responder, resultando em erros de *timeout* e indisponibilidade para os clientes.

**Resolução:** Criei uma política de roteamento em **Failover** no Route 53, associando o registro primário (`us-west-2a`) a um Route 53 Health Check e definindo o registro secundário (`us-west-2b`) como *standby*. Ao simular a indisponibilidade do servidor primário, o Health Check detectou o estado `Unhealthy`, disparando a alteração na resolução DNS para o IP do servidor secundário e garantindo a continuidade do serviço.

### Aprendizados

- Compreendi como funciona a política de roteamento em **Failover** (Ativo/Passivo) no Amazon Route 53 para resiliência de aplicações.
- Entendi como os **Route 53 Health Checks** monitoram *endpoints* HTTP (porta 80) e tomam decisões automáticas de comutação de tráfego.
- Aprendi a configurar notificações via **Amazon SNS** para alertar administradores por e-mail em cenários de degradação do ambiente.
- Observei na prática o tempo de convergência e propagação do DNS ao alterar o tráfego entre Zonas de Disponibilidade (*us-west-2a* para *us-west-2b*).
- Reforcei a importância de arquiteturas desacopladas e estratégias de Disaster Recovery para manter a alta disponibilidade do negócio.

<details>
<summary>📂 Evidências Técnicas </summary>

• **Linha de Base da Aplicação Primária (us-west-2a):** ![aws-ec2-primary-app-baseline-us-west-2a.png](docs/assets/aws-ec2-primary-app-baseline-us-west-2a.png)
• **Configuração dos Registros de Failover:** ![aws-route53-hostedzone-failover-records.png](docs/assets/aws-route53-hostedzone-failover-records.png)
• **Detecção de Indisponibilidade no Route 53 Health Check (Unhealthy):** ![aws-route53-healthcheck-unhealthy-detected.png](docs/assets/aws-route53-healthcheck-unhealthy-detected.png)
• **Comutação Automática do Roteamento DNS para o Servidor Secundário:** ![aws-route53-dns-failover-active.png](docs/assets/aws-route53-dns-failover-active.png)
• **Confirmação da Inscrição de Alerta de Saúde via Amazon SNS:** ![aws-sns-subscription-confirmation-email.png](docs/assets/aws-sns-subscription-confirmation-email.png)

</details>

---

## Observações

Este repositório reúne minha evolução prática em Cloud Computing utilizando AWS. Cada laboratório documenta o contexto da atividade, os problemas encontrados, a investigação realizada, a solução aplicada e os principais aprendizados obtidos durante o processo.

O objetivo é construir uma documentação contínua da minha jornada em infraestrutura, segurança e computação em nuvem, registrando não apenas os resultados, mas também o raciocínio desenvolvido ao longo dos estudos.
