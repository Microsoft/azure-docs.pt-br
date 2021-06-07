---
title: Solucionar problemas de monitorar área de trabalho virtual do Windows-Azure
description: Como solucionar problemas com Azure Monitor para área de trabalho virtual do Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/29/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: dda58868432248fe93a9fbc83d1e538dfc9b61ba
ms.sourcegitcommit: dae6b628a8d57540263a1f2f1cdb10721ed1470d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "105709439"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop"></a>Solucionar problemas de Azure Monitor para área de trabalho virtual do Windows

Este artigo apresenta problemas conhecidos e soluções para problemas comuns em Azure Monitor para área de trabalho virtual do Windows.

## <a name="issues-with-configuration-and-setup"></a>Problemas com configuração e configuração

Se a pasta de trabalho de configuração não estiver funcionando corretamente para automatizar a instalação, você poderá usar esses recursos para configurar seu ambiente manualmente:

- Para habilitar o diagnóstico manualmente ou acessar o espaço de trabalho Log Analytics, consulte [Enviar diagnóstico de área de trabalho virtual do Windows para log Analytics](diagnostics-log-analytics.md).
- Para instalar a extensão de Log Analytics em um host de sessão manualmente, consulte [log Analytics extensão de máquina virtual para Windows](../virtual-machines/extensions/oms-windows.md).
- Para configurar um novo espaço de trabalho Log Analytics, consulte [criar um espaço de trabalho log Analytics no portal do Azure](../azure-monitor/logs/quick-create-workspace.md).
- Para adicionar, remover ou editar contadores de desempenho, consulte [Configurando contadores de desempenho](../azure-monitor/agents/data-sources-performance-counters.md).
- Para configurar logs de eventos do Windows para um espaço de trabalho Log Analytics, consulte [coletar fontes de dados de log de eventos do Windows com o agente de log Analytics](../azure-monitor/agents/data-sources-windows-events.md).

## <a name="my-data-isnt-displaying-properly"></a>Meus dados não estão sendo exibidos corretamente

Se os dados não estiverem sendo exibidos corretamente, verifique as seguintes soluções comuns:

- Primeiro, verifique se você configurou corretamente com a pasta de trabalho de configuração, conforme descrito em [usar Azure monitor para área de trabalho virtual do Windows para monitorar a implantação](azure-monitor.md). Se você não tiver nenhum contador ou evento, os dados associados a eles não aparecerão na portal do Azure.
- Verifique suas permissões de acesso & entre em contato com os proprietários do recurso para solicitar permissões ausentes; qualquer pessoa que monitorar a área de trabalho virtual do Windows requer as seguintes permissões:
    - Acesso de leitura às assinaturas do Azure que mantêm seus recursos de área de trabalho virtual do Windows
    - Acesso de leitura aos grupos de recursos da assinatura que contêm os hosts de sessão da área de trabalho virtual do Windows 
    - Acesso de leitura a qualquer Log Analytics espaços de trabalho que você estiver usando
- Talvez seja necessário abrir portas de saída no firewall do servidor para permitir que Azure Monitor e Log Analytics enviem dados para o Portal. Para saber como fazer isso, consulte os seguintes artigos:
      - [Azure Monitor portas de saída](../azure-monitor/app/ip-addresses.md)
      - [Log Analytics requisitos de firewall](../azure-monitor/agents/log-analytics-agent.md#firewall-requirements). 
- Não está vendo dados da atividade recente? Talvez você queira aguardar 15 minutos e atualizar o feed. Azure Monitor tem um período de latência de 15 minutos para popular dados de log. Para saber mais, confira [tempo de ingestão de dados de log em Azure monitor](../azure-monitor/logs/data-ingestion-time.md).

Se você não estiver perdendo nenhuma informação, mas seus dados ainda não estiverem sendo exibidos corretamente, pode haver um problema na consulta ou nas fontes de dados. Examine [problemas e limitações conhecidos](#known-issues-and-limitations). 

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>Quero personalizar Azure Monitor para área de trabalho virtual do Windows

Azure Monitor para área de trabalho virtual do Windows usa Azure Monitor pastas de trabalho. As pastas de trabalho permitem que você salve uma cópia do modelo de pasta de trabalho de desktop virtual do Windows e faça suas próprias personalizações.

Por design, os modelos de pasta de trabalho personalizada não adotarão automaticamente as atualizações do grupo de produtos. Para obter mais informações, consulte [Solucionando problemas de insights baseados em pasta de trabalho](../azure-monitor/insights/troubleshoot-workbooks.md) e a [visão geral das pastas de trabalho](../azure-monitor/visualize/workbooks-overview.md).

## <a name="i-cant-interpret-the-data"></a>Não consigo interpretar os dados

Saiba mais sobre os termos de dados no [Glossário do Azure monitor para área de trabalho virtual do Windows](azure-monitor-glossary.md).

## <a name="the-data-i-need-isnt-available"></a>Os dados de que preciso não estão disponíveis

Se você quiser monitorar mais contadores de desempenho ou logs de eventos do Windows, poderá habilitá-los para enviar informações de diagnóstico para seu espaço de trabalho do Log Analytics e monitorá-los no **diagnóstico do host: navegador do host**. 

- Para adicionar contadores de desempenho, consulte [Configurando contadores de desempenho](../azure-monitor/agents/data-sources-performance-counters.md#configuring-performance-counters)
- Para adicionar eventos do Windows, consulte [Configurando logs de eventos do Windows](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)

Não consegue encontrar um ponto de dados para ajudar a diagnosticar um problema? Envie-nos comentários!

- Para saber como deixar comentários, consulte [visão geral da solução de problemas, comentários e suporte para área de trabalho virtual do Windows](troubleshoot-set-up-overview.md).
- Você também pode deixar comentários para a Área de Trabalho Virtual do Windows no [Hub de comentários da Área de Trabalho Virtual do Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

## <a name="known-issues-and-limitations"></a>Limitações e problemas conhecidos

A seguir estão os problemas e as limitações que estamos cientes e trabalhando para corrigir:

- Você só pode monitorar um pool de hosts de cada vez. 
- Para salvar as configurações favoritas, você precisa salvar um modelo personalizado da pasta de trabalho. Modelos personalizados não adotarão automaticamente atualizações do grupo de produtos.
- Às vezes, a pasta de trabalho de configuração mostrará erros de "falha na consulta" ao carregar suas seleções. Atualize a consulta, reinsira sua seleção, se necessário, e o erro deve ser resolvido a si mesmo. 
- Algumas mensagens de erro não são frases de maneira amigável ao usuário e nem todas as mensagens de erro são descritas na documentação do.
- O contador de desempenho total de sessões pode contar sessões por um pequeno número e seu total de sessões pode parecer ultrapassar o limite máximo de sessões.
- A contagem de sessões disponíveis não reflete as políticas de dimensionamento no pool de hosts.   
- Você vê tempos de conexão contraditórios ou inesperados? Embora seja raro, o evento de conclusão de uma conexão pode ficar ausente e pode afetar alguns visuais e métricas.
- O tempo de conexão inclui o tempo que os usuários levam para inserir suas credenciais; Isso se correlaciona com a experiência, mas em alguns casos pode mostrar picos falsos. 
    

## <a name="next-steps"></a>Próximas etapas

Se você não tiver certeza de como interpretar os dados ou desejar saber mais sobre termos comuns, confira o [Glossário do Azure monitor para área de trabalho virtual do Windows](azure-monitor-glossary.md). Se você quiser saber como configurar e usar Azure Monitor para área de trabalho virtual do Windows, consulte [usar Azure monitor para área de trabalho virtual do Windows para monitorar sua implantação](azure-monitor.md).
