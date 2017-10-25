---
title: Responder a eventos com alertas do Azure Log Analytics | Microsoft Docs
description: "Esse tutorial ajuda você a entender os alertas no Log Analytics para identificar informações importantes no repositório do OMS e notificá-lo de forma proativa de problemas ou invocar ações para tentar corrigi-los."
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/20/2017
ms.author: magoedte
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ccf0bd57b7f54b1d8d57b460df9bb381b29ee381
ms.contentlocale: pt-br
ms.lasthandoff: 09/25/2017

---

# <a name="respond-to-events-with-log-analytics-alerts"></a>Responder a eventos com alertas do Log Analytics
Alertas no Log Analytics identificam informações importante no repositório de Log Analytics.  Eles são criados por regras de alerta que executam pesquisas de logs automaticamente em intervalos regulares. Se os resultados da pesquisa de logs correspondem a critérios específicos, é criado um registro de alerta e ele pode ser configurado para executar uma resposta automatizada.  Este tutorial é uma continuação do tutorial [Criar e compartilhar painéis de dados do Log Analytics](log-analytics-tutorial-dashboards.md).   

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma regra de alerta
> * Configurar uma regra de alerta para enviar uma notificação por email

Para concluir o exemplo neste tutorial, você deve ter uma máquina virtual existente [conectada ao espaço de trabalho do Log Analytics](log-analytics-quick-collect-azurevm.md).  

## <a name="log-in-to-azure-portal"></a>Fazer logon no portal do Azure
Faça logon no portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-alerts"></a>Criar alertas

Os alertas são criados por regras de alerta que executam pesquisas de log automaticamente em intervalos regulares.  Crie alertas com base em métricas de desempenho específicas ou quando determinados eventos são criados, na ausência de um evento ou quando um número de eventos são criados em uma janela de tempo específica.  Por exemplo, os alertas podem ser usados para notificá-lo de quando o uso médio da CPU excede determinado limite ou um evento é gerado quando um serviço Windows ou um daemon do Linux específico não está em execução.   Se os resultados da pesquisa de log corresponderem a critérios específicos, um registro de alerta será criado. A regra pode então executar automaticamente uma ou mais ações para notificar você proativamente do alerta ou invocar outro processo. 

No exemplo a seguir, criamos uma regra de alerta de medida de métrica que criará um alerta para cada objeto de computador na consulta com um valor que excede um limite de 90%.

1. No portal do Azure, clique em **Mais serviços** encontrado no canto inferior esquerdo. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Log Analytics**.
2. Inicie o portal do OMS selecionando o Portal do OMS e, na página **Visão Geral**, selecione **Pesquisa de Logs**.  
3. Selecione **Favoritos** na parte superior do portal e, no painel **Pesquisas Salvas** à direita, selecione a consulta *VMs do Azure – Utilização do Processador*.  
4. Clique em **Alerta** na parte superior da página para abrir a tela **Adicionar Regra de Alerta**.  
5. Configure a regra de alerta com as seguintes informações:  
   a. Forneça um **Nome** para o alerta, como *A utilização do processador de VM excedeu >90*  
   b. Para **Janela de Tempo**, especifique um intervalo de tempo para a consulta, como *30*.  A consulta retorna somente os registros que foram criados dentro desse intervalo de tempo atual.  
   c. **Frequência de Alerta** especifica a frequência com que a consulta deve ser executada.  Neste exemplo, especifique *5* minutos, que ocorrerá em nossa janela de tempo especificada.  
   d. Selecione **Medida da Métrica** e insira *90* para **Valor Agregado** e *3* para **Disparar alerta com base em**   
   e. Em **Ações**, desabilite a notificação por email.
6. Clique em **Salvar** para concluir a regra de alerta. Ela começa a ser executada imediatamente.<br><br> ![Exemplo de regra de alerta](media/log-analytics-tutorial-response/log-analytics-alert-01.png)

Os registros de alerta criados por regras de alerta no Log Analytics têm um Tipo de **Alerta** e um SourceSystem que indica **OMS**.<br><br> ![Exemplo de eventos de Alerta gerados](media/log-analytics-tutorial-response/log-analytics-alert-events-01.png)  

## <a name="alert-actions"></a>Ações de alerta
Execute ações avançadas com alertas, como criar uma notificação por email, iniciar um [runbook de Automação](../automation/automation-runbook-types.md), usar um webhook para criar um registro de incidente no sistema de gerenciamento de incidentes ITSM ou com a [Solução de Conector de Gerenciamento de Serviço de TI](log-analytics-itsmc-overview.md) como uma resposta quando os critérios de alerta são atendidos.   

As ações de email enviam um email com detalhes do alerta para um ou mais destinatários. Você pode especificar o assunto do email, mas seu conteúdo é um formato padrão criado pelo Log Analytics.  Vamos atualizar a regra de alerta criada anteriormente e configurá-la para enviar uma notificação por email, em vez de monitorar ativamente o registro de alerta com uma pesquisa de logs.     

1. No portal do OMS, no menu superior, selecione **Configurações** e, em seguida, **Alertas**.
2. Na lista de regras de alerta, clique no ícone de lápis ao lado do alerta criado anteriormente.
3. Na seção **Ações**, habilite as notificações por email.
4. Forneça um **Assunto** para o email, como *Limite de utilização do processador excedido >90*.
5. Adicione endereços de um ou mais destinatários de email no campo **Destinatários**.  Se você especificar mais de um endereço, separe os endereços com ponto e vírgula (;).
6. Clique em **Salvar** para concluir a regra de alerta. Ela começa a ser executada imediatamente.<br><br> ![Regra de alerta com notificação por email](media/log-analytics-tutorial-response/log-analytics-alert-02.png)

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como as regras de alerta podem identificar de forma proativa e responder a um problema quando executarem as pesquisas de log em intervalos agendados e correspondem a determinado critério.  

Siga este link para ver amostras de script do Log Analytics pré-criadas.  

> [!div class="nextstepaction"]
> [Amostras de script do Log Analytics](powershell-samples.md)
