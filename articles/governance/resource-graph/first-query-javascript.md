---
title: 'Início Rápido: Sua primeira consulta JavaScript'
description: Neste guia de início rápido, você seguirá as etapas necessárias para habilitar a biblioteca do Resource Graph para JavaScript e executará sua primeira consulta.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: ef93378acaf2c92cf4f7761345cea326af63d300
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533056"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-javascript"></a>Início Rápido: Executar sua primeira consulta do Resource Graph usando JavaScript

Este guia de início rápido descreve o processo de adição das bibliotecas à instalação do JavaScript. A primeira etapa para o uso do Azure Resource Graph é inicializar um aplicativo JavaScript com as bibliotecas necessárias.

Ao final desse processo, você terá adicionado as bibliotecas à instalação do JavaScript e executado sua primeira consulta do Resource Graph.

## <a name="prerequisites"></a>Pré-requisitos

- **Assinatura do Azure**: caso você não tenha uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

- **Node.js**: o [Node.js](https://nodejs.org/) versão 12 ou superior é necessário.

## <a name="application-initialization"></a>Inicialização de aplicativo

A fim de habilitar o JavaScript para consultar o Azure Resource Graph, o ambiente precisa ser configurado. Essa configuração funciona sempre que o JavaScript pode ser usado, incluindo o [Bash no Windows 10](/windows/wsl/install-win10).

1. Inicialize um novo projeto Node.js executando o comando a seguir.

   ```bash
   npm init -y
   ```

1. Adicione uma referência ao módulo yargs.

   ```bash
   npm install yargs
   ```

1. Adicione uma referência ao módulo do Azure Resource Graph.

   ```bash
   npm install @azure/arm-resourcegraph
   ```

1. Adicione uma referência à biblioteca de autenticação do Azure.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > Em _package.json_, confirme se a versão de `@azure/arm-resourcegraph` é **2.0.0** ou superior e se a versão de `@azure/ms-rest-nodeauth` é **3.0.3** ou superior.

## <a name="query-the-resource-graph"></a>Consultar o Resource Graph

1. Crie um arquivo chamado _index.js_ e insira o código a seguir.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const resourceGraph = require("@azure/arm-resourcegraph");

   if (argv.query && argv.subs) {
       const subscriptionList = argv.subs.split(",");

       const query = async () => {
          const credentials = await authenticator.interactiveLogin();
          const client = new resourceGraph.ResourceGraphClient(credentials);
          const result = await client.resources(
             {
                 query: argv.query,
                 subscriptions: subscriptionList,
             },
             { resultFormat: "table" }
          );
          console.log("Records: " + result.totalRecords);
          console.log(result.data);
       };

       query();
   }
   ```

1. Insira o seguinte comando no terminal:

   ```bash
   node index.js --query "Resources | project name, type | limit 5" --subs <YOUR_SUBSCRIPTION_ID_LIST>
   ```

   Lembre-se de substituir o espaço reservado `<YOUR_SUBSCRIPTION_ID_LIST>` pela lista separada por vírgula de IDs da assinatura do Azure.

   > [!NOTE]
   > Por essa consulta de exemplo não fornecer um modificador de classificação, como `order by`, executar essa consulta várias vezes provavelmente produzirá um conjunto diferente de recursos por solicitação.

1. Altere o primeiro parâmetro para `index.js` e altere a consulta para `order by` a propriedade **Name**. Substitua `<YOUR_SUBSCRIPTION_ID_LIST>` pela ID da assinatura:

   ```bash
   node index.js --query "Resources | project name, type | limit 5 | order by name asc" --subs "<YOUR_SUBSCRIPTION_ID_LIST>"
   ```

   À medida que o script tenta se autenticar, uma mensagem semelhante à seguinte é exibida no terminal:

   > Para se conectar, use um navegador da Web para abrir a página https://microsoft.com/devicelogin e insira o código FGB56WJUGK para se autenticar.

   Depois que você se autenticar no navegador, o script continuará sendo executado.

   > [!NOTE]
   > Assim como ocorre com a primeira consulta, executar esta consulta várias vezes provavelmente produzirá um conjunto diferente de recursos por solicitação. A ordem dos comandos de consulta é importante. Neste exemplo, o `order by` vem após o `limit`. Essa ordem de comando limita primeiro os resultados da consulta e os ordena.

1. Altere o primeiro parâmetro para `index.js` e altere a consulta para, primeiro, `order by` a propriedade **Name** e depois `limit` conforme os primeiros cinco resultados. Substitua `<YOUR_SUBSCRIPTION_ID_LIST>` pela ID da assinatura:

   ```bash
   node index.js --query "Resources | project name, type | order by name asc | limit 5" --subs "<YOUR_SUBSCRIPTION_ID_LIST>"
   ```

Quando a consulta final for executada várias vezes, supondo que nada em seu ambiente esteja sendo alterado, os resultados retornados serão consistentes e ordenados segundo a propriedade **Name**, mas ainda limitados aos cinco primeiros resultados.

## <a name="clean-up-resources"></a>Limpar os recursos

Caso deseje remover as bibliotecas instaladas do aplicativo, execute o comando a seguir.

```bash
npm uninstall @azure/arm-resourcegraph @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você adicionou as bibliotecas do Resource Graph ao seu ambiente do JavaScript e executou sua primeira consulta. Para saber mais sobre a linguagem do Resource Graph, prossiga para a página de detalhes da linguagem de consulta.

> [!div class="nextstepaction"]
> [Obter mais informações sobre a linguagem de consulta](./concepts/query-language.md)
