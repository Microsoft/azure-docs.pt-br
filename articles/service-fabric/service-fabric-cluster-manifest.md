---
title: "Configurar o cluster do Azure Service Fabric autônomo | Microsoft Docs"
description: "Aprenda a configurar seu cluster autônomo ou particular do Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 0c5ec720-8f70-40bd-9f86-cd07b84a219d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/02/2017
ms.author: dekapur
ms.openlocfilehash: 660e7b59ae0e92692121620341562e412a6e8eae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configuration-settings-for-standalone-windows-cluster"></a>Definições de configuração para o cluster autônomo no Windows
Este artigo descreve como configurar um cluster autônomo do Service Fabric usando o arquivo ***ClusterConfig.JSON***. Use esse arquivo para especificar informações, como os nós do Service Fabric e seus endereços IP, tipos diferentes de nós no cluster, configurações de segurança, bem como a topologia da rede em termos de domínios de falha/atualização, para o cluster autônomo.

Quando você [baixa o pacote do Service Fabric autônomo](service-fabric-cluster-creation-for-windows-server.md#downloadpackage), alguns exemplos do arquivo ClusterConfig.JSON são baixados em seu computador. Os exemplos com *DevCluster* em seus nomes ajudarão a criar um cluster com todos os três nós no mesmo computador, como nós lógicos. Fora isso, pelo menos um nó deve ser marcado como um nó principal. Este cluster é útil para um ambiente de desenvolvimento ou de teste, e não tem suporte como um cluster de produção. Os exemplos com *MultiMachine* em seus nomes ajudarão a criar um cluster de qualidade de produção, com cada nó em um computador separado. O número de nós primários para esses clusters será baseado no [nível de confiabilidade](#reliability). Na API versão 5.7 de 05-2017, removemos a propriedade do nível de confiabilidade. Em vez disso, nosso código está calculando o nível de confiabilidade mais otimizado para seu cluster. Não use essa propriedade na versão de código 5.7 nem posteriores.


1. *ClusterConfig.Unsecure.DevCluster.JSON* e *ClusterConfig.Unsecure.MultiMachine.JSON* mostram como criar um cluster de teste ou de produção sem segurança, respectivamente. 
2. *ClusterConfig.Windows.DevCluster.JSON* e *ClusterConfig.Windows.MultiMachine.JSON* mostram como criar um cluster de teste ou de produção protegido usando a [segurança do Windows](service-fabric-windows-cluster-windows-security.md).
3. *ClusterConfig.X509.DevCluster.JSON* e *ClusterConfig.X509.MultiMachine.JSON* mostram como criar um cluster de teste ou de produção protegido usando a [segurança baseada no certificado X509](service-fabric-windows-cluster-x509-security.md). 

Agora, vamos examinar as várias seções de um arquivo ***ClusterConfig.JSON*** conforme mostrado a seguir.

## <a name="general-cluster-configurations"></a>Configurações gerais do cluster
Isso abrange as configurações específicas mais amplas do cluster, conforme mostra o trecho de JSON abaixo.

    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",

Você pode atribuir qualquer nome amigável ao cluster do Service Fabric, atribuindo a ele a variável **name** . O **clusterConfigurationVersion** é o número de versão do cluster; você deve aumentá-lo toda vez que atualizar seu cluster do Service Fabric. No entanto, deixe a **apiVersion** com o valor padrão.

<a id="clusternodes"></a>

## <a name="nodes-on-the-cluster"></a>Nós no cluster
Você pode configurar os nós no cluster de seu Service Fabric usando a seção **nós** , como mostra o trecho de código a seguir.

    "nodes": [{
        "nodeName": "vm0",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType1",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType2",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],

Um cluster do Service Fabric deve conter pelo menos 3 nós. Você pode adicionar mais nós a esta seção de acordo com a sua configuração. A tabela a seguir explica as definições de configuração para cada nó.

| **Configuração de nó** | **Descrição** |
| --- | --- |
| nodeName |Você pode atribuir qualquer nome amigável ao nó. |
| iPAddress |Descubra o endereço IP do seu nó abrindo uma janela de comando e digitando `ipconfig`. Anote o endereço IPV4 e atribua a ele a variável **iPAddress** . |
| nodeTypeRef |Cada nó pode ser receber um tipo de nó diferente. Os [tipos de nó](#nodetypes) são definidos na seção abaixo. |
| faultDomain |Os domínios de falha permitem que os administradores de cluster definam os nós físicos que podem falhar ao mesmo tempo devido às dependências físicas compartilhadas. |
| upgradeDomain |Os domínios de atualização descrevem conjuntos de nós que são desligados para atualizações do Service Fabric quase ao mesmo tempo. Você pode escolher quais nós atribuir a quais Domínios de atualização, pois eles não são limitados por quaisquer requisitos físicos. |

## <a name="cluster-properties"></a>Propriedades do cluster
A seção **propriedades** no ClusterConfig.JSON é usada para configurar o cluster da seguinte maneira.

<a id="reliability"></a>

### <a name="reliability"></a>Confiabilidade
O conceito de **reliabilityLevel** define o número de réplicas ou as instâncias dos serviços de sistema do Service Fabric que podem ser executados em nós do cluster primários. Determina a confiabilidade desses serviços e, portanto, do cluster. O valor é calculado pelo sistema em tempo de criação e atualização de cluster.

### <a name="diagnostics"></a>Diagnostics
A seção **diagnosticsStore** permite configurar parâmetros para habilitar o diagnóstico e solucionar problemas de falhas de nó e do cluster, conforme mostra o trecho a seguir. 

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
    }

Os **metadados** são uma descrição do diagnóstico do cluster e podem ser definidos de acordo com sua configuração. Essas variáveis ajudam na coleta de logs de rastreamento ETW, despejos de memória e contadores de desempenho. Leia [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) e [Rastreamento ETW](https://msdn.microsoft.com/library/ms751538.aspx) para saber mais sobre os logs de rastreamento de ETW. Todos os logs que incluem [Despejos de memória](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) e [contadores de desempenho](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) podem ser direcionados para a pasta **connectionString** em seu computador. Você também pode usar *AzureStorage* para o armazenamento de diagnóstico. Veja abaixo um exemplo de trecho de código.

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "AzureStorage",
        "IsEncrypted": "false",
        "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
    }

### <a name="security"></a>Segurança
A seção **segurança** é necessária para um cluster autônomo seguro do Service Fabric. O trecho a seguir mostra uma parte desta seção.

    "security": {
        "metadata": "This cluster is secured using X509 certificates.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        . . .
    }

Os **metadados** são uma descrição de seu cluster seguro e podem ser definidos de acordo com sua configuração. O **ClusterCredentialType** e o **ServerCredentialType** determinam o tipo de segurança que o cluster e os nós implementarão. Eles podem ser definidos como *X509* para segurança baseada em certificados ou como *Windows* para segurança baseada no Azure Active Directory. O restante da seção **segurança** será baseado no tipo de segurança. Leia [Segurança baseada em certificados em um cluster autônomo](service-fabric-windows-cluster-x509-security.md) ou [Segurança do Windows em um cluster autônomo](service-fabric-windows-cluster-windows-security.md) para saber como preencher o restante da seção de **segurança**.

<a id="nodetypes"></a>

### <a name="node-types"></a>Tipos de nó
A seção **nodeTypes** descreve o tipo de nó que seu cluster tem. Pelo menos um tipo de nó deve ser especificado para um cluster, como mostrado no fragmento a seguir. 

    "nodeTypes": [{
        "name": "NodeType0",
        "clientConnectionEndpointPort": "19000",
        "clusterConnectionEndpointPort": "19001",
        "leaseDriverEndpointPort": "19002"
        "serviceConnectionEndpointPort": "19003",
        "httpGatewayEndpointPort": "19080",
        "reverseProxyEndpointPort": "19081",
        "applicationPorts": {
            "startPort": "20575",
            "endPort": "20605"
        },
        "ephemeralPorts": {
            "startPort": "20606",
            "endPort": "20861"
        },
        "isPrimary": true
    }]

O **nome** é o nome amigável para esse tipo de nó específico. Para criar um nó desse tipo, atribua o nome amigável à variável **nodeTypeRef** para esse nó, conforme mencionado [acima](#clusternodes). Para cada tipo de nó, defina os pontos de extremidade de conexão que serão usados. Você pode escolher qualquer número de porta para esses pontos de extremidade de conexão, desde que eles não entrem em conflito com qualquer outro ponto de extremidade neste cluster. Em um cluster de vários nós, haverá um ou mais nós primários (ou seja, **isPrimary** definido como *true*), dependendo do [**reliabilityLevel**](#reliability). Leia [Considerações de planejamento de capacidade de cluster do Service Fabric](service-fabric-cluster-capacity.md) para obter informações sobre **nodeTypes** e **reliabilityLevel**, bem como para saber quais são os tipos de nó primários e não primários. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Pontos de extremidade usados para configurar os tipos de nó
* *clientConnectionEndpointPort* é a porta usada pelo cliente para se conectar ao cluster, ao usar as APIs de cliente. 
* *clusterConnectionEndpointPort* é a porta na qual os nós se comuniquem entre si.
* *leaseDriverEndpointPort* é a porta usada pelo driver de concessão de cluster para descobrir se os nós ainda estão ativos. 
* *serviceConnectionEndpointPort* é a porta usada pelos aplicativos e serviços implantados em um nó, para se comunicar com o cliente do Service Fabric no nó específico.
* *httpGatewayEndpointPort* é a porta usada pelo Service Fabric Explorer para se conectar ao cluster.
* *ephemeralPorts* substituem as [portas dinâmicas usadas pelo sistema operacional](https://support.microsoft.com/kb/929851). O Service Fabric usará parte dessas portas como portas do aplicativo e o restante estará disponível para o SO. Ele também mapeará esse intervalo para o intervalo existente presente no SO. Então, para todas as finalidades, você pode usar os intervalos fornecidos nos arquivos JSON de exemplo. Você precisa certificar-se de que a diferença entre as portas de início e de fim é pelo menos 255. Você poderá encontrar conflitos se a diferença for muito baixa, uma vez que esse intervalo é compartilhado com o sistema operacional. Veja o intervalo de portas dinâmicas configurado executando `netsh int ipv4 show dynamicport tcp`.
* *applicationPorts* são portas que serão usadas pelos aplicativos do Service Fabric. O intervalo de portas do aplicativo deve ser amplo o bastante para cobrir o requisito de ponto de extremidade dos aplicativos. Esse intervalo deve ser exclusivo no intervalo de portas dinâmico no computador, isto é, o intervalo *ephemeralPorts* conforme definido na configuração.  O Service Fabric usará essas portas sempre que novas portas forem necessárias, bem como cuidará de abrir o firewall para essas portas. 
* *reverseProxyEndpointPort* é um ponto de extremidade de proxy reverso opcional. Consulte [Reverter Proxy do Service Fabric](service-fabric-reverseproxy.md) para obter mais detalhes. 

### <a name="log-settings"></a>Configurações de log
A seção **fabricSettings** permite que você defina os diretórios raiz para os dados e logs do Service Fabric. Você pode personalizá-los somente durante a criação inicial do cluster. Veja abaixo um exemplo de trecho de código desta seção.

    "fabricSettings": [{
        "name": "Setup",
        "parameters": [{
            "name": "FabricDataRoot",
            "value": "C:\\ProgramData\\SF"
        }, {
            "name": "FabricLogRoot",
            "value": "C:\\ProgramData\\SF\\Log"
    }]

Recomendamos usar uma unidade não de SO, como FabricDataRoot e FabricLogRoot, pois isso proporciona mais confiabilidade contra falhas do SO. Observe que se você personalizar somente a raiz dos dados, a raiz do log será colocada um nível abaixo da raiz dos dados.

### <a name="stateful-reliable-service-settings"></a>Configurações de Reliable Service com estado
A seção **KtlLogger** permite que você defina as configurações globais dos Reliable Services. Para obter mais detalhes sobre essas configurações, leia [Configurar o Reliable Services com estado](service-fabric-reliable-services-configuration.md).
O exemplo a seguir mostra como alterar o log de transações compartilhado que é criado para dar apoio às coleções confiáveis para serviços com estado.

    "fabricSettings": [{
        "name": "KtlLogger",
        "parameters": [{
            "name": "SharedLogSizeInMB",
            "value": "4096"
        }]
    }]

### <a name="add-on-features"></a>Recursos de complemento
Para configurar recursos de complemento, a apiVersion deve ser configurada como ' 04-2017' ou superior e addonFeatures precisa ser configurado:

    "apiVersion": "04-2017",
    "properties": {
      "addOnFeatures": [
          "DnsService",
          "RepairManager"
      ]
    }

### <a name="container-support"></a>Suporte a contêiner
Para habilitar o suporte de contêiner para o contêiner do windows server e o contêiner do hyper-v para clusters autônomos, o recurso de complemento 'DnsService' deve ser habilitado.


## <a name="next-steps"></a>Próximas etapas
Depois de configurar um arquivo ClusterConfig.JSON completo de acordo com a configuração do cluster independente, é possível implantar o cluster seguindo o artigo [Criar e gerenciar um cluster em execução no Windows Server](service-fabric-cluster-creation-for-windows-server.md) e continuando com [Visualizando o cluster com o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

