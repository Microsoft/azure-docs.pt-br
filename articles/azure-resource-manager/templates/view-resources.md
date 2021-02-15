---
title: Descobrir Propriedades de recurso
description: Descreve como procurar Propriedades de recurso.
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: c8bbe0dcb1bc9dc9751a1dc0d0b98a6368473546
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91327741"
---
# <a name="discover-resource-properties"></a>Descobrir Propriedades de recurso

Antes de criar modelos do Resource Manager, você precisa entender quais tipos de recursos estão disponíveis e quais valores usar em seu modelo. Este artigo mostra algumas maneiras pelas quais você pode encontrar as propriedades a serem incluídas no modelo.

## <a name="find-resource-provider-namespaces"></a>Localizar namespaces do provedor de recursos

Os recursos em um modelo ARM são definidos com um namespace do provedor de recursos e um tipo de recurso. Por exemplo, Microsoft. Storage/storageAccounts é o nome completo do tipo de recurso da conta de armazenamento. Microsoft. Storage é o namespace. Se você ainda não souber os namespaces para os tipos de recursos que deseja usar, consulte [provedores de recursos para serviços do Azure](../management/azure-services-resource-providers.md).

![Mapeamento de namespace do provedor de recursos do Resource Manager](./media/view-resources/resource-provider-namespace-and-azure-service-mapping.png)

## <a name="export-templates"></a>Exportar modelos

A maneira mais fácil de obter as propriedades de modelo para seus recursos existentes é exportar o modelo. Para obter mais informações, consulte [exportação única e de vários recursos para um modelo no portal do Azure](./export-template-portal.md).

## <a name="use-resource-manager-tools-extension"></a>Usar extensão de ferramentas do Resource Manager

Visual Studio Code e a extensão de ferramentas de Azure Resource Manager ajudam a ver exatamente quais propriedades são necessárias para cada tipo de recurso. Eles fornecem IntelliSense e trechos de código que simplificam a maneira como você define um recurso em seu modelo. Para obter mais informações, consulte [início rápido: criar modelos de Azure Resource Manager com Visual Studio Code](./quickstart-create-templates-use-visual-studio-code.md#add-an-azure-resource).

A captura de tela a seguir mostra que um recurso de conta de armazenamento é adicionado a um modelo:

![Trechos de extensão de ferramentas do Resource Manager](./media/view-resources/resource-manager-tools-extension-snippets.png)

A extensão também fornece uma lista de opções para as propriedades de configuração.

![Valores configuráveis da extensão de ferramentas do Gerenciador de recursos](./media/view-resources/resource-manager-tools-extension-configurable-properties.png)

## <a name="use-template-reference"></a>Usar referência de modelo

A referência de modelo de Azure Resource Manager é o recurso mais abrangente para o esquema de modelo. Você pode encontrar versões de API, formato de modelo e informações de propriedade.

1. Navegue até [Azure Resource Manager referência de modelo](/azure/templates/).
1. No painel de navegação esquerdo, selecione **armazenamento**e, em seguida, selecione **todos os recursos**. A página todos os recursos resume os tipos de recursos e as versões.

    ![versões de recurso de referência de modelo](./media/view-resources/resource-manager-template-reference-resource-versions.png)

    Se você souber o tipo de recurso, poderá ir diretamente para esta página com o seguinte formato de URL: `https://docs.microsoft.com/azure/templates/{provider-namespace}/{resource-type}` .

1. Selecione a versão mais recente. É recomendável usar a versão mais recente da API.

    A seção **formato do modelo** lista todas as propriedades da conta de armazenamento. o **SKU** está na lista:

    ![formato de conta de armazenamento de referência de modelo](./media/view-resources/resource-manager-template-reference-storage-account-sku.png)

    Role para baixo para ver o **objeto SKU** na seção **valores de propriedade** . O artigo mostra os valores permitidos para o nome do SKU:

    ![valores de SKU da conta de armazenamento de referência de modelo](./media/view-resources/resource-manager-template-reference-storage-account-sku-values.png)

    No final da página, a seção **modelos de início rápido** lista alguns modelos de início rápido do Azure que contêm o tipo de recurso:

    ![modelos de início rápido da conta de armazenamento de referência de modelo](./media/view-resources/resource-manager-template-reference-quickstart-templates.png)

A referência de modelo é vinculada de cada um dos sites de documentação do serviço do Azure.  Por exemplo, o [site de documentação Key Vault](../../key-vault/general/overview.md):

![Referência de modelo do Resource Manager Key Vault](./media/view-resources/resource-manager-template-reference-key-vault.png)

## <a name="use-resource-explorer"></a>Usar o Gerenciador de recursos

O Gerenciador de recursos é inserido no portal do Azure. Antes de usar esse método, você precisa de uma conta de armazenamento. Se você não tiver um, selecione o seguinte botão para criar um:

[![Implantar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json)

1. Entre no [portal do Azure](https://portal.azure.com).
1. Na caixa de pesquisa, insira **Gerenciador de recursos**e selecione **Gerenciador de recursos**.

    ![A captura de tela mostra a pesquisa do Gerenciador de recursos no portal do Azure.](./media/view-resources/azure-portal-resource-explorer.png)

1. Da esquerda, expanda **assinaturas**e, em seguida, expanda sua assinatura do Azure. Você pode encontrar a conta de armazenamento em ambos os **provedores** ou **ResourceGroups**.

    ![portal do Azure Gerenciador de recursos](./media/view-resources/azure-portal-resource-explorer-home.png)

    - **Provedores**: expanda **provedores**  ->  **Microsoft. Storage**  ->  **storageAccounts**e selecione sua conta de armazenamento.
    - **ResourceGroups**: selecione o grupo de recursos, que contém a conta de armazenamento, selecione **recursos**e, em seguida, selecione a conta de armazenamento.

    À direita, você verá a configuração de SKU para a conta de armazenamento existente semelhante a:

    ![portal do Azure SKU da conta de armazenamento do Gerenciador de recursos](./media/view-resources/azure-portal-resource-explorer-sku.png)

## <a name="use-resourcesazurecom"></a>Usar Resources.azure.com

O Resources.azure.com é um site público que pode ser acessado por qualquer pessoa com uma assinatura do Azure. Está em versão prévia.  Considere usar o [Gerenciador de recursos](#use-resource-explorer) em vez disso. Essa ferramenta fornece essas funcionalidades:

- Descubra as APIs de gerenciamento de recursos do Azure.
- Obtenha a documentação da API e informações de esquema.
- Faça chamadas à API diretamente em suas próprias assinaturas.

Para demonstrar como recuperar informações de esquema usando essa ferramenta, você precisa de uma conta de armazenamento. Se você não tiver um, selecione o seguinte botão para criar um:

[![Implantar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json)

1. Navegue até [Resources.Azure.com](https://resources.azure.com/). Leva alguns instantes para a ferramenta popular o painel esquerdo.
1. Selecione **assinaturas**.

    ![mapeamento de API do resource.azure.com](./media/view-resources/resources-azure-com-api-mapping.png)

    O nó à esquerda corresponde à chamada à API à direita. Você pode fazer a chamada à API selecionando o botão **obter** .
1. Da esquerda, expanda **assinaturas**e, em seguida, expanda sua assinatura do Azure. Você pode encontrar a conta de armazenamento em ambos os **provedores** ou **ResourceGroups**.

    - **Provedores**: expanda **provedores**  ->  **Microsoft. Storage**  ->  **storageAccounts**e navegue até a conta de armazenamento.
    - **ResourceGroups**: selecione o grupo de recursos, que contém a conta de armazenamento e, em seguida, selecione **recursos**.

    À direita, você verá a configuração de SKU para a conta de armazenamento existente semelhante a:

    ![portal do Azure SKU da conta de armazenamento do Gerenciador de recursos](./media/view-resources/azure-portal-resource-explorer-sku.png)

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a localizar informações de esquema de modelo. Para saber mais sobre como criar modelos do Resource Manager, consulte [entender a estrutura e a sintaxe de modelos ARM](./template-syntax.md).
