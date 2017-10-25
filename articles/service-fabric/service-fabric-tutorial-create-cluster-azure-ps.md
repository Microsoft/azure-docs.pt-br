---
title: Criar um cluster do Service Fabric no Azure | Microsoft Docs
description: Saiba como criar um cluster do Windows ou Linux do Service Fabric no Azure usando o PowerShell.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/18/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 86c01a55304c5f5179e0e94d67f318e42075fd48
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-windows-cluster-in-azure-using-powershell"></a>Criar um cluster do Windows no Azure usando o PowerShell
Este tutorial mostra como criar um cluster do Service Fabric do Windows em execução no Azure. Ao terminar, você terá um cluster em execução na nuvem no qual você poderá implantar aplicativos.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um cluster do Service Fabric seguro no Azure usando o PowerShell
> * Proteger o cluster com um certificado X.509
> * Conectar o cluster usando o PowerShell
> * Remover um cluster

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial:
- Se você não tem uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Instale o [SDK do Service Fabric e o módulo do PowerShell](service-fabric-get-started.md)
- Instale o [módulo do Azure PowerShell versão 4.1 ou superior](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)

O procedimento a seguir cria uma versão prévia (única máquina virtual) de nó único cluster do Service Fabric. O cluster é protegido por um certificado autoassinado que obtém criado juntamente com o cluster e, em seguida, é colocado em um cofre de chaves. Os clusters de nó único não podem ser dimensionados além de uma máquina virtual e clusters de versão prévia não podem ser atualizados para versões mais recentes.

Para calcular o custo incorrido ao executar um cluster do Service Fabric no Azure, use a [Calculadora de Preços do Azure](https://azure.microsoft.com/pricing/calculator/).
Para obter mais informações sobre a criação de clusters do Service Fabric, confira [Criar um cluster do Service Fabric usando o Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="create-the-cluster-using-azure-powershell"></a>Criar o cluster usando o Azure PowerShell
1. Baixe uma cópia local do modelo do Azure Resource Manager e o arquivo de parâmetro do repositório [modelo do Azure Resource Manager para o Service Fabric](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Tutorial) do GitHub.  O *azuredeploy.json* é o modelo do Azure Resource Manager que define um cluster do Service Fabric. O *azuredeploy.parameters.json* é um arquivo de parâmetros para personalizar a implantação do cluster.

2. Personalize os seguintes parâmetros no arquivo de parâmetros *azuredeploy.parameters.json*:

   | Parâmetro       | Descrição | Valor sugerido |
   | --------------- | ----------- | --------------- |
   | clusterLocation | A região do Azure na qual o cluster será implantado. | *Por exemplo, westeurope, eastasia, eastus* |
   | clusterName     | Nome do cluster que você deseja criar. | *Por exemplo, bobs-sfpreviewcluster* |
   | adminUserName   | A conta do administrador local nas máquinas virtuais do cluster. | *Qualquer nome de usuário válido do Windows Server* |
   | adminPassword   | Senha da conta do administrador local nas máquinas virtuais do cluster. | *Qualquer senha válida do Windows Server* |
   | clusterCodeVersion | A versão do Service Fabric a ser executada. (255.255.X.255 são versões de visualização). | **5.7.198.9494** |
   | vmInstanceCount | O número de máquinas virtuais no cluster (pode ser 1 ou 3-99). | **1** | *Para um cluster de visualização, especifique apenas uma máquina virtual* |

3. Abra um console do PowerShell, faça logon no Azure e selecione a assinatura na qual você deseja implantar o cluster:

   ```powershell
   Login-AzureRmAccount
   Select-AzureRmSubscription -SubscriptionId <subscription-id>
   ```
4. Crie e criptografe uma senha para o certificado a ser usado pelo Service Fabric.

   ```powershell
   $pwd = "<your password>" | ConvertTo-SecureString -AsPlainText -Force
   ```
5. Crie o cluster e seu certificado executando o seguinte comando:

   ```powershell
      New-AzureRmServiceFabricCluster `
          -TemplateFile C:\Users\me\Desktop\azuredeploy.json `
          -ParameterFile C:\Users\me\Desktop\azuredeploy.parameters.json `
          -CertificateOutputFolder C:\Users\me\Desktop\ `
          -CertificatePassword $pwd `
          -CertificateSubjectName "mycluster.westeurope.cloudapp.azure.com" `
          -ResourceGroupName myclusterRG
   ```

   >[!NOTE]
   >O parâmetro `-CertificateSubjectName` deve ser alinhado com o parâmetro clusterName, especificado no arquivo de parâmetros, bem como ao domínio vinculado à região do Azure escolhida, como `clustername.eastus.cloudapp.azure.com`.

Depois que a configuração for concluído, ele produz informações sobre o cluster criado no Azure. Ele também copia o certificado de cluster para o diretório - CertificateOutputFolder no caminho especificado para esse parâmetro. Você precisará deste certificado para acessar o Service Fabric Explorer e exibir a integridade do cluster.

Anote a URL para o cluster, o que pode ser semelhante a seguinte URL: *https://mycluster.westeurope.cloudapp.azure.com:19080*

## <a name="modify-the-certificate--access-service-fabric-explorer"></a>Modificar o certificado e acessar o Gerenciador do Service Fabric ##

1. Clique duas vezes no certificado para abrir o Assistente para Importação de Certificados.

2. Use as configurações padrão, mas marque a caixa de seleção **Marcar esta chave como exportável.** , na etapa **proteção de chave privada**. O Visual Studio precisa exportar o certificado ao configurar o Registro de Contêiner do Azure para autenticação do Cluster do Service Fabric, mais adiante neste tutorial.

3. Agora você pode abrir o Service Fabric Explorer em um navegador. Para fazer isso, navegue até o **ManagementEndpoint** URL para o cluster usando um navegador da web e selecione o certificado que foi salvo no seu computador.

>[!NOTE]
>Ao abrir o Service Fabric Explorer, você verá um erro de certificado, pois está usando um certificado autoassinado. No Edge, você precisa clicar em *Detalhes* e no link *Ir para a página da Web*. No Chrome, você precisa clicar em *Avançado* e no link *Continuar*.

>[!NOTE]
>Se a criação do cluster falhar, você sempre poderá executar novamente o comando, o que atualiza os recursos já implantados. Se um certificado tiver sido criado como parte da implantação com falha, um novo será gerado. Para solucionar problemas de criação do cluster, consulte [Criar um cluster do Service Fabric usando o Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="connect-to-the-secure-cluster"></a>Conectar-se ao cluster seguro
Conecte-se ao cluster usando o módulo do PowerShell do Service Fabric instalado com o SDK do Service Fabric.  Primeiro, instale o certificado no repositório pessoal (Meu) do usuário atual no seu computador.  Execute o seguinte comando do PowerShell:

```powershell
$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Agora você está pronto para se conectar ao seu cluster seguro.

O módulo do PowerShell do **Service Fabric** fornece vários cmdlets para gerenciar clusters, aplicativos e serviços do Service Fabric.  Use o cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) para se conectar ao cluster seguro. Os detalhes de ponto de extremidade de conexão e de impressão digital do certificado podem ser encontrados na saída da etapa anterior.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Verifique se você está conectado e se o cluster está íntegro, usando o cmdlet [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth).

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Limpar recursos

Um cluster é composto por outros recursos do Azure, além do próprio recurso do cluster. A maneira mais simples de excluir o cluster e todos os recursos que ele consume é excluir o grupo de recursos.

Faça logon no Azure e selecione a ID de assinatura com a qual você deseja remover o cluster.  Você pode encontrar sua ID de assinatura fazendo logon no [Portal do Azure](http://portal.azure.com). Exclua o grupo de recursos e todos os recursos de cluster usando o [cmdlet Remove-AzureRMResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId "Subcription ID"

$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar um cluster do Service Fabric no Azure
> * Proteger o cluster com um certificado X.509
> * Conectar o cluster usando o PowerShell
> * Remover um cluster

Em seguida, avance para o próximo tutorial para saber como implantar um aplicativo existente.
> [!div class="nextstepaction"]
> [Implantar um aplicativo existente do .NET com o Docker Compose](service-fabric-host-app-in-a-container.md)
