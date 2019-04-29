---
title: Atualizar para o SDK do .NET do Azure Search versão 5 – Azure Search
description: Migre o código para o SDK do .NET do Azure Search versão 5 de versões mais antigas. Conheça as novidades e quais alterações de código são necessárias.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: d7684aa79ac9f58c2a047b01a6d9f5263795221d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61291700"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-5"></a>Atualizando para o SDK do .NET do Azure Search versão 5
Se você estiver usando a visualização da versão 4.0 ou mais antiga do [SDK .NET do Azure Search](https://aka.ms/search-sdk), este artigo ajudará você a atualizar seu aplicativo para usar a versão 5.

Para obter uma explicação mais geral do SDK, incluindo exemplos, confira [Como usar o Azure Search de um aplicativo .NET](search-howto-dotnet-sdk.md).

A versão 5 do SDK do .NET do Azure Search contém algumas alterações de versões anteriores. A maioria das alterações é leve e, portanto, a alteração do seu código não deve exigir muito. Confira [Etapas da atualização](#UpgradeSteps) para obter instruções sobre como alterar o seu código para usar a nova versão do SDK.

> [!NOTE]
> Se você estiver usando a visualização da versão 2.0 ou mais antiga, será necessário primeiro atualizar para a versão 3 e, em seguida, atualizar para a versão 5. Confira [Atualizando para o SDK do .NET do Azure Search versão 3](search-dotnet-sdk-migration.md) para obter instruções.
>
> Sua instância de serviço do Azure Search dá suporte a várias versões da API REST, incluindo a última. Você pode continuar usando uma versão quando ela não for mais a última, mas aconselhamos a migrar seu código para usar a versão mais recente. Ao usar a API REST, você deve especificar a versão da API em cada solicitação por meio do parâmetro api-version. Ao usar o SDK do .NET, a versão do SDK que você está usando determina a versão correspondente da API REST. Se estiver usando um SDK mais antigo, você poderá continuar executando esse código sem alterações, mesmo se o serviço for atualizado para dar suporte a uma versão de API mais recente.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-5"></a>Novidades na versão 5
A versão 5 do SDK do .NET do Azure Search destina-se à versão mais recente disponível da API REST do Azure Search, especificamente a 2017-11-11. Isso possibilita o uso de vários recursos novos do Azure Search por meio de um aplicativo .NET, incluindo o seguinte:

* [Sinônimos](search-synonyms.md).
* Você agora pode acessar programaticamente avisos no histórico de execução do indexador (consulte a `Warning` propriedade `IndexerExecutionResult` em [referência do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet) para obter mais detalhes).
* Suporte para .NET Core 2.
* Oferece suporte à nova estrutura de pacote usando apenas as partes do SDK que você precisa (consulte [alterações significativas na versão 5](#ListOfChanges) para obter detalhes).

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Etapas da atualização
Primeiro, atualize sua referência NuGet para `Microsoft.Azure.Search` usando o Console do Gerenciador de Pacotes NuGet ou clicando com o botão direito do mouse nas referências do projeto e selecionando “Gerenciar pacotes NuGet...” no Visual Studio.

Depois que o NuGet tiver baixado os novos pacotes e suas dependências, recompile o projeto. Dependendo de como o código está estruturado, ele poderá ser recriado com êxito. Nesse caso, você está pronto para começar!

Se o build falhar, você verá um erro de build semelhante ao seguinte:

    The name 'SuggesterSearchMode' does not exist in the current context

A próxima etapa é corrigir esse erro de build. Consulte [Alterações da falha na versão 5](#ListOfChanges) para obter detalhes sobre a causa do erro e como corrigi-lo.

Observe que, devido a alterações no empacotamento do SDK .NET do Azure Search, você deve recriar seu aplicativo para usar a versão 5. Essas alterações são detalhadas nas [Alterações significativas na versão 5](#ListOfChanges).

Você pode ver avisos de build adicionais relacionados a propriedades ou métodos obsoletos. Os avisos incluirão instruções sobre o que deve ser usado no lugar do recurso preterido. Por exemplo, se seu aplicativo usa o `IndexingParametersExtensions.DoNotFailOnUnsupportedContentType` método, você deve receber um aviso que diz "Esse comportamento está habilitado por padrão, portanto, chamar esse método não é mais necessário."

Após corrigir todos os avisos ou erros de build, você poderá fazer alterações no aplicativo para aproveitar as novas funcionalidades, se desejar. Novos recursos no SDK são detalhados em [Novidades na versão 5](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-5"></a>Alterações significativas na versão 5

### <a name="new-package-structure"></a>Nova estrutura de pacote

A alteração de falha mais significativa na versão 5 é que o `Microsoft.Azure.Search` assembly e seu conteúdo foram dividido em quatro assemblies separados que agora são distribuídos como quatro pacotes do NuGet separados:

 - `Microsoft.Azure.Search`: Esse é um metapacote que inclui todos os outros pacotes do Azure Search como dependências. Se você estiver atualizando de uma versão anterior do SDK, a atualização e recriação deste pacote devem ser suficientes para iniciar usando uma nova versão.
 - `Microsoft.Azure.Search.Data`: Use esse pacote se estiver desenvolvendo um aplicativo .NET usando o Azure Search e só precisa consultar ou atualizar documentos nos índices. Se você também precisa criar ou atualizar índices, mapas de sinônimo ou outros recursos de nível de serviço, use o `Microsoft.Azure.Search` pacote em vez disso.
 - `Microsoft.Azure.Search.Service`: Use esse pacote se estiver desenvolvendo a automação no .NET para gerenciar índices, mapas de sinônimos, indexadores, fontes de dados ou outros recursos no nível do serviço do Azure Search. Se você precisar apenas consultar ou atualizar em seus índices, use o `Microsoft.Azure.Search.Data` pacote em vez disso. Se você precisar de toda a funcionalidade do Azure Search, use o `Microsoft.Azure.Search` pacote em vez disso.
 - `Microsoft.Azure.Search.Common`: Tipos comuns necessários para as bibliotecas .NET do Azure Search. Você não precisa usar esse pacote diretamente em seu aplicativo; somente é destinada a ser usado como uma dependência.
 
Essa alteração está interrompendo tecnicamente porque muitos tipos foram movidos entre os assemblies. É por isso que recriar seu aplicativo é necessário para atualizar para a versão 5 do SDK.

Há algumas outras alterações significativas na versão 5 que podem exigir alterações de código, além da recompilação do aplicativo.

### <a name="change-to-suggesters"></a>Alterar para sugestores 

O construtor `Suggester` não tem mais um parâmetro `enum` para `SuggesterSearchMode`. Essa enumeração tinha apenas um valor e, portanto, era redundante. Se você observar erros de build como um resultado disso, basta remover as referências para o parâmetro `SuggesterSearchMode`.

### <a name="removed-obsolete-members"></a>Remover membros obsoletos

Você pode ver erros de build relacionados a métodos ou propriedades que foram marcados como obsoletos nas versões anteriores e subsequentemente removidos na versão 5. Se você encontrar esses erros, veja aqui como resolvê-los:

- Se você estiver usando o `IndexingParametersExtensions.IndexStorageMetadataOnly` método, use `SetBlobExtractionMode(BlobExtractionMode.StorageMetadata)` em vez disso.
- Se você estiver usando o `IndexingParametersExtensions.SkipContent` método, use `SetBlobExtractionMode(BlobExtractionMode.AllMetadata)` em vez disso.

### <a name="removed-preview-features"></a>Recursos de visualização removidos

Se você estiver atualizando da visualização da versão 4.0 para a versão 5, lembre-se de que o suporte a análise da matriz JSON e CSV para a indexadores de Blobs foi removido, visto que esses recursos ainda estão em visualização. Especificamente, os seguintes métodos da classe `IndexingParametersExtensions` foram removidos:

- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Se seu aplicativo tiver uma dependência rígida desses recursos, você não poderá atualizar para a versão 5 do SDK do .NET do Azure Search. Você pode continuar a usar a visualização da versão 4.0. No entanto, lembre-se que **não é recomendável usar SDKs de visualização em aplicativos de produção**. Os recursos de visualização destinam-se apenas a fins de avaliação e podem ser alterados.

## <a name="conclusion"></a>Conclusão
Se precisar de mais detalhes sobre como usar o SDK do .NET do Azure Search, confira o [Tutorial .NET](search-howto-dotnet-sdk.md).

Apreciamos os seus comentários sobre o SDK. Se tiver problemas, fique à vontade para solicitar ajuda no [Fórum do MSDN sobre o Azure Search](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Se encontrar um bug, você poderá apresentar um problema no [repositório GitHub sobre o SDK do .NET do Azure](https://github.com/Azure/azure-sdk-for-net/issues). Não deixe de colocar o prefixo "[Azure Search]" no título do problema.

Obrigado por usar o Azure Search!
