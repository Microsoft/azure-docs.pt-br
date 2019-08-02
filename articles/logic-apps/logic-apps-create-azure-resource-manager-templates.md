---
title: Criar modelos de aplicativo lógico para implantação-aplicativos lógicos do Azure
description: Saiba como criar modelos de Azure Resource Manager para automatizar a implantação em aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 07/26/2019
ms.openlocfilehash: 7d216a3706c13a5fff312850e244a521ab22ae9e
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68386532"
---
# <a name="create-azure-resource-manager-templates-to-automate-deployment-for-azure-logic-apps"></a>Criar modelos de Azure Resource Manager para automatizar a implantação para aplicativos lógicos do Azure

Para ajudá-lo a automatizar a criação e a implantação de seu aplicativo lógico, este artigo descreve as maneiras como você pode criar um [modelo de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) para seu aplicativo lógico. Para obter uma visão geral sobre a estrutura e a sintaxe de um modelo que inclui sua definição de fluxo de trabalho e outros [recursos necessários para a implantação, consulte Visão geral: Automatize a implantação de aplicativos lógicos com modelos](logic-apps-azure-resource-manager-templates-overview.md)de Azure Resource Manager.

Os aplicativos lógicos do Azure fornecem um [modelo de aplicativos lógicos pré-criados Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) que você pode reutilizar, não apenas para criar aplicativos lógicos, mas também para definir os recursos e parâmetros a serem usados para implantação. Você pode usar esse modelo para seus próprios cenários de negócios ou personalize o modelo para atender às suas necessidades. Para obter mais informações sobre modelos de Azure Resource Manager, consulte estes tópicos:

* [Estrutura e sintaxe do modelo de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
* [Criar modelos do Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-authoring-templates.md)
* [Desenvolva modelos do Azure Resource Manager para consistência de nuvem](../azure-resource-manager/templates-cloud-consistency.md)

> [!IMPORTANT]
> As conexões em seu modelo devem usar o mesmo grupo de recursos do Azure e o mesmo local que seu aplicativo lógico.

<a name="visual-studio"></a>

## <a name="create-templates-with-visual-studio"></a>Criar modelos com o Visual Studio

Para a maneira mais fácil de criar modelos de aplicativo lógico com parâmetros válidos que estão principalmente prontos para implantação, use o Visual Studio (versão gratuita da Comunidade ou superior) e as ferramentas de aplicativos lógicos do Azure para Visual Studio. Você pode [criar seu aplicativo lógico no Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) ou [Localizar e baixar um aplicativo lógico existente do portal do Azure para o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

Ao baixar seu aplicativo lógico, você obtém um modelo que inclui as definições para seu aplicativo lógico e outros recursos, como conexões. O modelo também *parametriza*, ou define parâmetros para, os valores usados para implantar seu aplicativo lógico e outros recursos. Você pode fornecer os valores para esses parâmetros em um arquivo de parâmetros separado. Dessa forma, você pode alterar esses valores com mais facilidade com base nas suas necessidades de implantação. Para saber mais, consulte esses tópicos:

* [Criar aplicativos lógicos com o Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Gerenciar aplicativos lógicos no Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)

<a name="azure-powershell"></a>

## <a name="create-templates-with-azure-powershell"></a>Criar modelos com Azure PowerShell

Você pode criar modelos do Resource Manager usando Azure PowerShell com o [módulo LogicAppTemplate](https://github.com/jeffhollan/LogicAppTemplateCreator). Este módulo de software livre primeiro avalia seu aplicativo lógico e todas as conexões que o aplicativo lógico usa. Em seguida, o módulo gera recursos de modelo com os parâmetros necessários para a implantação.

Por exemplo, suponha que você tenha um aplicativo lógico que recebe uma mensagem de uma fila do barramento de serviço do Azure e carrega dados para um banco de dado SQL do Azure. O módulo preserva toda a lógica de orquestração e parametriza as cadeias de conexão SQL e do barramento de serviço para que você possa fornecer e alterar esses valores com base nas suas necessidades de implantação.

### <a name="install-powershell-modules"></a>Instalar módulos do PowerShell

1. Se você ainda não fez isso, instale o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

1. Para obter a maneira mais fácil de instalar o módulo LogicAppTemplate do [Galeria do PowerShell](https://www.powershellgallery.com/packages/LogicAppTemplate), execute este comando:

   ```text
   PS> Install-Module -Name LogicAppTemplate
   ```

   Para atualizar para a versão mais recente, execute este comando:

   ```text
   PS> Update-Module -Name LogicAppTemplate
   ```

Ou, para instalar manualmente, siga as etapas no GitHub para [criador de modelo de aplicativo lógico](https://github.com/jeffhollan/LogicAppTemplateCreator).

### <a name="install-azure-resource-manager-client"></a>Instalar Azure Resource Manager cliente

Para que o módulo LogicAppTemplate funcione com qualquer token de acesso de assinatura e locatário do Azure, instale a [ferramenta de cliente Azure Resource Manager](https://github.com/projectkudu/ARMClient), que é uma ferramenta de linha de comando simples que chama a API de Azure Resource Manager.

Quando você executa o `Get-LogicAppTemplate` comando com essa ferramenta, o comando obtém primeiro um token de acesso por meio da ferramenta ARMClient, canaliza o token para o script do PowerShell e cria o modelo como um arquivo JSON. Para obter mais informações sobre a ferramenta, consulte este [artigo sobre a ferramenta de cliente do Azure Resource Manager](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html).

### <a name="generate-template-with-powershell"></a>Gerar modelo com o PowerShell

Para gerar seu modelo depois de instalar o módulo LogicAppTemplate, execute este comando do PowerShell:

```text
PS> Get-LogicAppTemplate
```

Para seguir a recomendação de encanamento em um token da [ferramenta de cliente Azure Resource Manager](https://github.com/projectkudu/ARMClient), execute este comando, em `$SubscriptionId` que é a sua ID de assinatura do Azure:

```text
PS> armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

Após a extração, você pode criar um arquivo de parâmetros do modelo executando este comando:

```text
PS> Get-ParameterTemplate -TemplateFile $filename | Out-File '<parameters-file-name>.json'
```

Para extração com referências de Azure Key Vault (somente estático), execute este comando:

```text
PS> Get-ParameterTemplate -TemplateFile $filename -KeyVault Static | Out-File $fileNameParameter
```

| Parâmetros | Necessário | Descrição |
|------------|----------|-------------|
| TemplateFile | Sim | O caminho do arquivo para o arquivo de modelo |
| KeyVault | Não | Uma enumeração que descreve como lidar com possíveis valores de cofre de chaves. O padrão é `None`. |
||||

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Implantar modelos de aplicativo lógico](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
