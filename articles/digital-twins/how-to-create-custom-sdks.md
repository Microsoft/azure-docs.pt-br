---
title: Criar SDKs personalizados para o gêmeos digital do Azure com o autorest
titleSuffix: Azure Digital Twins
description: Consulte como gerar SDKs personalizados para usar o gêmeos digital do Azure com idiomas diferentes do C#.
author: baanders
ms.author: baanders
ms.date: 4/24/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-js
ms.openlocfilehash: e0c0d18dbb3596733d02430554fd40ec16180c64
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980653"
---
# <a name="create-custom-sdks-for-azure-digital-twins-using-autorest"></a>Criar SDKs personalizados para o gêmeos digital do Azure usando o REST

No momento, os únicos SDKs de plano de dados publicados para interagir com as APIs do Azure digital gêmeos são para .NET (C#), JavaScript e Java. Você pode ler sobre esses SDKs e as APIs em geral, em [*How-to: Use the Azure digital gêmeos APIs and SDKs*](how-to-use-apis-sdks.md). Se você estiver trabalhando em outra linguagem, este artigo mostrará como gerar seu próprio SDK do plano de dados no idioma de sua escolha, usando o REST.

>[!NOTE]
> Você também pode usar o autorest para gerar um SDK do plano de controle, se desejar. Para fazer isso, conclua as etapas neste artigo usando o arquivo **Swagger** (openapi) mais recente do plano de controle da [pasta Swagger do plano de controle](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/) em vez do plano de dados um.

## <a name="set-up-your-machine"></a>Configurar seu computador

Para gerar um SDK, será necessário:
* O [REST](https://github.com/Azure/autorest), versão 2.0.4413 (versão 3 não tem suporte no momento)
* [Node.js](https://nodejs.org) como um pré-requisito para o REST
* O arquivo Swagger (openapi) do Azure digital gêmeos **Data plano** mais recente da [pasta Swagger do plano de dados](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)e sua pasta de exemplos que o acompanha.  Baixe o arquivo do Swagger *digitaltwins.jsem* e sua pasta de exemplos em seu computador local.

Depois que o computador estiver equipado com tudo, na lista acima, você estará pronto para usar o REST para criar o SDK.

## <a name="create-the-sdk-with-autorest"></a>Criar o SDK com o REST 

Se você tiver Node.js instalado, poderá executar esse comando para verificar se você tem a versão correta do REST instalado:
```cmd/sh
npm install -g autorest@2.0.4413
```

Para executar o REST no arquivo do Azure digital gêmeos Swagger, siga estas etapas:
1. Copie o arquivo Swagger do Azure digital gêmeos e sua pasta de exemplos que o acompanha em um diretório de trabalho.
2. Use uma janela de prompt de comando para alternar para esse diretório de trabalho.
3. Execute o comando executar como a seguir. Substitua o `<language>` espaço reservado pelo idioma de sua escolha: `python` , `java` , `go` e assim por diante. (Você pode encontrar a lista completa de opções no [Leiame do autorest](https://github.com/Azure/autorest).)

```cmd/sh
autorest --input-file=digitaltwins.json --<language> --output-folder=ADTApi --add-credentials --azure-arm --namespace=ADTApi
```

Como resultado, você verá uma nova pasta chamada *ADTApi* em seu diretório de trabalho. Os arquivos do SDK gerados terão o namespace *ADTApi*. Você continuará a usar esse namespace por meio do restante dos exemplos de uso neste artigo.

O REST oferece suporte a uma ampla variedade de geradores de código de linguagem.

## <a name="add-the-sdk-to-a-visual-studio-project"></a>Adicionar o SDK a um projeto do Visual Studio

Você pode incluir os arquivos gerados pelo autorest diretamente em uma solução .NET. No entanto, é provável que você queira incluir o SDK do gêmeos digital do Azure em vários projetos separados (seus aplicativos cliente, aplicativos Azure Functions e assim por diante). Por esse motivo, pode ser útil criar um projeto separado (uma biblioteca de classes .NET) a partir dos arquivos gerados. Em seguida, você pode incluir esse projeto de biblioteca de classes em várias soluções como uma referência de projeto.

Esta seção fornece instruções sobre como criar o SDK como uma biblioteca de classes, que é seu próprio projeto e pode ser incluído em outros projetos. Essas etapas dependem do **Visual Studio** (você pode instalar a versão mais recente [aqui](https://visualstudio.microsoft.com/downloads/)).

Siga estas etapas:

1. Criar uma nova solução do Visual Studio para uma biblioteca de classes
2. Usar *ADTApi* como o nome do projeto
3. No Gerenciador de soluções, selecione o projeto *ADTApi* da solução gerada e escolha *Adicionar > item existente...*
4. Localize a pasta na qual você gerou o SDK e selecione os arquivos no nível raiz
5. Pressione "OK"
6. Adicione uma pasta ao projeto (selecione o projeto com o botão direito do mouse em Gerenciador de Soluções e escolha *adicionar > nova pasta*)
7. Nomear os *modelos* de pasta
8. Selecione a pasta *modelos* no Gerenciador de soluções e selecione *Adicionar > item existente...*
9. Selecione os arquivos na pasta *modelos* do SDK gerado e pressione "OK"

Para compilar o SDK com êxito, seu projeto precisará destas referências:
* `Microsoft.Rest.ClientRuntime`
* `Microsoft.Rest.ClientRuntime.Azure`

Para adicioná-los, abra *ferramentas > Gerenciador de pacotes nuget > gerenciar pacotes NuGet para solução...*.

1. No painel, certifique-se de que a guia *procurar* esteja selecionada
2. Pesquisar *Microsoft. REST*
3. Selecione os `ClientRuntime` pacotes e e `ClientRuntime.Azure` adicione-os à sua solução

Agora você pode criar o projeto e incluí-lo como uma referência de projeto em qualquer aplicativo de gêmeos digital do Azure que você escreve.

## <a name="general-guidelines-for-generated-sdks"></a>Diretrizes gerais para SDKs gerados

Esta seção contém informações gerais e diretrizes para usar o SDK gerado.

### <a name="synchronous-and-asynchronous-calls"></a>Chamadas síncronas e assíncronas

Todas as funções do SDK são fornecidas em versões síncronas e assíncronas.

### <a name="typed-and-untyped-data"></a>Dados digitados e não tipados

As chamadas à API REST geralmente retornam objetos fortemente tipados. No entanto, como o Azure digital gêmeos permite que os usuários definam seus próprios tipos personalizados para o gêmeos, não há como definir previamente os dados de retorno estáticos para muitas chamadas de gêmeos digitais do Azure. Em vez disso, as APIs retornam tipos de wrapper fortemente tipados, quando aplicável, e os dados de texto cruzado personalizado estão em objetos Json.NET (usados sempre que o tipo de dados "Object" aparece nas assinaturas de API). Você pode converter esses objetos adequadamente no seu código.

### <a name="error-handling"></a>Tratamento de erros

Sempre que ocorrer um erro no SDK (incluindo erros de HTTP, como 404), o SDK gerará uma exceção. Por esse motivo, é importante encapsular todas as chamadas de API nos blocos try/catch.

Aqui está um trecho de código que tenta adicionar um entrelaçamento e captura todos os erros nesse processo:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_errorHandling":::

### <a name="paging"></a>Paginamento

O REST gera dois tipos de padrões de paginação para o SDK:
* Um para todas as APIs, exceto a API de consulta
* Um para a API de consulta

No padrão de paginação não consulta, aqui está um método de exemplo que mostra como recuperar uma lista paginável de relações de saída do Azure digital gêmeos:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod":::

O segundo padrão é gerado somente para a API de consulta. Ele usa um `continuationToken` explicitamente.

>[!TIP]
> Um motivo principal para obter páginas é calcular os [encargos de unidade de consulta](concepts-query-units.md) para uma chamada à API de consulta.

Aqui está um exemplo com esse padrão:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="PagedQuery":::

## <a name="next-steps"></a>Próximas etapas

Percorra as etapas para criar um aplicativo cliente onde você pode usar seu SDK:
* [*Tutorial: Codificar um aplicativo cliente*](tutorial-code.md)
