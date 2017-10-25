---
title: Envie alertas do Azure Application Insights | Microsoft Docs
description: Tutorial para enviar alertas em resposta a erros em seu aplicativo usando o Azure Application Insights.
keywords: 
author: bwren
ms.author: bwren
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: ce297476cfdf80564c6c0cb835955a146f7a1c12
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Monitorar e alertar sobre a integridade do aplicativo com o Azure Application Insights

O Azure Application Insights permite monitorar seu aplicativo e enviar alertas quando ele não está disponível, apresentando falhas ou sofrendo problemas de desempenho.  Este tutorial o guiará pelo processo de criação de testes para verificar continuamente a disponibilidade de seu aplicativo e enviar os diferentes tipos de alertas em resposta a problemas detectados.  Você aprenderá como:

> [!div class="checklist"]
> * Criar um teste de disponibilidade para verificar continuamente a resposta do aplicativo
> * Enviar email aos administradores quando ocorrer um problema
> * Criar alertas com base em métricas de desempenho 
> * Use um Aplicativo Lógico para enviar telemetria resumida conforme um agendamento.


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Instale o [Visual Studio 2017](https://www.visualstudio.com/downloads/) com as cargas de trabalho a seguir:
    - Desenvolvimento Web e ASP.NET
    - Desenvolvimento do Azure
    - Implante um aplicativo .NET para Azure e [habilite o SDK do Application Insights](app-insights-asp-net.md). 


## <a name="log-in-to-azure"></a>Fazer logon no Azure
Faça logon no portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-availability-test"></a>Criar teste de disponibilidade
Testes de disponibilidade no Application Insights permitem testar automaticamente seu aplicativo de vários locais em todo o mundo.   Neste tutorial, você executará um teste simples para garantir que o aplicativo esteja disponível.  Você também pode criar um passo a passo completo para testar sua operação detalhada. 

1. Selecione **Application Insights** e, em seguida, selecione sua assinatura.  
1. Selecione **Disponibilidade** no menu **Investigar** e clique em **Adicionar teste**.
 
    ![Adicionar teste de disponibilidade](media/app-insights-tutorial-alert/add-test.png)

2. Digite um nome para o teste e deixe os outros padrões.  Isso solicita a página inicial do aplicativo a cada cinco minutos de cinco localizações geográficas diferentes. 
3. Selecione **Alertas** para abrir o painel **Alertas**, no qual você pode definir os detalhes de como responder se o teste falha. Tipo de um endereço de email para enviar quando os critérios de alerta forem atendidos.  Opcionalmente, você pode digitar o endereço de um webhook para chamar quando os critérios de alerta forem atendidos.

    ![Criar teste](media/app-insights-tutorial-alert/create-test.png)
 
4. Retorne ao painel de teste e, depois de alguns minutos, você deverá começar a ver resultados do teste de disponibilidade.  Clique no nome do teste para exibir detalhes de cada local.  O gráfico de dispersão mostra o sucesso e a duração de cada teste.

    ![Detalhes do teste](media/app-insights-tutorial-alert/test-details.png)

5.  Você pode fazer drill down até os detalhes de qualquer teste específico clicando no seu ponto no gráfico de dispersão.  O exemplo a seguir mostra os detalhes de uma solicitação com falha.

    ![Resultado do teste](media/app-insights-tutorial-alert/test-result.png)
  
6. Se os critérios de alerta forem atendidos, uma mensagem semelhante à seguinte será enviada ao endereço que você especificou.

    ![Email de alerta](media/app-insights-tutorial-alert/alert-mail.png)


## <a name="create-an-alert-from-metrics"></a>Crie um alerta de métricas
Além de enviar alertas de um teste de disponibilidade, você pode criar um alerta de qualquer métrica de desempenho que esteja sendo coletada para o seu aplicativo.

2. Selecione **Alertas** no menu **Configurar**.  Isso abre o painel de Alertas do Azure.  Pode haver outras regras de alerta configuradas aqui para outros serviços.
3. Clique em **Adicionar alerta da métrica**.  Isso abre o painel para criar uma nova regra de alerta.

    ![Adicionar alerta da métrica](media/app-insights-tutorial-alert/add-metric-alert.png)

4. Digite um **Nome** para a regra de alerta e selecione seu aplicativo no menu suspenso para **Recurso**.
5. Selecione uma **Métrica** a amostrar.  Um gráfico é exibido para indicar que o valor dessa solicitação nas últimas 24 horas.  Isso ajuda a configurar a condição para a métrica.

    ![Adicionar regra de alerta](media/app-insights-tutorial-alert/add-alert-01.png)

6. Especifique uma **Condição** e um valor de **Limite**. Este é o número de vezes que a métrica deve ser excedida para um alerta ser criado. 
6. Em **Notificar via**, marque a caixa **Proprietários, contribuidores e leitores de email** para enviar um email para esses usuários quando a condição de alerta for atendida e adicionar o endereço de email de quaisquer destinatários adicionais.  Você também pode especificar um webhook ou um aplicativo lógico aqui que seja executado quando a condição for atendida.  Isso pode ser usado para tentar reduzir o problema detectado ou 

    ![Adicionar regra de alerta](media/app-insights-tutorial-alert/add-alert-02.png)


## <a name="proactively-send-information"></a>Enviar informações proativamente
Os alertas são criados em reação a um conjunto específico de problemas identificados em seu aplicativo e normalmente reservam alertas para condições críticas que exigem atenção imediata.  Você pode receber informações proativamente sobre o seu aplicativo com um Aplicativo Lógico que seja executado automaticamente em um agendamento.  Por exemplo, você enviar um email aos administradores diariamente com informações de resumo que exijam mais avaliação.

Para obter detalhes sobre a criação de um Aplicativo Lógico com o Application Insights, consulte [Automatizar processos do Application Insights usando Aplicativos Lógicos](automate-with-logic-apps.md)

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu a alertar sobre problemas, avance para o próximo tutorial para aprender a analisar como os usuários estão interagindo com o seu aplicativo.

> [!div class="nextstepaction"]
> [Entender os usuários](app-insights-tutorial-users.md)