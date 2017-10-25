---
title: "Solução pré-configurada de manutenção preditiva | Microsoft Docs"
description: "Uma descrição da solução pré-configurada de manutenção preditiva do Azure IoT Suite."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: b370b3d7-2ce5-4906-9818-3aeedd471ee3
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: 8bad198488c4940a83eb32ec02122a91d47ca86c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="predictive-maintenance-preconfigured-solution-overview"></a>Visão geral da solução pré-configurada de manutenção preditiva

A [solução pré-configurada][lnk_preconfigured_solutions] de *manutenção preditiva* é uma das soluções pré-configuradas lançadas como parte do [Microsoft Azure IoT Suite][lnk_iot_suite]. Essa solução integra a coleta de telemetria do dispositivo em tempo real com um modelo preditivo criado com o [Azure Machine Learning][lnk-machine-learning].

Com o Azure IoT Suite, você pode se conectar e monitorar ativos de forma rápida e fácil, além de analisar a telemetria em tempo real em painéis e visualizações. Na solução de manutenção preditiva, os paineis e visualizações fornecem uma nova inteligência que pode promover ganhos de eficiência e aumentar os fluxos de receita.

## <a name="the-scenario"></a>O cenário

A Fabrikam é uma companhia aérea regional que se dedica a fornecer uma excelente experiência ao cliente a preços competitivos. Uma das causas de atrasos de voos são problemas de manutenção e a manutenção dos motores de aeronave representa um desafio singular. A Fabrikam precisa evitar a falha do motor durante o voo a todo custo e, portanto, ela inspeciona seus motores regularmente e agenda a manutenção de acordo com um plano. No entanto, os motores de aeronave nem sempre se desgastam da mesma forma. Algum tipo de manutenção desnecessária é realizada nos motores. O mais importante é que problemas ocorrem, o que pode fazer com que uma aeronave permaneça em solo até que a manutenção seja realizada. Uma aeronave estar em um local onde os técnicos certos ou as peças de reposição certas não estão disponíveis pode custar muito caro.

Os motores de aeronave da Fabrikam são instrumentados com sensores que monitoram as condições do motor durante o voo. A Fabrikam usa a solução de manutenção preditiva para coletar os dados de sensor coletados durante o voo. Após vários anos acumulando dados operacionais e de falha do motor, os cientistas de dados da Fabrikam modelaram uma maneira para prever a RUL (Vida Útil Restante) de um motor de aeronave. O modelo usa uma correlação entre os dados de quatro sensores do motor e o desgaste do motor que leva a uma eventual falha. Embora a Fabrikam continue realizando inspeções regulares para garantir a segurança, ela agora pode usar os modelos para calcular a RUL para cada motor após cada voo. O modelo usa a telemetria coletada de mecanismos de durante o voo. A Fabrikam agora pode prever os futuros pontos de falha e planejar a manutenção e reparar antecipadamente.

> [!NOTE]
> O modelo de solução usa dados reais de desgaste do mecanismo.

Ao prever o momento em que a manutenção é necessária, a Fabrikam pode otimizar suas operações para reduzir os custos.

Coordenadores de manutenção trabalham com os agendadores para:

- Planeje a manutenção para coincidir com uma aeronave parar em um local específico.
- Faça com que haja tempo suficiente para a aeronave ficar fora de serviço sem causar interrupções de agendamento.
- Para agendar técnicos para garantir que aeronave é atendidas com eficiência sem tempo de espera.

Os gerentes de controle de inventário recebem planos de manutenção, para que possam otimizar seu processo de encomendas e inventário de peças de reposição.

Essas atividades permitem que a Fabrikam minimize o tempo de solo de aeronave e reduza os custos operacionais, ao mesmo tempo que garante a segurança dos passageiros e da tripulação.

Para entender como o [Azure IoT Suite][lnk_iot_suite] fornece recursos que os clientes precisam para aproveitar o potencial da manutenção preditiva, examine este [infográfico][lnk_infographic].

## <a name="how-the-predictive-maintenance-solution-is-built"></a>Como a solução de manutenção preditiva é criada

A solução usa um modelo do Azure Machine Learning existente como modelo para mostrar esses recursos trabalhando desde a telemetria de dispositivo coletada até os serviços IoT Suite. A Microsoft criou um [modelo de regressão][lnk_regression_model] de um mecanismo de aeronave baseado em dados<sup>\[1\]</sup> disponíveis ao público e diretrizes passo a passo sobre como usar o modelo.

A solução de manutenção preditiva de IoT do Azure usa o modelo de regressão criado com base neste modelo. O modelo é implantado em sua assinatura do Azure e exposto por meio de uma API gerada automaticamente. A solução inclui um subconjunto dos dados de teste que representa 4 (do total de 100) 4 (de total de 21) e de mecanismos de fluxos de dados de sensor. Esses dados são suficientes para fornecer um resultado preciso do modelo treinado.

*\[1\] A. Saxena e K. Goebel (2008). “Turbofan Engine Degradation Simulation Data Set” (Conjunto de dados da simulação de degradação do turbofan), Repositório de dados de prognóstico da NASA Ames (http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/), NASA Ames Research Center, Moffett Field, CA*

## <a name="get-started-with-predictive-maintenance"></a>Introdução à manutenção de previsão

Este tutorial mostra como provisionar a solução de previsão de manutenção. Ele também explica os recursos básicos da solução de previsão de manutenção. Você pode acessar muitos desses recursos por meio do painel de solução que é implantado junto com a solução pré-configurada.

Para concluir este tutorial, você precisa de uma assinatura ativa do Azure.

> [!NOTE]
> Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][lnk_free_trial].

1. Faça logon em [azureiotsuite.com][lnk-azureiotsuite] usando as credenciais de sua conta do Azure e clique em **+** para criar uma nova solução.
1. Clique em **selecione** o **manutenção previsão** lado a lado.
1. Insira um **nome da solução** sua manutenção previsão pré-configurados de solução.
1. Selecione a **Região** e a **Assinatura** que você deseja usar para provisionar a solução.
1. Clique em **Criar Solução** para iniciar o processo de provisionamento. Este processo normalmente leva vários minutos para ser executado.

### <a name="wait-for-the-provisioning-process-to-complete"></a>Aguarde o processo de provisionamento ser concluído

1. Clique no bloco da sua solução com o status **Provisionamento** .
1. Observe os **estados de Provisionamento** à medida que os serviços do Azure são implantados em sua assinatura do Azure.
1. Após o provisionamento ser concluído, o status será alterado para **Pronto**.
1. Clique no bloco para ver os detalhes da solução no painel à direita. Nesse painel, você pode iniciar o painel de solução e acessar o espaço de trabalho do Machine Learning.

> [!NOTE]
> Se estiver tendo problemas para implantar a solução pré-configurada, dê uma olhada em [Permissões no site azureiotsuite.com][lnk-permissions] e nas [Perguntas frequentes][lnk-faq]. Se os problemas persistirem, crie um tíquete de serviço no [portal][lnk-portal].

Há detalhes que você esperaria ver e que não estão listados para sua solução? Envie sugestões de recursos no [User Voice](https://feedback.azure.com/forums/321918-azure-iot).

## <a name="view-the-solution"></a>Exibir a solução

Esta seção o orienta a solução da interface do usuário.

### <a name="predictive-maintenance-dashboard"></a>Painel Manutenção Preditiva

Esta página no aplicativo Web usa controles JavaScript do PowerBI (confira o [repositório de elementos visuais do PowerBI][lnk-powerbi]) para visualizar:

* Os dados de saída de trabalhos do Stream Analytics no armazenamento de blobs.
* O RUL e a contagem de ciclo por motor da aeronave.

### <a name="observing-the-behavior-of-the-cloud-solution"></a>Observando o comportamento da solução de nuvem

No portal do Azure, navegue até o grupo de recursos com o nome da solução escolhido para exibir os recursos provisionados.

![][img-resource-group]

Quando você provisiona a solução pré-configurada, recebe um email com um link para o espaço de trabalho do Machine Learning. Você também pode navegar até o Espaço de Trabalho do Machine Learning na página [azureiotsuite.com][lnk-azureiotsuite] para sua solução provisionada. Um bloco fica disponível nessa página quando a solução entra no estado **pronto**.

![][img-machine-learning]

No portal da solução, você pode ver que o exemplo é provisionado com quatro dispositivos simulados para representar duas aeronaves com dois motores por aeronave, cada um com quatro sensores. Quando você navega pela primeira vez até o portal de solução, a simulação é interrompida.

![][img-simulation-stopped]

Clique em **Iniciar simulação** para iniciar a simulação. O histórico de sensor, RUL, ciclos e histórico de RUL preenchem o painel.

![][img-simulation-running]

Quando a RUL for menor que 160 (um limite aleatório escolhido para fins de demonstração), o portal da solução exibirá um símbolo de aviso ao lado da exibição da RUL. O portal da solução também destaca o motor de aeronave em amarelo. Observe que os valores da RUL têm uma tendência descendente geral, mas tendem a subir e a descer. Este comportamento é resultado de durações variáveis do ciclo e da precisão do modelo.

![][img-simulation-warning]

A simulação completa leva cerca de 35 minutos para concluir 148 ciclos. O limite de 160 do RUL é atingido pela primeira vez aproximadamente aos cinco minutos, e os dois motores atingem o limite aproximadamente aos oito minutos.

A simulação percorre todo o conjunto de dados dos 148 ciclos e estabelece nos valores finais de RUL e de ciclo.

Você pode parar a simulação a qualquer momento, mas clicar em **Iniciar Simulação** repetirá a simulação desde o início do conjunto de dados.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como o Azure IoT habilita cenários de manutenção preditiva, leia [Capturar o valor da Internet das Coisas][lnk_capture_value].

Faça um [passo a passo][lnk-predictive-walkthrough] da solução de manutenção preditiva.

Você também pode explorar alguns dos outros recursos das soluções pré-configuradas do IoT Suite:

* [Perguntas frequentes sobre o IoT Suite][lnk-faq]
* [Segurança IoT desde o início][lnk-security-groundup]

[img-resource-group]: media/iot-suite-predictive-overview/resource-group.png
[img-simulation-stopped]: media/iot-suite-predictive-overview/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-overview/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-overview/simulation-warning.png
[img-machine-learning]: media/iot-suite-predictive-overview/machine-learning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk-predictive-walkthrough]: iot-suite-predictive-walkthrough.md
[lnk_preconfigured_solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk_iot_suite]: iot-suite-overview.md
[lnk_infographic]: https://www.microsoft.com/server-cloud/predictivemaintenance/Index.html
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3

[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-permissions]: iot-suite-permissions.md
[lnk-portal]: http://portal.azure.com/
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/