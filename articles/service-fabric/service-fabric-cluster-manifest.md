---
title: Configurar o cluster autônomo do Azure Service Fabric
description: Aprenda a configurar seu cluster autônomo ou local do Azure Service Fabric.
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: fd93263b38340ce080cca1aecb98f3a599ff1861
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843151"
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>Definições de configuração para um cluster autônomo no Windows
Este artigo descreve as definições de configuração de um cluster autônomo do Service Fabric do Azure que pode ser definido no arquivo *ClusterConfig.json*. Este arquivo será utilizado para especificar informações sobre os nós do cluster, as configurações de segurança, assim como a topologia de rede em termos de domínio de atualização e falha.  Depois de alterar ou adicionar definições de configuração, você pode [Criar um cluster autônomo](service-fabric-cluster-creation-for-windows-server.md) ou [atualizar a configuração de um cluster autônomo](service-fabric-cluster-config-upgrade-windows-server.md).

Ao [fazer o download do pacote do Service Fabric autônomo](service-fabric-cluster-creation-for-windows-server.md#downloadpackage), os exemplos ClusterConfig.json também são incluídos. As exemplos com "DevCluster" em seus nomes criam um cluster com os três nós no mesmo computador, utilizando nós lógicos. Desses nós, pelo menos um deve ser marcado como nó principal. Esse tipo de cluster é útil para ambientes de teste ou desenvolvimento. Ele não tem suporte como um cluster de produção. Os exemplos que possuem "MultiMachine" em seus nomes ajudam a criar clusters de nível de produção, com cada nó em um computador separado. O número de nós primários para esses clusters é baseado no [nível de confiabilidade](#reliability) do cluster. Na API versão 5.7 de 05-2017, removemos a propriedade do nível de confiabilidade. Em vez disso, nosso código calcula o nível de confiabilidade mais otimizado para seu cluster. Não tente definir um valor para essa propriedade em versões 5.7 em diante.

* ClusterConfig.Unsecure.DevCluster.json e lusterConfig.Unsecure.MultiMachine.json mostram como criar um cluster de teste ou de produção sem segurança, respectivamente.

* ClusterConfig.Windows.DevCluster.jse ClusterConfig.Windows.MultiMachine.jsem mostrar como criar clusters de teste ou de produção que são protegidos usando a [segurança do Windows](service-fabric-windows-cluster-windows-security.md).

* ClusterConfig.X509.DevCluster.jse ClusterConfig.X509.MultiMachine.jsem mostrar como criar clusters de teste ou de produção que são protegidos usando [a segurança baseada em certificado X509](service-fabric-windows-cluster-x509-security.md).

Agora, vamos examinar as várias seções de um arquivo ClusterConfig.json.

## <a name="general-cluster-configurations"></a>Configurações gerais do cluster
As configurações de cluster geral abordam as configurações gerais específicas do cluster, conforme mostrado no seguinte snippet JSON:

```json
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",
```

Você pode atribuir qualquer nome amigável ao cluster do Service Fabric, atribuindo a ele a variável name. O clusterConfigurationVersion é o número de versão do cluster. Aumente-o toda vez que você atualizar seu cluster do Service Fabric. Deixe apiVersion com o valor padrão.

## <a name="nodes-on-the-cluster"></a>Nós no cluster
Você pode configurar os nós no cluster de seu Service Fabric usando a seção nodes, como mostra o snippet de código a seguir:
```json
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
```

Um cluster do Service Fabric deve conter pelo menos três nós. Você pode adicionar mais nós a esta seção de acordo com a sua configuração. A tabela abaixo explica as definições de configuração para cada nó:

| **Configuração do nó** | **Descrição** |
| --- | --- |
| nodeName |Você pode atribuir qualquer nome amigável ao nó. |
| iPAddress |Descubra o endereço IP do seu nó abrindo uma janela de comando e digitando `ipconfig`. Anote o endereço IPV4 e atribua a ele a variável iPAddress. |
| nodeTypeRef |Cada nó pode ser receber um tipo de nó diferente. Os [tipos de nó](#node-types) são definidos na seção a seguir. |
| faultDomain |Os domínios de falha permitem que os administradores de cluster definam os nós físicos que podem falhar ao mesmo tempo devido às dependências físicas compartilhadas. |
| upgradeDomain |Os domínios de atualização descrevem conjuntos de nós que são desligados para atualizações do Service Fabric quase ao mesmo tempo. Você pode escolher quais nós atribuir a quais domínios de atualização, pois eles não são limitados por requisitos físicos. |

## <a name="cluster-properties"></a>Propriedades do cluster
A seção propriedades no ClusterConfig.json é usada para configurar o cluster da seguinte maneira:

### <a name="reliability"></a>Confiabilidade
O conceito de reliabilityLevel define o número de réplicas ou as instâncias dos serviços de sistema do Service Fabric que podem ser executados em nós do cluster primários. Determina a confiabilidade desses serviços e, portanto, do cluster. O valor é calculado pelo sistema na hora da criação e da atualização do cluster.

### <a name="diagnostics"></a>Diagnósticos
Na seção diagnosticsStore, você pode configurar parâmetros para habilitar o diagnóstico e solucionar problemas de falhas de nó e do cluster, conforme mostra o seguinte snippet de código: 

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "FileShare",
    "IsEncrypted": "false",
    "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
}
```

Os metadados são uma descrição do diagnóstico do cluster e podem ser definidos de acordo com sua configuração. Essas variáveis ajudam na coleta de logs de rastreamento ETW e despejos de memória, além de contadores de desempenho. Para saber mais sobre os logs de rastreamento de ETW, leia [Tracelog](/windows-hardware/drivers/devtest/tracelog) e [Rastreamento ETW](/dotnet/framework/wcf/samples/etw-tracing). Todos os logs que incluem [despejos de memória](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf) e [contadores de desempenho](/windows/win32/perfctrs/performance-counters-portal) podem ser direcionados para a pasta connectionString em seu computador. Você também pode usar AzureStorage para armazenar diagnósticos. Consulte o seguinte snippet de código de exemplo:

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "AzureStorage",
    "IsEncrypted": "false",
    "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
}
```

### <a name="security"></a>Segurança
A seção security é necessária para um cluster autônomo seguro do Service Fabric. O snippet abaixo mostra uma parte desta seção:

```json
"security": {
    "metadata": "This cluster is secured using X509 certificates.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    . . .
}
```

Os metadados são uma descrição de seu cluster seguro e podem ser definidos de acordo com sua configuração. O ClusterCredentialType e o ServerCredentialType determinam o tipo de segurança que o cluster e os nós implementam. Eles podem ser definidos como *X509* para uma segurança baseada em certificado ou *Windows* para segurança baseada em Active Directory. O restante da seção segurança se baseia no tipo de segurança. Para saber como preencher o restante da seção de segurança, confira [Segurança baseada em certificados em um cluster autônomo](service-fabric-windows-cluster-x509-security.md) ou [Segurança do Windows em um cluster autônomo](service-fabric-windows-cluster-windows-security.md).

### <a name="node-types"></a>Tipos de nó
A seção nodeTypes descreve o tipo de nó que seu cluster tem. Pelo menos um tipo de nó deve ser especificado para um cluster, como mostrado no seguinte snippet de código: 

```json
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
```

O nome é o nome amigável para esse tipo de nó específico. Para criar um nó desse tipo, atribua o nome amigável à variável nodeTypeRef para esse nó, [conforme mencionado anteriormente](#nodes-on-the-cluster). Para cada tipo de nó, defina os pontos de extremidade de conexão que serão usados. Você pode escolher qualquer número da porta para esses pontos de extremidade de conexão, desde que eles não entrem em conflito com qualquer outro ponto de extremidade neste cluster. Em um cluster de vários nós, há um ou mais nós primários (ou seja, isPrimary definido como *true*), dependendo do [reliabilityLevel](#reliability). Para saber quais são os tipos de nó primários e não primários, confira [Considerações de planejamento de capacidade de cluster do Service Fabric](service-fabric-cluster-capacity.md) para saber mais sobre nodeTypes e reliabilityLevel. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Pontos de extremidade usados para configurar os tipos de nó
* clientConnectionEndpointPort é a porta usada pelo cliente para se conectar ao cluster quando as APIs de cliente são usadas. 
* clusterConnectionEndpointPort é a porta na qual os nós se comunicam entre si.
* leaseDriverEndpointPort é a porta usada pelo driver de concessão de cluster para descobrir se os nós ainda estão ativos. 
* serviceConnectionEndpointPort é a porta usada pelos aplicativos e serviços implantados em um nó, para se comunicar com o cliente do Service Fabric no nó específico.
* httpGatewayEndpointPort é a porta usada pelo Service Fabric Explorer para se conectar ao cluster.
* ephemeralPorts substituem as [portas dinâmicas usadas pelo sistema operacional](https://support.microsoft.com/kb/929851). O Service Fabric usa parte dessas portas como portas do aplicativo e o restante fica disponível para o SO. Ele também mapeia esse intervalo para o intervalo existente presente no SO. Então, para todas as finalidades, você pode usar os intervalos fornecidos nos arquivos JSON de exemplo. Verifique se a diferença entre as portas de início e de fim é de pelo menos 255. Você poderá encontrar conflitos se a diferença for muito baixa, uma vez que esse intervalo é compartilhado com o sistema operacional. Para ver o intervalo de portas dinâmicas configurado, execute `netsh int ipv4 show dynamicport tcp`.
* applicationPorts são portas usadas pelos aplicativos do Service Fabric. O intervalo de portas do aplicativo deve ser amplo o bastante para cobrir o requisito de ponto de extremidade dos aplicativos. Esse intervalo deve ser exclusivo no intervalo de portas dinâmico no computador, isto é, o intervalo ephemeralPorts conforme definido na configuração. O Service Fabric usará essas portas sempre que novas portas forem necessárias, e também cuidará de abrir o firewall para essas portas. 
* reverseProxyEndpointPort é um ponto de extremidade de proxy reverso opcional. Para saber mais, confira [proxy inverso do Service Fabric](service-fabric-reverseproxy.md). 

### <a name="log-settings"></a>Configurações de log
Na seção fabricSettings, você pode definir os diretórios raiz para os dados e logs do Service Fabric. Você só pode personalizar esses diretórios durante a criação inicial do cluster. Confira o seguinte snippet de código de exemplo da seção:

```json
"fabricSettings": [{
    "name": "Setup",
    "parameters": [{
        "name": "FabricDataRoot",
        "value": "C:\\ProgramData\\SF"
    }, {
        "name": "FabricLogRoot",
        "value": "C:\\ProgramData\\SF\\Log"
}]
```

Recomendamos que você use uma unidade que não seja a do SO, como o FabricDataRoot e o FabricLogRoot. Ela oferece mais confiabilidade para evitar situações de falta de resposta do SO. Se você personalizar somente a raiz dos dados, a raiz do log será colocada um nível abaixo da raiz dos dados.

### <a name="stateful-reliable-services-settings"></a>Configurações de serviço confiável com estado
Na seção KtlLogger, você pode definir as configurações globais dos Reliable Services. Para saber mais sobre essas configurações, confira [Configurar serviços confiáveis com estado](service-fabric-reliable-services-configuration.md). O exemplo a seguir mostra como alterar o log de transações compartilhado criado para fazer backup de quaisquer coleções confiáveis para serviços com estado:

```json
"fabricSettings": [{
    "name": "KtlLogger",
    "parameters": [{
        "name": "SharedLogSizeInMB",
        "value": "4096"
    }]
}]
```

### <a name="add-on-features"></a>Recursos de complemento
Para configurar recursos de complemento, configure a apiVersion de abril de 2017 ou mais recente e configure o addonFeatures conforme mostrado aqui:

```json
"apiVersion": "04-2017",
"properties": {
    "addOnFeatures": [
        "DnsService",
        "RepairManager"
    ]
}
```
Todos os recursos complementares disponíveis podem ser vistos na [referência da API REST do Service Fabric](/rest/api/servicefabric/sfrp-model-addonfeatures).

### <a name="container-support"></a>Suporte a contêiner
Para habilitar o suporte de contêiner para os contêineres do Windows Server e os contêineres do Hyper-V para clusters autônomos, o recurso de complemento DnsService precisa ser habilitado.

## <a name="next-steps"></a>Próximas etapas
Depois de ter uma *ClusterConfig.jscompleta no* arquivo configurado de acordo com a configuração do cluster autônomo, você pode implantar o cluster. Siga as etapas em [Criar um cluster do Service Fabric autônomo](service-fabric-cluster-creation-for-windows-server.md). 

Se você tiver um cluster autônomo implantado, você também pode [atualizar a configuração de um cluster autônomo](service-fabric-cluster-config-upgrade-windows-server.md). 

Saiba como [visualizar seu cluster com o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
