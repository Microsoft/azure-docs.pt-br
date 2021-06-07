---
title: Definir configurações de aplicativo para Aplicativos Web Estáticos do Azure
description: Aprenda a definir configurações de aplicativo para Aplicativos Web Estáticos do Azure
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: buhollan
ms.custom: devx-track-js
ms.openlocfilehash: 2886cb6433c4f8a65eb0661eded0ddd08248e137
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102219175"
---
# <a name="configure-application-settings-for-azure-static-web-apps-preview"></a>Definir configurações de aplicativo para Aplicativos Web Estáticos do Azure (Versão prévia)

As configurações de aplicativo contêm definições de configuração para valores que podem ser alterados, como cadeias de conexão de banco de dados. A adição de configurações de aplicativo permite que você modifique a entrada de configuração para seu aplicativo, sem precisar alterar o código do aplicativo.

As configurações de aplicativo são:

- Criptografado em repouso
- Copiado para ambientes [de preparo](review-publish-pull-requests.md) e de produção

Às vezes, as configurações de aplicativo também são chamadas de variáveis de ambiente.

> [!IMPORTANT]
> As configurações de aplicativo descritas neste artigo se aplicam somente à API de back-end de um Aplicativo Web Estático do Azure.
>
> Para obter informações sobre como usar variáveis de ambiente com seu aplicativo Web de front-end, consulte os documentos da sua [estrutura JavaScript](#javascript-frameworks-and-libraries) ou do [Gerador de site estático](#static-site-generators).

## <a name="prerequisites"></a>Pré-requisitos

- Um aplicativo dos Aplicativos Web Estáticos do Azure
- [CLI do Azure](/cli/azure/install-azure-cli)

## <a name="types-of-application-settings"></a>Tipos de configurações de aplicativo

Normalmente, há dois aspectos em um aplicativo de Aplicativos Web Estáticos do Azure. O primeiro é o aplicativo Web, ou conteúdo estático, que é representado por HTML, CSS, JavaScript e imagens. O segundo é a API de back-end, que é alimentada por um aplicativo do Azure Functions.

Este artigo demonstra como gerenciar configurações de aplicativo para a API de back-end no Azure Functions.

As configurações de aplicativo descritas neste artigo não podem ser usadas ou referenciadas em aplicativos Web estáticos. No entanto, muitas estruturas de front-end e geradores de site estáticos permitem o uso de variáveis de ambiente durante o desenvolvimento. No momento da compilação, essas variáveis são substituídas por seus valores no HTML ou JavaScript gerado. Como os dados em HTML e JavaScript são facilmente detectáveis pelo visitante do site, evite colocar informações confidenciais no aplicativo de front-end. As configurações que contêm dados confidenciais estão mais bem localizadas na parte da API do seu aplicativo.

Para obter informações sobre como usar variáveis de ambiente com sua estrutura ou biblioteca de JavaScript, consulte os artigos a seguir para obter mais detalhes.

### <a name="javascript-frameworks-and-libraries"></a>Estruturas e bibliotecas de JavaScript

- [Angular](https://angular.io/guide/build#configuring-application-environments)
- [React](https://create-react-app.dev/docs/adding-custom-environment-variables/)
- [Svelte](https://linguinecode.com/post/how-to-add-environment-variables-to-your-svelte-js-app)
- [Vue](https://cli.vuejs.org/guide/mode-and-env.html)

### <a name="static-site-generators"></a>Geradores de site estáticos

- [Gatsby](https://www.gatsbyjs.org/docs/environment-variables/)
- [Hugo](https://gohugo.io/getting-started/configuration/)
- [Jekyll](https://jekyllrb.com/docs/configuration/environments/)

## <a name="about-api-app-settings"></a>Sobre as configurações do aplicativo de API

As APIs nos Aplicativos Web Estáticos do Azure são fornecidas pelo Azure Functions, o que permite que você defina as configurações do aplicativo no arquivo _local.settings.json_. Esse arquivo define as configurações do aplicativo na propriedade `Values` da configuração.

O exemplo a seguir _local.settings.json_ mostra como adicionar um valor para `DATABASE_CONNECTION_STRING`.

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
  }
}
```

As configurações definidas na propriedade `Values` podem ser referenciadas no código como variáveis de ambiente, disponíveis no objeto `process.env`.

```js
const connectionString = process.env.DATABASE_CONNECTION_STRING;
```

O arquivo `local.settings.json` não é acompanhado pelo repositório do GitHub porque informações confidenciais, como cadeias de conexão de banco de dados, geralmente são incluídas no arquivo. Como as configurações locais permanecem em seu computador, você precisa carregar manualmente suas configurações no Azure.

Em geral, o carregamento de suas configurações é feito com pouca frequência, o que não é necessário em todas as compilações.

## <a name="uploading-application-settings"></a>Definindo configurações de aplicativo

Você pode definir as configurações do aplicativo por meio do portal do Azure ou com a CLI do Azure.

### <a name="using-the-azure-portal"></a>Usando o portal do Azure

O portal do Azure fornece uma interface para criar, atualizar e excluir configurações de aplicativo.

1. Navegue até o [Portal do Azure](https://portal.azure.com).

1. Na barra de pesquisa, pesquise por e selecione **Aplicativos Web Estáticos**.

1. Clique na opção **Configuração** na barra lateral.

1. Selecione o ambiente ao qual você deseja aplicar as configurações do aplicativo. Os ambientes de preparo são criados automaticamente quando uma solicitação de pull é gerada e são promovidos para produção depois que a solicitação de pull é mesclada. Você pode definir configurações de aplicativo por ambiente.

1. Clique no botão **Adicionar botão** para adicionar uma nova configuração de aplicativo.

    :::image type="content" source="media/application-settings/configuration.png" alt-text="Exibição de configuração dos Aplicativos Web Estáticos do Azure":::

1. Insira um **nome** e um **valor**.

1. Clique em **OK**.

1. Clique em **Save** (Salvar).

### <a name="using-the-azure-cli"></a>Usando a CLI do Azure

Você pode usar o comando `az rest` para fazer uploads em massa de suas configurações para o Azure. O comando aceita configurações de aplicativo como objetos JSON em uma propriedade pai chamada `properties`.

A maneira mais fácil de criar um arquivo JSON com os valores apropriados é criar uma versão modificada do seu arquivo _local.settings.json_.

1. Para garantir que o novo arquivo com dados confidenciais não seja exposto publicamente, adicione a entrada a seguir ao arquivo _.gitignore_.

   ```bash
   local.settings*.json
   ```

2. Em seguida, faça uma cópia do seu arquivo _local.settings.json_ e nomeie-o _local.settings.properties.json_.

3. Dentro do novo arquivo, remova todos os outros dados do arquivo, exceto as configurações do aplicativo, e renomeie `Values` para `properties`.

   Seu arquivo agora deve ter aparência semelhante à do seguinte exemplo:

   ```json
   {
     "properties": {
       "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
     }
   }
   ```

O comando CLI do Azure requer vários valores específicos para sua conta para executar o carregamento. Na janela _Visão geral_ do recurso Aplicativos Web Estáticos, você tem acesso às seguintes informações:

1. Nome do site estático
2. Nome do grupo de recursos
3. ID da assinatura

:::image type="content" source="media/application-settings/overview.png" alt-text="Visão geral dos Aplicativos Web Estáticos do Azure":::

4. Em um terminal ou linha de comando, execute o comando a seguir. Certifique-se de substituir os espaços reservados de `<YOUR_STATIC_SITE_NAME>`, `<YOUR_RESOURCE_GROUP_NAME>` e `<YOUR_SUBSCRIPTION_ID>` pelos valores na janela _Visão Geral_.

   ```bash
   az rest --method put --headers "Content-Type=application/json" --uri "/subscriptions/<YOUR_SUBSCRIPTION_ID>/resourceGroups/<YOUR_RESOURCE_GROUP_NAME>/providers/Microsoft.Web/staticSites/<YOUR_STATIC_SITE_NAME>/config/functionappsettings?api-version=2019-12-01-preview" --body @local.settings.properties.json
   ```

> [!IMPORTANT]
> O arquivo "local.settings.properties.json" deve estar no mesmo diretório em que esse comando é executado. Esse arquivo pode ser nomeado da forma que você desejar. Esse nome não é significativo.

### <a name="view-application-settings-with-the-azure-cli"></a>Exibir configurações de aplicativo com a CLI do Azure

As configurações de aplicativo estão disponíveis para exibição por meio da CLI do Azure.

- Em um terminal ou linha de comando, execute o comando a seguir. Certifique-se de substituir os espaços reservados `<YOUR_SUBSCRIPTION_ID>`, `<YOUR_RESOURCE_GROUP_NAME>` e `<YOUR_STATIC_SITE_NAME>` pelos seus valores.

   ```bash
   az rest --method post --uri "/subscriptions/<YOUR_SUBSCRIPTION_ID>/resourceGroups/<YOUR_RESOURCE_GROUP_NAME>/providers/Microsoft.Web/staticSites/<YOUR_STATIC_SITE_NAME>/listFunctionAppSettings?api-version=2019-12-01-preview"
   ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configurar o desenvolvimento local](local-development.md)
