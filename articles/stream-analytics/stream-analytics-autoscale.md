---
title: Trabalhos de autoescala Stream Analytics
description: Este artigo descreve como fazer o dimensionamento automático Stream Analytics trabalho com base em uma agenda predefinida ou valores de métricas de trabalho
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/03/2020
ms.openlocfilehash: a8e089e302e9d40c69cf7ff2a3480c17894e1463
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98016279"
---
# <a name="autoscale-stream-analytics-jobs-using-azure-automation"></a>Autoescala Stream Analytics trabalhos usando a automação do Azure

Você pode otimizar o custo de seus trabalhos de Stream Analytics Configurando o dimensionamento automático. O dimensionamento automático aumenta ou diminui as unidades de streaming do seu trabalho (SUs) para corresponder à alteração em sua carga de entrada. Em vez de provisionar o trabalho, você pode escalar ou reduzir verticalmente conforme necessário. Há duas maneiras de configurar seus trabalhos para dimensionamento automático:
1. **Defina previamente uma agenda** quando você tiver uma carga de entrada previsível. Por exemplo, você espera uma taxa mais alta de eventos de entrada durante o dia e deseja que seu trabalho seja executado com mais SUs.
2. Dispare verticalmente **e reduza as operações com base nas métricas de trabalho** quando você não tem uma carga de entrada previsível. Você pode alterar dinamicamente o número de SUs com base em suas métricas de trabalho, como o número de eventos de entrada ou eventos de entrada de registro posterior.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar a configurar o dimensionamento automático para seu trabalho, conclua as etapas a seguir.
1. Seu trabalho é otimizado para ter uma [topologia paralela](./stream-analytics-parallelization.md). Se você puder alterar a escala de seu trabalho enquanto ele estiver em execução, seu trabalho terá uma topologia paralela e poderá ser configurado para dimensionamento automático.
2. [Crie uma conta de automação do Azure](../automation/automation-create-standalone-account.md) com a opção "RunAsAccount" habilitada. Essa conta deve ter permissões para gerenciar seus trabalhos de Stream Analytics.

## <a name="set-up-azure-automation"></a>Configurar a automação do Azure
### <a name="configure-variables"></a>Configurar variáveis
Adicione as seguintes variáveis dentro da conta de automação do Azure. Essas variáveis serão usadas nos runbooks descritos nas próximas etapas.

| Nome | Type | Valor |
| --- | --- | --- |
| **jobName** | String | Nome do seu trabalho de Stream Analytics que você deseja fazer o dimensionamento automático. |
| **resourceGroupName** | String | Nome do grupo de recursos no qual seu trabalho está presente. |
| **subId** | String | ID da assinatura na qual seu trabalho está presente. |
| **increasedSU** | Inteiro | O maior valor de SU para o qual você deseja que o trabalho seja dimensionado em um agendamento. Esse valor deve ser uma das opções de SU válidas que você vê nas configurações de **escala** de seu trabalho enquanto ele está em execução. |
| **decreasedSU** | Inteiro | O menor valor de SU para o qual você deseja que seu trabalho seja dimensionado em um agendamento. Esse valor deve ser uma das opções de SU válidas que você vê nas configurações de **escala** de seu trabalho enquanto ele está em execução. |
| **maxSU** | Inteiro | O valor máximo de SU para o qual você deseja que seu trabalho seja dimensionado em etapas quando o dimensionamento automático por carga. Esse valor deve ser uma das opções de SU válidas que você vê nas configurações de **escala** de seu trabalho enquanto ele está em execução. |
| **minSu** | Inteiro | O valor mínimo de SU para o qual você deseja que o trabalho seja dimensionado em etapas quando o dimensionamento automático por carga. Esse valor deve ser uma das opções de SU válidas que você vê nas configurações de **escala** de seu trabalho enquanto ele está em execução. |

![Adicionar variáveis na automação do Azure](./media/autoscale/variables.png)

### <a name="create-runbooks"></a>Criar runbooks
A próxima etapa é criar dois runbooks do PowerShell. Um para escalar verticalmente e outro para operações de redução vertical.
1. Em sua conta de automação do Azure, vá para **Runbooks** em **automação de processo**  e selecione **criar runbook**.
2. Nomeie o primeiro runbook *ScaleUpRunbook* com o tipo definido como PowerShell. Use o [script do PowerShell do ScaleUpRunbook](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/ScaleUpRunbook.ps1) disponível no github. Salve e publique-o.
3. Crie outro runbook chamado *ScaleDownRunbook* com o tipo PowerShell. Use o [script do PowerShell do ScaleDownRunbook](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/ScaleDownRunbook.ps1) disponível no github. Salve e publique-o.

![Runbooks de dimensionamento automático na automação do Azure](./media/autoscale/runbooks.png)

Agora você tem runbooks que podem disparar automaticamente as operações de escala e redução vertical em seu trabalho de Stream Analytics. Esses runbooks podem ser disparados usando um agendamento predefinido ou podem ser definidos dinamicamente com base em métricas de trabalho.

## <a name="autoscale-based-on-a-schedule"></a>Dimensionamento automático baseado em agendamento
A automação do Azure permite que você configure uma agenda para disparar seus runbooks.
1. Em sua conta de automação do Azure, selecione **agendas** em **recursos compartilhados**. Em seguida, selecione **Adicionar um agendamento**.
2. Por exemplo, você pode criar dois agendamentos. Um que representa quando você deseja que seu trabalho seja escalado verticalmente e outro que represente quando você deseja que seu trabalho seja reduzido horizontalmente. Você pode definir uma recorrência para esses agendamentos.

   ![Agendas na Automação do Azure](./media/autoscale/schedules.png)

3. Abra seu **ScaleUpRunbook** e, em seguida, selecione **agendas** em **recursos**. Em seguida, você pode vincular seu runbook a uma agenda criada nas etapas anteriores. Você pode ter vários agendamentos vinculados ao mesmo runbook, o que pode ser útil quando você deseja executar a mesma operação de escala em diferentes momentos do dia.

![Agendando runbooks na automação do Azure](./media/autoscale/schedulerunbook.png)

1. Repita a etapa anterior para **ScaleDownRunbook**.

## <a name="autoscale-based-on-load"></a>Dimensionamento automático com base na carga
Pode haver casos em que você não pode prever a carga de entrada. Nesses casos, é mais adequado aumentar/reduzir em etapas dentro de um limite mínimo e máximo. Você pode configurar regras de alerta em seus trabalhos de Stream Analytics para disparar runbooks quando as métricas de trabalho estiverem acima ou abaixo de um limite.
1. Em sua conta de automação do Azure, crie mais duas variáveis de inteiro chamadas **minsu** e **maxSU**. Isso define os limites nos quais seu trabalho será dimensionado em etapas.
2. Crie dois novos runbooks. Você pode usar o [script do PowerShell do StepScaleUp](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/StepScaleUp.ps1) que aumenta o SUS de seu trabalho em incrementos até o valor de **maxSU** . Você também pode usar o [script do PowerShell do StepScaleDown](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/StepScaleDown.ps1) que diminui o SUS de seu trabalho em etapas até que o valor de **minsu** seja atingido. Como alternativa, você pode usar os runbooks da seção anterior se tiver valores de SU específicos para os quais deseja dimensionar.
3. Em seu trabalho de Stream Analytics, selecione **regras de alerta** em **monitoramento**. 
4. Crie dois grupos de ação. Um para ser usado para a operação de expansão e outro para a operação de redução vertical. Selecione **Gerenciar ações** e clique em **Adicionar grupo de ações**. 
5. Preencha os campos obrigatórios. Escolha **runbook de automação** ao selecionar o **tipo de ação**. Selecione o runbook que você deseja disparar quando o alerta for disparado. Em seguida, crie o grupo de ações.

   ![Criar grupo de ações](./media/autoscale/create-actiongroup.png)
6. Crie uma [**nova regra de alerta**](./stream-analytics-set-up-alerts.md#set-up-alerts-in-the-azure-portal) em seu trabalho. Especifique uma condição com base em uma métrica de sua escolha. [ *Eventos de entrada*, *Su% de utilização* ou eventos de entrada de *registro*](./stream-analytics-monitoring.md#metrics-available-for-stream-analytics) posterior são métricas recomendadas a serem usadas para definir a lógica de dimensionamento automático. Também é recomendável usar a granularidade de *agregação* de 1 minuto e a *frequência de avaliação* ao disparar operações de escala vertical. Isso garante que seu trabalho tenha recursos amplos para lidar com grandes picos no volume de entrada.
7. Selecione o grupo de ação criado na última etapa e crie o alerta.
8. Repita as etapas de 2 a 4 para todas as operações de escala adicionais que você deseja disparar com base na condição de métricas de trabalho.

É uma prática recomendada executar testes de escala antes de executar o trabalho em produção. Ao testar seu trabalho em diferentes cargas de entrada, você tem uma noção de quantas SUs seu trabalho precisa para uma taxa de transferência de entrada diferente. Isso pode informar as condições que você define em suas regras de alerta que disparam operações de escalar verticalmente e reduzir horizontalmente. 

## <a name="next-steps"></a>Próximas etapas
* [Criar consultas paralelizáveis no Azure Stream Analytics](stream-analytics-parallelization.md)
* [Dimensionar Azure Stream Analytics trabalhos para aumentar a taxa de transferência](stream-analytics-scale-jobs.md)