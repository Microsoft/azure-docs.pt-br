---
title: 'Tutorial: gerenciar a computação com o Azure Functions'
description: Como usar Azure Functions para gerenciar a computação do pool SQL dedicado (anteriormente conhecido como SQL DW) no Azure Synapse Analytics.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/27/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f0731f0deaf46ec419cfe43037804e10f2b73fd4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96448376"
---
# <a name="use-azure-functions-to-manage-compute-resources-for-your-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Use Azure Functions para gerenciar recursos de computação para seu pool SQL dedicado (anteriormente conhecido como SQL DW) no Azure Synapse Analytics

Este tutorial usa Azure Functions para gerenciar recursos de computação para um pool SQL dedicado (anteriormente conhecido como SQL DW) no Azure Synapse Analytics.

Para usar um Aplicativo de funções do Azure com um pool SQL dedicado (anteriormente conhecido como SQL DW), você deve criar uma [conta de entidade de serviço](../../active-directory/develop/howto-create-service-principal-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). A conta da entidade de serviço precisa de acesso de colaborador na mesma assinatura que a instância do pool SQL dedicado (anteriormente conhecido como SQL DW).

## <a name="deploy-timer-based-scaling-with-an-azure-resource-manager-template"></a>Implantar o escalonador baseado em temporizador com um modelo do Azure Resource Manager

Para implantar o modelo, você precisa das informações a seguir:

- Nome do grupo de recursos que a instância do pool de SQL dedicado (anteriormente conhecido como SQL DW) está em
- Nome do servidor em que a instância do pool de SQL dedicado (anteriormente conhecido como SQL DW) está
- Nome da instância do pool de SQL dedicado (anteriormente conhecido como SQL DW)
- A ID de locatário (ID do diretório) do Azure Active Directory
- ID da assinatura
- ID do aplicativo da entidade de serviço
- Chave secreta da entidade de serviço

Após reunir as informações anteriores, implante este modelo:

[![Imagem mostrando um botão rotulado como "Implantar no Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json)

Depois de implantar o modelo, você deve encontrar três novos recursos: um plano do Serviço de Aplicativo do Azure gratuito, um plano do Aplicativo de funções baseado em consumo e uma conta de armazenamento que manipula o registro em log e a fila de operações. Continue a ler as outras seções para saber como modificar as funções implantadas de acordo com as suas necessidades.

## <a name="change-the-compute-level"></a>Alterar o nível de computação

1. Navegue até o serviço d do Aplicativo de funções. Se você implantou o modelo com os valores padrão, esse serviço deve ser chamado *DWOperations*. Quando seu Aplicativo de funções for aberto, você deve observar que há cinco funções implantadas para a função do Serviço de Aplicativo de funções.

   ![Funções implantadas com o modelo](./media/manage-compute-with-azure-functions/five-functions.png)

2. Selecione *DWScaleDownTrigger* ou *DWScaleUpTrigger* para escalar ou reduzir verticalmente. No menu suspenso, selecione Integrar.

   ![Selecione Integrar para a função](./media/manage-compute-with-azure-functions/select-integrate.png)

3. O valor exibido deve ser *ScaleDownTime %* ou *ScaleUpTime %*. Esses valores indicam que o agendamento se baseia nos valores definidos nas suas [Configurações do aplicativo](../../azure-functions/functions-how-to-use-azure-function-app-settings.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Por enquanto, você pode ignorar esse valor e alterar o agendamento para a hora de sua preferência com base nas próximas etapas.

4. Na área de agendamento, adicione a expressão CRON que você gostaria de refletir com que frequência deseja que a análise de Synapse do Azure seja dimensionada verticalmente.

   ![Alterar o agendamento da função](./media/manage-compute-with-azure-functions/change-schedule.png)

   O valor de `schedule` é uma [expressão CRON](https://en.wikipedia.org/wiki/Cron#CRON_expression) que inclui estes seis campos:

   ```json
   {second} {minute} {hour} {day} {month} {day-of-week}
   ```

   Por exemplo *"0 30 9 * * 1-5"* refletiria um gatilho cada dia útil às 9:30. Para obter mais informações, visite [exemplos de agendamento](../../azure-functions/functions-bindings-timer.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#example) do Azure Functions.

## <a name="change-the-time-of-the-scale-operation"></a>Altere o tempo da operação de escala

1. Navegue até o serviço d do Aplicativo de funções. Se você implantou o modelo com os valores padrão, esse serviço deve ser chamado *DWOperations*. Quando seu Aplicativo de funções for aberto, você deve observar que há cinco funções implantadas para a função do Serviço de Aplicativo de funções.

2. Selecione *DWScaleDownTrigger* ou *DWScaleUpTrigger* para escalar ou reduzir verticalmente o valor de computação. Ao selecionar as funções, o painel deve mostrar o arquivo *index.js*.

   ![Alterar o nível de computação do gatilho da função](././media/manage-compute-with-azure-functions/index-js.png)

3. Altere o valor de *ServiceLevelObjective* para o nível que você deseja e selecione salvar. O *ServiceLevelObjective* é o nível de computação para o qual sua instância de data warehouse será dimensionada com base na agenda definida na seção integrar.

## <a name="use-pause-or-resume-instead-of-scale"></a>Usar pausar ou retomar em vez do dimensionamento

Atualmente, as funções habilitadas por padrão são *DWScaleDownTrigger* e *DWScaleUpTrigger*. No entanto, se você quiser usar a funcionalidade de pausar e retomar, você pode habilitar *DWPauseTrigger* ou *DWResumeTrigger*.

1. Navegue até o painel Funções.

   ![Painel Funções](./media/manage-compute-with-azure-functions/functions-pane.png)

2. Selecione na alternância deslizante para os gatilhos correspondentes que você deseja habilitar.

3. Navegue até as guias *Integrar* dos respectivos gatilhos para alterar o agendamento.

   > [!NOTE]
   > A diferença funcional entre os gatilhos de escala e os gatilhos de pausar/retomar é a mensagem que é enviada para a fila. Para saber mais, confira [Adicionar uma nova função de gatilho](manage-compute-with-azure-functions.md#add-a-new-trigger-function).

## <a name="add-a-new-trigger-function"></a>Adicionar uma nova função de gatilho

Atualmente, há apenas duas funções de dimensionamento incluídas no modelo. Com essas funções, no decorrer de um dia, você só pode escalar e reduzir verticalmente somente uma vez. Para um controle mais granular, como reduzir verticalmente várias vezes por dia ou ter um comportamento de dimensionamento diferente nos finais de semana, você precisa adicionar outro gatilho.

1. Criar uma nova função em branco. Selecione o *+* botão próximo ao local de funções para mostrar o painel de modelo de função.

   ![Captura de tela que mostra o menu "aplicativos de função" com o ícone "mais" ao lado de "funções" selecionado.](./media/manage-compute-with-azure-functions/create-new-function.png)

2. Em idioma, selecione *JavaScript* e, em seguida, selecione *TimerTrigger*.

   ![Criar nova função](./media/manage-compute-with-azure-functions/timertrigger-js.png)

3. Nomeie a sua função e defina o agendamento. A imagem mostra como fazer para disparar sua função todos os sábados à meia-noite (tarde da noite de sexta-feira).

   ![Reduzir verticalmente sábado](./media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. Copie o conteúdo de *index.js* de uma das outras funções de gatilho.

   ![Copiar index.js](././media/manage-compute-with-azure-functions/index-js.png)

5. Defina a sua variável de operação para o comportamento desejado da seguinte maneira:

   ```JavaScript
   // Resume the dedicated SQL pool (formerly SQL DW) instance
   var operation = {
       "operationType": "ResumeDw"
   }

   // Pause the dedicated SQL pool (formerly SQL DW) instance
   var operation = {
       "operationType": "PauseDw"
   }

   // Scale the dedicated SQL pool (formerly SQL DW)l instance to DW600c
   var operation = {
       "operationType": "ScaleDw",
       "ServiceLevelObjective": "DW600c"
   }
   ```

## <a name="complex-scheduling"></a>Agendamentos complexos

Esta seção demonstra brevemente o que é necessário para aproveitar mais os recursos de agendamento complexo para pausar, retomar e dimensionar.

### <a name="example-1"></a>Exemplo 1

Escale verticalmente diariamente às 8:00 até DW600c e reduza às 20:00 para DW200c.

| Função  | Agenda     | Operação                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW600c"}` |
| Function2 | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200c"}` |

### <a name="example-2"></a>Exemplo 2

Escalar verticalmente verticalmente às 8:00 para DW1000c, reduzir horizontalmente uma vez para DW600 em 16:00 e reduzir verticalmente em 19:10 para DW200c.

| Função  | Agenda     | Operação                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000c"}` |
| Function2 | 0 0 16 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600c"}` |
| Function3 | 0 0 22 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200c"}` |

### <a name="example-3"></a>Exemplo 3

Escale verticalmente às 8:00 até DW1000c, escale para baixo uma vez para DW600c às 16:00 nos dias da semana. Pausar sexta-feira às 23:00, retomar segunda-feira de manhã às 7:00.

| Função  | Agenda       | Operação                                |
| :-------- | :------------- | :--------------------------------------- |
| Function1 | 0 0 8 * * 1-5  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000c"}` |
| Function2 | 0 0 16 * * 1-5 | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600c"}` |
| Function3 | 0 0 23 * * 5   | `var operation = {"operationType": "PauseDw"}` |
| Function4 | 0 0 7 * * 1    | `var operation = {"operationType": "ResumeDw"}` |

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [gatilho de temporizador](../../azure-functions/functions-create-scheduled-function.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Azure functions.

Consulte [repositório de exemplos](https://github.com/Microsoft/sql-data-warehouse-samples)do pool SQL dedicado (anteriormente conhecido como SQL DW).
