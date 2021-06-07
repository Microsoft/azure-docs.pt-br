---
title: Criar SDKs de linguagem personalizada com o AutoRest
titleSuffix: Azure Digital Twins
description: Saiba como usar o Write-REST para gerar SDKs de idioma personalizado para escrever o código de gêmeos digital do Azure em outras linguagens que não têm SDKs publicados.
author: baanders
ms.author: baanders
ms.date: 3/9/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom:
- devx-track-js
- contperf-fy21q3
ms.openlocfilehash: 35cf54199f8f2c187ad397c21fb941111f07c4a3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561833"
---
# <a name="create-custom-language-sdks-for-azure-digital-twins-using-autorest"></a>Criar SDKs de idioma personalizado para o gêmeos digital do Azure usando o REST

Se você precisar trabalhar com o gêmeos digital do Azure usando uma linguagem que não tenha um [SDK do gêmeos digital do Azure publicado](how-to-use-apis-sdks.md), este artigo mostrará como usar o autorest para gerar seu próprio SDK no idioma de sua escolha. 

Os exemplos neste artigo mostram a criação de um [SDK do plano de dados](how-to-use-apis-sdks.md#overview-data-plane-apis), mas esse processo também funcionará para gerar um SDK do plano de  [controle](how-to-use-apis-sdks.md#overview-control-plane-apis) .

## <a name="prerequisites"></a>Pré-requisitos

Para gerar um SDK, primeiro você precisará concluir a configuração a seguir em seu computador local:
* Instalar o [**autorest**](https://github.com/Azure/autorest), versão 2.0.4413 (versão 3 não tem suporte no momento)
* Instalar [**Node.js**](https://nodejs.org), que é um pré-requisito para usar o autorest
* Instalar o [ **Visual Studio**](https://visualstudio.microsoft.com/downloads/)
* Baixe o arquivo Swagger (openapi) do Azure digital gêmeos **Data plano** mais recente da [pasta Swagger do plano de dados](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins), juntamente com a pasta que o acompanha, de exemplos. O arquivo do Swagger é aquele chamado *digitaltwins.js*.

>[!TIP]
> Para criar um **SDK do plano de controle** em vez disso, conclua as etapas neste artigo usando o arquivo Swagger (openapi) mais recente do **plano** de controle da [pasta Swagger do plano de controle](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/) , em vez do plano de dados um.

Quando o computador estiver equipado com tudo, na lista acima, você estará pronto para usar o REST para criar um SDK.

## <a name="create-the-sdk-using-autorest"></a>Criar o SDK usando o REST 

Depois de instalar Node.js, você pode executar esse comando para verificar se você tem a versão necessária do REST instalado:
```cmd/sh
npm install -g autorest@2.0.4413
```

Para executar o REST no arquivo do Azure digital gêmeos Swagger, siga estas etapas:
1. Copie o arquivo Swagger do Azure digital gêmeos e sua pasta de exemplos que o acompanha em um diretório de trabalho.
2. Use uma janela de prompt de comando para alternar para esse diretório de trabalho.
3. Execute o comando executar como a seguir. Substitua o `<language>` espaço reservado pelo idioma de sua escolha: `python` , `java` , `go` e assim por diante. (Você pode encontrar a lista completa de opções no [Leiame do autorest](https://github.com/Azure/autorest).)

```cmd/sh
autorest --input-file=digitaltwins.json --<language> --output-folder=DigitalTwinsApi --add-credentials --azure-arm --namespace=DigitalTwinsApi
```

Como resultado, você verá uma nova pasta chamada *DigitalTwinsApi* em seu diretório de trabalho. Os arquivos do SDK gerados terão o namespace *DigitalTwinsApi*. Você continuará a usar esse namespace por meio do restante dos exemplos de uso neste artigo.

O REST oferece suporte a uma ampla variedade de geradores de código de linguagem.

## <a name="make-the-sdk-into-a-class-library"></a>Tornar o SDK em uma biblioteca de classes

Você pode incluir os arquivos gerados pelo autorest diretamente em uma solução .NET. No entanto, é provável que você queira incluir o SDK do gêmeos digital do Azure em vários projetos separados (seus aplicativos cliente, Azure Functions aplicativos e muito mais). Por esse motivo, pode ser útil criar um projeto separado (uma biblioteca de classes .NET) a partir dos arquivos gerados. Em seguida, você pode incluir esse projeto de biblioteca de classes em várias soluções como uma referência de projeto.

Esta seção fornece instruções sobre como criar o SDK como uma biblioteca de classes, que é seu próprio projeto e pode ser incluído em outros projetos. Essas etapas dependem do **Visual Studio**.

Estas são as etapas:

1. Criar uma nova solução do Visual Studio para uma biblioteca de classes
2. Usar *DigitalTwinsApi* como o nome do projeto
3. No Gerenciador de soluções, selecione o projeto *DigitalTwinsApi* da solução gerada e escolha *Adicionar > item existente...*
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

## <a name="tips-for-using-the-sdk"></a>Dicas para usar o SDK

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
