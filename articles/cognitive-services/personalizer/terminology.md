---
title: Terminologia – personalizador
description: O personalizador usa terminologia do reforço Learning. Esses termos são usados no portal do Azure e nas APIs.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: cd0d85be5447aad0f2a3c37041e7d5d5d047a468
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91777285"
---
# <a name="personalizer-terminology"></a>Terminologia do personalizador

O personalizador usa terminologia do reforço Learning. Esses termos são usados no portal do Azure e nas APIs.

## <a name="conceptual-terminology"></a>Terminologia conceitual

* **Loop de aprendizagem**: você cria um recurso personalizado, chamado de _loop de aprendizagem_, para cada parte do aplicativo que pode se beneficiar da personalização. Caso você tenha mais de uma experiência para personalização, crie um loop para cada uma.

* **Modelo**: um modelo personalizado captura todos os dados aprendidos sobre o comportamento do usuário, obtendo dados de treinamento da combinação dos argumentos que você envia para classificar e recompensar chamadas e com um comportamento de treinamento determinado pela política de aprendizado.

* **Modo online**: o [comportamento de aprendizado](#learning-behavior) padrão para personalizador em que seu loop de aprendizagem usa o aprendizado de máquina para criar o modelo que prevê a **ação principal** para seu conteúdo.

* **Modo aprendiz**: um [comportamento de aprendizado](#learning-behavior) que ajuda a iniciar um modelo personalizado para treinar sem afetar os resultados e as ações dos aplicativos.

## <a name="learning-behavior"></a>Comportamento de aprendizagem:

* **Modo online**: retorna a melhor ação. Seu modelo responderá a chamadas de classificação com a melhor ação e usará as chamadas de recompensa para aprender e melhorar suas seleções ao longo do tempo.
* **[Modo de aprendiz](concept-apprentice-mode.md)**: saiba como um aprendiz. Seu modelo aprenderá observando o comportamento do seu sistema existente. Chamadas de classificação sempre retornarão a **ação padrão** do aplicativo (linha de base).

## <a name="personalizer-configuration"></a>Configuração do personalizador

O personalizador é configurado no [portal do Azure](https://portal.azure.com).

* **Recompensas**: Configure os valores padrão para recompensar o tempo de espera, recompensa padrão e recompensar política de agregação.

* **Exploração**: configurar a porcentagem de chamadas de classificação a serem usadas para exploração

* **Frequência de atualização do modelo**: com que frequência o modelo é retreinado.

* **Retenção de dados**: a quantidade de dias de dados a serem armazenados. Isso pode afetar as avaliações offline, que são usadas para melhorar seu loop de aprendizado.

## <a name="use-rank-and-reward-apis"></a>Usar as APIs de classificação e recompensa

* **Classificação**: dadas as ações com recursos e os recursos de contexto, use explorar ou explorar para retornar a ação principal (item de conteúdo).

    * **Ações**: as ações são itens de conteúdo, como produtos ou promoções, para escolher. O personalizador escolhe a ação superior (retornada ID da ação de recompensa) para mostrar aos usuários por meio da API de classificação.

    * **Contexto**: para fornecer uma classificação mais precisa, forneça informações sobre seu contexto, por exemplo:
        * Seu usuário.
        * O dispositivo que ele está usando.
        * A hora atual.
        * Outros dados sobre a situação atual.
        * Dados históricos sobre o usuário ou o contexto.

        Seu aplicativo específico pode ter informações de contexto diferentes.

    * **[Recursos](concepts-features.md)**: uma unidade de informações sobre um item de conteúdo ou um contexto de usuário. Certifique-se de usar apenas os recursos agregados. Não use horários específicos, IDs de usuário ou outros dados não agregados como recursos.

        * Um _recurso de ação_ são metadados sobre o conteúdo.
        * Um _recurso de contexto_ são metadados sobre o contexto no qual o conteúdo é apresentado.

* **Exploração**: o serviço personalizado está explorando quando, em vez de retornar a melhor ação, ele escolhe uma ação diferente para o usuário. O serviço Personalizador evita desvios e estagnação e pode se adaptar ao comportamento contínuo do usuário com a exploração.

* **Exploração**: o serviço personalizador usa o modelo atual para decidir a melhor ação com base nos dados anteriores.

* **Duração do experimento**: a quantidade de tempo que o serviço personalizado aguarda para um prêmio, começando do momento em que a chamada de classificação ocorreu para esse evento.

* **Eventos inativos**: um evento inativo é aquele em que você chamou Rank, mas não tem certeza de que o usuário verá o resultado, devido às decisões do aplicativo cliente. Os eventos inativos permitem criar e armazenar resultados de personalização e decidir como descartá-los posteriormente sem afetar o modelo de machine learning.


* **Recompensa**: uma medida de como o usuário respondeu à ID da ação de recompensa retornada da API de classificação, como uma pontuação entre 0 e 1. O valor de 0 a 1 é definido pela lógica de negócios, com base em como a opção ajudou a atingir as metas de negócios de personalização. O loop de aprendizagem não armazena esse prêmio como um histórico de usuário individual.

## <a name="evaluations"></a>Avaliações

### <a name="offline-evaluations"></a>Avaliações offline

* **Avaliação**: uma avaliação offline determina a melhor política de aprendizado para seu loop com base nos dados do seu aplicativo.

* **Política de aprendizagem**: como o personalizador treina um modelo em cada evento será determinado por alguns parâmetros que afetam o funcionamento do algoritmo de aprendizado de máquina. Um novo loop de aprendizado começa com uma **política de aprendizado** padrão, que pode produzir um desempenho moderado. Ao executar [avaliações](concepts-offline-evaluation.md), o personalizador cria novas políticas de aprendizado especificamente otimizadas para os casos de uso do loop. O personalizador terá um desempenho significativamente melhor com políticas otimizadas para cada loop específico, gerado durante a avaliação. A política de aprendizado é denominada _configurações de aprendizado_ nas **configurações de modelo e aprendizado** para o recurso personalizado no portal do Azure.

### <a name="apprentice-mode-evaluations"></a>Avaliações do modo aprendiz

O modo aprendiz fornece as seguintes **métricas de avaliação**:
* **Linha de base – recompensa média**: recompensas médias do padrão do aplicativo (linha de base).
* **Personalizador – recompensa média**: a média do personalizador de recompensas pode ter atingido potencialmente.
* **Recompensa média contínua**: taxa de linha de base e recompensa de personalizador – normalizado sobre os eventos 1000 mais recentes.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [ética e uso responsável](ethics-responsible-use.md)