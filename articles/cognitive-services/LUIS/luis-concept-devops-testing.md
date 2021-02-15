---
title: Testando para DevOps para aplicativos LUIS
description: Como testar seu aplicativo Reconhecimento vocal (LUIS) em um ambiente DevOps.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/3/2020
ms.openlocfilehash: cd2fd8dc8c10864089b198db1ca1089f994a3ffb
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788444"
---
# <a name="testing-for-luis-devops"></a>Teste para DevOps do LUIS

Os engenheiros de software que estão desenvolvendo um aplicativo de Reconhecimento vocal (LUIS) podem aplicar práticas DevOpss sobre [controle do código-fonte](luis-concept-devops-sourcecontrol.md), [compilações automatizadas](luis-concept-devops-automation.md), [testes](luis-concept-devops-testing.md)e [Gerenciamento de liberações](luis-concept-devops-automation.md#release-management) seguindo estas diretrizes.

Em metodologias de desenvolvimento de software Agile, o teste desempenha uma função integral na criação de software de qualidade. Cada alteração significativa em um aplicativo LUIS deve ser acompanhada por testes criados para testar a nova funcionalidade que o desenvolvedor está criando no aplicativo. Esses testes são verificados em seu repositório de código-fonte junto com a `.lu` origem do seu aplicativo Luis. A implementação da alteração é concluída quando o aplicativo cumpre os testes.

Os testes são uma parte crítica dos [fluxos de trabalho de CI/CD](luis-concept-devops-automation.md). Quando as alterações em um aplicativo LUIS são propostas em uma solicitação pull (PR) ou depois que as alterações são mescladas em seu Branch principal, os fluxos de trabalho de CI devem executar os testes para verificar se as atualizações não causaram nenhuma regressão.

## <a name="how-to-do-unit-testing-and-batch-testing"></a>Como fazer testes de unidade e testes em lote

Há dois tipos diferentes de teste para um aplicativo LUIS que você precisa executar em fluxos de trabalho de integração contínua:

- **Testes de unidade** – testes relativamente simples que verificam a funcionalidade principal do seu aplicativo Luis. Um teste de unidade passa quando a intenção esperada e as entidades esperadas são retornadas para um determinado expressão de teste. Todos os testes de unidade devem passar pela execução de teste para serem concluídos com êxito.  
Esse tipo de teste é semelhante ao [teste interativo](./luis-concept-test.md) que você pode fazer no [portal do Luis](https://www.luis.ai/).

- **Testes em lote** -o teste em lote é um teste abrangente de seu modelo treinado atual para medir seu desempenho. Diferentemente dos testes de unidade, os testes em lote não são aprovados | falha no teste. A expectativa de teste em lote não é que cada teste retornará a intenção esperada e as entidades esperadas. Em vez disso, um teste em lote ajuda a exibir a precisão de cada tentativa e entidade em seu aplicativo e ajuda a comparar ao longo do tempo à medida que você faz melhorias.  
Esse tipo de teste é o mesmo que o [teste em lotes](./luis-how-to-batch-test.md) que você pode executar interativamente no portal do Luis.

Você pode empregar testes de unidade desde o início do projeto. O teste em lote é realmente um valor assim que você desenvolveu o esquema do seu aplicativo LUIS e está trabalhando para melhorar sua precisão.

Para testes de unidade e testes em lote, certifique-se de que seus declarações de teste sejam mantidos separados do seu declarações de treinamento. Se você testar os mesmos dados nos quais treina, obterá a falsa impressão de que seu aplicativo está com um bom desempenho quando estiver apenas se sobreajustando aos dados de teste. Os testes devem ser vistos pelo modelo para testar o quão bem ele está generalizando.

### <a name="writing-tests"></a>Escrevendo testes

Quando você escreve um conjunto de testes, para cada teste, precisa definir:

* Enunciado do teste
* Intenção esperada
* Entidades esperadas.

Use a [sintaxe do arquivo de lote](./luis-how-to-batch-test.md#batch-syntax-template-for-intents-with-entities) Luis para definir um grupo de testes em um arquivo formatado em JSON. Por exemplo:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities":
    [
        {
            "entity": "entity name 1 goes here",
            "startPos": 14,
            "endPos": 23
        },
        {
            "entity": "entity name 2 goes here",
            "startPos": 14,
            "endPos": 23
        }
    ]
  }
]
```

Algumas ferramentas de teste, como [NLU. O DevOps](https://github.com/microsoft/NLU.DevOps) também oferece suporte a arquivos de teste formatados por LUDown.

#### <a name="designing-unit-tests"></a>Criando testes de unidade

Os testes de unidade devem ser projetados para testar a funcionalidade principal do seu aplicativo LUIS. Em cada iteração, ou Sprint, do desenvolvimento de seu aplicativo, você deve escrever um número suficiente de testes para verificar se a funcionalidade principal que você está implementando nessa iteração está funcionando corretamente.

Em cada teste de unidade, para um determinado expressão de teste, você pode:

* Testar se a intenção correta é retornada
* Teste se as entidades ' Key '-aquelas que são essenciais para sua solução estão sendo retornadas.
* Teste se a [Pontuação de previsão](./luis-concept-prediction-score.md) para intenção e entidades excede um limite definido. Por exemplo, você pode decidir que só considerará que um teste foi aprovado se a pontuação de previsão para a intenção e para as entidades-chave exceder 0,75.

Em testes de unidade, é uma boa ideia testar se as entidades-chave foram retornadas na resposta de previsão, mas ignorar quaisquer falsos positivos. *Falsos positivos* são entidades que são encontradas na resposta de previsão, mas que não são definidas nos resultados esperados para seu teste. Ignorando falsos positivos, ele torna menos oneroso para criar testes de unidade e, ao mesmo tempo, permite que você se concentre no teste de que os dados que são fundamentais para sua solução estão sendo retornados em uma resposta de previsão.

> [!TIP]
> O [NLU. ](https://github.com/microsoft/NLU.DevOps) A ferramenta DevOps dá suporte a todas as suas necessidades de teste de Luis. O `compare` comando quando usado no [modo de teste de unidade](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#unit-test-mode) irá declarar que todos os testes são aprovados e ignorará resultados falsos positivos para entidades que não são rotuladas nos resultados esperados.

#### <a name="designing-batch-tests"></a>Criando testes em lote

Os conjuntos de teste em lotes devem conter um grande número de casos de teste, projetados para teste em todas as intenções e todas as entidades em seu aplicativo LUIS. Confira [teste em lote no portal do Luis](./luis-how-to-batch-test.md) para obter informações sobre como definir um conjunto de teste em lotes.

### <a name="running-tests"></a>Executando testes

O portal do LUIS oferece recursos para ajudar no teste interativo:

* Os [**testes interativos**](./luis-concept-test.md) permitem que você envie um exemplo de expressão e obtenha uma resposta de intenções e entidades reconhecidas por Luis. Você verifica o sucesso do teste por inspeção visual.

* O [**teste em lotes**](./luis-how-to-batch-test.md) usa um arquivo de teste em lotes como entrada para validar sua versão treinada ativa para medir sua precisão de previsão. Um teste em lote ajuda a exibir a precisão de cada tentativa e entidade em sua versão ativa, exibindo os resultados com um gráfico.

#### <a name="running-tests-in-an-automated-build-workflow"></a>Executando testes em um fluxo de trabalho de compilação automatizada

Os recursos de teste interativo no portal do LUIS são úteis, mas para DevOps, os testes automatizados executados em um fluxo de trabalho de CI/CD trazem determinados requisitos:

* As ferramentas de teste devem ser executadas em uma etapa de fluxo de trabalho em um servidor de compilação. Isso significa que as ferramentas devem ser capazes de executar na linha de comando.
* As ferramentas de teste devem ser capazes de executar um grupo de testes em um ponto de extremidade e verificar automaticamente os resultados esperados em relação aos resultados reais.
* Se os testes falharem, as ferramentas de teste deverão retornar um código de status para interromper o fluxo de trabalho e "falhar na compilação".

O LUIS não oferece uma ferramenta de linha de comando ou uma API de alto nível que oferece esses recursos. Recomendamos que você use o [NLU. Ferramenta DevOps](https://github.com/microsoft/NLU.DevOps) para executar testes e verificar os resultados, na linha de comando e durante o teste automatizado em um fluxo de trabalho de CI/CD.

Os recursos de teste que estão disponíveis no portal do LUIS não exigem um ponto de extremidade publicado e fazem parte dos recursos de criação do LUIS. Quando você estiver implementando testes em um fluxo de trabalho de build automatizado, deverá publicar a versão do aplicativo LUIS para ser testada em um ponto de extremidade para que as ferramentas de teste, como NLU. O DevOps pode enviar solicitações de previsão como parte do teste.

> [!TIP]
> * Se você estiver implementando sua própria solução de teste e escrevendo código para enviar declarações de teste para um ponto de extremidade, lembre-se de que, se você estiver usando a chave de criação LUIS, a taxa de transação permitida será limitada a 5TPS. Em vez disso, limite a taxa de envio ou use uma chave de previsão.
> * Ao enviar consultas de teste para um ponto de extremidade, lembre-se de usar `log=false` na cadeia de caracteres de consulta de sua solicitação de previsão. Isso garante que o declarações de teste não seja registrado pelo LUIS e termine na lista de revisão do ponto de extremidade declarações apresentada pelo recurso LUIS [Active Learning](./luis-concept-review-endpoint-utterances.md) e, como resultado, seja adicionado acidentalmente ao declarações de treinamento do seu aplicativo.

#### <a name="running-unit-tests-at-the-command-line-and-in-cicd-workflows"></a>Executando testes de unidade na linha de comando e em fluxos de trabalho de CI/CD

Você pode usar o [NLU. Pacote DevOps](https://github.com/microsoft/NLU.DevOps) para executar testes na linha de comando:

* Use o NLU. DevOps [comando de teste](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Test.md) para enviar testes de um arquivo de teste para um ponto de extremidade e capturar os resultados reais de previsão em um arquivo.
* Use o NLU. DevOps o [comando Compare](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md) para comparar os resultados reais com os resultados esperados definidos no arquivo de teste de entrada. O `compare` comando gera a saída de teste NUnit e, quando usada no [modo de teste de unidade](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#unit-test-mode) pelo uso do `--unit-test` sinalizador, irá declarar que todos os testes foram aprovados.

### <a name="running-batch-tests-at-the-command-line-and-in-cicd-workflows"></a>Executando testes em lote na linha de comando e em fluxos de trabalho de CI/CD

Você também pode usar o NLU. Pacote DevOps para executar testes em lotes na linha de comando.

* Use o NLU. DevOps [comando de teste](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Test.md) para enviar testes de um arquivo de teste para um ponto de extremidade e capturar os resultados reais de previsão em um arquivo, assim como nos testes de unidade.
* Use o NLU. [Comando DevOps Compare](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md) no [modo de teste de desempenho](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#performance-test-mode) para medir o desempenho de seu aplicativo, você também pode comparar o desempenho do seu aplicativo com relação a um parâmetro de comparação de desempenho de linha de base, por exemplo, os resultados da última confirmação para a versão principal ou atual. No modo de teste de desempenho, o `compare` comando gera saída de teste NUnit e [resultados de teste em lotes](./luis-glossary.md#batch-test) no formato JSON.

## <a name="luis-non-deterministic-training-and-the-effect-on-testing"></a>LUIS treinamento não determinístico e o efeito sobre os testes

Quando o LUIS está treinando um modelo, como uma intenção, ele precisa de ambos os dados positivos, o declarações de treinamento rotulado que você forneceu para treinar o aplicativo para os dados de modelo e dados negativos que *não* são exemplos válidos do uso desse modelo. Durante o treinamento, o LUIS cria os dados negativos de um modelo a partir de todos os dados positivos que você forneceu para os outros modelos, mas em alguns casos que podem produzir um desequilíbrio de dados. Para evitar esse desequilíbrio, o LUIS amostra um subconjunto dos dados negativos de uma maneira não determinística para otimizar para um conjunto de treinamento com balanceamento melhor, melhor desempenho do modelo e tempo de treinamento mais rápido.

O resultado desse treinamento não determinístico é que você pode obter uma [resposta de previsão ligeiramente diferente entre diferentes sessões de treinamento](./luis-concept-prediction-score.md), geralmente para tentativas e/ou entidades em que a pontuação de [previsão](./luis-concept-prediction-score.md) não está alta.

Se você quiser desabilitar o treinamento não determinístico para as versões do aplicativo LUIS que você está criando para fins de teste, use a [API de configurações de versão](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) com a `UseAllTrainingData` configuração definida como `true` .

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [implementação de fluxos de trabalho de CI/CD](luis-concept-devops-automation.md)
* Saiba como [implementar o DevOps para Luis com o GitHub](luis-how-to-devops-with-github.md)