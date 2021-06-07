---
title: Coleta de dados
description: Saiba quais dados de exemplo coletar ao desenvolver seu aplicativo
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: c901d4024292ad03a9195ad0cbd226f473917ee4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98738077"
---
# <a name="data-collection-for-your-app"></a>Coleta de dados para seu aplicativo

Um aplicativo Reconhecimento vocal (LUIS) precisa de dados como parte do desenvolvimento de aplicativos.

## <a name="data-used-in-luis"></a>Dados usados em LUIS

O LUIS usa texto como dados para treinar e testar seu aplicativo LUIS para classificação de [tentativas](luis-concept-intent.md) e para extração de [entidades](luis-concept-entity-types.md). Você precisa de um grande conjunto de dados suficiente para que você tenha dados suficientes para criar conjuntos de dados separados para treinamento e teste que têm a diversidade e a distribuição chamadas especificamente abaixo.  Os dados em cada um desses conjuntos não devem se sobrepor.

## <a name="training-data-selection-for-example-utterances"></a>Seleção de dados de treinamento, por exemplo declarações

Selecione declarações para seu conjunto de treinamento com base nos seguintes critérios:

* **Os dados reais são os melhores**:
    * **Dados reais do aplicativo cliente**: selecione declarações que são dados reais do aplicativo cliente.  Se o cliente envia um formulário da Web com sua consulta hoje e você está criando um bot, você pode começar usando os dados do formulário da Web.
    * **Dados de origem** da alocação: se você não tiver dados existentes, considere a possibilidade de declarações de público-fonte.  Tente fazer a declarações de origem da sua população de usuário real para seu cenário para obter a melhor aproximação dos dados reais que seu aplicativo verá. A declarações humana de alta geração é melhor do que a declarações gerada por computador.  Quando você cria um conjunto de dados de declarações sintético gerado em padrões específicos, ele não terá grande parte da variação natural que você verá com pessoas criando o declarações e não acabará generalizando bem na produção.
* **Diversidade de dados**:
    * **Diversidade de região**: Verifique se os dados de cada tentativa são tão diversificados quanto possível, incluindo _frases_ (escolha do Word) e _gramática_.  Se você estiver ensinando uma intenção sobre as políticas de RH sobre dias de férias, verifique se você tem declarações que representam os termos que são usados para todas as regiões que você está servindo.  Por exemplo, na Europa, as pessoas podem perguntar sobre `taking a holiday` e nos EUA `taking vacation days` .
    * **Diversidade de idioma**: se você tiver usuários com várias linguagens nativas que estão se comunicando em uma segunda linguagem, certifique-se de ter declarações que representem alto-falantes não nativos.
    * **Diversidade de entrada**: considere o caminho de entrada de dados. Se você estiver coletando dados de uma pessoa, departamento ou dispositivo de entrada (microfone), provavelmente falta a diversidade que será importante para seu aplicativo aprender sobre todos os caminhos de entrada.
    * **Diversidade de Pontuação**: considere que as pessoas usam níveis variados de pontuação em aplicativos de texto e verifique se você tem uma diversidade de como a pontuação é usada. Se você estiver usando dados provenientes da fala, ele não terá nenhuma pontuação, portanto, os dados não devem ser.
* **Distribuição de dados**: Verifique se os dados espalhados entre as tentativas representam a mesma propagação de dados que o aplicativo cliente recebe. Se seu aplicativo LUIS classificará declarações que são solicitações para agendar uma licença (50%), mas também verá declarações sobre a consulta de deixar dias restantes (20%), aprovando folhas (20%) e alguns do escopo e do bate-papo do Chit (10%) em seguida, o conjunto de dados deve ter os percentuais de exemplo de cada tipo de expressão.
* **Usar todos os formulários de dados**: se seu aplicativo Luis receberá dados em várias formas, certifique-se de incluir esses formulários em seu declarações de treinamento. Por exemplo, se o aplicativo cliente usa entrada de texto de fala e digitada, você precisa ter declarações de fala para texto, bem como declarações tipados.  Você verá diferentes variações de como as pessoas falam de como elas digitam, bem como erros diferentes no reconhecimento de fala e digitações.  Toda essa variação deve ser representada em seus dados de treinamento.
* **Exemplos positivos e negativos**: para ensinar um aplicativo Luis, ele deve saber qual é a intenção (positivo) e o que não é (negativo). Em LUIS, declarações só pode ser positivo para uma única intenção. Quando um expressão é adicionado a uma intenção, o LUIS automaticamente faz com que o mesmo exemplo expressão um exemplo negativo para todas as outras intenções.
* **Dados fora do escopo do aplicativo**: se seu aplicativo receberá declarações que estão fora de suas intenções definidas, certifique-se de fornecê-los. Os exemplos que não estão atribuídos a uma determinada intenção definida serão rotulados com a intenção **nenhum** .  É importante ter exemplos realistas para a intenção **None** prever corretamente declarações que estão fora do escopo das intenções definidas.

    Por exemplo, se você estiver criando um bot de RH voltado para o tempo de licença e tiver três tentativas:
    * agendar ou editar uma licença
    * consultar os dias de licença disponíveis
    * aprovar/Desaprovamos sair

    Você deseja ter certeza de que tem declarações que abrangem essas duas intenções, mas também que cobrem possíveis declarações fora desse escopo que o aplicativo deve servir como estes:
    * `What are my medical benefits?`
    * `Who is my HR rep?`
    * `tell me a joke`
* **Exemplos raros**: seu aplicativo precisará ter exemplos raros, bem como exemplos comuns.  Se seu aplicativo nunca tiver visto exemplos raros, não será capaz de identificá-los em produção. Se você estiver usando dados reais, poderá prever com mais precisão como seu aplicativo LUIS funcionará na produção.

### <a name="quality-instead-of-quantity"></a>Qualidade em vez de quantidade

Considere a qualidade dos dados existentes antes de adicionar mais dados.  Com o LUIS, você está usando o ensino de máquina.  A combinação dos seus rótulos e dos recursos de aprendizado de máquina que você define é o que o aplicativo LUIS usa.  Ele não depende simplesmente da quantidade de rótulos para fazer a melhor previsão.  A diversidade de exemplos e sua representação do que seu aplicativo LUIS verá na produção é a parte mais importante.

### <a name="preprocessing-data"></a>Pré-processando dados

As etapas de pré-processamento a seguir ajudarão a criar um aplicativo LUIS melhor:

* **Remover duplicatas**: os declarações duplicados não serão prejudicados, mas eles não ajudarão, portanto, removê-los economizará o tempo de rotulagem.
* **Aplicar o mesmo pré-processamento de aplicativo cliente**: se o aplicativo cliente, que chama o ponto de extremidade de previsão Luis, aplica o processamento de dados em tempo de execução antes de enviar o texto para Luis, você deve treinar o aplicativo Luis nos dados que são processados da mesma maneira. Por exemplo, se seu aplicativo cliente usa [verificação ortográfica do Bing](../bing-spell-check/overview.md) para corrigir a ortografia nas entradas para Luis, corrija seu declarações de treinamento e teste antes de adicionar ao Luis.
* **Não aplique novos processos de limpeza que o aplicativo cliente não usa**: se seu aplicativo cliente aceitar texto gerado por fala diretamente sem qualquer limpeza, como gramática ou pontuação, seu declarações precisará refletir o mesmo, incluindo qualquer pontuação ausente e qualquer outro reconhecimento incorreto que você precisará considerar.
* **Não limpar os dados**: não se livra da entrada malformada que você pode obter do reconhecimento de fala com erros, keyprimas acidentais ou texto digitado incorretamente/grafado. Se seu aplicativo for ver entradas como essas, é importante que ele seja treinado e testado neles. Adicione um propósito de _entrada malformado_ se você não esperar que seu aplicativo o entenda. Rotule esses dados para ajudar seu aplicativo LUIS a prever a resposta correta em tempo de execução. O aplicativo cliente pode escolher uma resposta apropriada para declarações ininteligível, como `Please try again` .

### <a name="labeling-data"></a>Rotulando dados

* **Rotular texto como se estivesse correto**: o exemplo declarações deve ter todas as formas de uma entidade rotuladas. Isso inclui o texto que está com a grafia incorreta, digitado incorretamente e o erro de tradução.

### <a name="data-review-after-luis-app-is-in-production"></a>Análise de dados após o aplicativo LUIS estar em produção

[Examine o ponto de extremidade declarações](luis-concept-review-endpoint-utterances.md) para monitorar o tráfego real expressão depois de implantar um aplicativo para produção.  Isso permite que você atualize seu declarações de treinamento com dados reais, o que melhorará seu aplicativo. Qualquer aplicativo criado com dados de cenário de software público ou não real precisará ser melhorado com base em seu uso real.

## <a name="test-data-selection-for-batch-testing"></a>Testar a seleção de dados para teste em lotes

Todos os princípios listados acima para treinar o declarações se aplicam ao declarações que você deve usar para o [conjunto de teste](./luis-how-to-batch-test.md). Certifique-se de que a distribuição entre tentativas e entidades espelhe a distribuição real o mais próximo possível.

Não reutilize declarações do seu conjunto de treinamento em seu conjunto de teste. Isso diferencia os resultados de forma inadequada e não lhe dará a indicação correta de como seu aplicativo LUIS será executado na produção.

Depois que a primeira versão do seu aplicativo for publicada, você deverá atualizar seu conjunto de testes com declarações do tráfego real para garantir que seu conjunto de testes reflita sua distribuição de produção e você possa monitorar o desempenho realista ao longo do tempo.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como o LUIS altera seus dados antes da previsão](luis-concept-data-alteration.md)