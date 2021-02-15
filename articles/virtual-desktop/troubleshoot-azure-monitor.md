---
title: Solucionar problemas de monitorar o Windows Virtual Desktop Preview-Azure
description: Como solucionar problemas com Azure Monitor para área de trabalho virtual do Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1818dc558ba45e318b71e1443556cc48feaede8b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367666"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop-preview"></a>Solucionar problemas de Azure Monitor para área de trabalho virtual do Windows (versão prévia)

>[!IMPORTANT]
>O Azure Monitor para área de trabalho virtual do Windows está atualmente em visualização pública. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendamos usá-la para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo apresenta problemas conhecidos e soluções para problemas comuns em Azure Monitor para área de trabalho virtual do Windows (versão prévia).

## <a name="issues-with-configuration-and-setup"></a>Problemas com configuração e configuração

Se a pasta de trabalho de configuração não estiver funcionando corretamente para automatizar a instalação, você poderá usar esses recursos para configurar seu ambiente manualmente:

- Para habilitar o diagnóstico manualmente ou acessar o espaço de trabalho Log Analytics, consulte [Enviar diagnóstico de área de trabalho virtual do Windows para log Analytics](diagnostics-log-analytics.md).
- Para instalar a extensão de Log Analytics em um host manualmente, consulte [log Analytics extensão de máquina virtual para Windows](../virtual-machines/extensions/oms-windows.md).
- Para configurar um novo espaço de trabalho Log Analytics, consulte [criar um espaço de trabalho log Analytics no portal do Azure](../azure-monitor/learn/quick-create-workspace.md).
- Para adicionar ou remover contadores de desempenho, consulte [Configurando contadores de desempenho](../azure-monitor/platform/data-sources-performance-counters.md).
- Para configurar eventos para um espaço de trabalho Log Analytics, consulte [coletar fontes de dados de log de eventos do Windows com o agente de log Analytics](../azure-monitor/platform/data-sources-windows-events.md).

## <a name="my-data-isnt-displaying-properly"></a>Meus dados não estão sendo exibidos corretamente

Se os dados não estiverem sendo exibidos corretamente, verifique sua configuração, permissões e verifique se os endereços IP necessários estão desbloqueados. 

- Primeiro, verifique se você preencheu todos os campos na pasta de trabalho de configuração, conforme descrito em [usar Azure monitor para área de trabalho virtual do Windows para monitorar a implantação](azure-monitor.md). Se você não tiver nenhum contador ou evento, os dados associados a eles não aparecerão na portal do Azure.

- Verifique suas permissões de acesso & entre em contato com os proprietários do recurso para solicitar permissões ausentes; qualquer pessoa que monitorar a área de trabalho virtual do Windows requer as seguintes permissões:

    - Acesso de leitura às assinaturas do Azure que mantêm seus recursos de área de trabalho virtual do Windows
    - Acesso de leitura aos grupos de recursos da assinatura que contêm os hosts de sessão da área de trabalho virtual do Windows 
    - Acesso de leitura ao espaço de trabalho do Log Analytics

- Talvez seja necessário abrir portas de saída no firewall do servidor para permitir que Azure Monitor enviem dados para o portal, consulte [portas de saída](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses). 

- Não está vendo dados da atividade recente? Talvez você queira aguardar 15 minutos e atualizar o feed. Azure Monitor tem um período de latência de 15 minutos para popular dados de log. Para saber mais, confira [tempo de ingestão de dados de log em Azure monitor](../azure-monitor/platform/data-ingestion-time.md).

Se você não estiver perdendo nenhuma informação, mas seus dados ainda não estiverem sendo exibidos corretamente, pode haver um problema na consulta ou nas fontes de dados. Examine nossos problemas e limitações conhecidos. 

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>Quero personalizar Azure Monitor para área de trabalho virtual do Windows

Azure Monitor para área de trabalho virtual do Windows usa Azure Monitor pastas de trabalho. As pastas de trabalho permitem que você salve uma cópia do modelo de pasta de trabalho de desktop virtual do Windows e faça suas próprias personalizações.

Por design, os modelos de pasta de trabalho personalizada não adotarão automaticamente as atualizações do grupo de produtos. Para obter mais informações, consulte [Solucionando problemas de insights baseados em pasta de trabalho](../azure-monitor/insights/troubleshoot-workbooks.md) e a [visão geral das pastas de trabalho](../azure-monitor/platform/workbooks-overview.md).

## <a name="i-cant-interpret-the-data"></a>Não consigo interpretar os dados

Saiba mais sobre os termos de dados no [Glossário do Azure monitor para área de trabalho virtual do Windows](azure-monitor-glossary.md).

## <a name="the-data-i-need-isnt-available"></a>Os dados de que preciso não estão disponíveis

Se você quiser monitorar mais contadores de desempenho ou eventos, poderá habilitá-los para enviar ao espaço de trabalho do Log Analytics e monitorá-los no diagnóstico do host: navegador de host. 

- Para adicionar contadores de desempenho, consulte [Configurando contadores de desempenho](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#configuring-performance-counters)
- Para adicionar eventos do Windows, consulte [Configurando logs de eventos do Windows](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events#configuring-windows-event-logs)

Não consegue encontrar um ponto de dados para ajudar a diagnosticar um problema? Envie-nos comentários!

- Para saber como deixar comentários, consulte [visão geral da solução de problemas, comentários e suporte para área de trabalho virtual do Windows](troubleshoot-set-up-overview.md).
- Você também pode deixar comentários para a área de trabalho virtual do Windows no [Hub de comentários da área de trabalho virtual do Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app) ou no [nosso fórum UserVoice](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

## <a name="known-issues-and-limitations"></a>Limitações e problemas conhecidos

Esses são problemas e limitações que estamos cientes e trabalhando para corrigir:

- Você só pode monitorar um pool de hosts de cada vez. 

- Para salvar as configurações favoritas, você precisa salvar um modelo personalizado da pasta de trabalho. Modelos personalizados não adotarão automaticamente atualizações do grupo de produtos.

- Algumas mensagens de erro não são fraseas de forma amigável e nem todas as mensagens de erro são descritas na documentação do.

- O contador de desempenho total de sessões pode contar sessões por um pequeno número e seu total de sessões pode parecer ultrapassar o limite máximo de sessões.

- A contagem de sessões disponíveis não reflete as políticas de dimensionamento no pool de hosts. 
    
- Embora seja raro, o evento de conclusão de uma conexão pode ficar ausente e isso pode afetar alguns visuais como conexões ao longo do tempo e o status da conexão do usuário.  
    
- A pasta de trabalho de configuração só dá suporte à configuração de hosts na mesma região que seu grupo de recursos. 

- O tempo de conexão inclui o tempo que os usuários levam para inserir suas credenciais; Isso se correlaciona com a experiência, mas em alguns casos pode mostrar picos falsos. 
    

## <a name="next-steps"></a>Próximas etapas

Se você não tiver certeza de como interpretar os dados ou desejar saber mais sobre termos comuns, confira o [Glossário do Azure monitor para área de trabalho virtual do Windows](azure-monitor-glossary.md). Se você quiser saber como configurar e usar Azure Monitor para área de trabalho virtual do Windows, consulte [usar Azure monitor para área de trabalho virtual do Windows para monitorar sua implantação](azure-monitor.md).