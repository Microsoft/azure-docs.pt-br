---
title: Solucionar problemas de monitorar o Windows Virtual Desktop Preview-Azure
description: Como solucionar problemas com Azure Monitor para área de trabalho virtual do Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 91cf6729911cdb674c5451f172e76a2e9d5943e4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466031"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop-preview"></a>Solucionar problemas de Azure Monitor para área de trabalho virtual do Windows (versão prévia)

>[!IMPORTANT]
>O Azure Monitor para área de trabalho virtual do Windows está atualmente em visualização pública. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendamos usá-la para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo apresenta problemas conhecidos e soluções para problemas comuns em Azure Monitor para área de trabalho virtual do Windows (versão prévia).

## <a name="the-configuration-workbook-isnt-working-properly"></a>A pasta de trabalho de configuração não está funcionando corretamente

Se a pasta de trabalho de configuração do Azure Monitor não estiver funcionando, você poderá usar esses recursos para definir suas partes manualmente:

- Para habilitar o diagnóstico manualmente ou acessar o espaço de trabalho Log Analytics, consulte [Enviar diagnóstico de área de trabalho virtual do Windows para log Analytics](diagnostics-log-analytics.md).
- Para instalar a extensão de Log Analytics em um host manualmente, consulte [log Analytics extensão de máquina virtual para Windows](../virtual-machines/extensions/oms-windows.md).
- Para configurar um novo espaço de trabalho Log Analytics, consulte [criar um espaço de trabalho log Analytics no portal do Azure](../azure-monitor/learn/quick-create-workspace.md).
- Para adicionar ou remover contadores de desempenho, consulte [Configurando contadores de desempenho](../azure-monitor/platform/data-sources-performance-counters.md).
- Para configurar eventos para um espaço de trabalho Log Analytics, consulte [coletar fontes de dados de log de eventos do Windows com o agente de log Analytics](../azure-monitor/platform/data-sources-windows-events.md).

Como alternativa, o problema pode ser causado por uma falta de recursos ou por não ter as permissões necessárias.

Se a assinatura não tiver nenhum recurso de área de trabalho virtual do Windows, ela não aparecerá no parâmetro de *assinatura* .

Se você não tiver acesso de leitura às assinaturas corretas, elas não aparecerão no parâmetro de *assinatura* e você não verá seus dados no painel. Para resolver esse problema, entre em contato com o proprietário da assinatura e solicite acesso de leitura.

## <a name="my-data-isnt-displaying-properly"></a>Meus dados não estão sendo exibidos corretamente

Se os dados não estiverem sendo exibidos corretamente, algo pode ter ocorrido durante o processo de configuração de Azure Monitor. Primeiro, verifique se você preencheu todos os campos na pasta de trabalho de configuração, conforme descrito em [usar Azure monitor para área de trabalho virtual do Windows para monitorar a implantação](azure-monitor.md). Você pode alterar as configurações para ambientes novos e existentes a qualquer momento. Se você não tiver nenhum contador ou evento, os dados associados a eles não aparecerão na portal do Azure.

Se você não estiver perdendo nenhuma informação, mas seus dados ainda não estiverem sendo exibidos corretamente, pode haver um problema na consulta ou nas fontes de dados. 

Se você não vir nenhum erro de instalação e ainda não vir os dados esperados, convém aguardar 15 minutos e atualizar o feed. Azure Monitor tem um período de latência de 15 minutos para popular os dados de log. Para saber mais, confira [tempo de ingestão de dados de log em Azure monitor](../azure-monitor/platform/data-ingestion-time.md).

Por fim, se você não estiver perdendo nenhuma informação, mas seus dados ainda não aparecerem, pode haver um problema na consulta ou nas fontes de dados. Talvez seja necessário entrar em contato com o suporte para resolver o problema, se esse for o caso.

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>Quero personalizar Azure Monitor para área de trabalho virtual do Windows

Azure Monitor para área de trabalho virtual do Windows usa Azure Monitor pastas de trabalho. As pastas de trabalho permitem que você salve uma cópia do modelo de pasta de trabalho de desktop virtual do Windows e faça suas próprias personalizações.

Os modelos personalizados não serão atualizados quando o grupo de produtos atualizar o modelo original. Isso é por design na ferramenta de pastas de trabalho, você precisará salvar uma cópia do modelo atualizado e recriar suas personalizações para adotar atualizações. Para obter mais informações, consulte [Solucionando problemas de insights baseados em pasta de trabalho](../azure-monitor/insights/troubleshoot-workbooks.md) e a [visão geral das pastas de trabalho](../azure-monitor/platform/workbooks-overview.md).

## <a name="i-cant-interpret-the-data"></a>Não consigo interpretar os dados

Saiba mais sobre os termos de dados no [Glossário do Azure monitor para área de trabalho virtual do Windows](azure-monitor-glossary.md).

## <a name="the-data-i-need-isnt-available"></a>Os dados de que preciso não estão disponíveis

Não consegue encontrar um ponto de dados para ajudar a diagnosticar um problema? Envie-nos comentários!

- Para saber como deixar comentários, consulte [visão geral da solução de problemas, comentários e suporte para área de trabalho virtual do Windows](troubleshoot-set-up-overview.md).
- Você também pode deixar comentários para a área de trabalho virtual do Windows no [Hub de comentários da área de trabalho virtual do Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app) ou no [nosso fórum UserVoice](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

## <a name="known-issues"></a>Problemas conhecidos

Esses são os problemas que estamos cientes e que estão trabalhando para corrigir:

- No momento, você pode selecionar apenas uma assinatura, grupo de recursos e pool de hosts para monitorar de cada vez. Por isso, ao usar a página relatórios do usuário para entender a experiência de um usuário, você precisa verificar se você tem o pool de hosts correto que o usuário está usando ou seus dados não preencherão os elementos visuais.

- No momento, não é possível salvar as configurações favoritas no Azure Monitor, a menos que você salve um modelo personalizado da pasta de trabalho. Isso significa que os administradores de ti precisarão inserir o nome da assinatura, os nomes do grupo de recursos e as preferências do pool de hosts toda vez que abrirem Azure Monitor para a área de trabalho virtual do Windows.

- Atualmente, não há uma maneira de exportar dados de Azure Monitor para a área de trabalho virtual do Windows para o Excel.

- Todos os alertas de severidade 1 Azure Monitor para todos os produtos na assinatura selecionada aparecerão na página Visão geral. Isso é por design, pois os alertas de outros produtos na assinatura podem afetar a área de trabalho virtual do Windows. No momento, a consulta é limitada a alertas de severidade 1, excluindo alertas de severidade 0 de alta prioridade da página Visão geral.

- Algumas mensagens de erro não são fraseas de forma amigável e nem todas as mensagens de erro são descritas na documentação do.

## <a name="next-steps"></a>Próximas etapas

Se você não tiver certeza de como interpretar os dados ou desejar saber mais sobre termos comuns, confira o [Glossário do Azure monitor para área de trabalho virtual do Windows](azure-monitor-glossary.md). Se você quiser saber como configurar e usar Azure Monitor para área de trabalho virtual do Windows, consulte [usar Azure monitor para área de trabalho virtual do Windows para monitorar sua implantação](azure-monitor.md).