---
title: Criar arquivos Bicep – Visual Studio Code
description: Usar Visual Studio Code e a extensão Bicep para arquivos Bicep para implantar recursos do Azure
author: mumian
ms.date: 03/26/2021
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: 4d1064351ddfacdebfa67fd9b2f517f592de3a7c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612868"
---
# <a name="quickstart-create-bicep-files-with-visual-studio-code"></a>Início Rápido: criar arquivos Bicep com o Visual Studio Code

A extensão Bicep para Visual Studio Code fornece suporte a idioma e preenchimento automático de recursos. Essas ferramentas ajudam a criar e validar arquivos [Bicep](./bicep-overview.md). Neste guia de início rápido, você usará a extensão para criar um arquivo Bicep do zero. Ao fazer isso, você experimentará os recursos de extensões, como validação e conclusões.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

Para concluir este início rápido, você precisa do [Visual Studio Code](https://code.visualstudio.com/) com a [extensão Bicep](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep) instalada. Você também precisa da [CLI do Azure](/cli/azure/) mais recente ou do [módulo do Azure PowerShell](/powershell/azure/new-azureps-module-az) mais recente instalado e autenticado.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-bicep-file"></a>Criar um arquivo Bicep

Crie e abra com um novo arquivo chamado *azuredeploy.bicep* com o Visual Studio Code.

## <a name="add-an-azure-resource"></a>Adicionar um recurso do Azure

Adicione um recurso básico da conta de armazenamento ao arquivo Bicep.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'storageaccount1' // must be globally unique
  location: 'eastus'
  tags:{
    diplayName: 'storageaccount1'
  }
  sku: {
      name: 'Standard_LRS'
      tier: 'Standard'
  }
  kind: 'Storage'
}
```

A declaração de recurso tem quatro componentes:

- **recurso**: palavra-chave.
- **nome simbólico** (STG): nome simbólico é um identificador para referenciar o recurso em todo o arquivo Bicep. Não se trata de qual será o nome do recurso quando for implantado. O nome do recurso é definido pela propriedade **Name**.  Confira o quarto componente desta lista.
- **tipo de recurso** (Microsoft.Storage/storageAccounts@2019-06-01): é composto pelo provedor de recursos (Microsoft.Storage), tipo de recurso (StorageAccounts) e apiVersion (2019-06-01). Cada provedor de recursos publica as próprias versões de API, portanto, esse valor é específico para o tipo. Você pode encontrar mais tipos e apiVersions para vários recursos do Azure da [referência de modelo do ARM](/azure/templates/).
- **propriedades** (tudo em = {...}): especifique as propriedades do tipo de recurso. Cada recurso tem uma propriedade `name`. A maioria dos recursos também tem uma propriedade `location`, que define a região em que o recurso é implantado. As outras propriedades variam por tipo de recurso e versão de API.

## <a name="completion-and-validation"></a>Conclusão e validação

Uma das funcionalidades mais poderosas da extensão é a integração com os esquemas do Azure. Os esquemas do Azure fornecem à extensão as funcionalidades de validação e de conclusão com reconhecimento de recursos. Vamos modificar a conta de armazenamento para ver a validação e a conclusão em ação.

Primeiro, atualize o tipo de conta de armazenamento para um valor inválido, como `megaStorage`. Observe que esta ação produz um aviso indicando que `megaStorage` não é um valor válido.

![Imagem mostrando uma configuração de armazenamento inválida](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-validation.png)

Para usar as funcionalidades de conclusão, remova `megaStorage`, coloque o cursor dentro das aspas simples e pressione `ctrl` + `space`. Esta ação apresenta uma lista de conclusão de valores válidos.

![Imagem mostrando o preenchimento automático da extensão](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-auto-completion.png)

## <a name="add-parameters"></a>Adicionar parâmetros

Agora crie e use um parâmetro para especificar o nome da conta de armazenamento.

Adicione o seguinte código ao início do arquivo:

```bicep
@minLength(3)
@maxLength(24)
@description('Specify a storage account name.')
param storageAccountName string
```

Os nomes da conta de armazenamento do Azure têm um comprimento mínimo de 3 caracteres e um máximo de 24. Use `minLength` e `maxLength` para fornecer os valores corretos.

Agora, no recurso de armazenamento, atualize a propriedade nome para usar o parâmetro. Para fazer isso, remova o nome do recurso de armazenamento atual, incluindo as aspas simples. pressione `ctrl` + `space`. Selecione o parâmetro **storageAccountName** na lista. Observe que os parâmetros podem ser referenciados diretamente usando os nomes no Bicep. Os modelos JSON exigem uma função de parâmetro().

![Imagem mostrando o preenchimento automático ao usar parâmetros em recursos do Bicep](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-valid-param.png)

## <a name="deploy-the-bicep-file"></a>Implante o arquivo Bicep

Abra o terminal integrado do Visual Studio Code usando a combinação de chaves `ctrl` + ```` ` ````, altere o diretório atual para onde o arquivo Bicep está localizado e, em seguida, use a CLI do Azure ou o módulo do Azure PowerShell para implantar o arquivo Bicep.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group create --name arm-vscode --location eastus

az deployment group create --resource-group arm-vscode --template-file azuredeploy.bicep --parameters storageAccountName={your-unique-name}
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name arm-vscode -Location eastus

New-AzResourceGroupDeployment -ResourceGroupName arm-vscode -TemplateFile ./azuredeploy.bicep -storageAccountName "{your-unique-name}"
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
> [Tutoriais do Bicep para iniciantes](./bicep-tutorial-create-first-bicep.md)
