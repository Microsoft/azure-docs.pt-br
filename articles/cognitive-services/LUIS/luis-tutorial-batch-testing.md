---
title: Lote de teste-LUIS
titleSuffix: Azure Cognitive Services
description: Este tutorial demonstra como usar o teste em lotes para localizar problemas de previsão de enunciado em seu aplicativo e corrigi-los.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/29/2019
ms.author: diberry
ms.openlocfilehash: d6b3f864ded8b6f5ac0a1d839768801788d7d765
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560128"
---
# <a name="tutorial-batch-test-data-sets"></a>Tutorial: testar conjuntos de dados em lote

Este tutorial demonstra como usar o teste em lotes para localizar problemas de previsão de enunciado em seu aplicativo e corrigi-los.  

O teste em lotes permite que você valide o estado do modelo ativo e treinado com um conjunto conhecido de entidades e enunciados rotulados. No arquivo em lotes em formato JSON, adicione os enunciados e defina os rótulos de entidade que você precisa prever dentro do enunciado. 

Requisitos para o teste em lotes:

* Máximo de 1000 enunciados por teste. 
* Sem duplicatas. 
* Tipos de entidade permitidos: somente entidades aprendidas por máquina de simples e compostas. Teste em lotes é útil apenas para intenções e entidades de aprendizado de máquina.

Ao usar um aplicativo diferente deste tutorial, *não* use os enunciados de exemplo já adicionados a uma intenção. 

**Neste tutorial, você aprenderá a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Aplicativo de exemplo de importação
> * Criar um arquivo de teste de lote 
> * Executar um teste de lote
> * Examinar resultados de teste
> * Corrigir erros 
> * Testar novamente o lote

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Aplicativo de exemplo de importação

Continue com o aplicativo criado no último tutorial, denominado **HumanResources**. 

Use as seguintes etapas:

1.  Baixe e salve o [arquivo JSON do aplicativo](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-review-HumanResources.json).

2. Importe o JSON em um novo aplicativo.

3. Na seção **Gerenciar**, na guia **Versões**, clone a versão e nomeie-a como `batchtest`. A clonagem é uma ótima maneira de testar vários recursos de LUIS sem afetar a versão original. Como o nome da versão é usado como parte da rota de URL, o nome não pode conter nenhum caractere que não seja válido em uma URL. 

4. Treine o aplicativo.

## <a name="batch-file"></a>Arquivo em lotes

1. Crie `HumanResources-jobs-batch.json` em um editor de texto ou [baixe-o](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources-jobs-batch.json). 

2. No arquivo em lotes em formato JSON, adicione enunciados com a **Intenção** que você quer que seja prevista no teste. 

   [!code-json[Add the intents to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-jobs-batch.json "Add the intents to the batch test file")]

## <a name="run-the-batch"></a>Executar o lote

1. Selecione **Testar** na barra de navegação superior. 

2. Selecione **Painel de teste do lote** no painel direito. 

    [![Captura de tela do aplicativo de LUIS com painel de teste do Lote realçado](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png)](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png#lightbox)

3. Selecione **Importar conjunto de dados**.

    [![Captura de tela do aplicativo de LUIS com o conjunto de dados de importação realçado](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png)](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png#lightbox)

4. Escolha o local do arquivo do `HumanResources-jobs-batch.json`.

5. Nomeie o conjunto de dados `intents only` e selecione **Concluído**.

    ![Selecionar arquivo](./media/luis-tutorial-batch-testing/hr-import-new-dataset-ddl.png)

6. Selecione o botão **Executar**. 

7. Selecione **Ver resultados**.

8. Examine os resultados no grafo e na legenda.

    [![Captura de tela do aplicativo de LUIS com resultados de teste em lotes](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png)](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png#lightbox)

## <a name="review-batch-results"></a>Examinar resultados do lote

O gráfico do lote exibe quatro quadrantes dos resultados. À direita do gráfico, há um filtro. Por padrão, o filtro é definido para a primeira intenção na lista. O filtro contém todas as intenções e apenas entidades simples e compostas. Ao selecionar uma [seção do gráfico](luis-concept-batch-test.md#batch-test-results) ou um ponto dentro do gráfico, os enunciados associados são exibidos abaixo do gráfico. 

Ao passar o mouse sobre o gráfico, um botão de rolagem do mouse pode ampliar ou reduzir a exibição no gráfico. Isso é útil quando há muitos pontos no gráfico agrupados em conjunto. 

O gráfico está em quatro quadrantes, com duas das seções exibidas em vermelho. **Essas são as seções que devem ser privilegiadas**. 

### <a name="getjobinformation-test-results"></a>Resultados do teste de GetJobInformation

Os resultados do teste de **GetJobInformation** exibidos no filtro mostram que 2 das quatro previsões tiveram êxito. Selecione o nome **Falso positivo** acima do quadrante superior direito para ver os enunciados abaixo do gráfico. 

![Enunciados de teste em lotes de LUIS](./media/luis-tutorial-batch-testing/hr-applyforjobs-false-positive-results.png)

Por que dois dos enunciados são previstos como **ApplyForJob**, em vez da intenção correta **GetJobInformation**? As duas intenções estão intimamente relacionadas em termos de escolha de palavras e disposição de palavras. Adicionalmente, há quase três vezes mais exemplos para **ApplyForJob** do que para **GetJobInformation**. Esse desnível de enunciado de exemplo favorece a intenção **ApplyForJob**. 

Observe que ambas as intenções possuem a mesma contagem de erros. Uma previsão incorreta em uma intenção também afeta a outra intenção. Ambos têm erros porque os enunciados foram incorretamente previstos para uma intenção e também incorretamente não previstos para outra intenção. 

![Erros de filtro de teste em lotes de LUIS](./media/luis-tutorial-batch-testing/hr-intent-error-count.png)

Os enunciados que correspondem ao ponto principal na seção **Falso positivo** são `Can I apply for any database jobs with this resume?` e `Can I apply for any database jobs with this resume?`. Para o primeiro enunciado, a palavra `resume` foi usada apenas em **ApplyForJob**. Para o segundo enunciado, a palavra `apply` foi usada apenas na intenção **ApplyForJob**.

## <a name="fix-the-app"></a>Corrigir o aplicativo

O objetivo desta seção é ter todos os enunciados corretamente previstos para **GetJobInformation**, corrigindo o aplicativo. 

Uma correção aparentemente rápida seria adicionar esses enunciados de arquivos em lotes à intenção correta. Não é isso que você pretende fazer. Você quer que o LUIS preveja corretamente esses enunciados sem adicioná-los como exemplos. 

Também há a possibilidade de pensar em remover enunciados da intenção **ApplyForJob** até que a quantidade de enunciados seja igual a da **GetJobInformation**. Isso corrigiria os resultados do teste, mas impediria o LUIS de prever essa intenção com precisão na próxima vez. 

A primeira correção é adicionar mais enunciados à intenção **GetJobInformation**. A segunda correção é reduzir o peso das palavras como `resume` e `apply` da intenção **ApplyForJob**. 

### <a name="add-more-utterances"></a>Adicionar mais enunciados

1. Feche o painel de teste em lotes, selecionando o botão **Testar** no painel de navegação superior. 

2. Selecione **GetJobInformation** na lista de intenções. 

3. Adicione mais enunciados que sejam variados por tamanho, escolha de palavras e disposição de palavras, certificando-se de incluir os termos `resume`, `c.v.` e `apply`:

    |Enunciados de exemplo para intenção **GetJobInformation**|
    |--|
    |Para concorrer à nova vaga de estoquista no depósito é necessário que eu me candidate com um currículo?|
    |Onde estão as vagas para trabalhos em telhado hoje?|
    |Ouvi dizer que há um trabalho de codificação médica que exige um currículo.|
    |Eu gostaria de um trabalho para ajudar os universitários a elaborar CVs. |
    |Aqui está o meu currículo, em busca por um novo cargo na faculdade comunitária e com experiência em computadores.|
    |Quais cargos estão disponíveis na área de Home Care e Puericultura?|
    |Há um suporte interno no jornal?|
    |Meu CV mostra que sou bom em analisar aquisições, orçamentos e perdas financeiras. Há algo disponível para esse tipo de trabalho?|
    |Como estão os trabalhos de perfuração de solo atualmente?|
    |Eu trabalhei 8 anos como motorista do EMS. Algum novo trabalho?|
    |Novos trabalhos de manipulação de alimentos exigem inscrição?|
    |Quantos novos trabalhos de jardinagem estão disponíveis?|
    |Há um novo cargo de RH para relações trabalhistas e negociações?|
    |Eu tenho mestrado em gerenciamento de arquivo e biblioteca. Alguma nova vaga?|
    |Há algum trabalho de babá para cuidar de crianças de 13 anos na cidade?|

    Não rotule a entidade **Trabalho** nos enunciados. Esta seção do tutorial está focada apenas na previsão de intenção.

4. Treine o aplicativo selecionando **Treinar** no canto superior direito da navegação.

## <a name="verify-the-new-model"></a>Verificar o novo modelo

Para verificar se os enunciados no teste em lotes estão previstos corretamente, execute o teste em lotes novamente.

1. Selecione **Testar** na barra de navegação superior. Se os resultados do lote ainda estiverem abertos, selecione **Retornar à lista**.  

2. Selecione o botão de reticências (***...***) à direita do nome do lote e selecione **Executar Conjunto de Dados**. Aguarde até que o teste de lote seja concluído. Observe que o botão **Consultar resultados** agora está verde. Isso significa que todo o lote foi executado com êxito.

3. Selecione **Ver resultados**. Todas as tentativas devem ter ícones verdes à esquerda dos nomes da intenção. 

    ![Captura de tela do LUIS com o botão de resultados do lote realçado](./media/luis-tutorial-batch-testing/hr-batch-test-intents-no-errors.png)

## <a name="create-batch-file-with-entities"></a>Criar arquivo em lotes com entidades 

Para verificar entidades em um teste em lotes, as entidades precisam ser rotuladas no arquivo JSON em lote. Somente as entidades aprendidas por máquina são usadas: entidades simples e compostas. Não adicione entidades que não sejam de aprendizado de máquina, pois sempre são localizadas por meio de expressões regulares ou por correspondências de texto explícitas.

A variação de entidades para contagem total de palavras ([token](luis-glossary.md#token)) pode afetar a qualidade de previsão. Certifique-se de que os dados de treinamento fornecidos à intenção com enunciados rotulados incluem vários comprimentos de entidade. 

Ao gravar e testar arquivos em lotes pela primeira vez, é melhor começar com alguns enunciados e entidades que você sabe que funcionam, além de alguns que você acha que podem ter sido previstos incorretamente. Isso ajuda a concentrar-se nas áreas problemáticas rapidamente. Após testar as intenções **GetJobInformation** e **ApplyForJob** usando diversos nomes de Trabalho diferentes, que não foram previstos, esse arquivo de teste em lotes foi desenvolvido para verificar se há algum problema de previsão com determinados valores para a entidade **Trabalho**. 

O valor de uma entidade **Trabalho**, fornecido nos enunciados de teste, geralmente é uma ou duas palavras, com alguns exemplos sendo mais palavras. Se o _próprio_ aplicativo de recursos humanos normalmente tiver nomes de trabalho com muitas palavras, os enunciados de exemplo rotulados com a entidade **Trabalho** nesse aplicativo não funcionarão bem.

1. Crie `HumanResources-entities-batch.json` em um editor de texto, como o [VSCode](https://code.visualstudio.com/), ou [baixe-o](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources-entities-batch.json).


2. No arquivo em lotes em formato JSON, adicione uma matriz de objetos que inclua enunciados com a **Intenção** que você quer prever no teste, bem como os locais de quaisquer entidades no enunciado. Como uma entidade é baseada em token, certifique-se de iniciar e parar cada entidade em um caractere. Não inicie nem termine o enunciado em um espaço. Isso causa um erro durante a importação do arquivo em lotes.  

   [!code-json[Add the intents and entities to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-entities-batch.json "Add the intents and entities to the batch test file")]


## <a name="run-the-batch-with-entities"></a>Executa o lote com entidades

1. Selecione **Testar** na barra de navegação superior. 

2. Selecione **Painel de teste do lote** no painel direito. 

3. Selecione **Importar conjunto de dados**.

4. Escolha o local do sistema de arquivos do arquivo `HumanResources-entities-batch.json`.

5. Nomeie o conjunto de dados `entities` e selecione **Concluído**.

6. Selecione o botão **Executar**. Aguarde até que o teste seja concluído.

7. Selecione **Ver resultados**.

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="review-entity-batch-results"></a>Examinar os resultados do lote da entidade

O gráfico é aberto com todas as intenções corretamente previstas. Role para baixo no filtro do lado direito para localizar as previsões de entidade com erros. 

1. Selecione a entidade **Trabalho** no filtro.

    ![Previsões de entidade de erro no filtro](./media/luis-tutorial-batch-testing/hr-entities-filter-errors.png)

    O gráfico é alterado para exibir as previsões de entidade. 

2. Selecione **Falso Negativo** no quadrante inferior esquerdo do gráfico. Em seguida, use o controle de combinação de teclas + E para alternar para a exibição de token. 

    [![Exibição do token das previsões de entidade](./media/luis-tutorial-batch-testing/token-view-entities.png)](./media/luis-tutorial-batch-testing/token-view-entities.png#lightbox)
    
    Analisar os enunciados abaixo do gráfico revela um erro consistente quando o nome do trabalho inclui `SQL`. Ao analisar os enunciados de exemplo e a lista de frases de Trabalho, o SQL é usado apenas uma vez e apenas como parte de um nome de trabalho maior, `sql/oracle database administrator`.

## <a name="fix-the-app-based-on-entity-batch-results"></a>Corrigir o aplicativo com base nos resultados do lote de entidade

Corrigir o aplicativo exige que o LUIS determine corretamente as variações de trabalhos do SQL. Há várias opções para essa correção. 

* Adicione explicitamente mais exemplos de expressões, que usam SQL e rotulem essas palavras como uma entidade Trabalho. 
* Adicionar explicitamente mais trabalhos do SQL à lista de frases

Essas tarefas são deixadas para você fazer.

Adicionar um [padrão](luis-concept-patterns.md) antes que a entidade seja corretamente prevista não corrigirá o problema. Isso ocorre porque o padrão não corresponderá até que todas as entidades no padrão sejam detectadas. 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

O tutorial usou um teste em lotes para encontrar problemas no modelo atual. O modelo foi corrigido e testado novamente com o arquivo em lotes para verificar se a alteração estava correta.

> [!div class="nextstepaction"]
> [Saiba mais sobre padrões](luis-tutorial-pattern.md)

