---
title: Implantar um aplicativo de cluster gerenciado Service Fabric (versão prévia) usando o modelo ARM
description: Implantar um aplicativo em um cluster gerenciado Service Fabric do Azure (versão prévia) usando um modelo de Azure Resource Manager.
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: b2d73180de61f44850b9c52a7ac24b0c23c36db2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100642312"
---
# <a name="deploy-a-service-fabric-managed-cluster-preview-application-using-arm-template"></a>Implantar um aplicativo de cluster gerenciado Service Fabric (versão prévia) usando o modelo ARM

Você tem várias opções para implantar aplicativos de Service Fabric do Azure em seu cluster Service Fabric gerenciado. É recomendável usar Azure Resource Manager. Se você usar o Resource Manager, poderá descrever aplicativos e serviços em JSON e, em seguida, implantá-los no mesmo modelo do Resource Manager que o cluster. Ao contrário do uso do PowerShell ou CLI do Azure para implantar e gerenciar aplicativos, se você usar o Resource Manager, não precisará aguardar até que o cluster esteja pronto; o registro, o provisionamento e a implantação do aplicativo podem acontecer em uma única etapa. Usar o Gerenciador de recursos é a melhor maneira de gerenciar o ciclo de vida do aplicativo em seu cluster. Para obter mais informações, consulte [práticas recomendadas: Infraestrutura como código](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources).

Gerenciar seus aplicativos como recursos no Resource Manager pode ajudá-lo a obter melhorias nessas áreas:

* Trilha de auditoria: o Resource Manager audita cada operação e mantém um log de atividades detalhado. Um log de atividades pode ajudá-lo a rastrear as alterações feitas nos aplicativos e no cluster.
* Controle de acesso baseado em função: você pode gerenciar o acesso a clusters e a aplicativos implantados no cluster usando o mesmo modelo do Resource Manager.
* Eficiência de gerenciamento: usar o Gerenciador de recursos fornece um único local (o portal do Azure) para gerenciar o cluster e implantações de aplicativos críticos.

Neste documento, você aprenderá a:

> [!div class="checklist"]
>
> * Implante recursos de aplicativo usando o Gerenciador de recursos.
> * Atualize os recursos do aplicativo usando o Gerenciador de recursos.
> * Excluir recursos do aplicativo.

## <a name="deploy-application-resources"></a>Implantar recursos do aplicativo

As etapas de alto nível executadas para implantar um aplicativo e seus serviços usando o modelo de recurso de aplicativo do Resource Manager são:
1. Empacote o código do aplicativo.
1. Carregue o pacote.
1. Referencie o local do pacote em um modelo do Resource Manager como um recurso de aplicativo. 

Para obter mais informações, veja [empacotar um aplicativo](service-fabric-package-apps.md#create-an-sfpkg).

Em seguida, você cria um modelo do Resource Manager, atualiza o arquivo de parâmetros com os detalhes do aplicativo e implanta o modelo no Cluster Service Fabric. [Explore exemplos](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/voting-sample-no-reverse-proxy/ARM-Managed-Cluster).

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Para implantar um aplicativo de um modelo do Resource Manager, você deve ter uma conta de armazenamento. A conta de armazenamento é usada para preparar a imagem do aplicativo. 

Você pode reutilizar uma conta de armazenamento existente ou pode criar uma nova conta de armazenamento para preparar seus aplicativos. Se você usar uma conta de armazenamento existente, poderá ignorar esta etapa. 

![Criar uma conta de armazenamento][CreateStorageAccount]

### <a name="configure-your-storage-account"></a>Configurar sua conta de armazenamento

Depois que a conta de armazenamento é criada, você cria um contêiner de BLOBs onde os aplicativos podem ser preparados. Na portal do Azure, vá para a conta de armazenamento do Azure na qual você deseja armazenar seus aplicativos. Selecione **BLOBs**  >  **Adicionar contêiner**. 

Os recursos no cluster podem ser protegidos definindo o nível de acesso público como **privado**. Você pode conceder acesso de várias maneiras:

* Autorize o acesso a BLOBs e filas usando [Azure Active Directory](../storage/common/storage-auth-aad-app.md).
* Conceda acesso ao blob do Azure e aos dados [da fila usando o Azure RBAC no portal do Azure](../storage/common/storage-auth-aad-rbac-portal.md).
* Delegar acesso usando uma [assinatura de acesso compartilhado](/rest/api/storageservices/delegate-access-with-shared-access-signature).

O exemplo na captura de tela a seguir usa acesso de leitura anônimo para BLOBs.

![Criar blob][CreateBlob]

### <a name="stage-the-application-in-your-storage-account"></a>Preparar o aplicativo em sua conta de armazenamento

Para poder implantar um aplicativo, você deve preparar o aplicativo no armazenamento de BLOBs. Neste tutorial, criamos o pacote de aplicativos manualmente. Tenha em mente que essa etapa pode ser automatizada. Para obter mais informações, consulte [empacotar um aplicativo](service-fabric-package-apps.md#create-an-sfpkg). 

Neste tutorial, usamos o aplicativo de [exemplo de votação](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart).

1. No Visual Studio, clique com o botão direito do mouse no projeto de **votação** e selecione **pacote**.

   ![Aplicativo de pacote][PackageApplication]  
1. Vá para o diretório *.\Service-Fabric-dotnet-quickstart\Voting\pkg\Debug* Compacte o conteúdo em um arquivo chamado *Voting.zip*. O arquivo de *ApplicationManifest.xml* deve estar na raiz no arquivo zip.

   ![Aplicativo zip][ZipApplication]  
1. Renomeie o arquivo para alterar a extensão de. zip para *. sfpkg*.

1. No portal do Azure, no contêiner **aplicativos** para sua conta de armazenamento, selecione **carregar** e, em seguida, carregar **votação. sfpkg**. 

   ![Carregar pacote do aplicativo][UploadAppPkg]

Agora, o aplicativo está preparado e você pode criar o modelo do Resource Manager para implantar o aplicativo.

### <a name="create-the-resource-manager-template"></a>Criar o modelo do Gerenciador de Recursos

O aplicativo de exemplo contém [modelos de Azure Resource Manager](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM) que você pode usar para implantar o aplicativo. Os nomes dos arquivos de modelo são *UserApp.js* e *UserApp.Parameters.js*.

> [!NOTE]
> O *UserApp.Parameters.jsno* arquivo deve ser atualizado com o nome do cluster.
>
>

| Parâmetro              | Descrição                                 | Exemplo                                                      | Comentários                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | O nome do cluster no qual você está implantando | It-cluster123                                                |                                                              |
| aplicativo            | O nome do aplicativo                 | Dica                                                       |
| version    | A ID do recurso, o tipo de aplicativo e a versão do aplicativo.       | /providers/Microsoft.ServiceFabric/managedClusters/sf-cluster-123/applicationTypes/VotingType/versions/1.0.0                                               | Deve corresponder ApplicationManifest.xml                 |              |
| serviceName            | O nome do serviço         | VotingWeb                                             | Deve estar no formato ServiceType            |
| serviceTypeName        | O nome do tipo do serviço                | VotingWebType                                                    | Deve corresponder ServiceManifest.xml                 |
| appPackageUrl          | A URL de armazenamento de BLOBs do aplicativo     | https: \/ /servicefabricapps.blob.Core.Windows.net/apps/Voting.sfpkg | A URL do pacote de aplicativos no armazenamento de BLOBs (o procedimento para definir a URL é descrito posteriormente no artigo) |

```json
{
    "apiVersion": "2021-01-01-preview",
    "type": "Microsoft.ServiceFabric/managedclusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2021-01-01-preview",
    "type": "Microsoft.ServiceFabric/managedclusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2021-01-01-preview",
    "type": "Microsoft.ServiceFabric/managedclusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2021-01-01-preview",
    "type": "Microsoft.ServiceFabric/managedclusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

### <a name="deploy-the-application"></a>Implantar o aplicativo

Execute o cmdlet **New-AzResourceGroupDeployment** para implantar o aplicativo no grupo de recursos que contém o cluster:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-the-service-fabric-application-by-using-resource-manager"></a>Atualizar o aplicativo Service Fabric usando o Gerenciador de recursos

> [!IMPORTANT]
> Qualquer serviço que está sendo implantado por meio da definição de JSON do ARM deve ser removido da seção DefaultServices do arquivo de ApplicationManifest.xml correspondente.


Você pode atualizar um aplicativo que já está implantado em um Cluster Service Fabric por um destes motivos:

* Um novo serviço é adicionado ao aplicativo. Uma definição de serviço deve ser adicionada a arquivos *service-manifest.xml* e *application-manifest.xml* quando um serviço é adicionado ao aplicativo. Para refletir uma nova versão de um aplicativo, você também deve alterar a versão do tipo de aplicativo de 1.0.0 para 1.0.1 no [UserApp.Parameters.jsem](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json):

    ```json
    "applicationTypeVersion": {
        "value": "1.0.1"
    },
    "serviceName2": {
        "value": "VotingData"
    },
    "serviceTypeName2": {
        "value": "VotingDataType"
    }
    ```

* Uma nova versão de um serviço existente é adicionada ao aplicativo. Os exemplos incluem alterações de código do aplicativo e atualizações para a versão e o nome do tipo de aplicativo. Para essa atualização, atualize UserApp.Parameters.jsdesta forma:

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```
## <a name="delete-application-resources"></a>Excluir recursos do aplicativo

Para excluir um aplicativo que foi implantado usando o modelo de recurso de aplicativo no Gerenciador de recursos:

1. Use o cmdlet [Get-AzResource](/powershell/module/az.resources/get-azresource) para obter a ID de recurso para o aplicativo:

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

1. Use o cmdlet [Remove-AzResource](/powershell/module/az.resources/remove-azresource) para excluir os recursos do aplicativo:

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>Próximas etapas

Obter informações sobre o modelo de recurso de aplicativo:

* [Modelar um aplicativo na Malha do Serviço](service-fabric-application-model.md)
* [Manifestos de serviço e aplicativo do Service Fabric](service-fabric-application-and-service-manifests.md)
* [Práticas recomendadas: Infraestrutura como código](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)
* [Gerenciar aplicativos e serviços como recursos do Azure](service-fabric-best-practices-infrastructure-as-code.md)


<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png