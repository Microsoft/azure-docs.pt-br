---
title: "Criar um cluster autônomo do Azure Service Fabric | Microsoft Docs"
description: "Crie um cluster do Azure Service Fabric em qualquer computador (físico ou virtual) executando o Windows Server, seja ele local ou em qualquer nuvem."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 31349169-de19-4be6-8742-ca20ac41eb9e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/10/2017
ms.author: chackdan;maburlik;dekapur
ms.openlocfilehash: 6aa2905a97ec6b8c125f2ab9572a8e40bf525b27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>Criar um cluster autônomo em execução no Windows Server
Você pode usar o Azure Service Fabric para criar clusters do Service Fabric em qualquer máquina virtual ou computador que estiver executando o Windows Server. Isso significa que você pode implantar e executar os aplicativos do Service Fabric em qualquer ambiente que tenha um conjunto de computadores com o Windows Server interconectados, seja localmente ou em qualquer provedor de nuvem. O Service Fabric fornece um pacote de instalação para criar os clusters do Service Fabric denominado pacote do Windows Server autônomo.

Este artigo guia você pelas etapas para criação de um cluster do Service Fabric.

> [!NOTE]
> Este pacote autônomo do Windows Server está disponível para venda e pode ser usado para implantações de produção. Este pacote pode conter novos recursos do Service Fabric que estão em “Visualização”. Role para baixo até “[Recursos de visualização incluídos neste pacote](#previewfeatures_anchor)”. para obter uma lista dos recursos da visualização. Você pode [baixar uma cópia do EULA](http://go.microsoft.com/fwlink/?LinkID=733084) agora.
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>Obter suporte para o pacote do Service Fabric para Windows Server
* Pergunte à comunidade sobre o pacote autônomo do Service Fabric para Windows Server no [Fórum do Service Fabric do Azure](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?).
* Abra um tíquete de [Suporte Profissional para o Service Fabric](http://support.microsoft.com/oas/default.aspx?prid=16146).  Saiba mais sobre o Suporte Profissional da Microsoft [aqui](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
* Você também pode obter suporte para este pacote como parte do [Suporte Premier da Microsoft](https://support.microsoft.com/en-us/premier).
* Para obter mais detalhes, consulte as [Opções de suporte do Azure Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-support).
* Para coletar logs para fins de suporte, execute o [Coletor de logs Autônomo do Service Fabric](service-fabric-cluster-standalone-package-contents.md).

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>Baixar o pacote do Service Fabric para Windows Server
Para criar o cluster, use o pacote do Service Fabric para Windows Server (Windows Server 2012 R2 e mais recente) encontrado aqui: <br>
[Link de Download – pacote autônomo do Service Fabric – Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690)

Encontre detalhes sobre o conteúdo do pacote [aqui](service-fabric-cluster-standalone-package-contents.md).

O pacote de tempo de execução do Service Fabric é baixado automaticamente no momento da criação do cluster. Se for implantar de um computador não conectado à Internet, baixe o pacote de tempo de execução fora de banda aqui: <br>
[Link de Download – Tempo de Execução do Service Fabric – Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)

Encontre amostras de configuração de cluster autônomo em: <br>
[Amostras de configuração de cluster autônomo](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>Criar o cluster
O Service Fabric pode ser implantado em um cluster de desenvolvimento de um computador usando o arquivo *ClusterConfig.Unsecure.DevCluster.json* incluído em [Amostras](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

Descompacte o pacote autônomo no seu computador, copie o arquivo de configuração de exemplo para o computador local, execute o script *CreateServiceFabricCluster.ps1* por meio de uma sessão do PowerShell do administrador, da pasta de pacote autônomo:
### <a name="step-1a-create-an-unsecured-local-development-cluster"></a>Etapa 1A: criar um cluster de desenvolvimento local desprotegido
```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Consulte a seção Configuração do ambiente em [Planejar e preparar a implantação do cluster](service-fabric-cluster-standalone-deployment-preparation.md) para obter detalhes de solução de problemas.

Se você concluiu a execução de cenários de desenvolvimento, poderá remover o cluster do Service Fabric do computador consultando as etapas na seção “[Remover um cluster](#removecluster_anchor)”. 

### <a name="step-1b-create-a-multi-machine-cluster"></a>Etapa 1B: criar um cluster com vários computadores
Depois que tiver verificado o planejamento e etapas de preparação detalhadas no link abaixo, você estará pronto para criar seu cluster de produção usando o arquivo de configuração do cluster. <br>
[Planejar e preparar a implantação do cluster](service-fabric-cluster-standalone-deployment-preparation.md)

1. Valide o arquivo de configuração que você gravou escrito executando o script *TestConfiguration.ps1* da pasta do pacote autônomo:  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    Você deverá ver uma saída como a abaixo. Se o campo inferior "Passed" é retornado como "True", as verificações de integridade são aprovadas e o cluster parece estar pronto para implantação com base na configuração de entrada.

    ```
    Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
    Running Best Practices Analyzer...
    Best Practices Analyzer completed successfully.
    
    LocalAdminPrivilege        : True
    IsJsonValid                : True
    IsCabValid                 : True
    RequiredPortsOpen          : True
    RemoteRegistryAvailable    : True
    FirewallAvailable          : True
    RpcCheckPassed             : True
    NoConflictingInstallations : True
    FabricInstallable          : True
    Passed                     : True
    ```

2. Criar o cluster: execute o script *CreateServiceFabricCluster.ps1* para implantar o cluster do Service Fabric em cada computador na configuração. 
    ```powershell
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
    ```

> [!NOTE]
> Rastreamentos de implantação são gravados na VM/computador em que você executou o script do PowerShell CreateServiceFabricCluster.ps1. Eles podem ser encontrados na subpasta DeploymentTraces, com base no diretório do qual o script foi executado. Para ver se o Service Fabric foi implantado corretamente em um computador, localize os arquivos instalados no diretório FabricDataRoot, conforme detalhado na seção FabricSettings do arquivo de configuração de cluster (por padrão, c:\ProgramData\SF). Além disso, processos FabricHost.exe e Fabric.exe podem ser vistos em execução no Gerenciador de Tarefas.
> 
> 

### <a name="step-1c-create-an-offline-internet-disconnected-cluster"></a>Etapa 1C: Criar um cluster offline (desconectado da Internet)
O pacote de tempo de execução do Service Fabric é baixado automaticamente durante a criação do cluster. Ao implantar um cluster em computadores que não estão conectados à Internet, você precisará baixar o pacote de tempo de execução do Service Fabric separadamente e fornecer o caminho para ele durante a criação do cluster.
O pacote de tempo de execução pode ser baixado separadamente, em outro computador conectado à Internet, em [Link de Download – Tempo de Execução do Service Fabric – Windows Server](https://go.microsoft.com/fwlink/?linkid=839354). Copie o pacote de tempo de execução para o local de origem da implantação do cluster offline e crie o cluster executando `CreateServiceFabricCluster.ps1` com o parâmetro `-FabricRuntimePackagePath` incluído, conforme mostrado abaixo: 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```
em que `.\ClusterConfig.json` e `.\MicrosoftAzureServiceFabric.cab` são os caminhos para a configuração do cluster e o arquivo .cab de tempo de execução, respectivamente.


### <a name="step-2-connect-to-the-cluster"></a>Etapa 2: conectar-se ao cluster
Para se conectar a um cluster seguro, confira [Service fabric connect to secure cluster](service-fabric-connect-to-secure-cluster.md) (Conexão do Service Fabric com um cluster seguro).

Para se conectar a um cluster não seguro, execute o seguinte comando do PowerShell:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>
```
Exemplo:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000
```
### <a name="step-3-bring-up-service-fabric-explorer"></a>Etapa 3: abrir o Service Fabric Explorer
Agora, você pode conectar o cluster com o Service Fabric Explorer diretamente de um dos computadores com http://localhost:19080/Explorer/index.html ou remotamente com http://<*IPAddressofaMachine*>:19080/Explorer/index.html.

## <a name="add-and-remove-nodes"></a>Adicionar e remover nós
Você pode adicionar ou remover os nós do cluster do Service Fabric autônomo quando seu negócio precisa mudar. Confira [Adicionar ou remover nós de um cluster do Service Fabric autônomo](service-fabric-cluster-windows-server-add-remove-nodes.md) para ver as etapas detalhadas.

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>Remover um cluster
Para remover um cluster, execute o script do PowerShell *RemoveServiceFabricCluster.ps1* da pasta do pacote e transmita o caminho para o arquivo de configuração JSON. Como alternativa, você pode especificar um local para o log de exclusão.

Esse script pode ser executado em qualquer computador que tenha o acesso de administrador para todos os computadores listados como nós no arquivo de configuração do cluster. O computador no qual este script é executado não precisa fazer parte do cluster.

```
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Dados de telemetria coletados e como recusá-los
Por padrão, o produto coleta a telemetria sobre o uso do Service Fabric para aprimorar o produto. O Analisador de Práticas Recomendadas executado como parte da instalação verifica a conectividade para [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1). Se ele não estiver acessível, a instalação falhará, a menos que você recuse a telemetria.

1. O pipeline de telemetria tenta carregar os dados a seguir para [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) uma vez por dia. É um upload de melhor esforço e não causa nenhum impacto sobre a funcionalidade do cluster. A telemetria é enviada somente do nó que executa o gerenciador de failover primário. Nenhum outro nó envia telemetria.
2. A Telemetria consiste no seguinte:

* Número de serviços
* Número de ServiceTypes
* Número de Aplicativos
* Número de ApplicationUpgrades
* Número de FailoverUnits
* Número de InBuildFailoverUnits
* Número de UnhealthyFailoverUnits
* Número de Réplicas
* Número de InBuildReplicas
* Número de StandByReplicas
* Número de OfflineReplicas
* CommonQueueLength
* QueryQueueLength
* FailoverUnitQueueLength
* CommitQueueLength
* Número de Nós
* IsContextComplete: True/False
* ClusterId: esse é um GUID gerado aleatoriamente para cada cluster
* ServiceFabricVersion
* Endereço IP da máquina virtual ou computador por meio do qual a telemetria é carregada

Para desabilitar a telemetria, adicione o seguinte ao elemento *propriedades* em sua configuração de cluster: *enableTelemetry: false*.

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>Recursos de preview incluídos neste pacote
Nenhuma.


> [!NOTE]
> A partir da nova [versão GA do cluster autônomo para Windows Server (versão 5.3.204.x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/), você pode atualizar seu cluster para versões futuras, de modo manual ou automático. Consulte o documento [Atualizar uma versão autônoma do cluster do Service Fabric](service-fabric-cluster-upgrade-windows-server.md) para ver mais detalhes.
> 
> 

## <a name="next-steps"></a>Próximas etapas
* [Implantar e remover aplicativos usando o PowerShell](service-fabric-deploy-remove-applications.md)
* [Definições de configuração para o cluster autônomo no Windows](service-fabric-cluster-manifest.md)
* [Adicionar ou remover nós de um cluster do Service Fabric autônomo](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Atualizar uma versão autônoma de cluster do Service Fabric](service-fabric-cluster-upgrade-windows-server.md)
* [Criar um cluster do Service Fabric autônomo com VMs do Azure executando o Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
* [Proteger um cluster autônomo no Windows usando a segurança](service-fabric-windows-cluster-windows-security.md)
* [Proteger um cluster autônomo no Windows usando os certificados X509](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
