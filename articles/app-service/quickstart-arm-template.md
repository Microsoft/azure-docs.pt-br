---
title: Criar um aplicativo do Serviço de Aplicativo usando um modelo do Azure Resource Manager
description: Crie seu primeiro aplicativo para o Serviço de Aplicativo do Azure em segundos usando um modelo do ARM (modelo do Azure Resource Manager), que é uma das muitas maneiras de fazer a implantação no Serviço de Aplicativo.
author: msangapu-msft
ms.author: msangapu
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 10/16/2020
ms.custom: subject-armqs, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-arm-template-uiex
ms.openlocfilehash: 8b048127c8a5581a34a62cfb67c19ba7aa50f2d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101701579"
---
# <a name="quickstart-create-app-service-app-using-an-arm-template"></a>Início Rápido: Criar um aplicativo do Serviço de Aplicativo usando um modelo do ARM

Comece a usar o [Serviço de Aplicativo do Azure](overview.md) implantando um aplicativo na nuvem usando um modelo do ARM (modelo do Azure Resource Manager) e a [CLI do Azure](/cli/azure/get-started-with-azure-cli) no Cloud Shell. Como você usa uma Camada gratuita do Serviço de Aplicativo, não é gerado nenhum custo para a conclusão deste início rápido.

 [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

Use o seguinte botão para implantação no **Linux**:

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-linux%2Fazuredeploy.json)

Use o seguinte botão para implantação no **Windows**:

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-windows%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="review-the-template"></a>Examinar o modelo

::: zone pivot="platform-windows"
O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-app-service-docs-windows). Ele implanta um plano do Serviço de Aplicativo e um aplicativo do Serviço de Aplicativo no Windows. É compatível com .NET Core, .NET Framework, PHP, Node.js e aplicativos HTML estáticos. Para Java, confira [Criar aplicativo Java](./quickstart-java.md).

:::code language="json" source="~/quickstart-templates/101-app-service-docs-windows/azuredeploy.json":::

Há dois recursos do Azure definidos no modelo:

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): criar um plano do Serviço de Aplicativo.
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): criar um aplicativo do Serviço de Aplicativo.

Este modelo contém vários parâmetros predefinidos para sua conveniência. Confira a tabela abaixo para obter os padrões de parâmetro e as descrições deles:

| parâmetros | Type    | Valor padrão                | DESCRIPTION |
|------------|---------|------------------------------|-------------|
| webAppName | string  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | Nome do aplicativo |
| local   | string  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | Região do aplicativo |
| sku        | string  | "F1"                         | Tamanho da instância (F1 = Camada Gratuita) |
| Linguagem   | string  | ".net"                       | Pilha da linguagem de programação (.net, php, node, html) |
| helloWorld | booleano | Falso                        | True = Implantar aplicativo "Olá, Mundo" |
| repoUrl    | string  | " "                          | Repositório Git externo (opcional) |
::: zone-end
::: zone pivot="platform-linux"
O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-app-service-docs-linux). Ele implanta um plano do Serviço de Aplicativo e um aplicativo do Serviço de Aplicativo no Linux. É compatível com todas as linguagens de programação com suporte no Serviço de Aplicativo.

:::code language="json" source="~/quickstart-templates/101-app-service-docs-linux/azuredeploy.json":::

Há dois recursos do Azure definidos no modelo:

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): criar um plano do Serviço de Aplicativo.
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): criar um aplicativo do Serviço de Aplicativo.

Este modelo contém vários parâmetros predefinidos para sua conveniência. Confira a tabela abaixo para obter os padrões de parâmetro e as descrições deles:

| parâmetros | Type    | Valor padrão                | DESCRIPTION |
|------------|---------|------------------------------|-------------|
| webAppName | string  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | Nome do aplicativo |
| local   | string  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | Região do aplicativo |
| sku        | string  | "F1"                         | Tamanho da instância (F1 = Camada Gratuita) |
| linuxFxVersion   | string  | "DOTNETCORE&#124;3.0        | "Pilha da linguagem de programação &#124; Versão" |
| repoUrl    | string  | " "                          | Repositório Git externo (opcional) |

---
::: zone-end

## <a name="deploy-the-template"></a>Implantar o modelo

A CLI do Azure é usada aqui para implantar o modelo. Você também pode usar o portal do Azure, o Azure PowerShell e a API REST. Para saber mais sobre outros métodos de implantação, confira [Implantar modelos](../azure-resource-manager/templates/deploy-powershell.md).

O código a seguir cria um grupo de recursos, um plano do Serviço de Aplicativo e um aplicativo Web. Um grupo de recursos padrão, o plano do Serviço de Aplicativo e o local foram definidos para você. Substitua `<app-name>` por um nome de aplicativo global exclusivo (os caracteres válidos são `a-z`, `0-9` e `-`).

::: zone pivot="platform-windows"
Execute o código a seguir para implantar um aplicativo .NET Framework no Windows.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" helloWorld="true" webAppName="<app-name>" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-windows/azuredeploy.json"
::: zone-end
::: zone pivot="platform-linux"
Run the code below to create a Python app on Linux.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --parameters webAppName="<app-name>" linuxFxVersion="PYTHON|3.7" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-linux/azuredeploy.json"
```

Para implantar uma pilha de linguagem diferente, atualize `linuxFxVersion` com os valores apropriados. Os exemplos são mostrados abaixo. Para mostrar as versões atuais, execute o seguinte comando no Cloud Shell: `az webapp config show --resource-group myResourceGroup --name <app-name> --query linuxFxVersion`

| Linguagem    | Exemplo:                                               |
|-------------|------------------------------------------------------|
| **.NET**    | linuxFxVersion="DOTNETCORE&#124;3.0"                 |
| **PHP**     | linuxFxVersion="PHP&#124;7.4"                        |
| **Node.js** | linuxFxVersion="NODE&#124;10.15"                     |
| **Java**    | linuxFxVersion="JAVA&#124;1.8 &#124;TOMCAT&#124;9.0" |
| **Python**  | linuxFxVersion="PYTHON&#124;3.7"                     |
| **Ruby**    | linuxFxVersion="RUBY&#124;2.6"                       |

---
::: zone-end

> [!NOTE]
> Você pode encontrar mais [exemplos de modelo do Serviço de Aplicativo do Azure aqui](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sites).

## <a name="validate-the-deployment"></a>Validar a implantação

Navegue até `http://<app_name>.azurewebsites.net/` e verifique se ela foi criada.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando o grupo de recursos não for mais necessário, [exclua-o](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> Implantar a partir do Git local

> [!div class="nextstepaction"]
> [ASP.NET Core com o Banco de Dados SQL](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> Python com Postgres

> [!div class="nextstepaction"]
> [PHP com MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Conectar-se a Banco de Dados SQL do Azure com Java](../azure-sql/database/connect-query-java.md?toc=%2fazure%2fjava%2ftoc.json)

> [!div class="nextstepaction"]
> [Mapear domínio personalizado](app-service-web-tutorial-custom-domain.md)
