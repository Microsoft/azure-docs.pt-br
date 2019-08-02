---
title: Dicas de solução de problemas para a pesquisa cognitiva - Azure Search
description: Dicas e solução de problemas para configurar pipelines de pesquisa cognitiva no Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 02/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: c0de4d2b9ad0d009b9cd363d19a2de3f29d810d4
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303461"
---
# <a name="troubleshooting-tips-for-cognitive-search"></a>Dicas de solução de problemas para a pesquisa cognitiva

Este artigo contém uma lista de dicas e truques para ajudá-lo a começar a usar as funcionalidades de pesquisa cognitiva no Azure Search. 

Se ainda não fez isso, percorra o [Tutorial: Saiba como chamar as APIs de pesquisa cognitiva](cognitive-search-quickstart-blob.md) para praticar a aplicação de enriquecimentos de pesquisa cognitiva em uma fonte de dados de blob.

## <a name="tip-1-start-with-a-small-dataset"></a>Dica 1: Comece com um conjunto de dados pequeno
A melhor maneira de encontrar problemas rapidamente é aumentar a velocidade com que você pode corrigi-los. A melhor maneira de reduzir o tempo de indexação é reduzindo o número de documentos a serem indexados. 

Comece criando uma fonte de dados com apenas alguns registros/documentos. O documento de amostra deve ser uma boa representação da variedade de documentos que serão indexados. 

Execute seu documento de amostra no pipeline de ponta a ponta e verifique se os resultados atendem às suas necessidades. Quando estiver satisfeito com os resultados, você poderá adicionar mais arquivos à sua fonte de dados.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>Dica 2: Verifique se as credenciais da fonte de dados estão corretas
A conexão da fonte de dados não é validada até que você defina um indexador que a utiliza. Se encontrar erros indicando que o indexador não pode acessar os dados, verifique se:
- Sua cadeia de conexão está correta. Especialmente quando estiver criando tokens SAS, certifique-se de usar o formato esperado pelo Azure Search. Consulte a seção [Como especificar credenciais](
https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials) para saber mais sobre os diferentes formatos compatíveis.
- O nome do contêiner no indexador está correto.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>Dica 3: Veja o que funciona mesmo que haja algumas falhas
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
## <a name="tip-4-looking-at-enriched-documents-under-the-hood"></a>Dica 4: Examine os documentos enriquecidos nos bastidores 
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

## <a name="tip-5-expected-content-fails-to-appear"></a>Dica 5: O conteúdo esperado não aparece

A falta de conteúdo pode ser resultado da remoção de documentos durante a indexação. As camadas Básica e Gratuita têm limites baixos de tamanho do documento. Qualquer arquivo que ultrapassar o limite será removido durante a indexação. Você pode verificar se há documentos removidos no portal do Azure. No painel do serviço de pesquisa, clique duas vezes no bloco Indexadores. Examine a proporção de documentos indexados com êxito. Se ela não for de 100%, você poderá clicar nela para obter mais detalhes. 

Se o problema está relacionado ao tamanho do arquivo, você poderá ver um erro como este: "O blob \<nome do arquivo >" tem o tamanho de \<tamanho do arquivo > bytes, que excede o tamanho máximo para a extração de documento para a camada de serviço atual. " Para obter mais informações sobre os limites de indexador, confira [Limites de serviço](search-limits-quotas-capacity.md).

Um segundo motivo para o conteúdo não aparecer pode ser a presença de erros de mapeamento de entrada/saída. Por exemplo, o nome da saída de destino é "People", mas o nome do campo de índice é "people", com letras minúsculas. O sistema pode retornar mensagens de êxito 201 para todo o pipeline, o que levará você a achar que a indexação foi bem-sucedida, quando na verdade um campo está vazio. 

## <a name="tip-6-extend-processing-beyond-maximum-run-time-24-hour-window"></a>Dica 6: Estenda o processamento além do tempo de execução máximo (janela de 24 horas)

A análise de imagem faz uso intensivo de computação até mesmo em casos simples, de modo que quando as imagens são especialmente grandes ou complexas, os tempos de processamento podem ultrapassar o tempo máximo permitido. 

O tempo de execução máximo varia por camada: vários minutos na Camada gratuita, indexação de 24 horas nas camadas faturáveis. Se o processamento não for concluído em um período de 24 horas para o processamento sob demanda, use uma agenda para que o indexador retome o processamento de onde parou. 

Para indexadores agendados, a indexação é retomada de acordo com a agenda no último documento bem-sucedido. Com o uso de um agenda recorrente, o indexador pode percorrer a lista de pendências de imagem ao longo de várias horas ou vários dias, até que todas as imagens não processadas sejam processadas. Para obter mais informações sobre a sintaxe de agendamento, confira a [Etapa 3: Criar um indexador](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer) ou consulte [como programar indexadores para o Azure Search](search-howto-schedule-indexers.md).

> [!NOTE]
> Se um indexador é definido para uma determinada agenda mas repetidamente falhar no mesmo documenta repetidamente cada vez que ele é executado, o indexador será iniciada executando em um intervalo menos frequente (até o máximo de pelo menos uma vez a cada 24 horas) com êxito até que ele faz aga de andamento no.  Se você acredita que corrigir qualquer problema que fazia com que o indexador ficar preso em um determinado ponto, você pode executar um em demanda tempo de execução do indexador, e se isso faz com êxito o andamento, o indexador irá retornar de seu intervalo de agendamento do conjunto novamente.

Para a indexação baseada em portal (conforme descrita no guia de início rápido), escolher a opção "Executar uma vez" do indexador limita o processamento a 1 hora (`"maxRunTime": "PT1H"`). Você talvez queira estender a janela de processamento para um período mais longo.

## <a name="tip-7-increase-indexing-throughput"></a>Dica 7: Aumente a produtividade da indexação

Para a [indexação paralela](search-howto-large-index.md), coloque os dados em vários contêineres ou várias pastas virtuais dentro do mesmo contêiner. Em seguida, crie vários pares de fonte de dados e indexador. Todos os indexadores podem usar o mesmo conjunto de habilidades e gravar no mesmo índice de pesquisa de destino, de modo que seu aplicativo de pesquisa não precisa estar ciente desse particionamento.
Para obter mais informações, consulte [Indexando grandes conjuntos de dados](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets).

## <a name="see-also"></a>Consulte também
+ [Guia de Início Rápido: Criar um pipeline de pesquisa cognitiva no portal](cognitive-search-quickstart-blob.md)
+ [Tutorial: Saiba mais sobre as APIs REST da pesquisa cognitiva](cognitive-search-tutorial-blob.md)
+ [Specifying data source credentials](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials) (Especificando credenciais de fonte de dados)
+ [Indexando grandes conjuntos de dados](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)
+ [Como definir um conjunto de habilidades](cognitive-search-defining-skillset.md)
+ [How to map enriched fields to an index](cognitive-search-output-field-mapping.md) (Como mapear campos enriquecidos para um índice)
