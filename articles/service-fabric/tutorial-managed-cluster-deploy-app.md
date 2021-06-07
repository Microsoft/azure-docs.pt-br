---
title: Implante um aplicativo em um cluster gerenciado do Service Fabric por meio do PowerShell (versão prévia)
description: Neste tutorial, você se conectará a um cluster gerenciado do Service Fabric e implantará um aplicativo por meio do PowerShell.
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: 36a91d2852bcda5f958441b48ef4721d6ccc83c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91410182"
---
# <a name="tutorial-deploy-an-app-to-a-service-fabric-managed-cluster-preview"></a>Tutorial: Implantar um aplicativo a um cluster gerenciado do Service Fabric (versão prévia)

Nesta série de tutoriais, discutiremos:

> [!div class="checklist"]
> * [Como implantar um cluster gerenciado do Service Fabric](tutorial-managed-cluster-deploy.md)
> * [Como escalar horizontalmente um cluster gerenciado do Service Fabric](tutorial-managed-cluster-scale.md)
> * [Como adicionar e remover nós em um cluster gerenciado do Service Fabric](tutorial-managed-cluster-add-remove-node-type.md)
> * Como implantar um aplicativo em um cluster gerenciado do Service Fabric

Esta parte da série aborda como:

> [!div class="checklist"]
> * Conectar-se ao cluster gerenciado do Service Fabric
> * Carregar um aplicativo em um cluster
> * Criar uma instância de um aplicativo em um cluster
> * Remover um aplicativo de um cluster

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster gerenciado do Service Fabric (confira [*Implantar um cluster gerenciado*](tutorial-managed-cluster-deploy.md)).

## <a name="connect-to-your-cluster"></a>Conectar-se ao seu cluster

Para se conectar ao cluster, você precisará da impressão digital do certificado do cluster. Você poderá encontrar esse valor na saída das propriedades do cluster da sua implantação de recursos ou consultando as propriedades do cluster em um recurso existente.

O comando a seguir pode ser usado para consultar o recurso de cluster para a impressão digital do certificado de cluster.

```powershell
$serverThumbprint = (Get-AzResource -ResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ServiceFabric/managedclusters/mysfcluster).Properties.clusterCertificateThumbprint
```

Com a impressão digital do certificado de cluster, você está pronto para se conectar ao cluster.

```powershell
$connectionEndpoint = "mysfcluster.eastus2.cloudapp.azure.com:19000"
Connect-ServiceFabricCluster -ConnectionEndpoint $connectionEndpoint -KeepAliveIntervalInSec 10 `
      -X509Credential `
      -ServerCertThumbprint $serverThumbprint  `
      -FindType FindByThumbprint `
      -FindValue $clientThumbprint `
      -StoreLocation CurrentUser `
      -StoreName My

```

### <a name="upload-an-application-package"></a>Carregar um pacote de aplicativos

Neste tutorial, usaremos o exemplo de [Aplicativo de Votação do Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/voting-sample-no-reverse-proxy). Para obter mais detalhes sobre a implantação de aplicativo do Service Fabric por meio do PowerShell, confira [Implantar e remover aplicativos do Service Fabric](service-fabric-deploy-remove-applications.md).

> [!NOTE]
> Na versão prévia do cluster gerenciado do Service Fabric, você não poderá publicar aplicativos diretamente do Visual Studio.

Primeiro, você precisará [empacotar o aplicativo para implantação](service-fabric-package-apps.md). Neste tutorial, siga as etapas para empacotar um aplicativo de dentro do Visual Studio. É importante anotar o caminho no qual o aplicativo foi empacotado, pois ele será usado para o caminho abaixo.

Quando o pacote de aplicativos tiver sido criado, você poderá carregar o pacote de aplicativos no cluster. Atualize o valor de `$path` para representar o caminho no qual o pacote de aplicativos existe e execute o seguinte:

```powershell
$path = "C:\Users\<user>\Documents\service-fabric-dotnet-quickstart\Voting\pkg\Debug"
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage
Register-ServiceFabricApplicationType -ApplicationPathInImageStore Debug
```

### <a name="create-an-application"></a>Criar um aplicativo

É possível instanciar um aplicativo de qualquer versão do tipo de aplicativo registrada com êxito usando o cmdlet New-ServiceFabricApplication. O nome de cada aplicativo deve começar com o esquema "fabric:" e ser exclusivo para cada instância do aplicativo. Quaisquer serviços padrão definidos no manifesto do aplicativo do tipo de aplicativo de destino também são criados.

```powershell
New-ServiceFabricApplication fabric:/Voting VotingType 1.0.0
```

Quando essa operação for concluída, você deverá ver as instâncias do aplicativo em execução no Service Fabric Explorer.

### <a name="remove-an-application"></a>Remover um aplicativo

Quando uma instância do aplicativo não for mais necessária, você poderá removê-la permanentemente por nome usando o cmdlet `Remove-ServiceFabricApplication`, que também remove automaticamente todos os serviços que pertencem ao aplicativo, removendo permanentemente todo o estado do serviço.

```powershell
Remove-ServiceFabricApplication fabric:/Voting
```

## <a name="next-steps"></a>Próximas etapas

Nesta etapa, implantamos um aplicativo a um cluster gerenciado do Service Fabric. Para saber mais sobre os clusters gerenciados do Service Fabric, confira:

> [!div class="nextstepaction"]
> [Perguntas mais frequentes sobre os clusters gerenciados do Service Fabric](faq-managed-cluster.md)
