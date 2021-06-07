---
title: Solucionar problemas das implantações
description: Saiba como monitorar implantações de modelo do ARM (modelo do Azure Resource Manager) e solucionar problemas ocorridos com elas. Mostra os logs de atividade e o histórico de implantação.
author: mumian
ms.date: 01/15/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 31c4e6383b5eaea2bb66dc1baafa0fbff4918a7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97589110"
---
# <a name="tutorial-troubleshoot-arm-template-deployments"></a>Tutorial: Solucionar problemas de implantações do modelo do ARM

Saiba como solucionar erros de implantação de modelo do ARM (modelo do Azure Resource Manager). Neste tutorial, você configurará dois erros em um modelo e aprenderá a usar os logs de atividades e o histórico de implantação para resolver os problemas.

Há dois tipos de erros relacionados à implantação de modelo:

- Os **erros de validação** surgem em cenários que podem ser determinados antes da implantação. Eles incluem erros de sintaxe no modelo ou a tentativa de implantar recursos que excederão suas cotas de assinatura.
- Os **erros de implantação** surgem em condições que ocorrem durante o processo de implantação. Eles incluem a tentativa de acessar um recurso que está sendo implantado em paralelo.

Ambos os tipos de erro retornam um código de erro que você pode usar para solucionar os problemas de implantação. Os dois tipos de erro aparecem no log de atividades. No entanto, os erros de validação não aparecem no seu histórico de implantações, porque a implantação nunca foi iniciada.

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> - Criar um modelo com problemas
> - Solucionar erros de validação
> - Solucionar problemas de erros de implantação
> - Limpar os recursos

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

- Visual Studio Code com a extensão de Ferramentas do Resource Manager. Confira [Início Rápido: Criar modelos do ARM com o Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

## <a name="create-a-problematic-template"></a>Criar um modelo com problemas

Abra um modelo chamado [Criar uma conta de armazenamento Standard](https://azure.microsoft.com/resources/templates/101-storage-account-create/) em [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/) e configure dois problemas de modelo.

1. No Visual Studio Code, escolha **Arquivo** > **Abrir Arquivo**.
2. Em **Nome do arquivo**, cole a seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Escolha **Abrir** para abrir o arquivo.
4. Altere a linha `apiVersion` para a seguinte linha:

    ```json
    "apiVersion1": "2018-07-02",
    ```

    - `apiVersion1` é um nome de elemento inválido. Esse é um erro de validação.
    - A versão da API deverá ser `"2018-07-01"`.  Esse é um erro de implantação.

5. Escolha **Arquivo** > **Salvar como** para salvar o arquivo como _azuredeploy.json_ em seu computador local.

## <a name="troubleshoot-the-validation-error"></a>Solucionar o erro de validação

Consulte a seção [Implantar o modelo](template-tutorial-create-multiple-instances.md#deploy-the-template) para implantar o modelo.

Você deverá receber um erro do shell semelhante a:

```azurepowershell
New-AzResourceGroupDeployment : 4:29:24 PM - Error: Code=InvalidRequestContent; Message=The request content was invalid and could not be deserialized: 'Could not find member 'apiVersion1' on object of type 'TemplateResource'. Path 'properties.template.resources[0].apiVersion1', line 36, position 24.'.
```

A mensagem de erro indica que o problema ocorre na `apiVersion1`.

Use o Visual Studio Code para corrigir o problema alterando `apiVersion1` para `apiVersion` e, em seguida, salve o modelo.

## <a name="troubleshoot-the-deployment-error"></a>Solucionar o erro de implantação

Consulte a seção [Implantar o modelo](template-tutorial-create-multiple-instances.md#deploy-the-template) para implantar o modelo.

Você deverá receber um erro do shell semelhante a:

```azurepowershell
New-AzResourceGroupDeployment : 4:48:50 PM - Resource Microsoft.Storage/storageAccounts 'storeqii7x2rce77dc' failed with message '{
  "error": {
    "code": "NoRegisteredProviderFound",
    "message": "No registered resource provider found for location 'centralus' and API version '2018-07-02' for type 'storageAccounts'. The supported api-versions are '2018-07-01, 2018-03-01-preview, 2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01, 2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'. The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast, japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast, australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth, francecentral'."
  }
}'
```

O erro de implantação pode ser encontrado no portal do Azure usando o seguinte procedimento:

1. Entre no [portal do Azure](https://portal.azure.com).
2. Abra o grupo de recursos selecionando **Grupos de recursos** e, em seguida, o nome do grupo de recursos. Você deverá ver **1 Reprovado** em **Implantação**.

    ![Captura de tela que realça a implantação com falha.](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error.png)
3. Selecione **Detalhes do erro**.

    ![Captura de tela que realça o link Detalhes do erro.](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error-details.png)

    A mensagem de erro é a mesma que a mostrada anteriormente:

    ![Captura de tela que mostra os detalhes do erro.](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error-summary.png)

Você também pode encontrar o erro nos logs de atividades:

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **Monitorar** > **Log de atividades**.
3. Use os filtros para encontrar o log.

    ![Solução de problemas do tutorial do Resource Manager](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-activity-log.png)

Use o Visual Studio Code para corrigir o problema e, em seguida, reimplante o modelo.

Para obter uma lista de erros comuns, confira [Solução de erros comuns de implantação do Azure com o Azure Resource Manager](common-deployment-errors.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Selecione o nome do grupo de recursos.  Você deverá ver um total de seis recursos no grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a solucionar erros de implantação de modelo do ARM.  Para obter mais informações, confira [Solução de erros comuns de implantação do Azure com o Azure Resource Manager](common-deployment-errors.md).
