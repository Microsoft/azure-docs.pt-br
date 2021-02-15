---
title: Onde e como usar-personalizar
description: O Personalizador pode ser aplicado em qualquer situação em que seu aplicativo pode selecionar o item, a ação ou o produto correto a ser exibido – visando aprimorar a experiência, alcançar melhores resultados de negócios ou melhorar a produtividade.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 87179950c33a6facb59ce499aa0ae393e53fb37f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777217"
---
# <a name="where-and-how-to-use-personalizer"></a>Onde e como usar o personalizador

Use o personalizador em qualquer situação em que seu aplicativo precise selecionar a ação (conteúdo) correta para ser exibida, a fim de melhorar a experiência, obter melhores resultados de negócios ou aumentar a produtividade.

O personalizador usa o reforço Learning para selecionar qual ação (conteúdo) mostrar o usuário. A seleção pode variar drasticamente, dependendo da quantidade, da qualidade e da distribuição dos dados enviados ao serviço.

## <a name="example-use-cases-for-personalizer"></a>Casos de uso de exemplo do Personalizador

* **Esclarecimento da intenção & Desambigüidade**: Ajude seus usuários a ter uma experiência melhor quando sua intenção não estiver clara, fornecendo uma opção personalizada.
* **Sugestões padrão** para menus & opções: faça com que o bot sugira o item mais provável de uma maneira personalizada como uma primeira etapa, em vez de apresentar um menu ou uma lista de alternativas pessoais.
* As **características do Bot & Tom**: para bots que podem variar o estilo de Tom, detalhamento e escrita, considere a variação dessas características.
* **Notificação & conteúdo do alerta**: decida qual texto deve ser usado para alertas a fim de envolver mais usuários.
* **Tempo de alerta de & de notificação**: tenha aprendizado personalizado de quando enviar notificações aos usuários para que eles se envolvam mais.


## <a name="expectations-required-to-use-personalizer"></a>Expectativas necessárias para usar o personalizador

Você pode aplicar o personalizador em situações em que você atende ou pode implementar as diretrizes a seguir.

|Diretriz|Explicação|
|--|--|
|Meta de negócios|Você tem uma meta de negócios ou de usabilidade para seu aplicativo.|
|Conteúdo|Você tem um lugar em seu aplicativo, em que a tomada de uma decisão contextual do que mostrar aos usuários melhorará essa meta.|
|Quantidade de conteúdo|Você tem menos de 50 ações a serem classificadas por chamada.|
|Agregar dados|A melhor opção pode e deve ser aprendida com o comportamento coletivo dos usuários e a pontuação total das recompensas.|
|Uso ético|O uso do aprendizado de máquina para personalização segue [diretrizes de uso responsável](ethics-responsible-use.md) e as opções escolhidas por você.
|Melhor opção única|A decisão contextual pode ser expressa como a classificação da melhor opção (ação) de um conjunto limitado de opções.|
|Resultado da Pontuação|O quão bem a escolha classificada para seu aplicativo pode ser determinada medindo algum aspecto do comportamento do usuário e expressando-o em uma _[Pontuação de recompensa](concept-rewards.md)_.|
|Tempo relevante|A pontuação de recompensa não traz muitos fatores de confusão ou externos. A duração do teste é curta o suficiente para que a pontuação de recompensa possa ser computada enquanto ela ainda é relevante.|
|Recursos de contexto suficientes|Você pode expressar o contexto para a classificação como uma lista de pelo menos 5 [recursos](concepts-features.md) que você imagina que ajudarão a fazer a escolha certa e que não incluem informações de identificação específicas do usuário.|
|Recursos de ação suficientes|Você tem informações sobre cada opção de conteúdo, _ação_, como uma lista de, pelo menos, 5 [recursos](concepts-features.md) que achar que ajudarão o Personalizador a fazer a escolha certa.|
|Dados diários|Não há eventos suficientes para permanecer acima da personalização ideal se o problema se desloca com o tempo (por exemplo, preferências de notícias ou moda). O Personalizador se adaptará a alterações contínuas no mundo real, mas os resultados não serão ideais se não houver eventos e dados suficientes para aprendizado, a fim de descobrir e estabelecer novos padrões. Você deve escolher um caso de uso que ocorre com uma frequência suficiente. Considere buscar casos de uso que ocorram, pelo menos, 500 vezes por dia.|
|Dados do histórico|Seu aplicativo pode reter dados por tempo suficiente para acumular um histórico de, pelo menos, 100.000 interações. Isso permite que o personalizado colete dados suficientes para executar avaliações offline e otimização de política.|

**Não use o personalizador** onde o comportamento personalizado não é algo que possa ser descoberto em todos os usuários. Por exemplo, usar o Personalizador para sugerir um primeiro pedido de pizza de uma lista de 20 itens de menu possíveis é útil, mas para qual contato ligar da lista de contatos dos usuários para pedir ajuda ao cuidar de crianças (como “Vovó”) não é algo personalizável em sua base de usuários.

## <a name="how-to-use-personalizer-in-a-web-application"></a>Como usar o Personalizador em um aplicativo Web

Adicionar um loop de aprendizado a um aplicativo Web inclui:

* Determine qual experiência personalizar, quais ações e recursos você tem, quais recursos de contexto usar e qual recompensa você definirá.
* Adicionar uma referência ao SDK de Personalização em seu aplicativo.
* Chame a API de Classificação quando estiver pronto para personalizar.
* Armazene a eventId. Envie uma recompensa com a API de Recompensa posteriormente.
1. Chame Ativar para o evento depois de ter certeza de que o usuário viu sua página personalizada.
1. Espere o usuário selecionar o conteúdo classificado.
1. Chame a API de Recompensa para especificar como foi o desempenho da saída da API de Classificação.

## <a name="how-to-use-personalizer-with-a-chat-bot"></a>Como usar o Personalizador com um chatbot

Neste exemplo, você verá como usar a Personalização para criar uma sugestão padrão em vez de enviar ao usuário uma série de menus ou opções de cada vez.

* Obtenha o [código](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/ChatbotExample) deste exemplo.
* Configure sua solução de bot. Publique seu aplicativo LUIS.
* Gerencie as chamadas à API de Classificação e de Recompensa para o bot.
    * Adicione o código para gerenciar o processamento de intenção do LUIS. Se **None** for retornado como a intenção superior ou a pontuação da intenção superior estiver abaixo de seu limite de lógica de negócios, envie a lista de intenções para o Personalizador para classificar as intenções.
    * Mostre a lista de intenções para o usuário como links selecionáveis com a primeira intenção sendo a intenção principal da resposta da API de Classificação.
    * Capturar a seleção do usuário e enviar isso na chamada à API de Recompensa.

### <a name="recommended-bot-patterns"></a>Padrões de bot recomendados

* Faça chamadas à API de Classificação do Personalizador sempre que for necessária uma desambiguação, em vez de armazenar os resultados em cache para cada usuário. O resultado da intenção de desambiguação pode ser alterado com o tempo para uma pessoa e permitir que a API de Classificação explore variações acelerará a aprendizagem geral.
* Escolha uma intenção comum com muitos usuários para que você tenha dados suficientes para personalizar. Por exemplo, perguntas introdutórias podem ser mais adequadas do que esclarecimentos menores no grafo de conversa que apenas alguns usuários podem acessar.
* Use as chamadas à API de Classificação para habilitar conversas do tipo “a primeira sugestão é a certa”, em que o usuário é questionado “Você gostaria de X?” ou “Você quis dizer X?” e o usuário pode apenas confirmar; em vez de dar opções para o usuário em que ele deve escolher em um menu. Por exemplo, User: "gostaria de pedir um" bot "de café:" você gostaria de um Double expresso? ". Dessa forma, o sinal de recompensa também é forte, pois refere-se diretamente a essa sugestão.

## <a name="how-to-use-personalizer-with-a-recommendation-solution"></a>Como usar o Personalizador com uma solução de recomendação

Muitas empresas usam mecanismos de recomendação, ferramentas de marketing e campanhas, segmentação e clustering de público-alvo, filtragem colaborativa e outros meios para recomendar produtos de um extenso catálogo aos clientes.

O [repositório GitHub de recomendados da Microsoft](https://github.com/Microsoft/Recommenders) fornece exemplos e práticas recomendadas para a criação de sistemas de recomendação, fornecidos como notebooks Jupyter. Ele fornece exemplos funcionais de preparação de dados, criação de modelos, avaliação, ajuste e operacionalização de mecanismos de recomendação para muitas abordagens comuns, incluindo xDeepFM, SAR, ALS, RBM e DKN.

O Personalizador pode funcionar com um mecanismo de recomendação quando ele está presente.

* Os mecanismos de recomendação usam grandes quantidades de itens (por exemplo, 500.000) e recomendam um subconjunto (como os 20 principais) de centenas ou milhares de opções.
* O Personalizador usa um pequeno número de ações com muitas informações sobre elas e classifica-as em tempo real para determinado contexto sofisticado, embora a maioria dos mecanismos de recomendação use apenas alguns atributos sobre usuários, produtos e suas interações.
* O Personalizador foi projetado para explorar com autonomia as preferências do usuário o tempo todo, o que produzirá melhores resultados quando o conteúdo for alterado rapidamente, como notícias, eventos ao vivo, conteúdo ao vivo da comunidade, conteúdo com atualizações diárias ou conteúdo sazonal.

Um uso comum é selecionar a saída de um mecanismo de recomendação (por exemplo, os 20 principais produtos para determinado cliente) e usar isso como as ações de entrada para o Personalizador.

## <a name="adding-content-safeguards-to-your-application"></a>Adicionar garantias de conteúdo ao seu aplicativo

Se o aplicativo permitir grandes variações no conteúdo mostrado aos usuários e uma parte desse conteúdo talvez não for segura ou for inadequada para alguns usuários, você deverá planejar com antecedência para verificar se as garantias corretas estão em vigor para evitar que seus usuários vejam conteúdo inaceitável. O melhor padrão para implementar garantias é:
    * Obtenha a lista de ações a ser classificada.
    * Filtre aquelas que não são viáveis para o público-alvo.
    * Classifique apenas essas ações viáveis.
    * Exiba a ação superior para o usuário.

Em algumas arquiteturas, a sequência acima pode ser difícil de ser implementada. Nesse caso, há uma abordagem alternativa para implementar proteções após a classificação, mas um provisionamento precisa ser feito para que as ações que ficarem fora da proteção não sejam usadas para treinar o modelo personalizado.

* Obtenha a lista de ações a ser classificada, com a aprendizagem desativada.
* Classifique as ações.
* Verifique se a ação superior é viável.
    * Se a ação superior for viável, ative a aprendizagem dessa classificação e, em seguida, mostre-a para o usuário.
    * Se a ação superior não for viável, não ative a aprendizagem dessa classificação e decida, por meio de sua própria lógica ou de abordagens alternativas, o que mostrar para o usuário. Mesmo se você usar a segunda melhor opção classificada, não ative a aprendizagem dessa classificação.


## <a name="next-steps"></a>Próximas etapas

[Ética e uso responsável](ethics-responsible-use.md).
