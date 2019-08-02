---
title: Como funciona o Personalizador – Personalizador
titleSuffix: Azure Cognitive Services
description: O Personalizador usa o aprendizado de máquina para descobrir qual ação deve ser usada em um contexto. Cada loop de aprendizado tem um modelo que é treinado exclusivamente nos dados que você enviou para ele por meio de chamadas de Classificação e Recompensa. Cada loop de aprendizado é completamente independente entre si.
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: diberry
ms.openlocfilehash: e55ccb508760c4473f71245c183948219f31985c
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663905"
---
# <a name="how-personalizer-works"></a>Como funciona o Personalizador

O Personalizador usa o aprendizado de máquina para descobrir qual ação deve ser usada em um contexto. Cada loop de aprendizado tem um modelo que é treinado exclusivamente nos dados que você enviou para ele por meio de chamadas de **Classificação** e **Recompensa**. Cada loop de aprendizado é completamente independente entre si. Crie um loop de aprendizado para cada parte ou comportamento do aplicativo que você deseja personalizar.

Para cada loop, **chame a API de Classificação**, baseado no contexto atual, com:

* Lista de possíveis ações: itens de conteúdo dos quais a ação principal será selecionada.
* Lista de [recursos de contexto](concepts-features.md): dados contextualmente relevantes, como usuário, conteúdo e contexto.

A API de **Classificação** decide usar:

* _Explorar_: O modelo atual para decidir a melhor ação com base em dados passados.
* _Examinar_: Selecione uma ação diferente em vez da ação principal.

A API de **Recompensa**:

* Coleta dados para treinar o modelo gravando os recursos e as pontuações de recompensa de cada chamada de classificação.
* Usa esses dados para atualizar o modelo com base nas configurações especificadas na _Política de Aprendizado_.

## <a name="architecture"></a>Arquitetura

A seguinte imagem mostra o fluxo de arquitetura das chamadas de Classificação e Recompensa:

![texto Alt](./media/how-personalizer-works/personalization-how-it-works.png "Como funciona a personalização")

1. O Personalizador usa um modelo interno de IA para determinar a classificação da ação.
1. O serviço decide se o modelo atual será explorado ou se novas opções para o modelo serão examinadas.  
1. O resultado de classificação é enviado ao EventHub.
1. Quando Personalizador receber a recompensa, a recompensa será enviada ao EventHub. 
1. A classificação e a recompensa são correlacionadas.
1. O modelo de IA é atualizado de acordo com os resultados de correlação.
1. O mecanismo de inferência é atualizado com o novo modelo. 

## <a name="research-behind-personalizer"></a>A pesquisa por trás do Personalizador

O Personalizador se baseia em ciência e pesquisa de ponta na área de [Aprendizado de Reforço](concepts-reinforcement-learning.md), incluindo artigos, atividades de pesquisa e áreas contínuas de exploração do Microsoft Research.

## <a name="terminology"></a>Terminologia

* **Loop de aprendizado**: É possível criar um loop de aprendizado para todas as partes de seu aplicativo que possam se beneficiar da personalização. Caso você tenha mais de uma experiência para personalização, crie um loop para cada uma. 

* **Ações**: Ações são os itens de conteúdo, como produtos ou promoções, à sua escolha. O Personalizador escolhe a ação principal a ser mostradas aos usuários, conhecida como a _ação de Recompensa_, por meio da API de Classificação. Cada ação pode enviar recursos com a solicitação de Classificação.

* **Context**: Para fornecer uma classificação mais precisa, forneça informações sobre seu contexto, por exemplo:
    * Seu usuário.
    * O dispositivo que ele está usando. 
    * A hora atual.
    * Outros dados sobre a situação atual.
    * Dados históricos sobre o usuário ou o contexto.

    Seu aplicativo específico pode ter informações de contexto diferentes. 

* **[Recursos](concepts-features.md)** : Uma unidade de informação sobre um item de conteúdo ou um contexto de usuário.

* **Recompensa**: Uma medida de como o usuário respondeu à ação retornada pela API de Classificação, como uma pontuação entre 0 e 1. O valor de 0 a 1 é definido pela lógica de negócios, com base em como a opção ajudou a atingir as metas de negócios de personalização. 

* **Exploração**: O serviço Personalizador faz uma exploração quando, em vez de retornar a melhor ação, escolhe uma ação diferente para o usuário. O serviço Personalizador evita desvios e estagnação e pode se adaptar ao comportamento contínuo do usuário com a exploração. 

* **Duração do Teste**: O tempo que o serviço Personalizador aguarda uma recompensa, a partir do momento em que ocorreu a chamada de Classificação para o evento.

* **Eventos Inativos**: Um evento inativo é aquele em que você chamou a Classificação, mas não tem certeza se o usuário verá o resultado, devido às decisões do aplicativo cliente. Os eventos inativos permitem criar e armazenar resultados de personalização e decidir como descartá-los posteriormente sem afetar o modelo de machine learning.

* **Modelo**: Um modelo do Personalizador captura todos os dados aprendidos sobre o comportamento do usuário, obtendo dados de treinamento da combinação dos argumentos enviados para as chamadas de Classificação e Recompensa e com um comportamento de treinamento determinado pela Política de Aprendizado. 

* **Política de aprendizagem**: O modo como o personalizado treinará um modelo em cada evento será determinado por alguns metaparâmetros que afetam o funcionamento dos algoritmos de aprendizado de máquina. Novos loops do personalizador começarão com uma política de aprendizado padrão, que pode produzir um desempenho moderado. Ao executar [avaliações](concepts-offline-evaluation.md), o personalizador pode criar novas políticas de aprendizado especificamente otimizadas para os casos de uso do loop. O personalizador terá um desempenho significativamente melhor com políticas otimizadas para cada loop específico, gerado durante a avaliação.

## <a name="example-use-cases-for-personalizer"></a>Casos de uso de exemplo do Personalizador

* Esclarecimento de intenção e desambiguidade: ajude seus usuários a terem uma melhor experiência quando sua intenção não estiver clara fornecendo uma opção personalizada a cada um deles.
* Sugestões padrão para menus e opções: faça o bot sugerir o item mais provável de maneira personalizada como uma primeira etapa, em vez de apresentar um menu impessoal ou uma lista de alternativas.
* Características de bot e tom: para bots que podem variar o tom, o nível de detalhes e o estilo de escrita, considere variar essas características de maneiras personalizadas.
* Conteúdo de notificação e alerta: decida que texto usar para alertas a fim de envolver mais os usuários.
* Tempo de alerta e notificação: tenha um aprendizado personalizado de quando enviar notificações para os usuários para envolvê-los mais.

## <a name="checklist-for-applying-personalizer"></a>Lista de verificação para aplicar o Personalizador

Você pode aplicar o Personalizador em situações em que:

* Você tem uma meta de negócios ou de usabilidade para seu aplicativo.
* Você tem um lugar em seu aplicativo, em que a tomada de uma decisão contextual do que mostrar aos usuários melhorará essa meta.
* A melhor opção pode e deve ser aprendida com o comportamento coletivo dos usuários e a pontuação total das recompensas.
* O uso do aprendizado de máquina para personalização segue as [diretrizes de uso responsável](ethics-responsible-use.md) e as opções para sua equipe.
* A decisão pode ser expressa como a classificação da melhor opção ([ação](concepts-features.md#actions-represent-a-list-of-options)) de um conjunto limitado de opções.
* Saber se essa opção funcionou bem pode ser calculado pela lógica de negócios, medindo algum aspecto do comportamento do usuário e expressando-o em um número entre -1 e 1.
* A pontuação de recompensa não traz muitos fatores de confusão ou externos, principalmente se a duração do experimento é curta o suficiente para que a pontuação de recompensa possa ser computada enquanto ainda é relevante.
* É possível expressar o contexto da classificação como um dicionário de pelo menos cinco recursos que você acha que ajudariam a fazer a escolha certa e isso não inclui informações de identificação pessoal.
* Você tem informações sobre cada ação como um dicionário de pelo menos cinco atributos ou recursos que você acha que ajudarão o Personalizador a fazer a escolha certa.
* É possível reter dados por tempo suficiente para acumular um histórico de pelo menos 100 mil interações.

## <a name="machine-learning-considerations-for-applying-personalizer"></a>Considerações sobre o aprendizado de máquina para aplicação do Personalizador

O Personalizador é baseado no aprendizado de reforço, uma abordagem do aprendizado de máquina ensinada pelos comentários que você fornece a ele. 

O Personalizador aprenderá melhor em situações em que:
* Não há eventos suficientes para permanecer acima da personalização ideal se o problema se desloca com o tempo (por exemplo, preferências de notícias ou moda). O Personalizador se adaptará a alterações contínuas no mundo real, mas os resultados não serão ideais se não houver eventos e dados suficientes para aprendizado, a fim de descobrir e estabelecer novos padrões. Você deve escolher um caso de uso que ocorre com uma frequência suficiente. Considere buscar casos de uso que ocorram, pelo menos, 500 vezes por dia.
* O contexto e as ações têm recursos suficientes para facilitar o aprendizado.
* Há menos de 50 ações a serem classificadas por chamada.
* Suas configurações de retenção de dados permitem ao Personalizador coletar dados suficientes para realizar avaliações offline e a otimização de política. Normalmente, isso representa, pelo menos, 50.000 pontos de dados.

## <a name="how-to-use-personalizer-in-a-web-application"></a>Como usar o Personalizador em um aplicativo Web

Adicionar um loop a um aplicativo Web inclui:

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
* Use as chamadas à API de Classificação para habilitar conversas do tipo “a primeira sugestão é a certa”, em que o usuário é questionado “Você gostaria de X?” ou “Você quis dizer X?” e o usuário pode apenas confirmar; em vez de dar opções para o usuário em que ele deve escolher em um menu. Por exemplo, Usuário: “gostaria de pedir um café” Bot: “você gostaria de um espresso duplo?”. Dessa forma, o sinal de recompensa também é forte, pois refere-se diretamente a essa sugestão.

## <a name="how-to-use-personalizer-with-a-recommendation-solution"></a>Como usar o Personalizador com uma solução de recomendação

Use o mecanismo de recomendação para filtrar um catálogo grande para alguns itens que podem ser apresentados como 30 ações possíveis enviadas à API de Classificação.

É possível usar mecanismos de recomendação com o Personalizador:

* Configure a [solução de recomendação](https://github.com/Microsoft/Recommenders/). 
* Ao exibir uma página, invoque o Modelo de Recomendação para obter uma pequena lista de recomendações.
* Chame a Personalização para classificar a saída da solução de recomendação.
* Envie comentários sobre a ação do usuário com a chamada à API de Recompensa.


## <a name="pitfalls-to-avoid"></a>Armadilhas a serem evitadas

* Não use o Personalizador quando o comportamento personalizado não for algo que pode ser descoberto entre todos os usuários, mas algo que deve ser lembrado para usuários específicos ou proveniente de uma lista de alternativas específica do usuário. Por exemplo, usar o Personalizador para sugerir um primeiro pedido de pizza de uma lista de 20 itens de menu possíveis é útil, mas para qual contato ligar da lista de contatos dos usuários para pedir ajuda ao cuidar de crianças (como “Vovó”) não é algo personalizável em sua base de usuários.


## <a name="adding-content-safeguards-to-your-application"></a>Adicionar garantias de conteúdo ao seu aplicativo

Se o aplicativo permitir grandes variações no conteúdo mostrado aos usuários e uma parte desse conteúdo talvez não for segura ou for inadequada para alguns usuários, você deverá planejar com antecedência para verificar se as garantias corretas estão em vigor para evitar que seus usuários vejam conteúdo inaceitável. O melhor padrão para implementar garantias é: O melhor padrão para implementar garantias é:
    * Obtenha a lista de ações a ser classificada.
    * Filtre aquelas que não são viáveis para o público-alvo.
    * Classifique apenas essas ações viáveis.
    * Exiba a ação superior para o usuário.

Em algumas arquiteturas, a sequência acima pode ser difícil de ser implementada. Nesse caso, há uma abordagem alternativa à implementação de garantias após a classificação, mas deve ser realizado um provisionamento para que as ações que não se enquadrem na garantia não sejam usadas para treinar o modelo do Personalizador.

* Obtenha a lista de ações a ser classificada, com a aprendizagem desativada.
* Classifique as ações.
* Verifique se a ação superior é viável.
    * Se a ação superior for viável, ative a aprendizagem dessa classificação e, em seguida, mostre-a para o usuário.
    * Se a ação superior não for viável, não ative a aprendizagem dessa classificação e decida, por meio de sua própria lógica ou de abordagens alternativas, o que mostrar para o usuário. Mesmo se você usar a segunda melhor opção classificada, não ative a aprendizagem dessa classificação.

## <a name="verifying-adequate-effectiveness-of-personalizer"></a>Verificar a eficácia adequada do Personalizador

É possível monitorar a eficácia do Personalizador periodicamente realizando [avaliações offline](how-to-offline-evaluation.md)

## <a name="next-steps"></a>Próximas etapas

Entenda [as situações em que você pode usar o Personalizador](where-can-you-use-personalizer.md).
Executar [avaliações offline](how-to-offline-evaluation.md)
