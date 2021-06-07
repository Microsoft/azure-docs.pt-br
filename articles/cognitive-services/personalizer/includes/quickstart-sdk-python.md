---
title: incluir arquivo
description: incluir arquivo
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: cog-serv-seo-aug-2020
ms.date: 08/25/2020
ms.openlocfilehash: 0298edc069ee1f61bd3ccd437914c9b01e083900
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445654"
---
[Documentação de referência](/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-personalizer) | [Pacote (pypi)](https://pypi.org/project/azure-cognitiveservices-personalizer/) | [Exemplos](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Personalizer)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Depois de obter a assinatura do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer"  title="Criar um recurso do Personalizador"  target="_blank">, crie um recurso do Personalizador </a> no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso que você criar para conectar o seu aplicativo à API do Personalizador. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Ao configurar o

[!INCLUDE [Change model frequency](change-model-frequency.md)]

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

Depois de instalar o Python, você pode instalar a biblioteca de clientes com:

```console
pip install azure-cognitiveservices-personalizer
```

### <a name="create-a-new-python-application"></a>Criar um novo aplicativo Python

Crie um arquivo Python e as variáveis para a chave de assinatura e o ponto de extremidade do recurso.

[!INCLUDE [Personalizer find resource info](find-azure-resource-info.md)]

```python
from azure.cognitiveservices.personalizer import PersonalizerClient
from azure.cognitiveservices.personalizer.models import RankableAction, RewardRequest, RankRequest
from msrest.authentication import CognitiveServicesCredentials

import datetime, json, os, time, uuid

key = "<paste-your-personalizer-key-here>"
endpoint = "<paste-your-personalizer-endpoint-here>"
```

## <a name="object-model"></a>Modelo de objeto

O cliente do Personalizador é um objeto [PersonalizerClient](/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.personalizer_client.personalizerclient) que se autentica no Azure usando Microsoft.Rest.ServiceClientCredentials, que contém sua chave.

Para solicitar o melhor item do conteúdo, crie uma [RankRequest](/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.models.rankrequest) e, em seguida, transmita-a para o método client.Rank. O método Rank retornará uma RankResponse.

Para enviar uma pontuação de recompensa ao Personalizador, defina a ID do evento e a pontuação de recompensa (valor) a serem enviados ao método [Reward](/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.operations.events_operations.eventsoperations#reward-event-id--value--custom-headers-none--raw-false----operation-config-) na classe EventOperations.

Determinar a recompensa neste início rápido é trivial. Em um sistema de produção, a determinação do que afeta a [pontuação de recompensa](../concept-rewards.md) e em que medida pode ser um processo complexo; você pode decidir alterá-lo ao longo do tempo. Isso deve ser uma das principais decisões de design na arquitetura do Personalizador.

## <a name="code-examples"></a>Exemplos de código

Esses snippets de código mostram como fazer o seguinte com a biblioteca de clientes do Personalizador para Python:

* [Autenticar o cliente](#authenticate-the-client)
* [API de Classificação](#request-the-best-action)
* [API de Recompensa](#send-a-reward)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie uma instância do `PersonalizerClient` com o `key` e o `endpoint` que você criou anteriormente.

```python
# Instantiate a Personalizer client
client = PersonalizerClient(endpoint, CognitiveServicesCredentials(key))
```

## <a name="get-content-choices-represented-as-actions"></a>Obter opções de conteúdo representadas como ações

As ações representam as opções de conteúdo entre as quais você deseja que o Personalizador selecione o melhor item de conteúdo. Adicione os métodos a seguir à classe Program para representar o conjunto de ações e os recursos.

```python
def get_actions():
    action1 = RankableAction(id='pasta', features=[{"taste":"salty", "spice_level":"medium"},{"nutrition_level":5,"cuisine":"italian"}])
    action2 = RankableAction(id='ice cream', features=[{"taste":"sweet", "spice_level":"none"}, { "nutritional_level": 2 }])
    action3 = RankableAction(id='juice', features=[{"taste":"sweet", 'spice_level':'none'}, {'nutritional_level': 5}, {'drink':True}])
    action4 = RankableAction(id='salad', features=[{'taste':'salty', 'spice_level':'none'},{'nutritional_level': 2}])
    return [action1, action2, action3, action4]
```

```python
def get_user_timeofday():
    res={}
    time_features = ["morning", "afternoon", "evening", "night"]
    time = input("What time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night\n")
    try:
        ptime = int(time)
        if(ptime<=0 or ptime>len(time_features)):
            raise IndexError
        res['time_of_day'] = time_features[ptime-1]
    except (ValueError, IndexError):
        print("Entered value is invalid. Setting feature value to", time_features[0] + ".")
        res['time_of_day'] = time_features[0]
    return res
```

```python
def get_user_preference():
    res = {}
    taste_features = ['salty','sweet']
    pref = input("What type of food would you prefer? Enter number 1.salty 2.sweet\n")
    
    try:
        ppref = int(pref)
        if(ppref<=0 or ppref>len(taste_features)):
            raise IndexError
        res['taste_preference'] = taste_features[ppref-1]
    except (ValueError, IndexError):
        print("Entered value is invalid. Setting feature value to", taste_features[0]+ ".")
        res['taste_preference'] = taste_features[0]
    return res
```

## <a name="create-the-learning-loop"></a>Criar o loop de aprendizado

O loop de aprendizado do Personalizador é um ciclo de chamadas de [Classificação](#request-the-best-action) e [Recompensa](#send-a-reward). Neste início rápido, cada chamada de classificação, para personalizar o conteúdo, é seguida por uma chamada de recompensa para informar ao Personalizador o nível de desempenho do serviço.

O código a seguir executa um loop em um ciclo que inclui perguntas sobre as preferências do usuário na linha de comando, envio dessas informações ao Personalizador para seleção da melhor ação, apresentação da seleção ao cliente para escolha de uma opção na lista e, em seguida, envio de uma recompensa ao Personalizador, sinalizando o nível de desempenho do serviço na seleção.

```python
keep_going = True
while keep_going:

    eventid = str(uuid.uuid4())

    context = [get_user_preference(), get_user_timeofday()]
    actions = get_actions()

    rank_request = RankRequest( actions=actions, context_features=context, excluded_actions=['juice'], event_id=eventid)
    response = client.rank(rank_request=rank_request)
    
    print("Personalizer service ranked the actions with the probabilities listed below:")
    
    rankedList = response.ranking
    for ranked in rankedList:
        print(ranked.id, ':',ranked.probability)

    print("Personalizer thinks you would like to have", response.reward_action_id+".")
    answer = input("Is this correct?(y/n)\n")[0]

    reward_val = "0.0"
    if(answer.lower()=='y'):
        reward_val = "1.0"
    elif(answer.lower()=='n'):
        reward_val = "0.0"
    else:
        print("Entered choice is invalid. Service assumes that you didn't like the recommended food choice.")

    client.events.reward(event_id=eventid, value=reward_val)

    br = input("Press Q to exit, any other key to continue: ")
    if(br.lower()=='q'):
        keep_going = False
```

Adicione os seguintes métodos, que [obtêm as opções de conteúdo](#get-content-choices-represented-as-actions), antes de executar o arquivo de código:

* `get_user_preference`
* `get_user_timeofday`
* `get_actions`

## <a name="request-the-best-action"></a>Solicitar a melhor ação

Para concluir a solicitação de Classificação, o programa pergunta as preferências do usuário para criar um `currentContent` das opções de conteúdo. O processo pode criar o conteúdo a ser excluído das ações, mostrado como `excludeActions`. A solicitação de Classificação precisa das ações e dos recursos delas, dos recursos do currentContext, das excludeActions e de uma ID exclusiva do evento para receber a resposta.

Este início rápido tem recursos de contexto simples da hora do dia e da preferência de alimentos do usuário. Em sistemas de produção, determinar e [avaliar](../concept-feature-evaluation.md) [ações e recursos](../concepts-features.md) pode ser uma questão não trivial.

```python
rank_request = RankRequest( actions=actions, context_features=context, excluded_actions=['juice'], event_id=eventid)
response = client.rank(rank_request=rank_request)
```

## <a name="send-a-reward"></a>Enviar uma recompensa

Para obter a pontuação de recompensa a ser enviada na solicitação de Recompensa, o programa obtém a seleção do usuário na linha de comando, atribui um valor numérico a cada seleção e, em seguida, envia uma ID exclusiva do evento e a pontuação de recompensa como o valor numérico para a API de Recompensa.

Este início rápido atribui um número simples como uma pontuação de recompensa, um zero ou um 1. Em sistemas de produção, determinar quando e o que enviar para a chamada de [Recompensa](../concept-rewards.md) pode ser uma questão não trivial, dependendo das suas necessidades específicas.

```python
reward_val = "0.0"
if(answer.lower()=='y'):
    reward_val = "1.0"
elif(answer.lower()=='n'):
    reward_val = "0.0"
else:
    print("Entered choice is invalid. Service assumes that you didn't like the recommended food choice.")

client.events.reward(event_id=eventid, value=reward_val)
```

## <a name="run-the-program"></a>Execute o programa

Execute o aplicativo com o python do seu diretório de aplicativo.

```console
python sample.py
```

![O programa de Início Rápido faz algumas perguntas para coletar as preferências do usuário, conhecidas como recursos e, em seguida, fornece a ação principal.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)
