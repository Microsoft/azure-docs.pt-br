---
title: Analisar e validar modelos
titleSuffix: Azure Digital Twins
description: Saiba como usar a biblioteca do analisador para analisar modelos de DTDL.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q3
ms.openlocfilehash: 155566a125485fda326f9f5e02d4aead0ffe30e3
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560746"
---
# <a name="parse-and-validate-models-with-the-dtdl-parser-library"></a>Analisar e validar modelos com a biblioteca do analisador DTDL

Os [modelos](concepts-models.md) no Azure digital gêmeos são definidos usando o DTDL (digital gêmeos Definition Language) baseado em JSON-ld. **É recomendável validar seus modelos offline antes de carregá-los na instância do gêmeos digital do Azure.**

Para ajudá-lo a fazer isso, uma biblioteca de análise de DTDL do lado do cliente .NET é fornecida no NuGet: [**Microsoft. Azure. DigitalTwins. Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). 

Você pode usar a biblioteca do analisador diretamente em seu código C# ou usar o projeto de exemplo de código independente de linguagem criado na biblioteca do analisador: [**exemplo de validador DTDL**](/samples/azure-samples/dtdl-validator/dtdl-validator).

## <a name="use-the-dtdl-validator-sample"></a>Usar o exemplo de validador DTDL

O [**validador DTDL**](/samples/azure-samples/dtdl-validator/dtdl-validator) é um projeto de exemplo que pode validar documentos de modelo para verificar se o DTDL é válido. Ele é criado na biblioteca do analisador do .NET e não é independente de linguagem. Você pode obtê-lo com o botão *baixar zip* no link de exemplo.

O código-fonte mostra exemplos de como usar a biblioteca do analisador. Você pode usar a amostra do validador como um utilitário de linha de comando para validar uma árvore de diretórios de arquivos DTDL. Ele também fornece um modo interativo.

Na pasta do exemplo do validador DTDL, consulte o arquivo *README.MD* para obter instruções sobre como empacotar o exemplo em um executável independente.

Depois de criar um pacote autocontido e adicionar o executável ao seu caminho, você poderá executar o validador com esse comando em um console do em seu computador:

```cmd/sh
DTDLValidator
```

Com as opções padrão, o exemplo procurará por `*.json` arquivos no diretório atual e em todos os subdiretórios. Você também pode adicionar a opção a seguir para fazer com que a pesquisa de exemplo no diretório indicado e todos os subdiretórios de arquivos com a extensão *. dtdl*:

```cmd/sh
DTDLValidator -d C:\Work\DTDL -e dtdl 
```

Você pode adicionar a `-i` opção para o exemplo entrar no modo interativo:

```cmd/sh
DTDLValidator -i
```

Para obter mais informações sobre este exemplo, consulte o código-fonte ou execute `DTDLValidator --help` .

## <a name="use-the-net-parser-library"></a>Usar a biblioteca do analisador do .NET 

A biblioteca [**Microsoft. Azure. DigitalTwins. Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/) fornece acesso de modelo às definições de DTDL, essencialmente agindo como equivalente à reflexão de C# para DTDL. Essa biblioteca pode ser usada independentemente de qualquer [SDK do gêmeos digital do Azure](how-to-use-apis-sdks.md), especialmente para validação de DTDL em um editor de texto ou Visual. É útil para verificar se os arquivos de definição de modelo são válidos antes de tentar carregá-los no serviço.

Para usar a biblioteca do analisador, você fornece um conjunto de documentos DTDL. Normalmente, você recuperaria esses documentos de modelo do serviço, mas também poderá tê-los disponíveis localmente, se o cliente tiver sido responsável por carregá-los no serviço em primeiro lugar. 

Este é o fluxo de trabalho geral para usar o analisador:
1. Recupere alguns ou todos os documentos do DTDL do serviço.
2. Passe os documentos de DTDL retornados e na memória para o analisador.
3. O analisador validará o conjunto de documentos passados para ele e retornará informações detalhadas sobre o erro. Essa capacidade é útil em cenários de editor.
4. Use as APIs do analisador para continuar analisando os modelos incluídos no conjunto de documentos. 

Os recursos do analisador incluem:
* Obter todas as interfaces de modelo implementadas (o conteúdo da `extends` seção da interface).
* Obtenha todas as propriedades, telemetria, comandos, componentes e relações declaradas no modelo. Esse comando também obtém todos os metadados incluídos nessas definições e usa a herança ( `extends` seções) em conta.
* Obter todas as definições de modelo complexas.
* Determine se um modelo pode ser atribuído de outro modelo.

> [!NOTE]
> Dispositivos [de IoT plug and Play (PnP)](../iot-pnp/overview-iot-plug-and-play.md) usam uma variante de sintaxe pequena para descrever sua funcionalidade. Essa variante de sintaxe é um subconjunto compatível semanticamente do DTDL que é usado no gêmeos digital do Azure. Ao usar a biblioteca do analisador, você não precisa saber qual variante de sintaxe foi usada para criar o DTDL para sua grade digital. O analisador sempre retornará, por padrão, o mesmo modelo para a sintaxe PnP e gêmeos digital do Azure.

### <a name="code-with-the-parser-library"></a>Código com a biblioteca do analisador

Você pode usar a biblioteca do analisador diretamente, para coisas como validação de modelos em seu próprio aplicativo ou para gerar interface do usuário, painéis e relatórios dinâmicos, controlados por modelos.

Para dar suporte ao exemplo de código do analisador abaixo, considere vários modelos definidos em uma instância do gêmeos digital do Azure:

:::code language="json" source="~/digital-twins-docs-samples/models/coffeeMaker-coffeeMakerInterface-coffeeBar.json":::

O código a seguir mostra um exemplo de como usar a biblioteca do analisador para refletir sobre essas definições em C#:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/parseModels.cs":::

## <a name="next-steps"></a>Próximas etapas

Quando terminar de escrever seus modelos, consulte como carregá-los (e outras operações de gerenciamento) com as APIs DigitalTwinsModels:
* [*Como: gerenciar modelos de DTDL*](how-to-manage-model.md)