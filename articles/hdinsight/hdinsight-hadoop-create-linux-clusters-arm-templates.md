---
title: Criar clusters Apache Hadoop usando modelos – Azure HDInsight
description: Aprenda a criar clusters para o HDInsight usando modelos do Resource Manager
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 04/07/2020
ms.openlocfilehash: c36cee8ce7076093499a76ec0952ad37bd7511a7
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945867"
---
# <a name="create-apache-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Criar clusters Apache Hadoop no HDInsight usando modelos do Resource Manager

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Neste artigo, você aprende várias maneiras de criar clusters do Azure HDInsight usando [modelos de Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md). Para saber mais sobre outras ferramentas e recursos de criação de cluster, clique no seletor de guias na parte superior desta página. Consulte também os [métodos de criação de cluster](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="resource-manager-templates"></a>Modelos do Gerenciador de Recursos

Um modelo do Resource Manager facilita a criação dos seguintes recursos para seu aplicativo em uma única operação coordenada:

* Clusters HDInsight e seus recursos dependentes (como a conta de armazenamento padrão).
* Outros recursos (como o Banco de Dados SQL do Azure para usar o [Apache Sqoop](https://sqoop.apache.org/)).

No modelo, você deve definir os recursos que são necessários para o aplicativo. Você também pode especificar parâmetros de implantação para inserir valores para ambientes diferentes. O modelo consiste em JSON e expressões que você pode usar para criar valores para sua implantação.

Você pode encontrar exemplos de modelo do HDInsight em [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight). Use o [Visual Studio Code](https://code.visualstudio.com/#alt-downloads) de plataforma cruzada com a [extensão do Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) ou um editor de texto para salvar o modelo em um arquivo da estação de trabalho.

Para obter mais informações sobre modelos do Resource Manager, consulte os seguintes artigos e exemplos:

* [Criar modelos do Gerenciador de Recursos do Azure](../azure-resource-manager/templates/template-syntax.md)
* [Implantar um aplicativo com o modelo do Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md)
* Referência de modelo de [Microsoft.HDInsight/clusters](/azure/templates/microsoft.hdinsight/allversions)
* [Modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)

## <a name="generate-templates"></a>Gerar modelos

O Resource Manager permite que você exporte um modelo do Resource Manager dos recursos existentes em sua assinatura usando diferentes ferramentas. Você pode usar esse modelo gerado para saber mais sobre a sintaxe do modelo ou automatizar a reimplantação de sua solução, conforme o necessário. Para obter mais informações, consulte [Export templates](../azure-resource-manager/templates/export-template-portal.md).

## <a name="deploy-using-the-portal"></a>Implantar usando o portal

Você pode implantar um modelo do Resource Manager usando o portal do Azure. Para obter mais informações, consulte [Implantar recursos de modelo personalizado](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="deploy-using-powershell"></a>Implantar usando o PowerShell

Você pode implantar um modelo do Resource Manager usando o Azure PowerShell. Para obter mais informações, consulte [Implantar recursos com modelos do Resource Manager e do Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) e [implantar modelo do Resource Manager privado com o token SAS e o Azure PowerShell](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="deploy-using-azure-cli"></a>Implantar usando a CLI do Azure

Você pode implantar um modelo do Resource Manager usando a CLI do Azure. Para obter mais informações, consulte [Implantar recursos com modelos do Resource Manager e da CLI do Azure](../azure-resource-manager/templates/deploy-cli.md) e [implantar modelo do Resource Manager privado com o token SAS e a CLI do Azure](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="deploy-using-the-rest-api"></a>Implantar usando a API REST

Você pode implantar um modelo do Resource Manager usando a API REST. Para obter mais informações, consulte [Implantar recursos com modelos do Resource Manager e a API REST do Resource Manager](../azure-resource-manager/templates/deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Implantação com o Visual Studio

 Use o Visual Studio para criar um projeto do grupo de recursos e implantá-lo ao Azure por meio da interface do usuário. Selecione o tipo de recursos a serem incluídos em seu projeto. Esses recursos são adicionados automaticamente ao modelo do Resource Manager. O projeto também fornece um script do PowerShell para implantar o modelo.

Para obter uma introdução ao uso do Visual Studio com grupos de recursos, veja [Criando e implantando grupos de recursos do Azure por meio do Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).

## <a name="troubleshoot"></a>Solucionar problemas

Se você tiver problemas com a criação de clusters HDInsight, confira os [requisitos de controle de acesso](hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu várias maneiras de criar um cluster HDInsight. Para saber mais, leia os seguintes artigos:

* Para obter mais modelos relacionados ao HDInsight, consulte [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight).
* Para obter um exemplo de como implantar recursos por meio da biblioteca de cliente do .NET, veja [Implantar recursos usando bibliotecas do .NET e um modelo](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Para obter um exemplo detalhado de implantação de um aplicativo, confira [Provisionar e implantar microsserviços de forma previsível no Azure](../app-service/deploy-complex-application-predictably.md).
* Para obter orientação sobre como implantar a solução em ambientes diferentes, confira [Ambientes de desenvolvimento e de teste no Microsoft Azure](../devtest-labs/devtest-lab-overview.md).
* Para saber mais sobre as seções do modelo do Azure Resource Manager, veja [Criando modelos](../azure-resource-manager/templates/template-syntax.md).
* Para obter uma lista das funções que podem ser usadas em um modelo do Azure Resource Manager, veja [Funções do modelo](../azure-resource-manager/templates/template-functions.md).
