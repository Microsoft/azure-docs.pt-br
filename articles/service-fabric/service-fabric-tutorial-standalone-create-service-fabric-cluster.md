---
title: Tutorial de instalação do cliente do Service Fabric autônomo - Azure Service Fabric | Microsoft Docs
description: Neste tutorial você aprenderá como instalar o cliente do Service Fabric autônomo no cluster que você criou no artigo do tutorial anterior.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 82358f4d5548464f1304d77a846d71863ed9efa1
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385011"
---
# <a name="tutorial-install-and-create-service-fabric-cluster"></a>Tutorial: Instalar e criar o cluster do Service Fabric

Os clusters autônomos do Service Fabric oferecem a opção de escolher seu próprio ambiente e criar um cluster como parte da abordagem “qualquer sistema operacional, qualquer nuvem” que está sendo adotada pelo Service Fabric. Nesta série de tutoriais, você cria um cluster autônomo hospedado no AWS ou no Azure e instala um aplicativo nele.

Este tutorial é a parte dois de uma série. Este tutorial guia você pelas etapas para criação de um cluster do Service Fabric.

Na segunda parte da série, você aprenderá como:

> [!div class="checklist"]
> * Baixar e instalar o pacote autônomo do Service Fabric
> * Criar o cluster do Service Fabric
> * Conectar-se ao cluster do Service Fabric

## <a name="download-the-service-fabric-for-windows-server-package"></a>Baixar o pacote do Service Fabric para Windows Server

O Service Fabric fornece um pacote de instalação para criar clusters autônomos do Service Fabric.  [Baixe o pacote de instalação](https://go.microsoft.com/fwlink/?LinkId=730690) no seu computador local.  Depois que ele tiver sido baixado com êxito copie-o sobre a conexão de RDP para a VM e cole-o na área de trabalho.

Selecione o arquivo zip e abra o menu de contexto e selecione **Extrair tudo** > **Extrair**.  Ao extrair os arquivos, você irá gerar uma pasta na área de trabalho com o mesmo nome do arquivo zip.

Se você quiser obter mais detalhes sobre o [conteúdo do pacote de instalação](service-fabric-cluster-standalone-package-contents.md).

## <a name="set-up-your-configuration-file"></a>Definir o arquivo de configuração

Você está criando um cluster do Windows de três nós, portanto você precisa modificar o arquivo `ClusterConfig.Unsecure.MultiMachine.json`.

Em seguida, atualize as três linhas de endereço IP que ocorrem no arquivo nas linhas 8, 15 e 22 para os endereços IP para cada uma das instâncias.

Depois de atualizar os nós, eles são exibidos da seguinte maneira:

```json
        {
            "nodeName": "vm0",
            "ipAddress": "172.31.27.1",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        }
```

Em seguida, você precisa atualizar algumas das propriedades.  Na linha 34, você precisa modificar a cadeia de conexão para o armazenamento de diagnóstico que deverá se parecer assim `"connectionstring": "C:\\ProgramData\\SF\\DiagnosticsStore"`

Por fim, na seção `nodeTypes` da configuração adicione uma nova seção para mapear as portas efêmeras que serão usadas pelo Windows.  O arquivo de configuração deve parecer com o seguinte:

```json
"applicationPorts": {
    "startPort": "20001",
    "endPort": "20031"
},
"ephemeralPorts": {
    "startPort": "20606",
    "endPort": "20861"
},
"isPrimary": true
```

## <a name="validate-the-environment"></a>Validar o ambiente

O script *TestConfiguration.ps1* no pacote autônomo é usado como um analisador de práticas recomendadas para validar se um cluster pode ser implantado em um ambiente específico. [A preparação para implantação](service-fabric-cluster-standalone-deployment-preparation.md) lista os pré-requisitos e requisitos do ambiente. Execute o script para verificar se você pode criar o cluster de desenvolvimento:

```powershell
cd .\Desktop\Microsoft.Azure.ServiceFabric.WindowsServer.6.2.274.9494\
.\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
```

Você deverá ver uma saída como a abaixo. Se o campo inferior “Passed” é retornado como `True`, as verificações de integridade são aprovadas e o cluster parece estar pronto para implantação com base na configuração de entrada.

```powershell
Trace folder already exists. Traces will be written to existing trace folder: C:\Users\Administrator\Desktop\Microsoft.Azure.ServiceFabric.WindowsServer.6.2.274.9494\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 :
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
DataDrivesAvailable        : True
NoDomainController         : True
Passed                     : True
```

## <a name="create-the-cluster"></a>Criar o cluster

Depois que você tiver validado com êxito sua configuração de cluster execute o script *CreateServiceFabricCluster.ps1* para implantar o cluster do Service Fabric para as máquinas virtuais no arquivo de configuração.

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -AcceptEULA
```

Se tudo funcionar, você receberá uma saída parecida com esta:

```powershell
Your cluster is successfully created! You can connect and manage your cluster using Microsoft Azure Service Fabric Explorer or PowerShell. To connect through PowerShell, run 'Connect-ServiceFabricCluster [ClusterConnectionEndpoint]'.
```

> [!NOTE]
> Rastreamentos de implantação são gravados na VM/computador em que você executou o script do PowerShell CreateServiceFabricCluster.ps1. Eles podem ser encontrados na subpasta DeploymentTraces, com base no diretório do qual o script foi executado. Para ver se o Service Fabric foi implantado corretamente em um computador, localize os arquivos instalados no diretório FabricDataRoot, conforme detalhado na seção FabricSettings do arquivo de configuração de cluster (por padrão, c:\ProgramData\SF). Além disso, processos FabricHost.exe e Fabric.exe podem ser vistos em execução no Gerenciador de Tarefas.
>
>

### <a name="bring-up-service-fabric-explorer"></a>Abrir o Service Fabric Explorer

Agora, você pode se conectar ao cluster com o Service Fabric Explorer diretamente de um dos computadores com http:\//localhost:19080/Explorer/index.html ou remotamente com http:\//<*IPAddressofaMachine*>:19080/Explorer/index.html.

## <a name="add-and-remove-nodes"></a>Adicionar e remover nós

Você pode adicionar ou remover os nós do cluster do Service Fabric autônomo quando seu negócio precisa mudar. Confira [Adicionar ou remover nós de um cluster do Service Fabric autônomo](service-fabric-cluster-windows-server-add-remove-nodes.md) para ver as etapas detalhadas.

## <a name="next-steps"></a>Próximas etapas

Na parte dois da série, você aprendeu sobre o carregamento de grandes quantidades de dados aleatórios para uma conta de armazenamento em paralelo, por exemplo como:

> [!div class="checklist"]
> * Configurar a cadeia de conexão
> * Compilar o aplicativo
> * Executar o aplicativo
> * Validar o número de conexões

Avance para a terceira parte da série para instalar um aplicativo no cluster que você criou.

> [!div class="nextstepaction"]
> [Instale o aplicativo no cluster do Service Fabric](service-fabric-tutorial-standalone-install-an-application.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
