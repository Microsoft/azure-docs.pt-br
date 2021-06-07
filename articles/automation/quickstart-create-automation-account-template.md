---
title: 'Início Rápido: Criar uma conta de Automação – modelo do Azure'
titleSuffix: Azure Automation
description: Este guia de início rápido mostra como criar uma conta de Automação usando o modelo do Azure Resource Manager.
services: automation
ms.author: magoedte
ms.date: 01/07/2021
ms.topic: quickstart
ms.workload: infrastructure-services
ms.custom:
- mvc
- subject-armqs
- mode-arm
ms.openlocfilehash: 50a8c057a6fdf6350a18225cd2dc538942d6686d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538411"
---
# <a name="quickstart-create-an-automation-account-by-using-arm-template"></a>Início Rápido: Criar uma conta de Automação usando o modelo do ARM

A Automação do Azure oferece um serviço de configuração e de automação baseado em nuvem que compatível com o gerenciamento consistente em seus ambientes Azure e não Azure. Este guia de início rápido mostra como implantar um modelo do ARM (Azure Resource Manager) que cria uma conta de Automação. O uso de um modelo do ARM apresenta menos etapas comparado a outros métodos de implantação.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-automation%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Examinar o modelo

Este modelo de exemplo realiza o seguinte:

* Automatiza a criação de um Azure Monitor espaço de trabalho Log Analytics.
* Automatiza a criação de uma conta de Automação do Azure.
* Vincula a conta de Automação ao workspace do Log Analytics.
* Adiciona runbooks de Automação de exemplo à conta.

>[!NOTE]
>Não há suporte para a criação da conta Executar como de Automação quando você está usando um modelo do ARM. Para criar uma conta Executar como manualmente no portal ou com o PowerShell, confira [Criar uma conta Executar como](create-run-as-account.md).

Depois de concluir essas etapas, você precisará [definir configurações de diagnóstico](automation-manage-send-joblogs-log-analytics.md) para sua conta de automação para enviar fluxos de trabalho e status do trabalho de runbook para o workspace do Log Analytics vinculado.

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-automation/).

:::code language="json" source="~/quickstart-templates/101-automation/azuredeploy.json":::

### <a name="api-versions"></a>Versões de API

A tabela a seguir lista a versão de API para os recursos usados neste exemplo.

| Recurso | Tipo de recurso | Versão da API |
|:---|:---|:---|
| [Workspace](/azure/templates/microsoft.operationalinsights/workspaces) | workspaces | 2020-03-01-preview |
| [Conta de automação](/azure/templates/microsoft.automation/automationaccounts) | automação | 2020-01-13-preview |
| [Serviços vinculados de workspace](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | workspaces | 2020-03-01-preview |

### <a name="before-you-use-the-template"></a>Antes de usar o modelo

O modelo de parâmetros JSON está configurado para que você especifique:

* O nome do workspace.
* A região para criar o workspace.
* O nome da conta de Automação.
* A região na qual a conta de Automação será criada.

Os seguintes parâmetros no modelo são definidos com um valor padrão para o espaço de trabalho Log Analytics:

* *sku* assume por padrão o novo tipo de preço por GB lançado no modelo de preço de abril de 2018.
* *dataRetention* usa como padrão 30 dias.

>[!WARNING]
>Se você deseja criar ou configurar um espaço de trabalho do Log Analytics em uma assinatura que tiver aceitado o modelo de preços de abril de 2018, o único tipo de preço válido do Log Analytics *PerGB2018*.
>

O modelo JSON especifica um valor padrão para os outros parâmetros que provavelmente seriam usados como uma configuração padrão em seu ambiente. Você pode armazenar o modelo em uma conta de armazenamento do Azure para acesso compartilhado na sua organização. Para obter mais informações sobre como trabalhar com modelos, confira [Implantar recursos com modelos do ARM e a CLI do Azure](../azure-resource-manager/templates/deploy-cli.md).

Se você for novo na automação do Azure e Azure Monitor, é importante entender os detalhes de configuração a seguir. Eles podem ajudá-lo a evitar erros ao tentar criar, configurar e usar um workspace do Log Analytics vinculado à sua nova conta de automação.

* Examine [detalhes adicionais](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace) para entender totalmente as opções de configuração do espaço de trabalho, como modo de controle de acesso, tipo de preço, retenção e nível de reserva de capacidade.

* Examine [mapeamentos de workspace](how-to/region-mappings.md) para especificar as regiões com suporte embutidas ou em um arquivo de parâmetro. Ao habilitar soluções, somente determinadas regiões têm suporte para vincular um workspace do Log Analytics e uma Conta de Automação na sua assinatura.

* Se você for novo nos logs do Azure Monitor e ainda não tiver implantado um espaço de trabalho, leia as [diretrizes de design do workspace](../azure-monitor/logs/design-logs-deployment.md). Elas ajudarão você a aprender sobre o controle de acesso e compreender as estratégias de implementação de design que recomendamos para sua organização.

## <a name="deploy-the-template"></a>Implantar o modelo

1. Selecione a imagem a seguir para entrar no Azure e abrir um modelo. O modelo cria uma conta da Automação do Azure e um workspace do Log Analytics e vincula a conta de Automação ao workspace.

    [![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-automation%2Fazuredeploy.json)

2. Insira os valores.

3. A implantação pode levar alguns minutos para ser concluída. Após a conclusão, a saída será semelhante ao seguinte:

    ![Resultados de exemplo, quando a implantação for concluída](media/quickstart-create-automation-account-template/template-output.png)

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

1. Entre no [portal do Azure](https://portal.azure.com).

2. No portal do Azure, abra a conta de Automação recém-criada. 

3. No painel esquerdo, selecione **Runbooks**. Na página **Runbooks**, estão listados três runbooks do tutorial criados com a conta de Automação.

    ![Runbooks do tutorial criados com a conta de Automação](./media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. No painel esquerdo, selecione **Workspace vinculado**. Na página **Workspace vinculado**, ele mostra o workspace do Log Analytics especificado anteriormente que foi vinculado à sua conta de Automação.

    ![Conta de Automação vinculada ao workspace do Log Analytics](./media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você não precisar mais deles, desvincule a conta de Automação do workspace do Log Analytics e exclua a conta de Automação e o workspace.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou uma conta de Automação, um workspace do Log Analytics e os vinculou.

Para saber mais, prossiga para os tutoriais da Automação do Azure.

> [!div class="nextstepaction"]
> [Tutoriais da Automação do Azure](learn/automation-tutorial-runbook-graphical.md)
