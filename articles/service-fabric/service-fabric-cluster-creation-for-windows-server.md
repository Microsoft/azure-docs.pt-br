---
title: Criar um cluster autônomo do Azure Service Fabric
description: Crie um cluster do Azure Service Fabric em qualquer computador (físico ou virtual) executando o Windows Server, seja ele local ou em qualquer nuvem.
ms.topic: conceptual
ms.date: 2/21/2019
ms.openlocfilehash: 41af655be07ccae2b66e75f5bfe87629cdb54924
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98785677"
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>Criar um cluster autônomo em execução no Windows Server
Você pode usar o Azure Service Fabric para criar clusters do Service Fabric em qualquer máquina virtual ou computador que estiver executando o Windows Server. Isso significa que você pode implantar e executar os aplicativos do Service Fabric em qualquer ambiente que tenha um conjunto de computadores com o Windows Server interconectados, seja localmente ou em qualquer provedor de nuvem. O Service Fabric fornece um pacote de instalação para criar os clusters do Service Fabric denominado pacote do Windows Server autônomo. Os clusters tradicionais do Service Fabric no Azure estão disponíveis como um serviço gerenciado, enquanto os clusters autônomos do Service Fabric são de autoatendimento. Para obter mais informações, consulte [Comparando clusters do Service Fabric autônomos e do Azure](./service-fabric-deploy-anywhere.md).

Este artigo guia você pelas etapas para criação de um cluster do Service Fabric.

> [!NOTE]
> Este pacote autônomo do Windows Server está disponível para venda e pode ser usado gratuitamente para implantações de produção. Este pacote pode conter novos recursos do Service Fabric que estão em “Visualização”. Role para baixo até “[Recursos de visualização incluídos neste pacote](#previewfeatures_anchor)”. para obter uma lista dos recursos da visualização. Você pode [baixar uma cópia do EULA](https://go.microsoft.com/fwlink/?LinkID=733084) agora.
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>Obter suporte para o pacote do Service Fabric para Windows Server
* Pergunte à comunidade sobre o pacote autônomo do Service Fabric para Windows Server na [página de perguntas e respostas da Microsoft sobre o Azure Service Fabric](/answers/topics/azure-service-fabric.html).
* Abra um tíquete de [Suporte Profissional para o Service Fabric](https://support.microsoft.com/oas/default.aspx?prid=16146).  Saiba mais sobre o Suporte Profissional da Microsoft [aqui](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
* Você também pode obter suporte para este pacote como parte do [Suporte Premier da Microsoft](https://support.microsoft.com/en-us/premier).
* Para obter mais detalhes, consulte as [Opções de suporte do Azure Service Fabric](./service-fabric-support.md).
* Para coletar logs para fins de suporte, execute o [Coletor de logs Autônomo do Service Fabric](service-fabric-cluster-standalone-package-contents.md).

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>Baixar o pacote do Service Fabric para Windows Server
Para criar o cluster, use o pacote do Service Fabric para Windows Server (Windows Server 2012 R2 e mais recente) encontrado aqui: <br>
[Link de Download – pacote autônomo do Service Fabric – Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690)

Encontre detalhes sobre o conteúdo do pacote [aqui](service-fabric-cluster-standalone-package-contents.md).

O pacote de runtime do Service Fabric é baixado automaticamente no momento da criação do cluster. Se for implantar de um computador não conectado à Internet, baixe o pacote de runtime fora de banda aqui: <br>
[Link de Download – Runtime do Service Fabric – Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)

Encontre amostras de configuração de cluster autônomo em: <br>
[Amostras de configuração de cluster autônomo](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>Criar o cluster
Vários arquivos de configuração de cluster de exemplo são instalados com o pacote de instalação. *ClusterConfig.Unsecure.DevCluster.json* é a configuração de cluster mais simples: um cluster não seguro de três nós em execução em um único computador.  Outros arquivos de configuração descrevem clusters únicos ou vários computadores protegidos com certificados x. 509 ou com a segurança do Windows.  Você não precisa modificar nenhuma definição de configuração padrão para este tutorial, mas examine o arquivo config e familiarizar-se com as configurações.  A seção **Nós** descreve os três nós no cluster: nome, endereço IP, [tipo de nó, domínio de falha e domínio de atualização](service-fabric-cluster-manifest.md#nodes-on-the-cluster).  A seção **Propriedades** define a [segurança, nível de confiabilidade, coleta de diagnóstico e tipos de nós](service-fabric-cluster-manifest.md#cluster-properties) para o cluster.

O cluster criado neste artigo não é seguro.  Qualquer pessoa pode conectar-se anonimamente e realizar operações de gerenciamento, portanto, os clusters de produção sempre devem ser protegidos usando os certificados x.509 ou a segurança do Windows.  A segurança só é configurada no momento de criação do cluster e não é possível habilitar a segurança após a criação dele. Atualize o arquivo de configuração para habilitar a [segurança de certificado](service-fabric-windows-cluster-x509-security.md) ou a [segurança do Windows](service-fabric-windows-cluster-windows-security.md). Leia [Proteger um cluster](service-fabric-cluster-security.md) para saber mais sobre a segurança do cluster do Service Fabric.

### <a name="step-1-create-the-cluster"></a>Etapa 1: Criar o cluster

#### <a name="scenario-a-create-an-unsecured-local-development-cluster"></a>Cenário A: Criar um cluster de desenvolvimento local desprotegido
O Service Fabric pode ser implantado em um cluster de desenvolvimento de um computador usando o arquivo *ClusterConfig.Unsecure.DevCluster.json* incluído em [Amostras](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

Descompacte o pacote autônomo no seu computador, copie o arquivo de configuração de exemplo para o computador local, execute o script *CreateServiceFabricCluster.ps1* por meio de uma sessão do PowerShell do administrador, da pasta de pacote autônomo.

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Consulte a seção Configuração do ambiente em [Planejar e preparar a implantação do cluster](service-fabric-cluster-standalone-deployment-preparation.md) para obter detalhes de solução de problemas.

Se você concluiu a execução de cenários de desenvolvimento, poderá remover o cluster do Service Fabric do computador consultando as etapas na seção “[Remover um cluster](#removecluster_anchor)”. 

#### <a name="scenario-b-create-a-multi-machine-cluster"></a>Cenário B: Criar um cluster com vários computadores
Depois que tiver verificado o planejamento e etapas de preparação detalhadas em [Planejar e preparar a implantação do cluster](service-fabric-cluster-standalone-deployment-preparation.md), você estará pronto para criar seu cluster de produção usando o arquivo de configuração do cluster.

O administrador do cluster que implanta e configura o cluster deve ter privilégios de administrador no computador. Você não pode instalar o Service Fabric em um controlador de domínio.

1. O script *TestConfiguration.ps1* no pacote autônomo é usado como um analisador de práticas recomendadas para validar se um cluster pode ser implantado em um ambiente específico. [A preparação para implantação](service-fabric-cluster-standalone-deployment-preparation.md) lista os pré-requisitos e requisitos do ambiente. Execute o script para verificar se você pode criar o cluster de desenvolvimento:  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    Você deve ver saídas semelhantes às seguintes. Se o campo inferior "Passed" é retornado como "True", as verificações de integridade são aprovadas e o cluster parece estar pronto para implantação com base na configuração de entrada.

    ```powershell
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

2. Crie o cluster:  Execute o script *CreateServiceFabricCluster.ps1* para implantar o cluster do Service Fabric em cada computador na configuração. 
    ```powershell
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
    ```

> [!NOTE]
> Rastreamentos de implantação são gravados na VM/computador em que você executou o script do PowerShell CreateServiceFabricCluster.ps1. Eles podem ser encontrados na subpasta DeploymentTraces, com base no diretório do qual o script foi executado. Para ver se o Service Fabric foi implantado corretamente em um computador, localize os arquivos instalados no diretório FabricDataRoot, conforme detalhado na seção FabricSettings do arquivo de configuração de cluster (por padrão, c:\ProgramData\SF). Além disso, processos FabricHost.exe e Fabric.exe podem ser vistos em execução no Gerenciador de Tarefas.
> 
> 

#### <a name="scenario-c-create-an-offline-internet-disconnected-cluster"></a>Cenário C: Criar um cluster offline (desconectado da Internet)
O pacote de runtime do Service Fabric é baixado automaticamente durante a criação do cluster. Ao implantar um cluster em computadores que não estão conectados à Internet, você precisará baixar o pacote de runtime do Service Fabric separadamente e fornecer o caminho para ele durante a criação do cluster.
O pacote de runtime pode ser baixado separadamente, em outro computador conectado à Internet, em [Link de Download – Runtime do Service Fabric – Windows Server](https://go.microsoft.com/fwlink/?linkid=839354). Copie o pacote de runtime para o local de origem da implantação do cluster offline e crie o cluster executando `CreateServiceFabricCluster.ps1` com o parâmetro `-FabricRuntimePackagePath` incluído, conforme mostrado neste exemplo: 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```

*.\ClusterConfig.json* e *.\MicrosoftAzureServiceFabric.cab* são os caminhos para a configuração do cluster e o arquivo .cab de runtime, respectivamente.

### <a name="step-2-connect-to-the-cluster"></a>Etapa 2: Conectar-se ao cluster
Conecte-se ao cluster para verificar se o cluster está em execução e disponível. O módulo ServiceFabric do PowerShell é instalado no runtime.  Você pode se conectar ao cluster de um dos nós do cluster ou de um computador remoto com o runtime do Service Fabric.  O cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) estabelece uma conexão com o cluster.

Para se conectar a um cluster não seguro, execute o seguinte comando do PowerShell:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>
```

Por exemplo:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.234:19000
```

Confira [Conectar-se a um cluster seguro](service-fabric-connect-to-secure-cluster.md) para obter outros exemplos de como se conectar a um cluster. Depois de se conectar ao cluster, use o script [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode) para exibir uma lista de nós de cluster e informações de status para cada nó. **HealthState** deve ser *OK* para cada nó.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- -------- --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     vm2      localhost       NodeType2 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm1      localhost       NodeType1 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm0      localhost       NodeType0 5.6.220.9494 0                     Up 00:02:43   00:00:00              OK
```

### <a name="step-3-visualize-the-cluster-using-service-fabric-explorer"></a>Etapa 3: Visualizar o cluster usando o Service Fabric Explorer
O [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) é uma boa ferramenta para visualizar o cluster e gerenciar os aplicativos.  O Service Fabric Explorer é um serviço executado no cluster, que você acessa usando um navegador, acessando `http://localhost:19080/Explorer`.

O painel do cluster fornece uma visão geral do cluster, incluindo um resumo do aplicativo e a integridade do nó. A exibição de nós mostra o layout físico do cluster. Para um nó específico, você pode inspecionar quais aplicativos têm código implantado naquele nó.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="add-and-remove-nodes"></a>Adicionar e remover nós
Você pode adicionar ou remover os nós do cluster do Service Fabric autônomo quando seu negócio precisa mudar. Confira [Adicionar ou remover nós de um cluster do Service Fabric autônomo](service-fabric-cluster-windows-server-add-remove-nodes.md) para ver as etapas detalhadas.

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>Remover um cluster
Para remover um cluster, execute o script do PowerShell *RemoveServiceFabricCluster.ps1* da pasta do pacote e transmita o caminho para o arquivo de configuração JSON. Como alternativa, você pode especificar um local para o log de exclusão.

Esse script pode ser executado em qualquer computador que tenha o acesso de administrador para todos os computadores listados como nós no arquivo de configuração do cluster. O computador no qual este script é executado não precisa fazer parte do cluster.

```powershell
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```powershell
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Dados de telemetria coletados e como recusá-los
Por padrão, o produto coleta a telemetria sobre o uso do Service Fabric para aprimorar o produto. O Analisador de Melhores Práticas executado como parte da instalação verifica a conectividade para [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1). Se ele não estiver acessível, a instalação falhará, a menos que você recuse a telemetria.

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
* IsContextComplete: Verdadeiro/Falso
* ClusterId: Esse é um GUID gerado aleatoriamente para cada cluster
* ServiceFabricVersion
* Endereço IP da máquina virtual ou computador por meio do qual a telemetria é carregada

Para desabilitar a telemetria, adicione o seguinte ao elemento *propriedades* em sua configuração de cluster: *enableTelemetry: false*.

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>Recursos de preview incluídos neste pacote
Nenhum.


> [!NOTE]
> A partir da nova [versão GA do cluster autônomo para Windows Server (versão 5.3.204.x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/), você pode atualizar seu cluster para versões futuras, de modo manual ou automático. Consulte o documento [Atualizar uma versão autônoma do cluster do Service Fabric](service-fabric-cluster-upgrade-windows-server.md) para ver mais detalhes.
> 
> 

## <a name="next-steps"></a>Próximas etapas
* [Implantar e remover aplicativos usando o PowerShell](service-fabric-deploy-remove-applications.md)
* [Definições de configuração para o cluster autônomo no Windows](service-fabric-cluster-manifest.md)
* [Adicionar ou remover nós de um cluster do Service Fabric autônomo](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Atualizar uma versão autônoma de cluster do Service Fabric](service-fabric-cluster-upgrade-windows-server.md)
* [Criar um cluster do Service Fabric autônomo com VMs do Azure executando o Windows](./service-fabric-cluster-creation-via-arm.md)
* [Proteger um cluster autônomo no Windows usando a segurança](service-fabric-windows-cluster-windows-security.md)
* [Proteger um cluster autônomo no Windows usando os certificados X509](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
[service-fabric-explorer]: ./media/service-fabric-cluster-creation-for-windows-server/sfx.png
