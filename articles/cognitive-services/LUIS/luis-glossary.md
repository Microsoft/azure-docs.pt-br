---
title: Glossário-LUIS
description: O glossário explica os termos que você pode encontrar à medida que trabalha com o Serviço de API do LUIS.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/08/2020
ms.openlocfilehash: 7c65c8272172cab9f5361d16141bf7b229037480
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98786937"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Glossário de noções básicas de linguagem de conceitos e vocabulário comuns
O Glossário de Reconhecimento vocal (LUIS) explica os termos que você pode encontrar enquanto trabalha com o serviço LUIS.

## <a name="active-version"></a>Versão ativa

A versão ativa é a [versão](luis-how-to-manage-versions.md) do seu aplicativo que é atualizada quando você faz alterações no modelo usando o portal do Luis. No portal do LUIS, se você quiser fazer alterações em uma versão que não seja a versão ativa, primeiro você precisará definir essa versão como ativa.

## <a name="active-learning"></a>Aprendizado ativo

O aprendizado ativo é uma técnica de aprendizado de máquina em que o modelo aprendido por máquina é usado para identificar novos exemplos informativos para rotular. No LUIS, o aprendizado ativo refere-se à adição de declarações do tráfego do ponto de extremidade cujas previsões atuais não estão claras para melhorar seu modelo. Clique em "examinar ponto de extremidade declarações" para exibir declarações para rotular.

Consulte também:
* [Informações conceituais](luis-concept-review-endpoint-utterances.md)
* [Tutorial de revisão do declarações de ponto de extremidade](luis-tutorial-review-endpoint-utterances.md)
* Como melhorar o aplicativo LUIS [examinando o ponto de extremidade declarações](luis-how-to-review-endpoint-utterances.md)

## <a name="application-app"></a>Aplicativo (aplicativo)

No LUIS, seu aplicativo ou aplicativo, é uma coleção de modelos aprendidos por máquina, criados no mesmo conjunto de dados, que funciona em conjunto para prever intenções e entidades para um cenário específico. Cada aplicativo tem um ponto de extremidade de previsão separado.

Se estiver criando um bot de RH, você poderá ter um conjunto de tentativas, como "tempo de licença de agenda", "consultar os benefícios" e "atualizar informações pessoais" e entidades para cada uma dessas intenções que você agrupa em um único aplicativo.

## <a name="authoring"></a>Criação

A criação é a capacidade de criar, gerenciar e implantar um aplicativo LUIS, usando o portal do LUIS ou as APIs de criação.

### <a name="authoring-key"></a>Chave de criação

A [chave de criação](luis-how-to-azure-subscription.md) é usada para criar o aplicativo. Não usada para consultas de ponto de extremidade no nível de produção. Para obter mais informações, confira [Limites da chave](luis-limits.md#key-limits).

### <a name="authoring-resource"></a>Recurso de criação

O [recurso de criação](luis-how-to-azure-subscription.md#azure-resources-for-luis) do Luis é um item gerenciável que está disponível por meio do Azure. O recurso é seu acesso às capacidades de criação, treinamento e publicação associadas do serviço do Azure. O recurso inclui autenticação, autorização e informações de segurança de que você precisa para acessar o serviço do Azure associado.

O recurso de criação tem um "tipo" do Azure `LUIS-Authoring` .

## <a name="batch-test"></a>Teste de lote

Os testes em lote são a capacidade de validar os modelos de um aplicativo LUIS atual com um conjunto de teste consistente e conhecido de declarações de usuário. O teste em lotes é definido em um [arquivo formatado em JSON](./luis-how-to-batch-test.md#batch-test-file).


Consulte também:
* [Conceitos](./luis-how-to-batch-test.md)
* [Como](luis-how-to-batch-test.md) executar um teste em lotes
* [Tutorial](./luis-how-to-batch-test.md) – criar e executar um teste em lotes

### <a name="f-measure"></a>Medida F

No teste de lote, uma medida da precisão do teste.

### <a name="false-negative-fn"></a>Falso negativo (FN)

No teste de lote, os pontos de dados representam enunciados em que o aplicativo previu incorretamente a ausência de entidade/intenção de destino.

### <a name="false-positive-fp"></a>Falso positivo (FP)

No teste de lote, os pontos de dados representam enunciados em que o aplicativo previu incorretamente a existência de entidade/intenção de destino.

### <a name="precision"></a>Precisão
No teste de lote, a precisão (também chamada de valor preditivo positivo) é a fração de enunciados relevantes entre os enunciados recuperados.

Um exemplo para um teste de lote de animais é o número de ovelha que foram previstas divididos pelo número total de animais (ovelha e não ovelha semelhantes).

### <a name="recall"></a>Recall

No teste de lote, recall (também conhecido como sensibilidade) é a capacidade de generalização do LUIS.

Um exemplo de teste de lote de animais é o número de ovelha que foram previstas divididos pelo número total de ovelha disponíveis.

### <a name="true-negative-tn"></a>Negativo verdadeiro (TN)

Um verdadeiro negativo é quando seu aplicativo não prevê corretamente nenhuma correspondência. No teste em lotes, um verdadeiro negativo ocorre quando seu aplicativo prevê uma intenção ou entidade para um exemplo que não tenha sido rotulado com essa intenção ou entidade.

### <a name="true-positive-tp"></a>Positivo verdadeiro (TP)

Verdadeiro positivo (TP) um verdadeiro positivo é quando seu aplicativo prevê uma correspondência corretamente. No teste em lotes, um verdadeiro positivo ocorre quando seu aplicativo prevê uma intenção ou entidade para obter um exemplo que tenha sido rotulado com essa intenção ou entidade.

## <a name="classifier"></a>Classificador

Um classificador é um modelo de computador aprendido que prevê a categoria ou a classe à qual uma entrada se encaixa.

Uma [intenção](#intent) é um exemplo de classificador.

## <a name="collaborator"></a>Colaborador

Um colaborador é conceitualmente a mesma coisa que um [contribuinte](#contributor). Um colaborador recebe acesso quando um proprietário adiciona o endereço de email do colaborador a um aplicativo que não é controlado com o controle de acesso baseado em função do Azure (RBAC do Azure). Se você ainda estiver usando colaboradores, deverá migrar sua conta do LUIS e usar os recursos de criação do LUIS para gerenciar colaboradores com o RBAC do Azure.

## <a name="contributor"></a>Colaborador

Um colaborador não é o [proprietário](#owner) do aplicativo, mas tem as mesmas permissões para adicionar, editar e excluir as intenções, entidades, declarações. Um colaborador fornece o Azure RBAC (controle de acesso baseado em função) para um aplicativo LUIS.

Consulte também:
* [Como](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource) adicionar colaboradores

## <a name="descriptor"></a>Descritor

Um descritor é o termo usado anteriormente para um [recurso](#features)de aprendizado de máquina.

## <a name="domain"></a>Domínio

No contexto do LUIS, um domínio é uma área de conhecimento. Seu domínio é específico do seu cenário. Domínios diferentes usam uma terminologia e linguagem específica que têm significado no contexto do domínio. Por exemplo, se você estiver criando um aplicativo para reproduzir música, seu aplicativo teria termos e idioma específicos para música – palavras como "música, faixa, álbum, letras do lado do b, artista". Para obter exemplos de domínios, consulte [domínios predefinidos](#prebuilt-domain).

## <a name="endpoint"></a>Ponto de Extremidade

### <a name="authoring-endpoint"></a>Ponto de extremidade de criação

A URL do ponto de extremidade de criação do LUIS é onde você cria, treina e publica seu aplicativo. A URL do ponto de extremidade contém a região ou o subdomínio personalizado do aplicativo publicado, bem como a ID do aplicativo.

Saiba mais sobre como criar seu aplicativo programaticamente da [referência do desenvolvedor](developer-reference-resource.md#rest-endpoints)

### <a name="prediction-endpoint"></a>Ponto de extremidade de previsão

A URL do ponto de extremidade de previsão LUIS é onde você envia consultas LUIS depois que o [aplicativo Luis](#application-app) é criado e publicado. A URL do ponto de extremidade contém a região ou o subdomínio personalizado do aplicativo publicado, bem como a ID do aplicativo. Você pode encontrar o ponto de extremidade na página de **[recursos do Azure](luis-how-to-azure-subscription.md)** do seu aplicativo ou pode obter a URL do ponto de extremidade da API [obter informações de aplicativo](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) .

Seu acesso ao ponto de extremidade de previsão é autorizado com a chave de previsão LUIS.

## <a name="entity"></a>Entidade

As [entidades](luis-concept-entity-types.md) são palavras em declarações que descrevem as informações usadas para atender ou identificar uma intenção. Se sua entidade for complexa e você quiser que seu modelo identifique partes específicas, você poderá dividir seu modelo em subentidades. Por exemplo, você pode desejar que o modelo preveja um endereço, mas também as subentidades de Street, City, State e ZipCode. As entidades também podem ser usadas como recursos para modelos. Sua resposta do aplicativo LUIS incluirá as intenções previstas e todas as entidades.

### <a name="entity-extractor"></a>Extrator de entidade

Um extrator de entidades, às vezes conhecido apenas como um extrator, é o tipo de modelo aprendido por máquina que o LUIS usa para prever entidades.

### <a name="entity-schema"></a>Esquema de entidade

O esquema de entidade é a estrutura que você define para entidades aprendidas com o computador com subentidades. O ponto de extremidade de previsão retorna todas as entidades e subentidades extraídas definidas no esquema.

### <a name="entitys-subentity"></a>Subentidade da entidade

Uma subentidade é uma entidade filho de uma entidade de aprendizado de máquina.

### <a name="non-machine-learning-entity"></a>Entidade de aprendizagem não Machine-Learning

Uma entidade que usa correspondência de texto para extrair dados:
* Entidade de lista
* Entidade de expressão regular

### <a name="list-entity"></a>Entidade de lista

Uma [entidade de lista](reference-entity-list.md) representa um conjunto fixo e fechado de palavras relacionadas junto com seus sinônimos. As entidades de lista são correspondências exatas, diferentemente das entidades aprendidas por computador.

A entidade será prevista se uma palavra na entidade de lista estiver incluída na lista. Por exemplo, se você tiver uma entidade de lista chamada "tamanho" e tiver as palavras "pequeno, médio, grande" na lista, a entidade de tamanho será prevista para todos os declarações em que as palavras "pequeno", "média" ou "grande" são usadas, independentemente do contexto.

### <a name="regular-expression"></a>Expressão regular

Uma [entidade de expressão regular](reference-entity-regular-expression.md) representa uma expressão regular. As entidades de expressão regular são correspondências exatas, diferentemente das entidades aprendidas por computador.
### <a name="prebuilt-entity"></a>Entidade predefinida

Consulte a entrada do modelo predefinido para [entidade predefinida](#prebuilt-entity)

## <a name="features"></a>Recursos

No Machine Learning, um recurso é uma característica que ajuda o modelo a reconhecer um determinado conceito. É uma dica que o LUIS pode usar, mas não uma regra rígida.

Esse termo também é conhecido como um **[recurso de aprendizado de máquina](luis-concept-feature.md)**.

Essas dicas são usadas em conjunto com os rótulos para saber como prever novos dados. O LUIS dá suporte a ambas as listas de frases e ao uso de outros modelos como recursos.

### <a name="required-feature"></a>Recurso necessário

Um recurso necessário é uma maneira de restringir a saída de um modelo LUIS. Quando um recurso de uma entidade é marcado como obrigatório, o recurso deve estar presente no exemplo para que a entidade seja prevista, independentemente do que o modelo aprendido pelo computador prevê.

Considere um exemplo em que você tem um recurso de número predefinido que você marcou como obrigatório na entidade quantidade para um bot de ordenação de menus. Quando o bot vê `I want a bajillion large pizzas?` , o bajillion não será previsto como uma quantidade, independentemente do contexto no qual ele aparece. Bajillion não é um número válido e não será previsto pelo número da entidade predefinida.

## <a name="intent"></a>Intencional

Uma [intenção](luis-concept-intent.md) representa uma tarefa ou ação que o usuário deseja executar. É uma finalidade ou meta expressa na entrada de um usuário, como a reserva de um vôo ou o pagamento de uma conta. No LUIS, um expressão como um todo é classificado como uma intenção, mas partes do expressão são extraídas como entidades

## <a name="labeling-examples"></a>Exemplos de rotulagem

Rotular, ou marcar, é o processo de associar um exemplo positivo ou negativo com um modelo.

### <a name="labeling-for-intents"></a>Rotulagem para tentativas
No LUIS, as intenções em um aplicativo são mutuamente exclusivas. Isso significa que quando você adiciona um expressão a uma intenção, ele é considerado um exemplo _positivo_ para essa intenção e um exemplo _negativo_ para todas as outras intenções. Exemplos negativos não devem ser confundidos com a intenção "nenhum", que representa declarações que estão fora do escopo do aplicativo.

### <a name="labeling-for-entities"></a>Rotulagem para entidades
No LUIS, você [rotula](label-entity-example-utterance.md) uma palavra ou frase no exemplo de uma intenção expressão com uma entidade como um exemplo _positivo_ . Rotular mostra a intenção que ele deve prever para esse expressão. Os declarações rotulados são usados para treinar a intenção.

## <a name="luis-app"></a>Aplicativo LUIS

Consulte a definição de [aplicativo (aplicativo)](#application-app).

## <a name="model"></a>Modelo

Um modelo (aprendido pelo computador) é uma função que faz uma previsão sobre os dados de entrada. Em LUIS, nos referimos aos classificadores de intenção e aos extratores de entidade genericamente como "modelos", e nos referimos a uma coleção de modelos treinados, publicados e consultados juntos como um "aplicativo".

## <a name="normalized-value"></a>Valor normalizado

Você adiciona valores às suas entidades de [lista](#list-entity) . Cada um desses valores pode ter uma lista de um ou mais sinônimos. Somente o valor normalizado é retornado na resposta.

## <a name="overfitting"></a>Sobreajuste

O sobreajuste acontece quando o modelo é concentradado nos exemplos específicos e não é capaz de generalizar bem.

## <a name="owner"></a>Proprietário

Cada aplicativo tem um proprietário, que é a pessoa que criou o aplicativo. O proprietário gerencia permissões para o aplicativo no portal do Azure.

## <a name="phrase-list"></a>Lista de frases

Uma [lista de frases](luis-concept-feature.md) é um tipo específico de recurso de aprendizado de máquina que inclui um grupo de valores (palavras ou frases) que pertencem à mesma classe e deve ser tratado da mesma forma (por exemplo, nomes de cidades ou produtos).

## <a name="prebuilt-model"></a>Modelo predefinido

Um [modelo predefinido](luis-concept-prebuilt-model.md) é uma intenção, entidade ou coleção de ambos, juntamente com exemplos rotulados. Esses modelos predefinidos comuns podem ser adicionados ao seu aplicativo para reduzir o trabalho de desenvolvimento do modelo necessário para seu aplicativo.

### <a name="prebuilt-domain"></a>Domínio predefinido

Um domínio predefinido é um aplicativo LUIS configurado para um domínio específico, como automação residencial (HomeAutomation) ou reservas de restaurante (RestaurantReservation). As intenções, declarações e entidades são configuradas para esse domínio.

### <a name="prebuilt-entity"></a>Entidade predefinida

Uma entidade predefinida é um LUIS de entidade que fornece tipos comuns de informações, como número, URL e email. Eles são criados com base em dados públicos. Você pode optar por adicionar uma entidade predefinida como uma entidade autônoma ou como um recurso a uma entidade

### <a name="prebuilt-intent"></a>Intenção predefinida

Uma intenção predefinida é uma intenção LUIS fornecer tipos comuns de informações e ter seu próprio exemplo rotulado declarações.

## <a name="prediction"></a>Previsão

Uma previsão é uma solicitação REST para o serviço de previsão de LUIS do Azure que recebe novos dados (usuário expressão) e aplica o aplicativo treinado e publicado a esses dados para determinar quais tentativas e entidades são encontradas.

### <a name="prediction-key"></a>Chave de previsão

A [chave de previsão](luis-how-to-azure-subscription.md) (anteriormente conhecida como chave de assinatura) é a chave associada ao serviço Luis que você criou no Azure que autoriza o uso do ponto de extremidade de previsão.

Essa chave não é a chave de criação. Se você tiver uma chave de ponto de extremidade de previsão, ela deverá ser usada para qualquer solicitação de ponto de extremidade em vez da chave de criação. Você pode ver sua chave de previsão atual dentro da URL do ponto de extremidade na parte inferior da página de recursos do Azure no site do LUIS. É o valor do par nome/valor da chave de assinatura.

### <a name="prediction-resource"></a>Recurso de previsão

O recurso de previsão do LUIS é um item gerenciável que está disponível por meio do Azure. O recurso é seu acesso à previsão associada do serviço do Azure. O recurso inclui previsões.

O recurso de previsão tem um "tipo" do Azure `LUIS` .

### <a name="prediction-score"></a>Pontuação de previsão

A [Pontuação](luis-concept-prediction-score.md) é um número de 0 e 1 que é uma medida de quão confiante é a confiança do sistema de que uma determinada entrada expressão corresponde a uma determinada intenção. Uma pontuação mais próxima de 1 significa que o sistema está muito confiante em relação à saída e uma pontuação mais próxima de 0 significa que o sistema tem certeza de que a entrada não corresponde a uma saída específica. As pontuações no meio significam que o sistema não tem certeza de como tomar a decisão.

Por exemplo, use um modelo que é usado para identificar se algum texto de cliente inclui uma ordem de alimentos. Ele pode dar uma pontuação de 1 para "gostaria de pedir uma café" (o sistema tem certeza de que isso é uma ordem) e uma pontuação de 0 para "minha equipe venceu o jogo pela noite" (o sistema tem muito certeza de que isso não é um pedido). E ele pode ter uma pontuação de 0,5 por "Vamos ter um chá" (não tem certeza se isso é um pedido ou não).

## <a name="programmatic-key"></a>Chave programática

Renomeada para [chave de criação](#authoring-key).

## <a name="publish"></a>Publicação

[Publicação](luis-how-to-publish-app.md) significa tornar uma versão Luis ativa disponível no [ponto de extremidade](#endpoint)de preparo ou de produção.

## <a name="quota"></a>Quota

A cota LUIS é a limitação do nível de assinatura do Azure. A cota LUIS pode ser limitada por solicitações por segundo (HTTP Status 429) e solicitações totais em um mês (HTTP Status 403).

## <a name="schema"></a>Esquema

Seu esquema inclui suas intenções e entidades junto com as subentidades. O esquema é inicialmente planejado para, em seguida, iterado ao longo do tempo. O esquema não inclui configurações de aplicativo, recursos ou declarações de exemplo.

## <a name="sentiment-analysis"></a>Análise de Sentimento
A Análise de sentimento fornece valores positivos ou negativos das declarações fornecidas pela [Análise de Texto](../text-analytics/overview.md).

## <a name="speech-priming"></a>Preparação da fala

A desobstrução de fala melhora o reconhecimento de palavras faladas e frases que são comumente usadas em seu cenário com os [serviços de fala](../speech-service/overview.md). Para aplicativos habilitados para estornar a fala, todos os exemplos de LUIS rotulados são usados para melhorar a precisão do reconhecimento de fala criando um modelo de fala personalizado para esse aplicativo específico. Por exemplo, em um jogo de xadrez, você deseja certificar-se de que quando o usuário disser "mover Knight", ele não será interpretado como "movimento noturno". O aplicativo LUIS deve incluir exemplos em que "cavalo" é rotulado como uma entidade.

## <a name="starter-key"></a>Chave de início

Uma chave gratuita a ser usada ao iniciar pela primeira vez usando LUIS.

## <a name="synonyms"></a>Sinônimos

Em LUIS [listar entidades](reference-entity-list.md), você pode criar um valor normalizado, que pode ter uma lista de sinônimos. Por exemplo, se você criar uma entidade de tamanho que tenha valores normalizados de pequeno, médio, grande e extra grande. Você pode criar sinônimos para cada valor como este:

|Valor de Nomalized| Sinônimos|
|--|--|
|Pequena| o pouco, 8 onças|
|Médio| regular, 12 onças|
|Grande| Big, 16 onças|
|Xtra grande| o maior, 24 onças|

O modelo retornará o valor normalizado para a entidade quando qualquer um dos sinônimos for visto na entrada.

## <a name="test"></a>Teste

[Testar](luis-concept-test.md) um aplicativo Luis significa exibir previsões de modelo.

## <a name="timezone-offset"></a>Diferença de fuso horário

O ponto de extremidade inclui [timezoneOffset](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). Este é o número, em minutos, que você deseja adicionar ou remover da entidade predefinida datetimeV2. Por exemplo, se a declaração for "que horas são agora?", o datetimeV2 retornado será a hora atual da solicitação do cliente. Se a solicitação do cliente for proveniente de um bot ou de outro aplicativo que não for o mesmo que o usuário do seu bot, você deverá passar o deslocamento entre o bot e o usuário.

Confira [Alterar o fuso horário da entidade datetimeV2 predefinida](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>Token
Um [token](luis-language-support.md#tokenization) é a menor unidade de texto que o Luis pode reconhecer. Isso difere ligeiramente entre os idiomas.

Para o **Inglês**, um token é um intervalo contínuo (sem espaços ou pontuação) de letras e números. Um espaço não é um token.

|Frase|Contagem de tokens|Explicação|
|--|--|--|
|`Dog`|1|Uma única palavra sem pontuação ou espaços.|
|`RMT33W`|1|Um número de localizador de registro. Ele pode ter números e letras, mas não tem pontuação.|
|`425-555-5555`|5|Um número de telefone. Cada marca de pontuação é um único token, portanto,  `425-555-5555` seria 5 tokens:<br>`425`<br>`-`<br>`555`<br>`-`<br>`5555` |
|`https://luis.ai`|7|`https`<br>`:`<br>`/`<br>`/`<br>`luis`<br>`.`<br>`ai`<br>|

## <a name="train"></a>Train

O [treinamento](luis-how-to-train.md) é o processo de ensinar Luis sobre quaisquer alterações na versão ativa desde o último treinamento.

### <a name="training-data"></a>Dados de treinamento

Os dados de treinamento são o conjunto de informações necessárias para treinar um modelo. Isso inclui o esquema, rotulado declarações, recursos e configurações do aplicativo.

### <a name="training-errors"></a>Erros de treinamento

Os erros de treinamento são previsões em seus dados de treinamento que não correspondem a seus rótulos.

## <a name="utterance"></a>Enunciado

Um [expressão](luis-concept-utterance.md) é uma entrada do usuário que é um representante de texto curto de uma frase em uma conversa. Trata-se de uma frase de idioma natural, como "tíquetes do livro 2 para Seattle na próxima terça-feira". Exemplos de declarações são adicionados para treinar o modelo e o modelo prevê no novo expressão em tempo de execução

## <a name="version"></a>Versão

Uma [versão](luis-how-to-manage-versions.md) do Luis é uma instância específica de um aplicativo Luis associado a uma ID do aplicativo Luis e ao ponto de extremidade publicado. Todo aplicativo LUIS tem pelo menos uma versão.
