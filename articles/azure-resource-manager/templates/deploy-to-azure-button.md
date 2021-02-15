---
title: Botão Implantar no Azure
description: Use o botão para implantar modelos de Azure Resource Manager de um repositório GitHub.
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: abe59f377474540e9209691df8b1d1a7b806c26d
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028736"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>Usar um botão de implantação para implantar modelos do repositório GitHub

Este artigo descreve como usar o botão **implantar no Azure** para implantar modelos de um repositório github. Você pode adicionar o botão diretamente ao arquivo _README.MD_ em seu repositório github. Ou então, você pode adicionar o botão a uma página da Web que faz referência ao repositório.

O escopo da implantação é determinado pelo esquema do modelo. Para obter mais informações, consulte:

- [grupos de recursos](deploy-to-resource-group.md)
- [assinaturas](deploy-to-subscription.md)
- [grupos de gerenciamento](deploy-to-management-group.md)
- [locatários](deploy-to-tenant.md)

## <a name="use-common-image"></a>Usar imagem comum

Para adicionar o botão à sua página da Web ou repositório, use a imagem a seguir:

```markdown
![Deploy to Azure](https://aka.ms/deploytoazurebutton)
```

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

A imagem aparece como:

![Botão Implantar no Azure](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>Criar URL para o modelo de implantação

Para criar a URL para o modelo, comece com a URL bruta para o modelo em seu repositório. Para ver a URL bruta, selecione **RAW**.

:::image type="content" source="./media/deploy-to-azure-button/select-raw.png" alt-text="selecionar bruto":::

O formato da URL é:

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Em seguida, converta a URL para um valor codificado por URL. Você pode usar um codificador online ou executar um comando. O exemplo do PowerShell a seguir mostra como codificar por URL um valor.

```powershell
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
[uri]::EscapeDataString($url)
```

A URL de exemplo tem o seguinte valor quando a URL é codificada.

```html
https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Cada link começa com a mesma URL base:

```html
https://portal.azure.com/#create/Microsoft.Template/uri/
```

Adicione o link de modelo codificado por URL ao final da URL base.

```html
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Você tem a URL completa para o link.

Normalmente, você hospeda o modelo em um repositório público. Se você usar um repositório privado, deverá incluir um token para acessar o conteúdo bruto do modelo. O token gerado pelo GitHub é válido apenas por um curto período de tempo. Você precisaria atualizar o link com frequência.

Se você estiver usando o [git com Azure Repos](/azure/devops/repos/git/) em vez de um repositório GitHub, ainda poderá usar o botão **implantar no Azure** . Verifique se seu repositório é público. Use a [operação itens](/rest/api/azure/devops/git/items/get) para obter o modelo. Sua solicitação deve estar no seguinte formato:

```http
https://dev.azure.com/{organization-name}/{project-name}/_apis/git/repositories/{repository-name}/items?scopePath={url-encoded-path}&api-version=6.0
```

Codifique essa URL de solicitação.

## <a name="create-deploy-to-azure-button"></a>Botão criar implantação no Azure

Por fim, coloque o link e a imagem juntos.

Para adicionar o botão com redução no arquivo _README.MD_ no repositório GitHub ou em uma página da Web, use:

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)
```

Para HTML, use:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json" target="_blank">
  <img src="https://aka.ms/deploytoazurebutton"/>
</a>
```

Para o Git com o repositório do Azure, o botão está no formato:

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fdev.azure.com%2Forgname%2Fprojectname%2F_apis%2Fgit%2Frepositories%2Freponame%2Fitems%3FscopePath%3D%2freponame%2fazuredeploy.json%26api-version%3D6.0)
```

## <a name="deploy-the-template"></a>Implantar o modelo

Para testar a solução completa, selecione o seguinte botão:

[![Implantar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)

O portal exibe um painel que permite que você forneça facilmente valores de parâmetro. Os parâmetros são preenchidos previamente com os valores padrão do modelo.

![Usar o portal para implantar](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre modelos, confira [Noções básicas de estrutura e sintaxe dos modelos do ARM](template-syntax.md).
