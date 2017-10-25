# Visão geral
## [O que são serviços de nuvem?](cloud-services-choose-me.md)
## [Arquivos de configuração e empacotamento do serviço de nuvem](cloud-services-model-and-package.md)

# Introdução
## [Exemplo de Serviço de Nuvem .NET](cloud-services-dotnet-get-started.md)
## [Exemplo de Serviço de Nuvem do Python para o Visual Studio](cloud-services-python-ptvs.md)
## [Configurar um cluster HPC híbrido com o Microsoft HPC Pack](cloud-services-setup-hybrid-hpcpack-cluster.md)

# Como
## Plano
### [Tamanhos de máquina virtual](cloud-services-sizes-specs.md)
### [Atualizações](cloud-services-update-azure-service.md)

## Desenvolver
### [Criar funções Web e de trabalho do PHP](../cloud-services-php-create-web-role.md)
### [Compilar e implantar um aplicativo Node.js](cloud-services-nodejs-develop-deploy-app.md)
### [Criar um aplicativo Web do Node.js usando o Express](cloud-services-nodejs-develop-deploy-express-app.md)
### Armazenamento e Visual Studio
#### [Armazenamento de blobs e serviços conectados](../visual-studio/vs-storage-cloud-services-getting-started-blobs.md)
#### [Armazenamento em fila e serviços conectados](../visual-studio/vs-storage-cloud-services-getting-started-queues.md)
#### [Armazenamento de tabelas e serviços conectados](../visual-studio/vs-storage-cloud-services-getting-started-tables.md)
### Configurar pacotes para compilação e implantação contínuas
#### [TFS e o Team Build](cloud-services-dotnet-continuous-delivery.md)
### [Configurar regras de tráfego para uma função](cloud-services-enable-communication-role-instances.md)
### [Manipular eventos de ciclo de vida do Serviço de Nuvem](cloud-services-role-lifecycle-dotnet.md)
### [Socket.io (Node.js)](cloud-services-nodejs-chat-app-socketio.md)
### [Usar o Twilio para fazer uma chamada telefônica (.NET)](../partner-twilio-cloud-services-dotnet-phone-call-web-role.md)
### [New Relic](../store-new-relic-cloud-services-dotnet-application-performance-management.md)

### Configurar tarefas de inicialização
#### [Criar tarefas de inicialização](cloud-services-startup-tasks.md)
#### [Tarefas de inicialização comuns](cloud-services-startup-tasks-common.md)
#### [Usar uma tarefa para instalar .NET em uma função de Serviço de Nuvem](cloud-services-dotnet-install-dotnet.md)

### Configurar a Área de Trabalho Remota
#### [Portal do Azure](cloud-services-role-enable-remote-desktop-new-portal.md)
#### [Portal clássico](cloud-services-role-enable-remote-desktop.md)
#### [PowerShell do Azure](cloud-services-role-enable-remote-desktop-powershell.md)

## Implantar
### Criar e implantar um serviço de nuvem no portal
#### [Portal do Azure](cloud-services-how-to-create-deploy-portal.md)
#### [Portal clássico](cloud-services-how-to-create-deploy.md)
### [Criar um contêiner de serviço de nuvem vazio no PowerShell](cloud-services-powershell-create-cloud-container.md)
### Configurar um nome de domínio personalizado
#### [Portal](cloud-services-custom-domain-name-portal.md)
#### [Portal clássico](cloud-services-custom-domain-name.md)
### [Preparar uma implantação de serviço de nuvem (Node.js)](cloud-services-nodejs-stage-application.md)
### [Conectar a um Controlador de Domínio personalizado](cloud-services-connect-to-custom-domain.md)

## Gerenciar serviço
### Tarefas comuns de gerenciamento
#### [Portal do Azure](cloud-services-how-to-manage-portal.md)
#### [Portal clássico](cloud-services-how-to-manage.md)
### Configurar serviço de nuvem
#### [Portal do Azure](cloud-services-how-to-configure-portal.md)
#### [Portal clássico](cloud-services-how-to-configure.md)
### [Gerenciar um Serviço de Nuvem usando Automação do Azure](automation-manage-cloud-services.md)
### Configurar o dimensionamento automático
#### [Portal do Azure](cloud-services-how-to-scale-portal.md)
#### [Portal clássico](cloud-services-how-to-scale.md)
### [Usar Python para gerenciar recursos do Azure](cloud-services-python-how-to-use-service-management.md)

### [Patches do SO convidado](cloud-services-guestos-msrc-releases.md)
### Desativação do SO convidado
#### [Política de desativação](cloud-services-guestos-retirement-policy.md)
#### [Aviso de desativação da Família 1](cloud-services-guestos-family1-retirement.md)
### [Notícias sobre o lançamento do SO convidado](cloud-services-guestos-update-matrix.md)
### [Roteiro do XPath de configuração da função de Serviços de Nuvem](cloud-services-role-config-xpath.md)

## Gerenciar certificados
### [Serviços de Nuvem e certificados de gerenciamento](cloud-services-certs-create.md)
### Configurar SSL 
#### [Portal](cloud-services-configure-ssl-certificate-portal.md)
#### [Portal clássico](cloud-services-configure-ssl-certificate.md)

## Monitoramento
### [Monitorar serviço de nuvem](cloud-services-how-to-monitor.md)
### [Desempenho de testes](../vs-azure-tools-performance-profiling-cloud-services.md)
#### [Teste com o Criador de Perfil do Visual Studio](cloud-services-performance-testing-visual-studio-profiler.md)
### Habilitar diagnósticos
#### [PowerShell do Azure](cloud-services-diagnostics-powershell.md)
#### [.NET](cloud-services-dotnet-diagnostics.md)
#### [Visual Studio](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
### [Usar contadores de desempenho no Diagnóstico do Azure](cloud-services-dotnet-diagnostics-performance-counters.md)
### [Armazenar e exibir dados de diagnóstico no Armazenamento do Azure](cloud-services-dotnet-diagnostics-storage.md)
### [Rastrear um Serviço de Nuvem com o Diagnóstico](cloud-services-dotnet-diagnostics-trace-flow.md)
### [Enviar dados de diagnóstico para o App Insights](cloud-services-dotnet-diagnostics-applicationinsights.md)

## Solucionar problemas
### Depurar 
#### [Habilitar a depuração remota com entrega contínua](cloud-services-virtual-machines-dotnet-continuous-delivery-remote-debugging.md)
#### [Opções para um Serviço de Nuvem](../vs-azure-tools-debugging-cloud-services-overview.md)
#### [Serviço de Nuvem local com o Visual Studio](../vs-azure-tools-debug-cloud-services-virtual-machines.md)
#### [Serviço de Nuvem publicado com o Visual Studio](../vs-azure-tools-intellitrace-debug-published-cloud-services.md)
### [Falha na alocação do Serviço de Nuvem](cloud-services-allocation-failures.md)
### [Causas comuns de reciclagem de funções do Serviço de Nuvem](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md)
### [O tamanho da pasta TEMP padrão é muito pequeno para uma função](cloud-services-troubleshoot-default-temp-folder-size-too-small-web-worker-role.md)
### [Problemas de implantação comuns](cloud-services-troubleshoot-deployment-problems.md)
### [Falha ao iniciar função](cloud-services-troubleshoot-roles-that-fail-start.md)
### [Diretrizes de recuperação](cloud-services-disaster-recovery-guidance.md)
### Perguntas frequentes sobre Serviços de Nuvem
#### [Perguntas frequentes de disponibilidade de aplicativos e serviços](cloud-services-application-and-service-availability-faq.md)
#### [Perguntas frequentes de configuração e gerenciamento](cloud-services-configuration-and-management-faq.md)
#### [Perguntas frequentes sobre rede e conectividade](cloud-services-connectivity-and-networking-faq.md)
#### [Perguntas frequentes sobre implantação](cloud-services-deployment-faq.md)

# Referência
## [Exemplos de código](https://azure.microsoft.com/en-us/resources/samples/?service=cloud-services)
## [Esquema XML .csdef](schema-csdef-file.md)
### [Esquema LoadBalancerProbe](schema-csdef-loadbalancerprobe.md)
### [Esquema WebRole](schema-csdef-webrole.md)
### [Esquema WorkerRole](schema-csdef-workerrole.md)
### [Esquema NetworkTrafficRules](schema-csdef-networktrafficrules.md)
## [Esquema XML .cscfg](schema-cscfg-file.md)
### [Esquema de Função](schema-cscfg-role.md)
### [Esquema NetworkConfiguration](schema-cscfg-networkconfiguration.md)
## [REST](https://msdn.microsoft.com/library/azure/ee460812)

# Recursos
## [Roteiro do Azure](https://azure.microsoft.com/roadmap/?category=compute)
## [Roteiro de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/cloud-services/)
## [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/en-us/home?forum=windowsazuredevelopment)
## [Preços](https://azure.microsoft.com/pricing/details/cloud-services/)
## [Calculadora de preço](https://azure.microsoft.com/pricing/calculator/)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=cloud-services&updatetype=&platform=)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=cloud-services)
