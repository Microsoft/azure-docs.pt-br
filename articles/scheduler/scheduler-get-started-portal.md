---
title: "Introdução ao Agendador do Azure no portal do Azure | Microsoft Docs"
description: "Introdução ao Agendador do Azure no Portal do Azure"
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: e69542ec-d10f-4f17-9b7a-2ee441ee7d68
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/10/2016
ms.author: deli
ms.openlocfilehash: 3861ee121ed1c4d086ea81640e84d924d7d17ea1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-scheduler-in-azure-portal"></a>Introdução ao Agendador do Azure no Portal do Azure
É fácil criar trabalhos agendados no Agendador do Azure. Neste tutorial, você aprenderá a criar um trabalho. Você também aprenderá sobre os recursos de monitoramento e gerenciamento do Agendador.

## <a name="create-a-job"></a>Criar um trabalho
1. Entre no [Portal do Azure](https://portal.azure.com/).  
2. Clique em **+Novo** > digite *Agendador* na caixa de pesquisa > selecione **Agendador** nos resultados > clique em **Criar**.
   
    ![][marketplace-create]
3. Vamos criar um trabalho que simplesmente visita http://www.microsoft.com/ com uma solicitação GET. Na tela **Trabalho do Agendador** , insira as seguintes informações:
   
   1. **Nome:** `getmicrosoft`  
   2. **Assinatura:** sua assinatura do Azure   
   3. **Coleção de Trabalhos:** selecione uma coleção de trabalhos existente ou clique em **Criar Novo** > insira um nome.
4. Em seguida, nas **Configurações de Ação**, defina os seguintes valores:
   
   1. **Tipo de ação:** ` HTTP`  
   2. **Método:** `GET`  
   3. **URL:** ` http://www.microsoft.com`  
      
      ![][action-settings]
5. Finalmente, vamos definir uma agenda. O trabalho pode ser definido como um único trabalho, mas convém escolher uma agenda de recorrência:
   
   1. **Recorrência**: `Recurring`
   2. **Iniciar**: data de hoje
   3. **Repetir a cada**: `12 Hours`
   4. **Terminar**: dois dias após a data de hoje  
      
      ![][recurrence-schedule]
6. Clique em **Criar**

## <a name="manage-and-monitor-jobs"></a>Gerenciar e monitorar trabalhos
Depois que um trabalho é criado, ele aparece no painel principal do Azure. Clique no trabalho e uma nova janela será aberta com as seguintes guias:

1. Propriedades  
2. Configurações de Ação  
3. Agenda  
4. Histórico
5. Usuários
   
   ![][job-overview]

### <a name="properties"></a>Propriedades
Essas propriedades somente leitura descrevem os metadados de gerenciamento para o trabalho do Agendador.

   ![][job-properties]

### <a name="action-settings"></a>Configurações de Ação
Clicar em um trabalho na tela **Trabalhos** permite que você configure esse trabalho. Isso permitirá definir configurações avançadas, se você não as tiver configurado no assistente de criação rápida.

Para todos os tipos de ação, você pode alterar a política de repetição e a ação de erro.

Para tipos de ação do trabalho HTTP e HTTPS, você pode alterar o método para qualquer verbo HTTP permitido. Você também pode adicionar, excluir ou alterar os cabeçalhos e informações de autenticação básica.

Para tipos de ação de fila de armazenamento, você pode alterar a conta de armazenamento, nome da fila, token SAS e corpo.

Para tipos de ação do barramento de serviço, você pode alterar o namespace, o caminho da fila/tópico, as configurações de autenticação, o tipo de transporte, as propriedades de mensagem e o corpo da mensagem.

   ![][job-action-settings]

### <a name="schedule"></a>Agenda
Isso permite reconfigurar o agendamento, se você desejar alterar o agendamento que criou no assistente de criação rápida.

Essa é uma oportunidade de criar [agendas complexas e recorrência avançada no trabalho](scheduler-advanced-complexity.md)

Você pode alterar a data e hora de início, o agendamento de recorrência e a data e hora de término (se o trabalho é recorrente).

   ![][job-schedule]

### <a name="history"></a>Histórico
A guia **Histórico** exibe as métricas selecionadas para cada execução do trabalho do sistema para o trabalho selecionado. Essas métricas fornecem valores em tempo real relacionados à integridade de seu Agendador:

1. Status  
2. Detalhes  
3. Tentativas de repetição
4. Ocorrência: 1ª, 2ª, 3ª etc.
5. Hora de início da execução  
6. Hora de término da execução
   
   ![][job-history]

Você pode clicar em uma execução para exibir seus **Detalhes de Histórico**, incluindo a resposta inteira para cada execução. Essa caixa de diálogo também permite que você copie a resposta para a área de transferência.

   ![][job-history-details]

### <a name="users"></a>Usuários
O RBAC (controle de acesso baseado em função) do Azure permite o gerenciamento de acesso refinado para o Agendador do Azure. Para saber como usar a guia Usuários, confira [Controle de Acesso baseado em função do Azure](../active-directory/role-based-access-control-configure.md)

## <a name="see-also"></a>Consulte também
 [O que é o Agendador?](scheduler-intro.md)

 [Conceitos, terminologia e hierarquia de entidades do Agendador](scheduler-concepts-terms.md)

 [Planos e Cobrança no Agendador do Azure](scheduler-plans-billing.md)

 [Como criar agendas complexas e recorrência avançada com o Agendador do Azure](scheduler-advanced-complexity.md)

 [Referência da API REST do Agendador](https://msdn.microsoft.com/library/mt629143)

 [Referência de cmdlets do PowerShell do Agendador](scheduler-powershell-reference.md)

 [Alta disponibilidade e confiabilidade do Agendador](scheduler-high-availability-reliability.md)

 [Limites, padrões e códigos de erro do Agendador](scheduler-limits-defaults-errors.md)

 [Autenticação de saída do Agendador](scheduler-outbound-authentication.md)

[marketplace-create]: ./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png
[action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png
[recurrence-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png
[job-properties]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png
[job-overview]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png
[job-action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png
[job-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png
[job-history]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png
[job-history-details]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png


[1]: ./media/scheduler-get-started-portal/scheduler-get-started-portal001.png
[2]: ./media/scheduler-get-started-portal/scheduler-get-started-portal002.png
[3]: ./media/scheduler-get-started-portal/scheduler-get-started-portal003.png
[4]: ./media/scheduler-get-started-portal/scheduler-get-started-portal004.png
[5]: ./media/scheduler-get-started-portal/scheduler-get-started-portal005.png
[6]: ./media/scheduler-get-started-portal/scheduler-get-started-portal006.png
[7]: ./media/scheduler-get-started-portal/scheduler-get-started-portal007.png
[8]: ./media/scheduler-get-started-portal/scheduler-get-started-portal008.png
[9]: ./media/scheduler-get-started-portal/scheduler-get-started-portal009.png
[10]: ./media/scheduler-get-started-portal/scheduler-get-started-portal010.png
[11]: ./media/scheduler-get-started-portal/scheduler-get-started-portal011.png
[12]: ./media/scheduler-get-started-portal/scheduler-get-started-portal012.png
[13]: ./media/scheduler-get-started-portal/scheduler-get-started-portal013.png
[14]: ./media/scheduler-get-started-portal/scheduler-get-started-portal014.png
[15]: ./media/scheduler-get-started-portal/scheduler-get-started-portal015.png
