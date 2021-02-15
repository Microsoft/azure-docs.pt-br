---
title: Como mover o recurso de serviço entre regiões
titleSuffix: Azure Cognitive Search
description: Este artigo mostrará como mover seus recursos do Azure Pesquisa Cognitiva de uma região para outra na nuvem do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/10/2020
ms.openlocfilehash: a5050958f01743ff3c6fdcdecfee3067b1d2073e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98934266"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>Mover o serviço de Pesquisa Cognitiva do Azure para outra região do Azure

Ocasionalmente, os clientes perguntam sobre como mover um serviço de pesquisa para outra região. Atualmente, não há nenhum mecanismo ou ferramenta interna para ajudar nessa tarefa, mas este artigo pode ajudá-lo a entender as etapas manuais para atingir o mesmo resultado.

> [!NOTE]
> Na portal do Azure, todos os serviços têm um comando **Exportar modelo** . No caso do Azure Pesquisa Cognitiva, esse comando produz uma definição básica de um serviço (nome, local, camada, réplica e contagem de partições), mas não reconhece o conteúdo do serviço nem carrega chaves, funções ou logs. Embora o comando exista, não é recomendável usá-lo para mover um serviço de pesquisa.

## <a name="prerequisites"></a>Pré-requisitos

+ Verifique se os serviços e recursos que sua conta usa têm suporte na região de destino.

+ Para recursos de visualização, verifique se sua assinatura foi aprovada para a região de destino.

## <a name="prepare-and-move"></a>Preparar e mover

1. Identifique dependências e serviços relacionados para entender o impacto total da realocação de um serviço, caso você precise mover mais do que apenas o Azure Pesquisa Cognitiva.

   O armazenamento do Azure é usado para registro em log, criação de uma loja de conhecimento e é uma fonte de dados externa comumente usada para a enriquecimento e a indexação de ia. Os serviços cognitivas são uma dependência no enriquecimento do ia. Os serviços cognitivas e o serviço de pesquisa devem estar na mesma região se você estiver usando o enriquecimento de ia.

1. Crie um inventário de todos os objetos no serviço para que você saiba o que mover: índices, mapas de sinônimos, indexadores, fontes de dados, habilidades. Se você habilitou o registro em log, crie e arquive os relatórios que talvez precisem para um registro histórico.

1. Verifique os preços e a disponibilidade na nova região para garantir a disponibilidade do Azure Pesquisa Cognitiva mais todos os serviços relacionados na nova região. A maioria dos recursos está disponível em todas as regiões, mas alguns recursos de visualização têm a disponibilidade restrita.

1. Crie um serviço na nova região e Republique-o do código-fonte quaisquer índices existentes, mapas de sinônimos, indexadores, fontes de dados e habilidades. Lembre-se de que os nomes de serviço devem ser exclusivos para que você não possa reutilizar o nome existente. Verifique cada habilidade para ver se as conexões com serviços cognitivas ainda são válidas em termos de requisito de mesma região. Além disso, se as lojas de conhecimento forem criadas, verifique as cadeias de conexão para o armazenamento do Azure se você estiver usando um serviço diferente.

1. Recarregue os índices e as lojas de conhecimento, se aplicável. Você usará o código do aplicativo para enviar dados JSON por push para um índice ou executar novamente indexadores para efetuar pull de documentos de fontes externas. 

1. Habilite o registro em log e, se estiver usando-os, recrie as funções de segurança.

1. Atualize os aplicativos cliente e os conjuntos de testes para usar o novo nome de serviço e as chaves de API e testar todos os aplicativos.

## <a name="discard-or-clean-up"></a>Descartar ou limpar

Exclua o serviço antigo depois que o novo serviço for totalmente testado e operacional. Excluir o serviço exclui automaticamente todo o conteúdo associado ao serviço.

## <a name="next-steps"></a>Próximas etapas

Os links a seguir podem ajudá-lo a localizar mais informações ao concluir as etapas descritas acima.

+ [Preços e regiões do Azure Pesquisa Cognitiva](https://azure.microsoft.com/pricing/details/search/)
+ [Escolha uma faixa](search-sku-tier.md)
+ [Criar um serviço de pesquisa](search-create-service-portal.md)
+ [Carregar documentos de pesquisa](search-what-is-data-import.md)
+ [Habilitar o registro em log](search-monitor-logs.md)


<!-- To move your Azure Cognitive Service account from one region to another, you will create an export template to move your subscription(s). After moving your subscription, you will need to move your data and recreate your service.

In this article, you'll learn how to:

> [!div class="checklist"]
> * Export a template.
> * Modify the template: adding the target region, search and storage account names.
> * Deploy the template to create the new search and storage accounts.
> * Verify your service status in the new region
> * Clean up resources in the source region.

## Prerequisites

- Ensure that the services and features that your account uses are supported in the target region.

- For preview features, ensure that your subscription is allowlisted for the target region. For more information about preview features, see [knowledge stores](./knowledge-store-concept-intro.md), [incremental enrichment](./cognitive-search-incremental-indexing-conceptual.md), and [private endpoint](./service-create-private-endpoint.md).

## Assessment and planning

When you move your search service to the new region, you will need to [move your data to the new storage service](../storage/common/storage-account-move.md?tabs=azure-portal#configure-the-new-storage-account) and then rebuild your indexes, skillsets and knowledge stores. You should record current settings and copy json files to make the rebuilding of your service easier and faster.

## Moving your search service's resources

To start you will export and then modify a Resource Manager template.

### Export a template

1. Sign in to the [Azure portal](https://portal.azure.com).

2. Go to your Resource Group page.

> [!div class="mx-imgBorder"]
> ![Resource Group page example](./media/search-move-resource/export-template-sample.png)

3. Select **All resources**.

3. In the left hand navigation menu select **Export template**.

4. Choose **Download** in the **Export template** page.

5. Locate the .zip file that you downloaded from the portal, and unzip that file to a folder of your choice.

The zip file contains the .json files that comprise the template and scripts to deploy the template.

### Modify the template

You will modify the template by changing the search and storage account names and regions. The names must follow the rules for each service and region naming conventions. 

To obtain region location codes, see [Azure Locations](https://azure.microsoft.com/global-infrastructure/locations/).  The code for a region is the region name with no spaces, **Central US** = **centralus**.

1. In the Azure portal, select **Create a resource**.

2. In **Search the Marketplace**, type **template deployment**, and then press **ENTER**.

3. Select **Template deployment**.

4. Select **Create**.

5. Select **Build your own template in the editor**.

6. Select **Load file**, and then follow the instructions to load the **template.json** file that you downloaded and unzipped in the previous section.

7. In the **template.json** file, name the target search and storage accounts by setting the default value of the search and storage account names. 

8. Edit the **location** property in the **template.json** file to the target region for both your search and storage services. This example sets the target region to `centralus`.

```json
},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Search/searchServices",
            "apiVersion": "2020-03-13",
            "name": "[parameters('searchServices_target_region_search_name')]",
            "location": "centralus",
            "sku": {
                "name": "standard"
            },
            "properties": {
                "replicaCount": 1,
                "partitionCount": 1,
                "hostingMode": "Default"
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[parameters('storageAccounts_tagetstorageregion_name')]",
            "location": "centralus",
            "sku": {
                "name": "Standard_RAGRS",
                "tier": "Standard"
            },
```

### Deploy the template

1. Save the **template.json** file.

2. Enter or select the property values:

- **Subscription**: Select an Azure subscription.

- **Resource group**: Select **Create new** and give the resource group a name.

- **Location**: Select an Azure location.

3. Click the **I agree to the terms and conditions stated above** checkbox, and then click the **Select Purchase** button.

## Verifying your services' status in new region

To verify the move, open the new resource group and your services will be listed with the new region.

To move your data from your source region to the target region, please see this article's guidelines for [moving your data to the new storage account](../storage/common/storage-account-move.md?tabs=azure-portal#move-data-to-the-new-storage-account).

## Clean up resources in your original region

To commit the changes and complete the move of your service account, delete the source service account.

## Next steps

[Create an index](./search-get-started-portal.md)

[Create a skillset](./cognitive-search-quickstart-blob.md)

[Create a knowledge store](./knowledge-store-create-portal.md) -->