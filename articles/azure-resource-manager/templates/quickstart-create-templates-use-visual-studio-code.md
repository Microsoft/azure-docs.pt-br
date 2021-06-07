---
title: Criar modelo – Visual Studio Code
description: Use a extensão de ferramentas do Visual Studio Code e do Azure Resource Manager para trabalhar em modelos do ARM (Azure Resource Manager).
author: neilpeterson
ms.date: 08/09/2020
ms.topic: quickstart
ms.author: nepeters
ms.openlocfilehash: 3e41d4f6f8b8aeb2be376ae1385189fe02e9b897
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102504650"
---
# <a name="quickstart-create-arm-templates-with-visual-studio-code"></a>Início rápido: Criar modelos do ARM com o Visual Studio Code

As ferramentas do Azure Resource Manager para o Visual Studio Code fornecem suporte à linguagem, snippets de recursos e preenchimento automático de recursos. Essas ferramentas ajudam a criar e validar modelos do ARM (Azure Resource Manager). Neste guia de início rápido, você usará a extensão para criar um modelo do ARM do zero. Ao fazer isso, você experimenta as funcionalidades de extensões, como snippets de modelos do Resource Manager, validação, conclusões e suporte ao arquivo de parâmetro.

Para concluir este início rápido, você precisa do [Visual Studio Code](https://code.visualstudio.com/), com a instalação da [extensão das ferramentas do Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools). Você também precisa da [CLI do Azure](/cli/azure/) ou do [módulo do Azure PowerShell](/powershell/azure/new-azureps-module-az) instalado e autenticado.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-arm-template"></a>Criar um modelo do Resource Manager

Crie e abra com um novo arquivo chamado *azuredeploy.json* com o Visual Studio Code. Insira `arm` no editor de códigos, que inicia os snippets do Azure Resource Manager para o scaffolding de um modelo do Resource Manager.

Selecione `arm!` para criar um modelo com escopo para uma implantação do grupo de recursos do Azure.

![Imagem mostrando os snippets de scaffolding do Azure Resource Manager](./media/quickstart-create-templates-use-visual-studio-code/1.png)

Este snippet cria os blocos de construção básicos para um modelo do Resource Manager.

![Imagem mostrando um modelo do Resource Manager com scaffolding completo](./media/quickstart-create-templates-use-visual-studio-code/2.png)

Observe que o modo de linguagem Visual Studio Code mudou de *JSON* para o *Modelo do Azure Resource Manager*. A extensão inclui um servidor de linguagem específico para modelos do Resource Manager que fornecem validação, conclusão e outros serviços de linguagem específicos para modelos do Resource Manager.

![Imagem mostrando o Azure Resource Manager como o modo de linguagem do Visual Studio Code](./media/quickstart-create-templates-use-visual-studio-code/3.png)

## <a name="add-an-azure-resource"></a>Adicionar um recurso do Azure

A extensão inclui snippets para muitos recursos do Azure. Esses snippets podem ser usados para adicionar recursos facilmente à implantação de modelo.

Coloque o cursor sobre o bloco **recursos** do modelo, digite `storage` e selecione o snippet *arm-storage*.

![Imagem mostrando um recurso que está sendo adicionado ao modelo do Resource Manager](./media/quickstart-create-templates-use-visual-studio-code/4.png)

Esta ação adiciona um recurso de armazenamento ao modelo.

![Imagem mostrando um recurso de Armazenamento do Azure em um modelo do Resource Manager](./media/quickstart-create-templates-use-visual-studio-code/5.png)

A tecla **tab** pode ser usada para tabular pelas propriedades configuráveis na conta de armazenamento.

![Imagem mostrando como a tecla tab pode ser usada para navegar pela configuração de recursos](./media/quickstart-create-templates-use-visual-studio-code/6.png)

## <a name="completion-and-validation"></a>Conclusão e validação

Uma das funcionalidades mais poderosas da extensão é a integração com os esquemas do Azure. Os esquemas do Azure fornecem à extensão as funcionalidades de validação e de conclusão com reconhecimento de recursos. Vamos modificar a conta de armazenamento para ver a validação e a conclusão em ação.

Primeiro, atualize o tipo de conta de armazenamento para um valor inválido, como `megaStorage`. Observe que esta ação produz um aviso indicando que `megaStorage` não é um valor válido.

![Imagem mostrando uma configuração de armazenamento inválida](./media/quickstart-create-templates-use-visual-studio-code/7.png)

Para usar as funcionalidades de conclusão, remova `megaStorage`, coloque o cursor dentro das aspas duplas e pressione `ctrl` + `space`. Esta ação apresenta uma lista de conclusão de valores válidos.

![Imagem mostrando o preenchimento automático da extensão](./media/quickstart-create-templates-use-visual-studio-code/8.png)

## <a name="add-template-parameters"></a>Adicionar parâmetros do modelo

Agora crie e use um parâmetro para especificar o nome da conta de armazenamento.

Coloque o cursor no bloco de parâmetros, adicione um retorno de carro, digite `"` e selecione o snippet `new-parameter`. Esta ação adiciona um parâmetro genérico ao modelo.

![Imagem mostrando um parâmetro que está sendo adicionado ao modelo do Resource Manager](./media/quickstart-create-templates-use-visual-studio-code/9.png)

Atualize o nome do parâmetro para `storageAccountName` e a descrição para `Storage Account Name`.

![Imagem mostrando o parâmetro concluído em um modelo do Resource Manager](./media/quickstart-create-templates-use-visual-studio-code/10.png)

Os nomes da conta de armazenamento do Azure têm um comprimento mínimo de 3 caracteres e um máximo de 24. Adicione `minLength` e `maxLength` ao parâmetro e forneça os valores apropriados.

![Imagem mostrando minLength e maxLength sendo adicionados a um parâmetro de modelo do Resource Manager](./media/quickstart-create-templates-use-visual-studio-code/11.png)

Agora, no recurso de armazenamento, atualize a propriedade nome para usar o parâmetro. Para fazer isso, remova o nome atual. Insira aspas duplas e um colchete de abertura `[`, que produz uma lista de funções de modelo do Resource Manager. Selecione *parâmetros* na lista.

![Imagem mostrando o preenchimento automático ao usar parâmetros em recursos do modelo do Resource Manager](./media/quickstart-create-templates-use-visual-studio-code/12.png)

Inserir uma aspa simples `'` dentro dos parênteses produz uma lista de todos os parâmetros definidos no modelo, neste caso, *storageAccountName*. Selecione o parâmetro.

![Imagem mostrando o parâmetro concluído em um recurso do modelo do Resource Manager](./media/quickstart-create-templates-use-visual-studio-code/13.png)

## <a name="create-a-parameter-file"></a>Criar um arquivo de parâmetro

Um arquivo de parâmetro do modelo do Resource Manager permite armazenar valores de parâmetros específicos do ambiente e transmitir esses valores como um grupo no momento da implantação. Por exemplo, você pode ter um arquivo de parâmetro com valores específicos para um ambiente de teste e outro para um ambiente de produção.

A extensão facilita a criação de um arquivo de parâmetro usando os modelos existentes. Para isso, clique com o botão direito do mouse no modelo do editor de códigos e selecione `Select/Create Parameter File`.

![Imagem mostrando o processo de clique com o botão direito do mouse para criar um arquivo de parâmetro usando um modelo do Resource Manager](./media/quickstart-create-templates-use-visual-studio-code/14.png)

Selecione `New` > `All Parameters` > Selecione um nome e um local para o arquivo de parâmetro.

![Imagem mostrando o nome e o diálogo para salvar o arquivo ao criar um arquivo de parâmetros usando um modelo do Resource Manager](./media/quickstart-create-templates-use-visual-studio-code/15.png)

Essa ação cria um novo arquivo de parâmetro e o mapeia com o modelo do qual ele foi criado. Você pode ver e modificar o mapeamento atual do arquivo de modelo/parâmetro na barra de status do Visual Studio Code enquanto o modelo está selecionado.

![Imagem mostrando o mapeamento de arquivo de modelo/parâmetro na barra de status do Visual Studio Code.](./media/quickstart-create-templates-use-visual-studio-code/16.png)

Agora que o arquivo de parâmetro foi mapeado para o modelo, a extensão valida o modelo e o arquivo de parâmetro juntos. Para ver essa validação na prática, adicione um valor de dois caracteres ao parâmetro `storageAccountName` no arquivo de parâmetro e salve o arquivo.

![Imagem mostrando um modelo invalidado devido a um problema no arquivo de parâmetro](./media/quickstart-create-templates-use-visual-studio-code/17.png)

Navegue de volta ao modelo do Resource Manager e observe que um erro indicando que o valor não atende aos critérios de parâmetro foi gerado.

![Imagem mostrando um modelo do Resource Manager válido](./media/quickstart-create-templates-use-visual-studio-code/18.png)

Atualize o valor para algo apropriado, salve o arquivo e navegue de volta ao modelo. Observe que o erro no parâmetro foi resolvido.

## <a name="deploy-the-template"></a>Implantar o modelo

Abra o terminal integrado do Visual Studio Code usando a combinação de teclas `ctrl` + ```` ` ```` e use o módulo da CLI do Azure ou do Azure PowerShell para implantar o modelo.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group create --name arm-vscode --location eastus

az deployment group create --resource-group arm-vscode --template-file azuredeploy.json --parameters azuredeploy.parameters.json
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name arm-vscode -Location eastus

New-AzResourceGroupDeployment -ResourceGroupName arm-vscode -TemplateFile ./azuredeploy.json -TemplateParameterFile ./azuredeploy.parameters.json
```
---

## <a name="clean-up-resources"></a>Limpar os recursos

Quando os recursos do Azure não forem mais necessários, use o módulo da CLI do Azure ou do Azure PowerShell para excluir o grupo de recursos de início rápido.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name arm-vscode
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
Remove-AzResourceGroup -Name arm-vscode
```
---

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutoriais para iniciante](./template-tutorial-create-first-template.md)
