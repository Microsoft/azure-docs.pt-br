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
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: bce6bfb61eb59d1fa66c550a133ac8b6f8d7f2c5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768991"
---
# <a name="quickstart-create-app-service-app-using-an-arm-template"></a>Início Rápido: Criar um aplicativo do Serviço de Aplicativo usando um modelo do ARM

Comece a usar o [Serviço de Aplicativo do Azure](overview.md) implantando um aplicativo na nuvem usando um <abbr title="Um arquivo JSON que define declarativamente um ou mais recursos do Azure e as dependências entre os recursos implantados. O modelo pode ser usado para implantar os recursos de forma consiste e repetida.">Modelo de ARM</abbr> e a [CLI do Azure](/cli/azure/get-started-with-azure-cli) no Cloud Shell. Como você usa uma Camada gratuita do Serviço de Aplicativo, não é gerado nenhum custo para a conclusão deste início rápido. <abbr title="Na sintaxe declarativa, você descreve a implantação pretendida sem gravar a sequência de comandos de programação para criar a implantação.">O modelo usa a sintaxe declarativa.</abbr>

 Se o seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de [modelos do ARM](../azure-resource-manager/templates/overview.md), selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

::: zone pivot="platform-windows"
[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-windows%2Fazuredeploy.json)
::: zone-end

::: zone pivot="platform-linux"
[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-linux%2Fazuredeploy.json)
::: zone-end

<hr/>

## <a name="1-prepare-your-environment"></a>1. Prepare o seu ambiente

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<hr/>

## <a name="2-review-the-template"></a>2. Examinar o modelo

::: zone pivot="platform-windows"
O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-app-service-docs-windows). Ele implanta um plano do Serviço de Aplicativo e um aplicativo do Serviço de Aplicativo no Windows.

:::code language="json" source="~/quickstart-templates/101-app-service-docs-windows/azuredeploy.json":::

<details>
<summary>Quais recursos e parâmetros são definidos no modelo?</summary>

Há dois recursos do Azure definidos no modelo:

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): criar um plano do Serviço de Aplicativo.
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): criar um aplicativo do Serviço de Aplicativo.

A seguinte tabela detalha os parâmetros padrão e suas descrições:

| Parâmetros | Type    | Valor padrão                | DESCRIPTION |
|------------|---------|------------------------------|-------------|
| webAppName | string  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | Nome do aplicativo |
| local   | string  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | Região do aplicativo |
| sku        | string  | "F1"                         | Tamanho da instância (F1 = Camada Gratuita) |
| Linguagem   | string  | ".net"                       | Pilha da linguagem de programação (.net, php, node, html) |
| helloWorld | booleano | Falso                        | True = Implantar aplicativo "Olá, Mundo" |
| repoUrl    | string  | " "                          | Repositório Git externo (opcional) |

---

</details>
::: zone-end
::: zone pivot="platform-linux"
O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-app-service-docs-linux). Ele implanta um plano do Serviço de Aplicativo e um aplicativo do Serviço de Aplicativo no Windows.

:::code language="json" source="~/quickstart-templates/101-app-service-docs-linux/azuredeploy.json":::

Este modelo inclui recursos e parâmetros do Azure que são definidos para sua conveniência.

<details>
<summary>Quais recursos e parâmetros são definidos no modelo?</summary>

Há dois recursos do Azure definidos no modelo:

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): criar um plano do Serviço de Aplicativo.
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): criar um aplicativo do Serviço de Aplicativo.

A seguinte tabela detalha os parâmetros padrão e suas descrições:

| Parâmetros | Type    | Valor padrão                | DESCRIPTION |
|------------|---------|------------------------------|-------------|
| webAppName | string  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | Nome do aplicativo |
| local   | string  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | Região do aplicativo |
| sku        | string  | "F1"                         | Tamanho da instância (F1 = Camada Gratuita) |
| linuxFxVersion   | string  | "DOTNETCORE&#124;3.0        | "Pilha da linguagem de programação &#124; Versão" |
| repoUrl    | string  | " "                          | Repositório Git externo (opcional) |

---

</details>

::: zone-end

<hr/>

## <a name="3-deploy-the-template"></a>3. Implantar o modelo

::: zone pivot="platform-windows"
Execute o código a seguir para implantar um aplicativo .NET Framework no Windows usando a CLI do Azure. 

Substitua <abbr title="Os caracteres válidos são `a-z`, `0-9` e `-`.">`<app-name>`</abbr> por um nome de aplicativo globalmente exclusivo. Para conhecer outros <abbr title="Você também pode usar o portal do Azure, o Azure PowerShell e a API REST.">métodos de implantação,</abbr>confira [Implantar modelos](../azure-resource-manager/templates/deploy-powershell.md). Você pode encontrar mais [exemplos de modelo do Serviço de Aplicativo do Azure aqui](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sites).

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" helloWorld="true" webAppName="<app-name>" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-windows/azuredeploy.json"
```
::: zone-end
::: zone pivot="platform-linux"
Execute o código a seguir para criar um aplicativo Python no Linux. 

Substitua <abbr title="Os caracteres válidos são `a-z`, `0-9` e `-`.">`<app-name>`</abbr> por um nome de aplicativo globalmente exclusivo.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --parameters webAppName="<app-name>" linuxFxVersion="PYTHON|3.7" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-linux/azuredeploy.json"
```
::: zone-end

<details>
<summary>O que o código está fazendo?</summary>
<p>Esses comandos executam as seguintes ações:</p>
<ul>
<li>Criar um padrão <abbr title="Um contêiner lógico para recursos do Azure relacionados que você pode gerenciar como uma unidade.">grupo de recursos</abbr>.</li>
<li>Criar um padrão <abbr title="O plano que especifica o local, o tamanho e os recursos do farm de servidores Web que hospeda o aplicativo.">Plano do Serviço de Aplicativo</abbr>.</li>
<li><a href="/cli/azure/webapp#az_webapp_create">Criar um <abbr title="A representação do aplicativo Web, que contém o código do aplicativo, os nomes de host DNS, os certificados e os recursos relacionados.">Aplicativo do Serviço de Aplicativo</abbr></a> com o nome especificado.</li>
</ul>
</details>

::: zone pivot="platform-windows"
<details>
<summary>Como implantar uma pilha de linguagem diferente?</summary>
Para implantar uma pilha de linguagem diferente, atualize <abbr title="Este modelo é compatível com .NET Core, .NET Framework, PHP, Node.js e aplicativos HTML estáticos. parâmetro de linguagem"></abbr> pelos valores apropriados. Para Java, confira <a href="/azure/app-service/quickstart-java-uiex">Criar aplicativo Java</a>.

| Parâmetros | Type    | Valor padrão                | Descrição |
|------------|---------|------------------------------|-------------|
| Linguagem   | string  | ".net"                       | Pilha da linguagem de programação (.net, php, node, html) |

---

</details>
::: zone-end
::: zone pivot="platform-linux"
<details>
<summary>Como implantar uma pilha de linguagem diferente?</summary>
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

</details>
::: zone-end

<hr/>

## <a name="4-validate-the-deployment"></a>4. Validar a implantação

Navegue até `http://<app_name>.azurewebsites.net/` e verifique se ela foi criada.

<hr/>

## <a name="5-clean-up-resources"></a>5. Limpar os recursos

Quando o grupo de recursos não for mais necessário, [exclua-o](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group).

<hr/>

## <a name="next-steps"></a>Próximas etapas

- Implantar a partir do Git local
- [ASP.NET Core com o Banco de Dados SQL](tutorial-dotnetcore-sqldb-app.md)
- Python com Postgres
- [PHP com MySQL](tutorial-php-mysql-app.md)
- [Conectar-se a Banco de Dados SQL do Azure com Java](../azure-sql/database/connect-query-java.md?toc=%2fazure%2fjava%2ftoc.json)
- [Mapear domínio personalizado](app-service-web-tutorial-custom-domain-uiex.md)
