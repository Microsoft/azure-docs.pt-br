---
title: Modo aprendiz – personalizador
description: Saiba como usar o modo aprendiz para obter confiança em um modelo sem alterar nenhum código.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 531917d9c48915f71354b4cd35747ecd9d33a6f8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100385023"
---
# <a name="use-apprentice-mode-to-train-personalizer-without-affecting-your-existing-application"></a>Use o modo aprendiz para treinar o personalizador sem afetar o aplicativo existente

Devido à natureza do **mundo real** do reforço Learning, um modelo personalizado só pode ser treinado em um ambiente de produção. Ao implantar um novo caso de uso, o modelo personalizador não é executado com eficiência porque leva tempo para que o modelo seja suficientemente treinado.  O **modo aprendiz** é um comportamento de aprendizado que facilita essa situação e permite que você tenha confiança no modelo – sem que o desenvolvedor Altere qualquer código.

[!INCLUDE [Important Blue Box - Apprentice mode pricing tier](./includes/important-apprentice-mode.md)]

## <a name="what-is-apprentice-mode"></a>O que é o modo aprendiz?

Semelhante a como um aprendiz aprende de um mestre, e com a experiência pode ser melhor; O modo aprendiz é um _comportamento_ que permite ao personalizador aprender observando os resultados obtidos da lógica do aplicativo existente.

Os treinamentos do personalizador imitando a mesma saída que o aplicativo. À medida que mais fluxos de eventos, o personalizador pode _acompanhar_ o aplicativo existente sem afetar a lógica e os resultados existentes. As métricas, disponíveis no portal do Azure e na API, ajudam você a entender o desempenho conforme o modelo aprende.

Depois que o personalizador tiver aprendido e obtido um certo nível de compreensão, o desenvolvedor poderá alterar o comportamento do modo aprendiz para o modo online. Nesse momento, o personalizador começa a influenciar as ações na API de classificação.

## <a name="purpose-of-apprentice-mode"></a>Finalidade do modo de aprendiz

O modo aprendiz fornece confiança no serviço personalizador e em seus recursos de aprendizado de máquina e fornece a garantia de que o serviço recebe informações que podem ser aprendidas, sem risco de tráfego online.

Os dois principais motivos para usar o modo aprendiz são:

* A mitigação de **frio é iniciada**: o modo aprendiz ajuda a gerenciar e avaliar o custo de um "novo" tempo de aprendizagem do modelo – quando não está retornando a melhor ação e não obteve um nível satisfatório de eficiência de cerca de 60-80%.
* **Validação de recursos de ação e contexto**: recursos enviados em ações e contexto podem ser inadequados ou imprecisos-muito pouco, muito, incorretos ou muito específicos para treinar o personalizador para obter a taxa de eficácia ideal. Use [avaliações de recursos](concept-feature-evaluation.md) para localizar e corrigir problemas com recursos.

## <a name="when-should-you-use-apprentice-mode"></a>Quando você deve usar o modo aprendiz?

Use o modo aprendiz para treinar o personalizador a fim de melhorar sua eficácia nos cenários a seguir, deixando a experiência dos usuários inalterada pelo personalizador:

* Você está implementando o personalizador em um novo caso de uso.
* Você alterou significativamente os recursos que envia em contexto ou ações.
* Você alterou significativamente quando e como calcula recompensas.

O modo aprendiz não é uma maneira eficaz de medir o impacto que o personalizador está tendo em pontuações de recompensa. Para medir a eficácia com que o personalizador está escolhendo a melhor ação possível para cada chamada de classificação, use [avaliações offline](concepts-offline-evaluation.md).

## <a name="who-should-use-apprentice-mode"></a>Quem deve usar o modo aprendiz?

O modo aprendiz é útil para desenvolvedores, cientistas de dados e tomadores de decisões de negócios:

* **Os desenvolvedores** podem usar o modo aprendiz para garantir que as APIs de classificação e recompensa estejam sendo usadas corretamente no aplicativo, e que os recursos enviados ao personalizado do aplicativo não contenham bugs ou recursos não relevantes, como um carimbo de data/hora ou um elemento userid.

* Os **cientistas de dados** podem usar o modo aprendiz para validar que os recursos são eficazes para treinar os modelos de personalização, que os tempos de espera de recompensa não são muito longos ou curtos.

* Os **tomadores de decisões de negócios** podem usar o modo aprendiz para avaliar o potencial do personalizador para melhorar os resultados (ou seja, recompensas) em comparação com a lógica de negócios existente. Isso permite que eles tomem uma decisão informada de afetar a experiência do usuário, onde a receita real e a satisfação do usuário estão em jogo.

## <a name="comparing-behaviors---apprentice-mode-and-online-mode"></a>Comparando comportamentos – modo aprendiz e modo online

Aprender quando no modo aprendiz difere do modo online das seguintes maneiras.

|Área|Modo Aprendiz|Modoonline|
|--|--|--|
|Impacto na experiência do usuário|Você pode usar o comportamento do usuário existente para treinar o personalizado ao deixá-lo observar (não afetar) o que a **ação padrão** teria e a recompensa obtida. Isso significa que a experiência dos usuários e os resultados de negócios deles não serão afetados.|Exibir a ação superior retornada da chamada de classificação para afetar o comportamento do usuário.|
|Velocidade de aprendizagem|O personalizador irá aprender mais lentamente quando estiver no modo de aprendiz do que ao aprender no modo online. O modo aprendiz só pode aprender observando as recompensas obtidas por sua **ação padrão**, o que limita a velocidade de aprendizado, pois nenhuma exploração pode ser executada.|Aprende mais rápido porque pode explorar o modelo atual e explorar novas tendências.|
|"Teto" de eficiência de aprendizagem|O personalizador pode aproximar, raramente correspondência e nunca exceder o desempenho de sua lógica de negócios base (o total de recompensa alcançado pela **ação padrão** de cada chamada de classificação). Esse teto de aproximação é reduzido pela exploração. Por exemplo, com a exploração de 20%, é muito improvável que o desempenho do modo aprendiz exceda 80%, e 60% é um destino razoável no qual se formou no modo online.|O personalizador deve exceder a linha de base dos aplicativos e, ao longo do tempo, você deve realizar a avaliação offline e a avaliação dos recursos para continuar a obter melhorias no modelo. |
|Valor da API de classificação para rewardActionId|A experiência dos usuários não é afetada, pois _rewardActionId_ é sempre a primeira ação que você envia na solicitação de classificação. Em outras palavras, a API de classificação não faz nada visível para seu aplicativo durante o modo aprendiz. As APIs de recompensa em seu aplicativo não devem alterar como ele usa a API de recompensa entre um modo e outro.|A experiência dos usuários será alterada pelo _rewardActionId_ que o personalizador escolhe para seu aplicativo. |
|Avaliações|O personalizador mantém uma comparação dos totais de recompensa que sua lógica de negócios padrão está obtendo, e o personalizador de recompensas será a obtenção se estiver no modo online nesse ponto. Uma comparação está disponível no portal do Azure para esse recurso|Avalie a eficácia do personalizado executando [avaliações offline](concepts-offline-evaluation.md), o que permite comparar o personalizado de recompensas total obtido com as recompensas em potencial da linha de base do aplicativo.|

Uma observação sobre a eficácia do modo aprendiz:

* A eficácia do personalizador no modo aprendiz raramente atingirá quase 100% da linha de base do aplicativo; e nunca exceda.
* As práticas recomendadas não tentarão chegar a 100% de realização; Mas um intervalo de 60% – 80% deve ser direcionado dependendo do caso de uso.

## <a name="using-apprentice-mode-to-train-with-historical-data"></a>Usando o modo aprendiz para treinar com dados históricos

Se você tiver uma quantidade significativa de dados históricos, gostaria de usar o para treinar o personalizador, você pode usar o modo aprendiz para reproduzir os dados por meio do personalizador.

Configure o personalizador no modo aprendiz e crie um script que chame Rank com as ações e os recursos de contexto dos dados históricos. Chame a API de recompensa com base em seus cálculos dos registros nesses dados. Você precisará de aproximadamente 50.000 eventos históricos para ver alguns resultados, mas 500.000 é recomendado para maior confiança nos resultados.

Ao treinar dados históricos, é recomendável que os dados enviados no (recursos para contexto e ações, seu layout no JSON usado para solicitações de classificação e o cálculo de recompensa nesse conjunto de dados de treinamento), corresponda aos dados (recursos e cálculo de recompensa) disponíveis no aplicativo existente.

Os dados offline e de postagem real tendem a ser mais incompletos e noisiers e diferem no formato. Embora o treinamento de dados históricos seja possível, os resultados dessa tarefa podem ser inconclusivos e não um bom indicador de como o personalizador aprenderá, especialmente se os recursos variam entre os dados anteriores e o aplicativo existente.

Normalmente, para o personalizador, quando comparado ao treinamento com dados históricos, alterar o comportamento para o modo aprendiz e aprender com um aplicativo existente é um caminho mais eficaz para ter um modelo eficaz, com menos trabalho, engenharia de dados e trabalhos de limpeza.

## <a name="using-apprentice-mode-versus-ab-tests"></a>Usando o modo aprendiz versus testes A/B

Só é útil fazer testes A/B de tratamentos do personalizador depois que ele tiver sido validado e estiver aprendendo no modo online. No modo aprendiz, somente a **ação padrão** é usada, o que significa que todos os usuários veriam efetivamente a experiência de controle.

Mesmo que o personalizador seja apenas o _tratamento_, o mesmo desafio estará presente quando a validação dos dados for boa para o personalizador de treinamento. O modo aprendiz poderia ser usado, com 100% do tráfego, e com todos os usuários obtendo a experiência de controle (não afetado).

Quando você tem um caso de uso usando o personalizador e o learning online, experimentos A/B permitem fazer coortes e comparação científica de resultados que podem ser mais complexos do que os sinais usados para recompensas. Uma pergunta de exemplo que um teste A/B pode responder é: `In a retail website, Personalizer optimizes a layout and gets more users to _check out_ earlier, but does this reduce total revenue per transaction?`

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [eventos ativos e inativos](concept-active-inactive-events.md)
