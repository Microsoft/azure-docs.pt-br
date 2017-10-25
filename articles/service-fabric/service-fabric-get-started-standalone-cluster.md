---
title: "Configurar um cluster autônomo do Azure Service Fabric | Microsoft Docs"
description: "Crie um cluster de desenvolvimento autônomo com três nós em execução no mesmo computador. Depois de concluir a instalação, você estará pronto para criar um cluster com várias máquinas."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/06/2017
ms.author: dekapur
ms.openlocfilehash: 5c8f4c784eed7b64810a3dd1c36c043d22a66936
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-service-fabric-standalone-cluster"></a>Criar seu primeiro cluster autônomo do Service Fabric
Você pode criar um cluster autônomo do Service Fabric em máquinas virtuais ou computadores que executam o Windows Server 2012 R2 ou Windows Server 2016, localmente ou na nuvem. Este guia de início rápido ajuda a criar um cluster autônomo de desenvolvimento em apenas alguns minutos.  Quando terminar, você terá um cluster de três nós em execução em um único computador no qual poderá implantar aplicativos.

## <a name="before-you-begin"></a>Antes de começar
O Service Fabric fornece um pacote de instalação para criar clusters autônomos do Service Fabric.  [Baixar o pacote de instalação](http://go.microsoft.com/fwlink/?LinkId=730690).  Descompacte o pacote de instalação em uma pasta no computador ou máquina virtual na qual você está configurando o cluster de desenvolvimento.  O conteúdo do pacote de instalação é descrito em detalhes [aqui](service-fabric-cluster-standalone-package-contents.md).

O administrador do cluster que implanta e configura o cluster deve ter privilégios de administrador no computador. Você não pode instalar o Service Fabric em um controlador de domínio.

## <a name="validate-the-environment"></a>Validar o ambiente
O script *TestConfiguration.ps1* no pacote autônomo é usado como um analisador de práticas recomendadas para validar se um cluster pode ser implantado em um ambiente específico. [A preparação para implantação](service-fabric-cluster-standalone-deployment-preparation.md) lista os pré-requisitos e requisitos do ambiente. Execute o script para verificar se você pode criar o cluster de desenvolvimento:

```powershell
.\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
```
## <a name="create-the-cluster"></a>Criar o cluster
Vários arquivos de configuração de cluster de exemplo são instalados com o pacote de instalação. *ClusterConfig.Unsecure.DevCluster.json* é a configuração de cluster mais simples: um cluster não seguro de três nós em execução em um único computador.  Outros arquivos de configuração descrevem clusters únicos ou vários computadores protegidos com certificados x. 509 ou com a segurança do Windows.  Você não precisa modificar nenhuma definição de configuração padrão para este tutorial, mas examine o arquivo config e familiarizar-se com as configurações.  A seção **Nós** descreve os três nós no cluster: nome, endereço IP, [tipo de nó, domínio de falha e domínio de atualização](service-fabric-cluster-manifest.md#nodes-on-the-cluster).  A seção **Propriedades** define a [segurança, nível de confiabilidade, coleta de diagnóstico e tipos de nós](service-fabric-cluster-manifest.md#cluster-properties) para o cluster.

Esse cluster não é seguro.  Qualquer pessoa pode conectar-se anonimamente e realizar operações de gerenciamento, portanto, os clusters de produção sempre devem ser protegidos usando os certificados x.509 ou a segurança do Windows.  A segurança só é configurada no momento de criação do cluster e não é possível habilitar a segurança após a criação dele.  Leia [Proteger um cluster](service-fabric-cluster-security.md) para saber mais sobre a segurança do cluster do Service Fabric.  

Para criar o cluster de desenvolvimento de três nós, execute o script *CreateServiceFabricCluster.ps1* em uma sessão de administrador do PowerShell:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

O pacote de tempo de execução do Service Fabric é baixado e instalado automaticamente no momento da criação do cluster.

## <a name="connect-to-the-cluster"></a>Conectar-se ao cluster
O cluster de desenvolvimento de três nós agora está em execução. O módulo ServiceFabric do PowerShell é instalado no tempo de execução.  Você pode verificar se o cluster está em execução no mesmo computador ou em um computador remoto com o tempo de execução do Service Fabric.  O cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) estabelece uma conexão com o cluster.   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint localhost:19000
```
Confira [Conectar-se a um cluster seguro](service-fabric-connect-to-secure-cluster.md) para obter outros exemplos de como se conectar a um cluster. Depois de se conectar ao cluster, use o script [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) para exibir uma lista de nós de cluster e informações de status para cada nó. **HealthState** deve ser *OK* para cada nó.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- -------- --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     vm2      localhost       NodeType2 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm1      localhost       NodeType1 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm0      localhost       NodeType0 5.6.220.9494 0                     Up 00:02:43   00:00:00              OK
```

## <a name="visualize-the-cluster-using-service-fabric-explorer"></a>Visualizar o cluster usando o Service Fabric Explorer
O [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) é uma boa ferramenta para visualizar o cluster e gerenciar os aplicativos.  O Service Fabric Explorer é um serviço que é executado no cluster, que você acessa usando um navegador, navegando até [http://localhost:19080/Explorer](http://localhost:19080/Explorer). 

O painel do cluster fornece uma visão geral do cluster, incluindo um resumo do aplicativo e a integridade do nó. A exibição de nós mostra o layout físico do cluster. Para um nó específico, você pode inspecionar quais aplicativos têm código implantado naquele nó.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="remove-the-cluster"></a>Remover o cluster
Para remover um cluster, execute o script do PowerShell *RemoveServiceFabricCluster.ps1* da pasta do pacote e transmita o caminho para o arquivo de configuração JSON. Como alternativa, você pode especificar um local para o log de exclusão.

```powershell
# Removes Service Fabric cluster nodes from each computer in the configuration file.
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -Force
```

Para remover o tempo de execução do Service Fabric do computador, execute o script do PowerShell a seguir da pasta do pacote.

```powershell
# Removes Service Fabric from the current computer.
.\CleanFabric.ps1
```

## <a name="next-steps"></a>Próximas etapas
Agora que você configurou um cluster de desenvolvimento autônomo, experimente os seguintes artigos:
* [Configurar um cluster de vários computadores autônomos](service-fabric-cluster-creation-for-windows-server.md) e habilitar a segurança.
* [Implantar aplicativos usando o PowerShell](service-fabric-deploy-remove-applications.md)

[service-fabric-explorer]: ./media/service-fabric-get-started-standalone-cluster/sfx.png
