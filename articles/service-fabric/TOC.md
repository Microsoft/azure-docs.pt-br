# [Documentação do Service Fabric](/azure/service-fabric)
# Visão geral
## [O que é o Service Fabric?](service-fabric-overview.md)

# Guia de início rápido
## [Criar um aplicativo .NET](service-fabric-quickstart-dotnet.md)
## [Implantar um aplicativo de contêiner do Linux](service-fabric-quickstart-containers-linux.md)
## [Implantar um aplicativo de contêiner do Windows](service-fabric-quickstart-containers.md)

# Tutoriais
## Implantar um aplicativo .NET
### [1-Criar um aplicativo .NET](service-fabric-tutorial-create-dotnet-app.md)
### [2-Implantar o aplicativo](service-fabric-tutorial-deploy-app-to-party-cluster.md)
### [3-Configurar CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
### [4- Monitorar e Diagnosticar](service-fabric-tutorial-monitoring-aspnet.md)

## Colocar um aplicativo .NET existente em um contêiner
### [1- Criar um cluster seguro no Azure](service-fabric-tutorial-create-cluster-azure-ps.md)
### [2- Implantar um aplicativo .NET usando o Docker Compose](service-fabric-host-app-in-a-container.md)
### [3 – Monitorar seu contêiner](service-fabric-tutorial-monitoring-wincontainers.md)

## Criar um aplicativo de contêiner do Linux
### [1 – Criar imagens de contêiner](service-fabric-tutorial-create-container-images.md)
### [2 – Empacotar e implantar contêineres](service-fabric-tutorial-package-containers.md)
### [3 – Fazer failover e dimensionar](service-fabric-tutorial-containers-failover.md)

## Criar e gerenciar um cluster
### 1 – Criar um cluster no Azure
#### [1a – Crie um cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
#### [1b – Crie um cluster do Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
### [2– Implantar o Gerenciamento de API com o Service Fabric](service-fabric-tutorial-deploy-api-management.md)

# Exemplos
## [Exemplos de código](https://azure.microsoft.com/resources/samples/?service=service-fabric)
## [PowerShell do Azure](service-fabric-powershell-samples.md)
## [CLI do Service Fabric](samples-cli.md)

# Conceitos
## [Entender os microsserviços](service-fabric-overview-microservices.md)
## [Visão global](service-fabric-content-roadmap.md)
## [Cenários de aplicativos](service-fabric-application-scenarios.md)
## [Padrões e cenários](service-fabric-patterns-and-scenarios.md)
## [Arquitetura](service-fabric-architecture.md)
## [Terminologia](service-fabric-technical-overview.md)

## Criar aplicativos e serviços
### Modelos de programação com suporte
#### [Visão geral](service-fabric-choose-framework.md)
#### Contêineres
##### [Visão geral](service-fabric-containers-overview.md)
##### [Docker compose (visualização)](service-fabric-docker-compose.md)
##### [Governança de recursos](service-fabric-resource-governance.md)
#### Reliable Services
##### [Visão geral](service-fabric-reliable-services-introduction.md)
##### [Ciclo de vida dos Reliable Services - C#](service-fabric-reliable-services-lifecycle.md)
##### [Ciclo de vida dos Reliable Services - Java](service-fabric-reliable-services-lifecycle-java.md)
##### [Coleções Confiáveis](service-fabric-reliable-services-reliable-collections.md)
##### [Recomendações e Diretrizes de coleções confiáveis](service-fabric-reliable-services-reliable-collections-guidelines.md)
##### [Trabalhando com Reliable Collections](service-fabric-work-with-reliable-collections.md)
##### [Transações e bloqueios](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
##### [Fila simultânea confiável](service-fabric-reliable-services-reliable-concurrent-queue.md)
##### [Serialização de coleção confiável](service-fabric-reliable-services-reliable-collections-serialization.md)
##### [Gerenciador de estado confiável e Aspectos internos confiáveis da coleção](service-fabric-reliable-services-reliable-collections-internals.md)
##### [Uso avançado](service-fabric-reliable-services-advanced-usage.md)

#### Reliable Actors
##### [Visão geral](service-fabric-reliable-actors-introduction.md)
##### [Arquitetura](service-fabric-reliable-actors-platform.md)
##### [Ciclo de vida e coleta de lixo](service-fabric-reliable-actors-lifecycle.md)
##### [Gerenciamento de estado](service-fabric-reliable-actors-state-management.md)
##### [Polimorfismo](service-fabric-reliable-actors-polymorphism.md)
##### [Reentrada](service-fabric-reliable-actors-reentrancy.md)
##### [Serialização de tipo](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

### [Modelo de aplicativo](service-fabric-application-model.md)
### [Modelo de hospedagem](service-fabric-hosting-model.md)

### Serviços
#### [Recursos do serviço](service-fabric-service-manifest-resources.md)
#### [Estado do serviço](service-fabric-concepts-state.md)
#### [Particionamento de serviço](service-fabric-concepts-partitioning.md)
#### [Disponibilidade de serviços](service-fabric-availability-services.md)
#### [Instâncias e réplicas](service-fabric-concepts-replica-lifecycle.md)
#### [Reconfiguração](service-fabric-concepts-reconfiguration.md)
#### Comunicação de serviço
##### [Visão geral](service-fabric-connect-and-communicate-with-services.md)
##### [Serviço DNS](service-fabric-dnsservice.md)
##### [Proxy reverso](service-fabric-reverseproxy.md)
##### [Configurar o proxy reverso para comunicação segura](service-fabric-reverseproxy-configure-secure-communication.md)
##### [Diagnósticos de proxy reverso](service-fabric-reverse-proxy-diagnostics.md)
### [Escalabilidade de aplicativos](service-fabric-concepts-scalability.md)
### [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

### [Planejar a capacidade do aplicativo](service-fabric-capacity-planning.md)

## Gerenciar aplicativos
### [Visão geral](service-fabric-application-lifecycle.md)
### [A configuração ImageStoreConnectionString](service-fabric-image-store-connection-string.md)
### Atualização de aplicativo
#### [Visão geral](service-fabric-application-upgrade.md)
#### [Configuração](service-fabric-visualstudio-configure-upgrade.md)
#### [Parâmetros de atualização de aplicativo](service-fabric-application-upgrade-parameters.md)
#### [Serialização de dados nas atualizações de aplicativo](service-fabric-application-upgrade-data-serialization.md)
#### [Tópicos avançados de atualização de aplicativo](service-fabric-application-upgrade-advanced.md)
### [Visão geral de análise de falhas](service-fabric-testability-overview.md)

## Criar e gerenciar clusters
### [Visão geral](service-fabric-deploy-anywhere.md)
### Planejar e preparar
#### [Planejamento da capacidade](service-fabric-cluster-capacity.md)
#### [Recuperação de desastre](service-fabric-disaster-recovery.md)
### [Descrevendo um cluster](service-fabric-cluster-resource-manager-cluster-description.md)
### [Segurança de cluster](service-fabric-cluster-security.md)
### [Diferenças de recurso entre o Linux e o Windows](service-fabric-linux-windows-differences.md)
### Clusters no Azure
#### [Tipos de nós e Conjuntos de Dimensionamento de VMs](service-fabric-cluster-nodetypes.md)
#### [Padrões de rede do cluster](service-fabric-patterns-networking.md)
### Gerenciador de recursos de cluster
#### [Visão geral](service-fabric-cluster-resource-manager-introduction.md)
#### [Arquitetura](service-fabric-cluster-resource-manager-architecture.md)
#### [Descrever um cluster](service-fabric-cluster-resource-manager-cluster-description.md)
#### [Visão geral de grupos de aplicativos](service-fabric-cluster-resource-manager-application-groups.md)
#### [Definir configurações do Resource Manager de Cluster](service-fabric-cluster-resource-manager-configure-services.md)
#### [Métricas de consumo de recursos](service-fabric-cluster-resource-manager-metrics.md)
#### [Usar afinidade de serviço](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
#### [Políticas de posicionamento de serviço](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
#### [Gerenciar um cluster](service-fabric-cluster-resource-manager-management-integration.md)
#### [Desfragmentação do cluster](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
#### [alancear um cluster](service-fabric-cluster-resource-manager-balancing.md)
#### [Limitação](service-fabric-cluster-resource-manager-advanced-throttling.md)
#### [Movimento de serviço](service-fabric-cluster-resource-manager-movement-cost.md)

## [Integrar com o Gerenciamento de API](service-fabric-api-management-overview.md)

## Monitorar e diagnosticar
### [Visão geral](service-fabric-diagnostics-overview.md)
### [Modelo de integridade](service-fabric-health-introduction.md)
### [Diagnóstico em Reliable Services com estado](service-fabric-reliable-services-diagnostics.md)
### [Diagnósticos em Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
### [Contadores de desempenho para o Serviço Remoto Confiável](service-fabric-reliable-serviceremoting-diagnostics.md)

# Guias de instruções
## Configurar seu ambiente de desenvolvimento
### [Windows](service-fabric-get-started.md)
### [Linux](service-fabric-get-started-linux.md)
### [Mac OS](service-fabric-get-started-mac.md)

## [Configurar a CLI do Service Fabric](service-fabric-cli.md)

## Compilar um aplicativo
### [Criar seu primeiro aplicativo C# no Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
### Compilar um serviço executável convidado
#### [Hospedar um aplicativo Node.js no Windows](quickstart-guest-app.md)
#### [Implantar um executável convidado](service-fabric-deploy-existing-app.md)
#### [Implantar vários executáveis de convidado](service-fabric-deploy-multiple-apps.md)
### Compilar um serviço de contêiner
#### [Criar um aplicativo de contêiner do Windows](service-fabric-get-started-containers.md)
#### [Criar um aplicativo de contêiner do Linux](service-fabric-get-started-containers-linux.md)
#### [Segurança do contêiner](service-fabric-securing-containers.md)
#### [Docker compose (visualização)](service-fabric-docker-compose.md)
#### [Governança de recursos para serviços e contêineres](service-fabric-resource-governance.md)
#### [Volume e drivers de log](service-fabric-containers-volume-logging-drivers.md)
#### [Serviços dentro de contêineres](service-fabric-services-inside-containers.md)
#### [Modos de rede do contêiner](service-fabric-networking-modes.md)

### Compilar um serviço de Reliable Services
#### [Visão geral](service-fabric-reliable-services-introduction.md)
#### Conceitos
##### [Ciclo de vida dos Reliable Services - C#](service-fabric-reliable-services-lifecycle.md)
##### [Ciclo de vida dos Reliable Services - Java](service-fabric-reliable-services-lifecycle-java.md)

#### Coleções Confiáveis
##### [Coleções Confiáveis](service-fabric-reliable-services-reliable-collections.md)
##### [Recomendações e Diretrizes de coleções confiáveis](service-fabric-reliable-services-reliable-collections-guidelines.md)
##### [Trabalhando com Reliable Collections](service-fabric-work-with-reliable-collections.md)
##### [Transações e bloqueios](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
##### [Fila simultânea confiável](service-fabric-reliable-services-reliable-concurrent-queue.md)
##### [Serialização de coleção confiável](service-fabric-reliable-services-reliable-collections-serialization.md)
##### [Gerenciador de estado confiável e Aspectos internos confiáveis da coleção](service-fabric-reliable-services-reliable-collections-internals.md)

#### Introdução
##### [C# em Windows](service-fabric-reliable-services-quick-start.md)
##### [Java no Linux](service-fabric-reliable-services-quick-start-java.md)
##### [Criar um aplicativo C# no Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
#### Comunicar-se com os serviços
##### [Comunicar-se com o Reliable Services](service-fabric-reliable-services-communication.md)

##### [Comunicação remota do serviço - C#](service-fabric-reliable-services-communication-remoting.md)
##### [Comunicação remota do serviço - Java](service-fabric-reliable-services-communication-remoting-java.md)
##### [WCF](service-fabric-reliable-services-communication-wcf.md)
##### [Comunicações seguras - C#](service-fabric-reliable-services-secure-communication.md)
##### [Comunicações seguras - Java](service-fabric-reliable-services-secure-communication-java.md)

#### [Configurar](service-fabric-reliable-services-configuration.md)
#### [Enviar notificações](service-fabric-reliable-services-notifications.md)
#### [Backup e restauração](service-fabric-reliable-services-backup-restore.md)

### Compilar um serviço de Reliable Actors
#### Introdução
##### [C# em Windows](service-fabric-reliable-actors-get-started.md)
##### [Ator Java no Linux](service-fabric-create-your-first-linux-application-with-java.md)
#### [Enviar notificações](service-fabric-reliable-actors-events.md)
#### [Definir lembretes e temporizadores](service-fabric-reliable-actors-timers-reminders.md)
#### [Configurar KVSActorStateProvider](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [Definir as configurações da comunicação](service-fabric-reliable-actors-fabrictransportsettings.md)
#### [Configurar ReliableDictionaryActorStateProvider](service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

### [Migrar o antigo aplicativo Java para dar suporte a Maven](service-fabric-migrate-old-javaapp-to-use-maven.md)

### [Configurar o proxy reverso para comunicação segura](service-fabric-reverseproxy-configure-secure-communication.md)

### [Compilar um serviço do ASP.NET Core](service-fabric-add-a-web-frontend.md)

### Configurar a segurança
#### [Gerenciar os segredos do aplicativo](service-fabric-application-secret-management.md)  
#### [Configurar políticas de segurança para seu aplicativo](service-fabric-application-runas-security.md)

## Trabalhar em um ambiente de desenvolvimento do Windows
### [Gerenciar aplicativos no Visual Studio](service-fabric-manage-application-in-visual-studio.md)
### [Configurar conexões seguras no Visual Studio](service-fabric-visualstudio-configure-secure-connections.md)
### [Configurar seu aplicativo para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md)
### [Depurar um serviço .NET no VS](service-fabric-debugging-your-application.md)
### [Erros e exceções comuns](service-fabric-errors-and-exceptions.md)
### [Monitorar e diagnosticar localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## Trabalhar em um ambiente de desenvolvimento do Linux
### [Introdução ao plug-in Eclipse para desenvolvimento de Java](service-fabric-get-started-eclipse.md)
### [Depurar um serviço Java no Eclipse](service-fabric-debugging-your-application-java.md)
### [Monitorar e diagnosticar localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

## [Implantar o Gerenciamento de API e o Service Fabric no Azure](service-fabric-api-management-quick-start.md)

## Migrar dos Serviços de Nuvem
### [Comparar os Serviços de Nuvem com o Service Fabric](service-fabric-cloud-services-migration-differences.md)
### [Migrar para o Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)
### [Práticas recomendadas](/azure/architecture/service-fabric/migrate-from-cloud-services)

## Gerenciar ciclo de vida do aplicativo
### [Preparar um aplicativo](service-fabric-package-apps.md)

### Implantar ou remover aplicativos
#### [Implantar aplicativos em um cluster local](service-fabric-get-started-with-a-local-cluster.md)
#### [Gerenciador de Recursos do Azure](service-fabric-application-arm-resource.md)
#### [PowerShell do Azure](service-fabric-deploy-remove-applications.md)
#### [CLI do Service Fabric](service-fabric-application-lifecycle-sfctl.md)
#### [Visual Studio](service-fabric-publish-app-remote-cluster.md)
#### [APIs de FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)

### Atualizar aplicativos
#### [Atualizar usando o Azure PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
#### [Atualizar usando o Visual Studio](service-fabric-application-upgrade-tutorial.md)
#### [Solucionar problemas de atualizações de aplicativo](service-fabric-application-upgrade-troubleshooting.md)

### Testar aplicativos e serviços
#### [Testar comunicação serviço a serviço](service-fabric-testability-scenarios-service-communication.md)
#### Simular falhas
##### [Como usar o caos controlado](service-fabric-controlled-chaos.md)
##### [Como usar as ações de teste](service-fabric-testability-actions.md)
##### [Durante cargas de trabalho](service-fabric-testability-workload-tests.md)
##### [Como usar os cenários de teste](service-fabric-testability-scenarios.md)
##### [Usando as APIs de transição do nó](service-fabric-node-transition-apis.md)
#### [Fazer teste de carga em seu aplicativo](service-fabric-vso-load-test.md)

### Configurar a integração contínua
#### [Configurar a integração contínua com o VSTS](service-fabric-set-up-continuous-integration.md)
#### [Implantar seu aplicativo Java Linux usando o Jenkins](service-fabric-cicd-your-linux-java-application-with-jenkins.md)

## Criar e gerenciar clusters
### Clusters no Azure
#### Criação
##### [Criar seu primeiro cluster no Azure](service-fabric-get-started-azure-cluster.md)
##### [Portal do Azure](service-fabric-cluster-creation-via-portal.md)
##### [Gerenciador de Recursos do Azure](service-fabric-cluster-creation-via-arm.md)
#### Escala
##### [Manualmente](service-fabric-cluster-scale-up-down.md)
##### [Programaticamente](service-fabric-cluster-programmatic-scaling.md)
#### [Atualizar](service-fabric-cluster-upgrade.md)
#### [Definir controle de acesso](service-fabric-cluster-security-roles.md)
#### [Configurar](service-fabric-cluster-fabric-settings.md)
#### [Abrir uma porta no balanceador de carga](create-load-balancer-rule.md)
#### [Gerenciar certificados de cluster](service-fabric-cluster-security-update-certs-azure.md)
#### [Excluir](service-fabric-cluster-delete.md)

### Clusters independentes
#### [Planejar e preparar para a implantação do cluster](service-fabric-cluster-standalone-deployment-preparation.md)
#### Criação
##### [Criar seu primeiro cluster autônomo](service-fabric-get-started-standalone-cluster.md)
##### [Criar localmente](service-fabric-cluster-creation-for-windows-server.md)
##### [Proteger usando certificados](service-fabric-windows-cluster-x509-security.md)  
##### [Proteger usando a segurança do Windows](service-fabric-windows-cluster-windows-security.md)
##### [Conteúdos do pacote autônomo](service-fabric-cluster-standalone-package-contents.md)
#### [Escala](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [Definir controle de acesso](service-fabric-cluster-security-roles.md)
#### [Configurar](service-fabric-cluster-manifest.md)
#### [Atualizar](service-fabric-cluster-upgrade-windows-server.md)

### [Visualizar um cluster](service-fabric-visualizing-your-cluster.md)
### [Conectar-se a um cluster seguro](service-fabric-connect-to-secure-cluster.md)
### [Nós de cluster de patch](service-fabric-patch-orchestration-application.md)

### Gerenciar e orquestrar recursos do cluster
#### [Visão geral do Resource Manager de cluster](service-fabric-cluster-resource-manager-introduction.md)
#### [Arquitetura do Gerenciador de Recursos do Cluster](service-fabric-cluster-resource-manager-architecture.md)
#### [Descrever um cluster](service-fabric-cluster-resource-manager-cluster-description.md)
#### [Visão geral de grupos de aplicativos](service-fabric-cluster-resource-manager-application-groups.md)
#### [Definir configurações do Resource Manager de Cluster](service-fabric-cluster-resource-manager-configure-services.md)
#### [Métricas de consumo de recursos](service-fabric-cluster-resource-manager-metrics.md)
#### [Usar afinidade de serviço](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
#### [Políticas de posicionamento de serviço](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
#### [Gerenciar um cluster](service-fabric-cluster-resource-manager-management-integration.md)
#### [Desfragmentação do cluster](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
#### [alancear um cluster](service-fabric-cluster-resource-manager-balancing.md)
#### [Limitação](service-fabric-cluster-resource-manager-advanced-throttling.md)
#### [Movimento de serviço](service-fabric-cluster-resource-manager-movement-cost.md)

## Monitorar e diagnosticar
### [Monitorar e diagnosticar aplicativos](service-fabric-diagnostics-overview.md)
### Gerar eventos
#### [Gerar eventos de nível de plataforma](service-fabric-diagnostics-event-generation-infra.md)
##### [Canal operacional](service-fabric-diagnostics-event-generation-operational.md)
##### [Eventos de Reliable Services](service-fabric-reliable-services-diagnostics.md)
##### [Eventos de Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
##### [Métricas de desempenho](service-fabric-diagnostics-event-generation-perf.md)
#### [Gerar eventos de nível de aplicativo](service-fabric-diagnostics-event-generation-app.md)
### Inspecionar a integridade do aplicativo e do cluster
#### [Monitorar a integridade do Service Fabric](service-fabric-health-introduction.md)
#### [Relatar e verificar a integridade de serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
#### [Adicionar relatórios de integridade personalizados](service-fabric-report-health.md)
#### [Solucionar problemas com relatórios de integridade do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
#### [Exibir relatórios de integridade](service-fabric-view-entities-aggregated-health.md)
#### [Monitorar contêineres do Windows Server](service-fabric-diagnostics-containers-windowsserver.md)
### Eventos de agregação
#### [Eventos de agregação com EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)
#### Eventos de agregação com o Diagnóstico do Azure
##### [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
##### [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
### Analisar eventos
#### [Analisar eventos com o Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
#### [Analisar eventos com o OMS](service-fabric-diagnostics-event-analysis-oms.md)
### [Solucionar problemas do seu cluster local](service-fabric-troubleshoot-local-cluster-setup.md)

# Referência
## [PowerShell do Azure](/powershell/module/azurerm.servicefabric/)
## [PowerShell](/powershell/module/servicefabric/?view=azureservicefabricps)
## [CLI do Azure (az sf)](/cli/azure/sf)
## [CLI do Service Fabric (sfctl)](service-fabric-sfctl.md)
### [aplicativo sfctl](service-fabric-sfctl-application.md)
### [caos sfctl](service-fabric-sfctl-chaos.md)
### [cluster sfctl](service-fabric-sfctl-cluster.md)
### [compor sfctl](service-fabric-sfctl-compose.md)
### [sfctl é](service-fabric-sfctl-is.md)
### [nó sfctl](service-fabric-sfctl-node.md)
### [partição sfctl](service-fabric-sfctl-partition.md)
### [réplica sfctl](service-fabric-sfctl-replica.md)
### [rpm sfctl](service-fabric-sfctl-rpm.md)
### [serviço sfctl](service-fabric-sfctl-service.md)
### [repositório sfctl](service-fabric-sfctl-store.md)
## [API Java](/java/api/overview/azure/servicefabric)
## [.NET](/dotnet/api/overview/azure/service-fabric?view=azure-dotnet)
## [REST](/rest/api/servicefabric)

# Recursos
## [Roteiro do Azure](https://azure.microsoft.com/roadmap/)
## [Perguntas comuns](service-fabric-common-questions.md)
## [Roteiro de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
## [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)
## [Preços](https://azure.microsoft.com/pricing/details/service-fabric/)
## [Calculadora de preço](https://azure.microsoft.com/pricing/calculator/)
## [Exemplo de código](http://aka.ms/servicefabricsamples)
## [Opções de suporte](service-fabric-support.md)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=service-fabric)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=service-fabric)
