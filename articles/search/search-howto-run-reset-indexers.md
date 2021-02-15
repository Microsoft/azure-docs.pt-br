---
title: Executar ou redefinir indexadores
titleSuffix: Azure Cognitive Search
description: Redefinir um indexador, habilidades ou documentos individuais para atualizar todo ou parte do e do índice ou da loja de conhecimento.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: 06f9d336bb04aa26cea4ebcdae1cfd045e1c2a1b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361240"
---
# <a name="how-to-run-or-reset-indexers-skills-or-documents"></a>Como executar ou redefinir indexadores, habilidades ou documentos

A execução do indexador pode ocorrer quando você cria o [indexador](search-indexer-overview.md)pela primeira vez, ao executar um indexador sob demanda ou ao definir um indexador em um agendamento. Após a execução inicial, um indexador mantém o controle de quais documentos de pesquisa foram indexados por meio de uma "marca d' água alta" interna. O marcador nunca é exposto na API, mas internamente o indexador sabe onde a indexação parou para que possa continuar de onde parou na próxima execução.

Você pode limpar a marca d' água alta redefinindo o indexador se desejar reprocessar do zero. As APIs de redefinição estão disponíveis em níveis decrescentes na hierarquia de objetos:

+ Toda a corpus de pesquisa (use os [indexadores de redefinição](#reset-indexers))
+ Um documento ou lista de documentos específicos (use [Redefinir documentos-visualização](#reset-docs))
+ Uma habilidade ou um enriquecimento específico em um documento (use [redefinição de habilidades-visualização](#reset-skills))

As APIs de redefinição são usadas para atualizar o conteúdo em cache (aplicável em cenários de [enriquecimento de ia](cognitive-search-concept-intro.md) ) ou para limpar a marca d' água alta e recriar o índice.

Redefinir, seguido por executar, pode reprocessar documentos existentes e novos documentos, mas não remove documentos de pesquisa órfãos no índice de pesquisa que foram criados em execuções anteriores. Para obter mais informações sobre exclusão, consulte [Adicionar, atualizar ou excluir documentos](/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="run-indexers"></a>Executar indexadores

[Criar indexador](/rest/api/searchservice/create-indexer) cria e executa o indexador, a menos que você o crie em um estado desabilitado ("Disabled": true). A primeira execução leva um pouco mais de tempo porque sua criação de objeto de cobertura também.

[Executar o indexador](/rest/api/searchservice/run-indexer) detectará e processará apenas o que for necessário para sincronizar o índice de pesquisa com a fonte de dados. O armazenamento de BLOBs tem detecção de alteração interna. Outras fontes de dados, como o Azure SQL ou Cosmos DB, precisam ser configuradas para detecção de alteração antes que o indexador possa ler apenas as linhas novas e atualizadas.

Você pode executar um indexador usando qualquer uma dessas abordagens:

+ Portal do Azure, usando o comando **executar** na página do indexador
+ [Executar indexador (REST)](/rest/api/searchservice/run-indexer)
+ [Método RunIndexers](/dotnet/api/azure.search.documents.indexes.searchindexerclient.runindexer) no SDK do .net do Azure (ou usando o método RunIndexer equivalente em outro SDK)

A execução do indexador está sujeita aos seguintes limites:

+ O número máximo de trabalhos do indexador é 1 por réplica sem trabalhos simultâneos.

  Se a execução do indexador já estiver na capacidade, você receberá esta notificação: "falha ao executar o indexador" <indexador-Name> ", erro:" outra invocação do indexador está em andamento; invocações simultâneas não são permitidas. "

+ O tempo máximo de execução é de 2 horas se você estiver usando um deDataSet e 24 horas sem. 

  Você pode ampliar o processamento colocando o indexador em um agendamento. A camada gratuita tem limites de tempo de execução mais baixos. Para obter a lista completa, consulte [limites do indexador](search-limits-quotas-capacity.md#indexer-limits)

<a name="reset-indexers"></a>

## <a name="reset-an-indexer"></a>Redefinir um indexador

A redefinição de um indexador está tudo abrangendo. No índice de pesquisa, qualquer documento de pesquisa que foi originalmente preenchido pelo indexador é marcado para processamento completo. Quaisquer documentos novos encontrados na fonte subjacente serão adicionados ao índice como documentos de pesquisa. Se o indexador estiver configurado para usar um habilidades e um [cache](search-howto-incremental-index.md), o configurador de habilidades será executado novamente e o cache será atualizado.

Você pode redefinir um indexador usando qualquer uma dessas abordagens, seguida por um indexador executado usando um dos métodos discutidos acima.

+ Portal do Azure, usando o comando **Redefinir** na página do indexador
+ [Redefinir indexador (REST)](/rest/api/searchservice/reset-indexer)
+ [Método ResetIndexers](/dotnet/api/azure.search.documents.indexes.searchindexerclient.resetindexer) no SDK do .net do Azure (ou usando o método RunIndexer equivalente em outro SDK)

Um sinalizador de redefinição é apagado após a execução ser concluída. Qualquer lógica de detecção de alteração regular que esteja funcionando para sua fonte de dados será retomada na próxima execução, selecionando quaisquer outros valores novos ou atualizados no restante do conjunto de dados.

> [!NOTE]
> Uma solicitação de redefinição determina o que é reprocessado (indexador, habilidade ou documento), mas não afeta o comportamento do tempo de execução do indexador. Se o indexador tiver parâmetros de tempo de execução, mapeamentos de campo, cache, opções de lote e assim por diante, essas configurações estarão todas em vigor quando você executar um indexador depois de tê-lo redefinido.

<a name="reset-skills"></a>

## <a name="reset-skills-preview"></a>Redefinir habilidades (visualização)

> [!IMPORTANT] 
> As [habilidades de redefinição](/rest/api/searchservice/preview-api/reset-skills) estão em visualização pública, disponíveis somente por meio da API REST de visualização. Os recursos de visualização são oferecidos no estado em que se encontram, sob [termos de uso complementares](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Para indexadores que têm habilidades, você pode redefinir habilidades específicas para forçar o processamento dessa habilidade e de quaisquer habilidades de downstream que dependam de sua saída. Os [aprimoramentos em cache](search-howto-incremental-index.md) também são atualizados. A redefinição de habilidades invalida os resultados de habilidades em cache, o que é útil quando uma nova versão de uma habilidade é implantada e você deseja que o indexador execute novamente essa habilidade para todos os documentos. 

As [habilidades de redefinição](/rest/api/searchservice/preview-api/reset-skills) estão disponíveis por meio do REST **`api-version=2020-06-30-Preview`** .

```http
POST https://[service name].search.windows.net/skillsets/[skillset name]/resetskills?api-version=2020-06-30-Preview
{
    "skillNames" : [
        "#1",
        "#5",
        "#6"
    ]
}
```

Você pode especificar habilidades individuais, conforme indicado no exemplo acima, mas se qualquer uma dessas habilidades exigir a saída de habilidades não listadas (#2 por meio de #4), as habilidades não listadas serão executadas, a menos que o cache possa fornecer as informações necessárias. Para que isso seja verdadeiro, os aprimoramentos em cache para habilidades #2 por meio de #4 não devem ter dependência em #1 (listado para redefinição).

Se nenhuma habilidade for especificada, o concordador inteiro será executado e, se o Caching estiver habilitado, o cache também será atualizado.

<a name="reset-docs"></a>

## <a name="reset-docs-preview"></a>Redefinir documentos (versão prévia)

> [!IMPORTANT] 
> [Redefinir documentos](/rest/api/searchservice/preview-api/reset-documents) está em visualização pública, disponível somente por meio da API REST de visualização. Os recursos de visualização são oferecidos no estado em que se encontram, sob [termos de uso complementares](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A [API redefinir documentos](https://docs.microsoft.com/rest/api/searchservice/preview-api/reset-documents) aceita uma lista de chaves de documento para que você possa atualizar documentos específicos. Se especificado, os parâmetros de redefinição se tornarão o único determinante do que é processado, independentemente de outras alterações nos dados subjacentes. Por exemplo, se 20 BLOBs foram adicionados ou atualizados desde a última execução do indexador, mas você redefinir apenas um documento, apenas um documento será processado.

Em uma base por documento, todos os campos nesse documento de pesquisa são atualizados com valores da fonte de dados. Você não pode escolher quais campos atualizar. 

Se o documento for aprimorado por meio de um qualificable e tiver dados armazenados em cache, o contextset será invocado apenas para os documentos especificados e o cache será atualizado para os documentos reprocessados.

Ao testar essa API pela primeira vez, as seguintes APIs irão ajudá-lo a validar e testar os comportamentos:

+ [Obtenha o status do indexador](/rest/api/searchservice/get-indexer-status) com a versão da API `2020-06-30-Preview` para verificar o status de redefinição e a execução. Você pode encontrar informações sobre a solicitação de redefinição no final da resposta de status.
+ [Redefinir documentos](/rest/api/searchservice/preview-api/reset-documents) com a versão da API `2020-06-30-Preview` para especificar quais documentos processar.
+ [Execute o indexador](/rest/api/searchservice/run-indexer) para executar o indexador (qualquer versão de API).
+ [Pesquise os documentos](/rest/api/searchservice/search-documents) para verificar se há valores atualizados, e também para retornar as chaves do documento se não tiver certeza do valor. Use `"select": "<field names>"` se você quiser limitar quais campos aparecem na resposta.

### <a name="formulate-and-send-the-reset-request"></a>Formular e enviar a solicitação de redefinição

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
{
    "documentKeys" : [
        "1001",
        "4452"
    ]
}
```

As chaves de documento fornecidas na solicitação são valores do índice de pesquisa, que podem ser diferentes dos campos correspondentes na fonte de dados. Se você não tiver certeza do valor de chave, [envie uma consulta](search-query-create.md) para retornar o valor. Você pode usar `select` para retornar apenas o campo de chave do documento.

Para BLOBs analisados em vários documentos de pesquisa (por exemplo, se você usou [jsonLines ou jsonArrays](search-howto-index-json-blobs.md), ou [delimitedText](search-howto-index-csv-blobs.md)) como um modo de análise, a chave do documento é gerada pelo indexador e pode ser desconhecida para você. Nessa situação, uma consulta para a chave do documento será fundamental para fornecer o valor correto.

Chamar a API várias vezes com chaves diferentes acrescenta as novas chaves à lista de chaves de documento redefinidas. Chamar a API com o **`overwrite`** parâmetro definido como true substituirá a lista atual de chaves do documento a ser redefinida com a carga da solicitação:

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
{
    "documentKeys" : [
        "200",
        "630"
    ],
    "overwrite": true
}
```

## <a name="check-reset-status"></a>Verificar status de redefinição

Para verificar o status de uma redefinição e ver quais chaves de documento estão enfileiradas para processamento, use [obter status do indexador](/rest/api/searchservice/get-indexer-status) com **`api-version=06-30-2020-Preview`** . A API de visualização retornará a **`currentState`** seção, que pode ser encontrada no final da resposta obter status do indexador.

O "modo" será **`indexingAllDocs`** para as habilidades de redefinição (porque potencialmente todos os documentos são afetados, para os campos que são preenchidos por meio do enriquecimento de ia).

Para redefinição de documentos, o modo é definido como **`indexingResetDocs`** . O indexador retém esse status até que todas as chaves de documento fornecidas na chamada redefinir documentos sejam processadas e nenhum outro trabalho do indexador seja executado enquanto a operação estiver em andamento. Localizar todos os documentos na lista de chaves do documento requer a quebra de cada documento para localizar e corresponder à chave, e isso pode levar algum tempo se o conjunto de dados for grande. Se um contêiner de blob contiver centenas de BLOBs e os documentos que você deseja redefinir estiverem no final, o indexador não encontrará os BLOBs correspondentes até que todos os outros tenham sido verificados primeiro.

```json
"currentState": {
    "mode": "indexingResetDocs",
    "allDocsInitialTrackingState": "{\"LastFullEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"LastAttemptedEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"NameHighWaterMark\":null}",
    "allDocsFinalTrackingState": "{\"LastFullEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"LastAttemptedEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"NameHighWaterMark\":null}",
    "resetDocsInitialTrackingState": null,
    "resetDocsFinalTrackingState": null,
    "resetDocumentKeys": [
        "200",
        "630"
    ]
}
```

Depois que os documentos são reprocessados, o indexador retorna ao **`indexingAllDocs`** modo e processará outros documentos novos ou atualizados na próxima execução.

## <a name="next-steps"></a>Próximas etapas

As APIs de redefinição são usadas para informar o escopo da próxima execução do indexador. Para o processamento real, você precisará invocar uma execução de indexador sob demanda ou permitir que um trabalho agendado conclua o trabalho. Depois que a execução for concluída, o indexador retornará ao processamento normal, independentemente de estar em um agendamento ou em um processamento sob demanda.

Depois de redefinir e executar novamente os trabalhos do indexador, você pode monitorar o status do serviço de pesquisa ou obter informações detalhadas por meio do log de diagnóstico.

+ [Operações do indexador (REST)](/rest/api/searchservice/indexer-operations)
+ [Monitorar o status do indexador de pesquisa](search-howto-monitor-indexers.md)
+ [Coletar e analisar dados de log](search-monitor-logs.md)
+ [Agendar um indexador](search-howto-schedule-indexers.md)