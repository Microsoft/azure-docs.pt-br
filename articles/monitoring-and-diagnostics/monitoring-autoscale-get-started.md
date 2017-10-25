---
title: "Introdução ao dimensionamento automático no Azure | Microsoft Docs"
description: Saiba como dimensionar seu recurso no Azure.
author: rajram
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d37d3fda-8ef1-477c-a360-a855b418de84
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: rajram
ms.openlocfilehash: 84ce3af2bef1e3204ad91567d155d7cbf90e9c7e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-autoscale-in-azure"></a>Introdução ao dimensionamento automático no Azure
Este artigo descreve como configurar o dimensionamento automático para seu recurso no Portal do Microsoft Azure.

O dimensionamento automático do Azure Monitor se aplica somente aos conjuntos de dimensionamento da máquina virtual, aos serviços de nuvem, aos planos do Serviço de Aplicativo do Azure e aos ambientes do Serviço de Aplicativo. 

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>Descobrir as configurações de dimensionamento automático na sua assinatura
Você pode descobrir todos os recursos a que o dimensionamento automático se aplica no Azure Monitor. Use as etapas a seguir para obter uma explicação passo a passo:

1. Abra o [Portal do Azure.][1]
2. Clique no ícone do Azure Monitor no painel esquerdo.
  ![Abra o Azure Monitor][2]
3. Clique em **Dimensionamento automático** para exibir todos os recursos para os quais o dimensionamento automático é aplicável, juntamente com o status atual.
  ![Descubra o dimensionamento automático no Azure Monitor][3]

Você pode usar o painel de filtro na parte superior para reduzir o escopo da lista e selecionar recursos em um grupo de recursos específico, os tipos de recursos específicos ou um recurso específico.

Para cada recurso, você encontrará a contagem de instâncias atual e o status de dimensionamento automático. O status de dimensionamento automático pode ser:

- **Não configurado**: você ainda não habilitou o dimensionamento automático para este recurso.
- **Habilitado**: você habilitou o dimensionamento automático para este recurso.
- **Desabilitado**: você desabilitou o dimensionamento automático para este recurso.

## <a name="create-your-first-autoscale-setting"></a>Crie sua primeira configuração de dimensionamento automático

Agora, vamos percorrer um passo a passo simples para criar sua primeira configuração de dimensionamento automático.

1. Abra a folha **Dimensionamento Automático** no Azure Monitor e selecione um recurso que deseja dimensionar. (As etapas a seguir usam um plano do serviço de aplicativo associado a um aplicativo Web. Você pode [criar seu primeiro aplicativo Web ASP.NET no Azure em 5 minutos.][4])
2. Observe que a contagem da instância atual é 1. Clique em **Habilitar dimensionamento automático**.
  ![Configuração de dimensionamento para um novo aplicativo Web][5]
3. Forneça um nome para a configuração de dimensionamento e clique em **Adicionar uma regra**. Observe as opções de regra de dimensionamento que são abertas como um painel de contexto no lado direito. Por padrão, ele define a opção de dimensionar sua contagem de instâncias em 1 se o percentual de CPU do recurso ultrapassar 70%. Deixe-o com seus valores padrão e clique em **Adicionar**.
  ![Criar configuração dimensionamento para um aplicativo Web][6]
4. Agora, você criou sua primeira regra de dimensionamento. Observe que o UX indica as práticas recomendadas e afirma que "É recomendável ter pelo menos uma escala na regra". Para fazer isso:
  
    a. Clique em **Adicionar uma Regra**. 

    b. Defina **Operador** como **Menor que**.

    c. Defina o **Limite** como **20**.

    d. Defina **Operação** como **Diminuir contagem por**.

   Agora, você deve ter uma configuração de dimensionamento que expande/reduz com base no uso da CPU.
   ![Dimensionamento com base na CPU][8]
5. Clique em **Salvar**.

Parabéns! Você criou com êxito sua primeira configuração de dimensionamento para fazer o dimensionamento automático de seu aplicativo Web com base no uso da CPU.

> [!NOTE] 
> As mesmas etapas são aplicáveis para começar a usar uma função de serviço de nuvem ou conjunto de dimensionamento de máquinas virtuais.

## <a name="other-considerations"></a>Outras considerações
### <a name="scale-based-on-a-schedule"></a>Dimensionamento com base em um planejamento
Além de dimensionar com base na CPU, você também pode definir seu dimensionamento de forma diferente em dias específicos da semana.

1. Clique em **Adicionar uma condição de dimensionamento**.
2. A configuração do modo e das regras de dimensionamento é a mesma que a da condição padrão.
3. Selecione **Repetir dias específicos** para o agendamento.
4. Selecione os dias e a hora de início/término em que a condição de dimensionamento deve ser aplicada.

![Condição de dimensionamento com base no agendamento][9]
### <a name="scale-differently-on-specific-dates"></a>Dimensionar de forma diferente em datas específicas
Além de dimensionar com base na CPU, você também pode definir seu dimensionamento de forma diferente em datas específicas.

1. Clique em **Adicionar uma condição de dimensionamento**.
2. A configuração do modo e das regras de dimensionamento é a mesma que a da condição padrão.
3. Selecione **Especificar datas de início/término** para o agendamento.
4. Selecione as datas e a hora de início/término em que a condição de dimensionamento deve ser aplicada.

![Condição de dimensionamento com base em datas][10]

### <a name="view-the-scale-history-of-your-resource"></a>Exibir o histórico de dimensionamento de seu recurso
Sempre que o recurso é expandido/reduzido, um evento é registrado no log de atividades. Você pode exibir o histórico de dimensionamento do seu recurso nas últimas 24 horas acessando a guia **Histórico de execução**.

![Histórico da execução][11]

Se quiser exibir o histórico completo de dimensionamento (de até 90 dias), selecione **Clique aqui para ver mais detalhes**. O log de atividade é aberto, com Dimensionamento Automático pré-selecionado para seu recurso e categoria.

### <a name="view-the-scale-definition-of-your-resource"></a>Exibir a definição de escala do seu recurso
Dimensionamento automático é um recurso do Gerenciador de Recursos do Azure. Você pode exibir a definição de escala no JSON acessando a guia **JSON**.

![Definição de escala][12]

Você pode fazer alterações no JSON diretamente, se necessário. Essas alterações serão refletidas depois que você as salvar.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Desabilitar a escala automática e dimensionar suas instâncias manualmente
Pode haver momentos em que você queira desabilitar sua configuração de dimensionamento atual e dimensionar manualmente seu recurso.

Clique no botão **Desabilitar dimensionamento automático** na parte superior.
![Desabilitar dimensionamento automático][13]

> [!NOTE] 
> Esta opção desabilita a sua configuração. No entanto, você pode voltar a ela depois que habilitar o Dimensionamento Automático novamente. 

Agora, você pode definir o número de instâncias para o qual deseja dimensionar manualmente.

![Definir dimensionamento manual][14]

Você sempre pode retornar para o dimensionamento automático clicando em **Habilitar dimensionamento automático** e **Salvar**.

## <a name="next-steps"></a>Próximas etapas
- [Crie um Alerta de Log de Atividades para monitorar todas as operações de mecanismo de dimensionamento automático em sua assinatura](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Crie um Alerta de Log de Atividades para monitorar todas as operações de escalar horizontalmente/reduzir horizontalmente com falha na sua assinatura](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/monitoring-autoscale-get-started/azure-monitor-launch.png
[3]: ./media/monitoring-autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: https://docs.microsoft.com/azure/app-service/app-service-web-get-started-dotnet
[5]: ./media/monitoring-autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/monitoring-autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/monitoring-autoscale-get-started/scale-in-recommendation.png
[8]: ./media/monitoring-autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/monitoring-autoscale-get-started/scale-condition-schedule.png
[10]: ./media/monitoring-autoscale-get-started/scale-condition-dates.png
[11]: ./media/monitoring-autoscale-get-started/scale-history.png
[12]: ./media/monitoring-autoscale-get-started/scale-definition-json.png
[13]: ./media/monitoring-autoscale-get-started/disable-autoscale.png
[14]: ./media/monitoring-autoscale-get-started/set-manualscale.png

