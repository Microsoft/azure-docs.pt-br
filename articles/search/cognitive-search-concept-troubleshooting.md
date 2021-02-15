---
title: Dicas para design de enriquecimento de ia
titleSuffix: Azure Cognitive Search
description: Dicas e solução de problemas para configurar pipelines de enriquecimento de ia no Azure Pesquisa Cognitiva.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 5882cc949d88e8c2a4102362cf5d2a3613e1d714
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475481"
---
# <a name="tips-for-ai-enrichment-in-azure-cognitive-search"></a>Dicas para o enriquecimento de ia no Azure Pesquisa Cognitiva

Este artigo contém uma lista de dicas e truques para manter você mudando à medida que começar a usar os recursos de enriquecimento de ia no Azure Pesquisa Cognitiva. 

Se você ainda não fez isso, percorra o [tutorial: saiba como chamar APIs de enriquecimento de ai](cognitive-search-quickstart-blob.md) para praticar na aplicação de aprimoramentos de ai a uma fonte de dados de BLOB.

## <a name="tip-1-start-with-a-small-dataset"></a>Dica 1: comece com um conjunto de dados pequeno
A melhor maneira de encontrar problemas rapidamente é aumentar a velocidade com que você pode corrigi-los. A melhor maneira de reduzir o tempo de indexação é reduzindo o número de documentos a serem indexados. 

Comece criando uma fonte de dados com apenas alguns registros/documentos. O documento de amostra deve ser uma boa representação da variedade de documentos que serão indexados. 

Execute seu documento de amostra no pipeline de ponta a ponta e verifique se os resultados atendem às suas necessidades. Quando estiver satisfeito com os resultados, você poderá adicionar mais arquivos à sua fonte de dados.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>Dica 2: verifique se as credenciais da fonte de dados estão corretas
A conexão da fonte de dados não é validada até que você defina um indexador que a utiliza. Se encontrar erros indicando que o indexador não pode acessar os dados, verifique se:
- Sua cadeia de conexão está correta. Especialmente quando você estiver criando tokens SAS, certifique-se de usar o formato esperado pelo Azure Pesquisa Cognitiva. Consulte a seção [Como especificar credenciais](
https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials) para saber mais sobre os diferentes formatos compatíveis.
- O nome do contêiner no indexador está correto.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>Dica 3: veja o que funciona mesmo que haja algumas falhas
Às vezes, uma pequena falha interrompe o funcionamento de um indexador. Isso não é um problema se você planeja corrigir os problemas um a um. No entanto, talvez você queira ignorar um determinado tipo de erro, permitindo que o indexador continue para que você possa ver quais fluxos estão funcionando.

Nesse caso, convém orientar o indexador a ignorar erros. Faça isso definindo *maxFailedItems* e *maxFailedItemsPerBatch* como -1 como parte da definição do indexador.

```
{
  "// rest of your indexer definition
   "parameters":
   {
      "maxFailedItems":-1,
      "maxFailedItemsPerBatch":-1
   }
}
```
> [!NOTE]
> Como prática recomendada, defina maxFailedItems, maxFailedItemsPerBatch como 0 para cargas de trabalho de produção

## <a name="tip-4-use-debug-sessions-to-identify-and-resolve-issues-with-your-skillset"></a>Dica 4: usar sessões de depuração para identificar e resolver problemas com seu qualificable 

As Sessões de depuração são um editor visual que funciona com um conjunto de habilidades existente no portal do Azure. Em uma sessão de depuração, você pode identificar e resolver erros, validar alterações e confirmar alterações em um confirmante de produção no pipeline de enriquecimento de ia. Este é um recurso de visualização [Leia a documentação](./cognitive-search-debug-session.md). Para obter mais informações sobre conceitos e introdução, consulte [depurar sessões](./cognitive-search-tutorial-debug-sessions.md).

As sessões de depuração funcionam em um único documento são uma ótima maneira de Compilar iterativamente pipelines de enriquecimento mais complexos.

## <a name="tip-5-looking-at-enriched-documents-under-the-hood"></a>Dica 5: examinando os documentos aprimorados nos bastidores 
Documentos enriquecidos são estruturas temporárias criadas durante enriquecimento e, em seguida, excluídos quando o processo é concluído.

Para capturar um instantâneo do documento enriquecido criado durante a indexação, adicione um campo chamado ```enriched``` ao índice. O indexador despeja automaticamente no campo uma representação de cadeia de caracteres de todos os enriquecimentos do documento.

O campo ```enriched``` conterá uma cadeia de caracteres que é uma representação lógica do documento enriquecido na memória em JSON.  No entanto, o valor do campo é um documento JSON válido. As aspas são de escape, de modo que você precisará substituir `\"` por `"` para ver o documento como JSON formatado. 

O campo enriquecido tem finalidade apenas de depuração, para ajudá-lo a entender a forma lógica do conteúdo no qual as expressões estão sendo avaliadas. Não dependa desse campo para fins de indexação.

Adicione um campo ```enriched``` como parte da definição do índice para fins de depuração:

#### <a name="request-body-syntax"></a>Sintaxe de Corpo da Solicitação
```json
{
  "fields": [
    // other fields go here.
    {
      "name": "enriched",
      "type": "Edm.String",
      "searchable": false,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```

## <a name="tip-6-expected-content-fails-to-appear"></a>Dica 6: falha na exibição do conteúdo esperado

A falta de conteúdo pode ser resultado da remoção de documentos durante a indexação. As camadas Básica e Gratuita têm limites baixos de tamanho do documento. Qualquer arquivo que ultrapassar o limite será removido durante a indexação. Você pode verificar se há documentos removidos no portal do Azure. No painel do serviço de pesquisa, clique duas vezes no bloco Indexadores. Examine a proporção de documentos indexados com êxito. Se ela não for de 100%, você poderá clicar nela para obter mais detalhes. 

Se o problema estiver relacionado ao tamanho do arquivo, você poderá ver um erro como este: "o blob \<file-name> " tem o tamanho de \<file-size> bytes, que excede o tamanho máximo para extração de documentos para sua camada de serviço atual. " Para obter mais informações sobre os limites de indexador, confira [Limites de serviço](search-limits-quotas-capacity.md).

Um segundo motivo para o conteúdo não aparecer pode ser a presença de erros de mapeamento de entrada/saída. Por exemplo, o nome da saída de destino é "People", mas o nome do campo de índice é "people", com letras minúsculas. O sistema pode retornar mensagens de êxito 201 para todo o pipeline, o que levará você a achar que a indexação foi bem-sucedida, quando na verdade um campo está vazio. 

## <a name="tip-7-extend-processing-beyond-maximum-run-time-24-hour-window"></a>Dica 7: estender o processamento além do tempo de execução máximo (janela de 24 horas)

A análise de imagem faz uso intensivo de computação até mesmo em casos simples, de modo que quando as imagens são especialmente grandes ou complexas, os tempos de processamento podem ultrapassar o tempo máximo permitido. 

O tempo de execução máximo varia por camada: vários minutos na Camada gratuita, indexação de 24 horas nas camadas faturáveis. Se o processamento não for concluído em um período de 24 horas para o processamento sob demanda, use uma agenda para que o indexador retome o processamento de onde parou. 

Para indexadores agendados, a indexação é retomada de acordo com a agenda no último documento bem-sucedido. Com o uso de um agenda recorrente, o indexador pode percorrer a lista de pendências de imagem ao longo de várias horas ou vários dias, até que todas as imagens não processadas sejam processadas. Para obter mais informações sobre a sintaxe de agendamento, consulte [agendar um indexador](search-howto-schedule-indexers.md).

> [!NOTE]
> Se um indexador for definido como um determinado agendamento, mas falhar repetidamente no mesmo documento repetidamente, sempre que for executado, o indexador começará a ser executado em um intervalo menos frequente (até o máximo de pelo menos uma vez a cada 24 horas) até que ele faça o progresso com êxito novamente.  Se acreditar que você corrigiu qualquer problema que estava fazendo com que o indexador fosse paralisado em um determinado ponto, você poderá executar uma execução sob demanda do indexador e, se isso fizer o progresso com êxito, o indexador retornará ao seu intervalo de agendamento definido novamente.

Para a indexação baseada em portal (conforme descrita no guia de início rápido), escolher a opção "Executar uma vez" do indexador limita o processamento a 1 hora (`"maxRunTime": "PT1H"`). Você talvez queira estender a janela de processamento para um período mais longo.

## <a name="tip-8-increase-indexing-throughput"></a>Dica 8: aumentar a taxa de transferência de indexação

Para a [indexação paralela](search-howto-large-index.md), coloque os dados em vários contêineres ou várias pastas virtuais dentro do mesmo contêiner. Em seguida, crie vários pares de fonte de dados e indexador. Todos os indexadores podem usar o mesmo conjunto de habilidades e gravar no mesmo índice de pesquisa de destino, de modo que seu aplicativo de pesquisa não precisa estar ciente desse particionamento.

## <a name="see-also"></a>Confira também

+ [Início rápido: criar um pipeline de enriquecimento de ia no portal](cognitive-search-quickstart-blob.md)
+ [Tutorial: aprender sobre APIs REST de rericação de AI](cognitive-search-tutorial-blob.md)
+ [Como configurar indexadores de BLOB](search-howto-indexing-azure-blob-storage.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
+ [How to map enriched fields to an index](cognitive-search-output-field-mapping.md) (Como mapear campos enriquecidos para um índice)
