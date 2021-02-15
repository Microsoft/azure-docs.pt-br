---
title: Como substituir sua infraestrutura de fita
description: Saiba como o backup do Azure fornece semântica semelhante à fita que permite que você faça backup e restaure dados no Azure
ms.topic: conceptual
ms.date: 04/30/2017
ms.openlocfilehash: 695cc2644521384527ecd871f3613a078e987aa7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89378433"
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>Mover o armazenamento de longo prazo da fita para a nuvem do Azure

Os clientes do Backup do Azure e do System Center Data Protection Manager podem:

* Fazer backup de dados em agendas mais adequadas às necessidades da organização.
* Manter os dados de backup por períodos mais longos.
* Tornar o Azure parte das necessidades de retenção de longo prazo (em vez de fita).

Este artigo explica como os clientes podem habilitar políticas de backup e retenção. Os clientes que usam fitas para tratar suas necessidades de retenção de longo prazo agora têm uma alternativa viável e eficiente com a disponibilidade desse recurso. O recurso está habilitado na versão mais recente do Backup do Azure (que está disponível [aqui](https://aka.ms/azurebackup_agent)). Os clientes do System Center DPM devem atualizar para, pelo menos, o DPM 2012 R2 UR5 antes de usar o DPM com o serviço de Backup do Azure.

## <a name="what-is-the-backup-schedule"></a>Qual é o agendamento de backup?

O agendamento de backup indica a frequência da operação de backup. Por exemplo, as configurações na tela a seguir indicam que os backups são feitos diariamente às 18h e à meia-noite.

![Agendamento diário](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

Os clientes também podem agendar um backup semanal. Por exemplo, as configurações na tela a seguir indicam que os backups são feitos a cada dois domingos e a cada duas quartas-feiras às 9h30 e à 1h.

![Agendamento semanal](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## <a name="what-is-the-retention-policy"></a>O que é a política de retenção?

A política de retenção especifica durante quanto tempo o backup deve ser armazenado. Em vez de especificar apenas uma “política simples” para todos os pontos de backup, os clientes podem especificar diferentes políticas de retenção com base em quando o backup é feito. Por exemplo, o ponto de backup feito diariamente (que funciona como um ponto de recuperação operacional) é preservado por 90 dias. O ponto de backup feito no final de cada trimestre para fins de auditoria é preservado por mais tempo.

![Política de retenção](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

O número total de “pontos de retenção” especificado nessa política é de 90 (pontos diários) + 40 (um a cada trimestre por 10 anos) = 130.

## <a name="example--putting-both-together"></a>Exemplo – Juntando ambos

![Tela de exemplo](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **Política de retenção diária**: os backups diários são armazenados por sete dias.
2. **Política de retenção semanal**: os backups feitos à meia-noite e a 5 PM sábado são preservados por quatro semanas.
3. **Política de retenção mensal**: backups feitos à meia-noite e 6 PM no último sábado de cada mês são preservados por 12 meses.
4. **Política de retenção anual**: os backups feitos à meia-noite no último sábado de cada março são preservados por 10 anos.

O número total de “pontos de retenção” (dos quais um cliente pode restaurar dados) no diagrama anterior é calculado da seguinte forma:

* dois pontos por dia por sete dias = 14 pontos de recuperação
* dois pontos por semana por quatro semanas = oito pontos de recuperação
* dois pontos por mês por 12 meses = 24 pontos de recuperação
* um ponto por ano por 10 anos = 10 pontos de recuperação

O número total de pontos de recuperação é 56.

> [!NOTE]
> Usando o backup do Azure, você pode criar até 9999 pontos de recuperação por instância protegida. Uma instância protegida é um computador, servidor (físico ou virtual) ou carga de trabalho que faz backup de dados no Azure.
>

## <a name="advanced-configuration"></a>Configuração avançada

Ao selecionar **Modificar** na tela anterior, os clientes têm flexibilidade adicional para especificar agendamentos de retenção.

![Modificar janela de política](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Backup do Azure, veja:

* [Introdução ao Backup do Azure](./backup-overview.md)
* [Teste o Backup do Azure](./backup-windows-with-mars-agent.md)
