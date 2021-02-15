---
title: Testar o arquivo de definição de interface do usuário
description: Descreve como testar a experiência do usuário para criar o Aplicativo Gerenciado do Azure por meio do portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: f76d3b81c2d5425f7bb91c5c86a79faa097794e3
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96434993"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Testar a interface do portal para aplicativos gerenciados do Azure

Depois de [criar o createUiDefinition.jsno arquivo](create-uidefinition-overview.md) para seu aplicativo gerenciado, você precisa testar a experiência do usuário. Para simplificar o teste, use um ambiente de área restrita que carregue o arquivo no Portal. Não é necessário realmente implantar o aplicativo gerenciado. A área restrita apresenta a interface do usuário na experiência atual do portal de tela inteira. Ou você pode usar um script para testar a interface. As duas abordagens são mostradas neste artigo. A área restrita é a maneira recomendada para visualizar a interface.

## <a name="prerequisites"></a>Pré-requisitos

* Um arquivo **createUiDefinition.json**. Se você não tiver esse arquivo, copie o [arquivo de exemplo](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json).

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="use-sandbox"></a>Usar área restrita

1. Abra a [área restrita criar definição de interface do usuário](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade).

   ![Mostrar área restrita](./media/test-createuidefinition/show-sandbox.png)

1. Substitua a definição vazia pelo conteúdo de seu createUiDefinition.jsno arquivo. Selecione **Visualização**.

   ![Selecionar visualização](./media/test-createuidefinition/select-preview.png)

1. O formulário que você criou é exibido. Você pode percorrer a experiência do usuário e preencher os valores.

   ![Mostrar formulário](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>Solução de problemas

Se o formulário não for exibido após a seleção da **Visualização**, você poderá ter um erro de sintaxe. Procure o indicador vermelho na barra de rolagem direita e navegue até ele.

![Mostrar erro de sintaxe](./media/test-createuidefinition/show-syntax-error.png)

Se o formulário não for exibido e, em vez disso, você vir um ícone de uma nuvem com descarte solto, seu formulário terá um erro, como uma propriedade ausente. Abra o Ferramentas para Desenvolvedores da Web no navegador. O **Console** exibe mensagens importantes sobre a interface.

![Mostrar erro](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>Usar script de teste

Para testar a interface no portal, copie um dos scripts a seguir para o computador local:

* [Script de carregamento do PowerShell – módulo AZ](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-AzCreateUIDefinition.ps1)
* [Script de carregamento do PowerShell-módulo do Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Script de sideload do Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

Para ver o arquivo de interface no portal, execute o script baixado. O script cria uma conta de armazenamento na assinatura do Azure e carrega o arquivo createUiDefinition.json para a conta de armazenamento. A conta de armazenamento é criada na primeira vez que você executa o script ou em caso de exclusão da conta de armazenamento. Se a conta de armazenamento já existe em sua assinatura do Azure, o script a reutiliza. O script abre o portal e carrega o arquivo da conta de armazenamento.

Forneça um local para a conta de armazenamento e especifique a pasta que tem o arquivo createUiDefinition.json.

Para o PowerShell, use:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

Para a CLI do Azure, use:

```bash
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

Se o arquivo createUiDefinition.json estiver na mesma pasta que o script e você já tiver criado a conta de armazenamento, você não precisará fornecer esses parâmetros.

Para o PowerShell, use:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1
```

Para a CLI do Azure, use:

```bash
./sideload-createuidef.sh
```

O script abre uma nova guia no navegador. Em seguida, exibe o portal com a interface para criar o aplicativo gerenciado.

Forneça valores para os campos. Quando terminar, você verá os valores passados para o modelo que podem ser encontrados no console de ferramentas de desenvolvimento do navegador.

![Mostrar valores](./media/test-createuidefinition/show-json.png)

Você pode usar esses valores como o arquivo de parâmetro para testar seu modelo de implantação.

Se o portal parar na tela de resumo, poderá haver um bug na seção de saída. Por exemplo, você pode ter referenciado um controle que não existe. Se um parâmetro na saída estiver vazio, o parâmetro poderá estar fazendo referência a uma propriedade que não existe. Por exemplo, a referência para o controle é válida, mas a referência da propriedade não é válida.

## <a name="test-your-solution-files"></a>Testar os arquivos de solução

Agora que você verificou que a interface do portal está funcionando conforme o esperado, é hora de validar que o arquivo createUiDefinition está corretamente integrado ao arquivo mainTemplate.json. É possível executar um teste de script de validação para testar o conteúdo dos arquivos de solução, incluindo o arquivo createUiDefinition. O script valida a sintaxe JSON, verifica expressões regex em campos de texto e garante que os valores de saída da interface do portal correspondam aos parâmetros do modelo. Para obter mais informações sobre como executar esse script, consulte [Executar verificações de validação estática para modelos](https://github.com/Azure/azure-quickstart-templates/tree/master/test).

## <a name="next-steps"></a>Próximas etapas

Após validar a interface do portal, saiba como tornar o[aplicativo gerenciado do Azure disponível no Marketplace](../../marketplace/create-new-azure-apps-offer.md).