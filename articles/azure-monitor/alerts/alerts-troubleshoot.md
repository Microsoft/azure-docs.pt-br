---
title: Solucionando problemas Azure Monitor alertas e notificações
description: Problemas comuns com alertas de Azure Monitor e soluções possíveis.
author: ofirmanor
ms.author: ofmanor
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: d65fa03a183c15022535debee6476844192ec6bf
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037839"
---
# <a name="troubleshooting-problems-in-azure-monitor-alerts"></a>Solucionando problemas em alertas de Azure Monitor

Este artigo aborda problemas comuns em Azure Monitor alertas e notificações.

Os alertas do Azure Monitor notificam você proativamente quando condições importantes são encontradas nos dados de monitoramento. Eles permitem que você identifique e resolva problemas antes que os usuários do seu sistema os percebam. Para obter mais informações sobre alertas, consulte [visão geral de alertas no Microsoft Azure](./alerts-overview.md).

Se você tiver um problema com um alerta acionando ou não acionando quando esperado, consulte os artigos abaixo. Você pode ver alertas "acionados" no portal do Azure.

- [Solução de problemas Azure Monitor alertas de métrica no Microsoft Azure](alerts-troubleshoot-metric.md)  
- [Solução de problemas Azure Monitor alertas de log no Microsoft Azure](alerts-troubleshoot-metric.md)

Se o alerta for acionado conforme o esperado de acordo com a portal do Azure, mas as notificações apropriadas não ocorrerem, use as informações no restante deste artigo para solucionar esse problema.

## <a name="action-or-notification-on-my-alert-did-not-work-as-expected"></a>A ação ou notificação em meu alerta não funcionou conforme o esperado

Se você puder ver um alerta acionado no portal do Azure, mas tiver um problema com algumas de suas ações ou notificações, consulte as seções a seguir.

## <a name="did-not-receive-expected-email"></a>Não recebeu o email esperado

Se você puder ver um alerta acionado no portal do Azure, mas não tiver recebido o email que você configurou sobre ele, siga estas etapas:

1. **O email foi suprimido por uma [regra de ação](../alerts/alerts-action-rules.md)**?

    Verifique clicando no alerta acionado no portal e examine a guia de histórico para [grupos de ações](./action-groups.md) suprimidos:

    ![Histórico de supressão da regra de ação de alerta](media/alerts-troubleshoot/history-action-rule.png)

1. **É o tipo de ação "email Azure Resource Manager função"?**

    Essa ação só examina Azure Resource Manager atribuições de função que estão no escopo da assinatura e do tipo *usuário*.  Veja se você atribuiu a função no nível de assinatura e não no nível de recurso ou grupo de recursos.

1. **Seu servidor de email e caixa de correio aceitam emails externos?**

    Verifique se os emails desses três endereços não estão bloqueados:
      - azure-noreply@microsoft.com  
      - azureemail-noreply@microsoft.com
      - alerts-noreply@mail.windowsazure.com

    É comum que listas de endereçamento internas ou listas de distribuição bloqueiem emails de endereços de email externos. Você deve permitir o email dos endereços de email acima.  
    Para testar, adicione um endereço de email de trabalho regular (não uma lista de endereçamento) ao grupo de ações e veja se os alertas chegam a esse email.

1. **O email foi processado pelas regras de caixa de entrada ou por um filtro de spam?**

    Verifique se não há nenhuma regra de caixa de entrada que exclua esses emails ou mova-os para uma pasta lateral. Por exemplo, as regras de caixa de entrada podem capturar remetentes específicos ou palavras específicas no assunto.

    Além disso, verifique:

   - as configurações de spam do seu cliente de email (como o Outlook, Gmail)
      - as configurações limites de remetente/spam/quarentena do seu servidor de email (como Exchange, Microsoft 365, G-Suite)
      - as configurações de seu dispositivo de segurança de email, se houver (como Barracuda, Cisco).

1. **Você cancelou acidentalmente a assinatura do grupo de ações?**

    Os emails de alerta fornecem um link para cancelar a assinatura do grupo de ações. Para verificar se você cancelou acidentalmente a assinatura desse grupo de ações:

    1. Abra o grupo de ações no portal e verifique a coluna status faça um dos seguintes procedimentos:

    ![coluna de status do grupo de ações](media/alerts-troubleshoot/action-group-status.png)

    2. Pesquise seu email para obter a confirmação de cancelamento da assinatura:

    ![cancelamento da assinatura do grupo de ações de alerta](media/alerts-troubleshoot/unsubscribe-action-group.png)

    Para assinar novamente – use o link no email de confirmação de cancelamento de assinatura que você recebeu ou remova o endereço de email do grupo de ações e, em seguida, adicione-o novamente. 
 
1. **Você foi classificado como limitado devido a muitos emails indo para um único endereço de email?**

    A [taxa de emails está limitada](alerts-rate-limiting.md) a 100 emails por hora para cada endereço de email. Se você passar desse limite, as notificações de email adicionais serão descartadas.  Verifique se recebeu uma mensagem indicando que o endereço sofreu limitação de taxa temporária: 
 
   ![Taxa de email limitada](media/alerts-troubleshoot/email-paused.png)

   Se você quiser receber um volume alto de notificações sem limitação por taxa, considere usar uma ação diferente, como webhook, aplicativo lógico, função do Azure ou runbooks de automação, nenhum dos quais têm limitação por taxa. 

## <a name="did-not-receive-expected-sms-voice-call-or-push-notification"></a>Não recebeu notificações de SMS, chamada de voz ou notificação por push esperadas

Se você puder ver um alerta acionado no portal, mas não tiver recebido o SMS, a chamada de voz ou a notificação por push que você configurou sobre ele, siga estas etapas: 

1. **A ação foi suprimida por uma [regra de ação](../alerts/alerts-action-rules.md)?**

    Verifique clicando no alerta acionado no portal e examine a guia de histórico para [grupos de ações](./action-groups.md) suprimidos: 

    ![Histórico de supressão da regra de ação de alerta](media/alerts-troubleshoot/history-action-rule.png)

   Se isso não for intencional, você poderá modificar, desabilitar ou excluir a regra de ação.
 
1. **SMS/Voice: seu número de telefone está correto?**

   Verifique a ação de SMS para erros de digitação no código do país ou no número de telefone.
 
1. **SMS/Voice: você recebeu a tarifa limitada?**

    Chamadas de voz e SMS têm limitações por taxa de, no máximo, uma notificação a cada cinco minutos por número de telefone. Se você ultrapassar esse limite, as notificações serão descartadas.

      - Chamada de voz: verifique seu histórico de chamadas e veja se você teve uma chamada diferente do Azure nos cinco minutos anteriores.
      - SMS: verifique o histórico de SMS em busca de uma mensagem que indique que o número de telefone sofreu uma limitação por taxa.

    Se você quiser receber um volume alto de notificações sem limitação por taxa, considere usar uma ação diferente, como webhook, aplicativo lógico, função do Azure ou runbooks de automação, nenhum dos quais têm limitação por taxa. 
 
1. **SMS: você cancelou acidentalmente a assinatura do grupo de ações?**

    Abra seu histórico de SMS e verifique se você aceitou a entrega de SMS desse grupo de ação específico (usando a resposta desabilitar action_group_short_name) ou de todos os grupos de ação (usando a resposta de parada). Para assinar novamente, envie o comando relevante do SMS (ENABLE action_group_short_name ou START) ou remova a ação SMS do grupo de ações e adicione-a novamente.  Para obter mais informações, confira [comportamento de alerta do SMS em grupos de ações](alerts-sms-behavior.md).

1. **Você bloqueou acidentalmente as notificações em seu telefone?**

   A maioria dos telefones celulares permite que você bloqueie chamadas ou SMS de números de telefone ou códigos curtos específicos ou que bloqueie notificações por push de aplicativos específicos (como o aplicativo móvel do Azure). Para verificar se você bloqueou acidentalmente as notificações em seu telefone, pesquise a documentação específica do sistema operacional e do modelo do seu telefone ou teste com um telefone e um número de telefone diferentes.

## <a name="expected-another-type-of-action-to-trigger-but-it-did-not"></a>Era esperado outro tipo de ação a ser disparada, mas ele não 
   
Se você puder ver um alerta acionado no portal, mas a ação configurada desse alerta não tiver sido disparada, siga estas etapas:

1. **A ação foi suprimida por uma regra de ação?**

    Verifique clicando no alerta acionado no portal e examine a guia de histórico para [grupos de ações](./action-groups.md) suprimidos:

    ![Histórico de supressão da regra de ação de alerta](media/alerts-troubleshoot/history-action-rule.png)
 
    Se isso não for intencional, você poderá modificar, desabilitar ou excluir a regra de ação.

1. **Um webhook não foi disparado?**

    1. **Os endereços IP de origem foram bloqueados?**
    
       Adicione os [endereços IP](./action-groups.md#action-specific-information) que o webhook é chamado na sua lista de permissões.

    1. **O ponto de extremidade do webhook funciona corretamente?**

       Verifique se o ponto de extremidade de webhook configurado está correto e se o ponto de extremidade está funcionando corretamente. Verifique seus logs do webhook ou instrumente o código dele para que você possa investigar (por exemplo, registrar o conteúdo de entrada).

    1. **Você está chamando o Slack ou o Microsoft Teams?**  
    Cada um desses pontos de extremidade espera um formato JSON específico. Siga [estas instruções](../alerts/action-groups-logic-app.md) para configurar uma ação de aplicativo lógico em vez disso.

    1. **O webhook ficou sem resposta ou retornou erros?** 

        O período de tempo limite de uma resposta de webhook é dez segundos. A chamada de webhook será repetida no máximo duas outras vezes quando os seguintes códigos de status HTTP forem retornados: 408, 429, 503, 504 ou quando o ponto de extremidade HTTP não responder. A primeira nova tentativa ocorre após 10 segundos. A segunda e última nova tentativa ocorre após 100 segundos. Se a segunda tentativa falhar, o ponto de extremidade não será chamado novamente por 30 minutos para nenhum grupo de ações.

## <a name="action-or-notification-happened-more-than-once"></a>A ação ou a notificação ocorreu mais de uma vez 

Se você tiver recebido uma notificação para um alerta (como um email ou um SMS) mais de uma vez ou se a ação do alerta (como webhook ou Azure Function) tiver sido disparada várias vezes, siga estas etapas: 

1. **É realmente o mesmo alerta?** 

    Em alguns casos, vários alertas semelhantes são acionados quase ao mesmo tempo. Portanto, pode parecer que o mesmo alerta disparou as ações dele várias vezes. Por exemplo, uma regra de alerta do log de atividades poderá ser configurada para ser acionada quando um evento for iniciado e quando tiver terminado (êxito ou falha), não filtrando pelo campo de status do evento. 

    Para verificar se essas ações ou notificações vieram de alertas diferentes, examine os detalhes do alerta, tais como o respectivo carimbo de data/hora e a ID do alerta ou, alternativamente a ela, a ID de correlação dele. Como alternativa, verifique a lista de alertas acionados no portal. Se esse for o caso, você precisaria adaptar a lógica da regra de alerta ou configurar a origem do alerta. 

1. **A ação é repetida em vários grupos de ação?** 

    Quando um alerta é acionado, cada um dos grupos de ações dele é processado de maneira independente. Portanto, se uma ação (como um endereço de email) aparecer em vários grupos de ações disparados, ela será chamada uma vez por grupo de ações. 

    Para verificar quais grupos de ações foram disparados, verifique a guia de histórico de alertas. Você veria nela ambos os grupos de ações definidos na regra de alerta e os grupos de ações adicionados ao alerta por regras de ação: 

    ![Ação repetida em vários grupos de ação](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)

## <a name="action-or-notification-has-an-unexpected-content"></a>A ação ou notificação tem um conteúdo inesperado

Se você tiver recebido o alerta, mas acredita que alguns de seus campos estão ausentes ou incorretos, siga estas etapas: 

1. **Você selecionou o formato correto para a ação?** 

    Cada tipo de ação (email, webhook, etc.) tem dois formatos: o padrão, o formato herdado e o [formato de esquema comum mais recente](../alerts/alerts-common-schema.md). Ao criar um grupo de ações, especifique o formato desejado por ação – ações diferentes nos grupos de ações podem ter formatos diferentes. 

    Por exemplo, para ação de webhook: 

    ![opção de esquema de ação de webhook](media/alerts-troubleshoot/webhook.png)

    Verifique se o formato especificado no nível de ação é o que você espera. Por exemplo, você pode ter desenvolvido código que responde a alertas (webhook, função, aplicativo lógico etc.), esperando um formato, mas posteriormente na ação, você ou outra pessoa especificou um formato diferente.  

    Além disso, verifique o formato de carga (JSON) para [alertas do log de atividades](../alerts/activity-log-alerts-webhook.md), para [alertas de pesquisa de logs](../alerts/alerts-log-webhook.md) (Application Insights e análise de logs), para [de alertas de métrica](alerts-metric-near-real-time.md#payload-schema), para o [esquema de alerta comum](../alerts/alerts-common-schema-definitions.md) e para os [alertas de métrica clássico](./alerts-webhooks.md) preteridos.

 
1. **Alertas do log de atividades: as informações estão disponíveis no log de atividades?** 

    Os [alertas do log de atividades](./activity-log-alerts.md) são alertas baseados em eventos gravados no log de atividades do Azure, como eventos sobre como criar, atualizar ou excluir recursos do Azure, eventos de integridade do serviço e integridade do recurso ou conclusões do Azure Advisor e Azure Policy. Se você tiver recebido um alerta com base no log de atividades, mas alguns campos necessários estiverem ausentes ou incorretos, primeiro verifique os eventos no próprio log de atividades. Se o recurso do Azure não gravar os campos que você está procurando em seu evento do log de atividades, esses campos não serão incluídos no alerta correspondente. 

## <a name="action-rule-is-not-working-as-expected"></a>A regra de ação não está funcionando conforme o esperado 

Se você puder ver um alerta acionado no portal, mas uma regra de ação relacionada não tiver funcionado conforme esperado, siga estas etapas: 

1. **A regra de ação está habilitada?** 

    Verifique a coluna de status da regra de ação para verificar se a função de ação relacionada está habilitada. 

    ![gráfico](media/alerts-troubleshoot/action-rule-status.png) 

    Se ela não estiver habilitada, você poderá habilitar a regra de ação selecionando-a e clicando em Habilitar. 

1. **É um alerta de integridade do serviço?** 

    Os alertas de integridade do serviço (serviço de monitoramento = "Service Health") não são afetados pelas regras de ação. 

1. **A regra de ação agiu após receber seu alerta?** 

    Verifique se a regra de ação processou o alerta clicando no alerta acionado no portal e examinando a guia de histórico.

    Aqui está um exemplo de regra de ação suprimindo todos os grupos de ações: 
 
     ![Histórico de supressão da regra de ação de alerta](media/alerts-troubleshoot/history-action-rule.png)

    Aqui está um exemplo de uma regra de ação que adiciona outro grupo de ações:

    ![Ação repetida em vários grupos de ação](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)
 

1. **O escopo e o filtro da regra de ação correspondem ao alerta acionado?** 

    Se você considerar que a regra de ação deveria ter sido disparada mas não foi ou que não deveria ter sido disparada mas foi, examine cuidadosamente as condições de filtro e de escopo da regra de ação em comparação com as propriedades do alerta acionado. 


## <a name="how-to-find-the-alert-id-of-a-fired-alert"></a>Como localizar a ID de alerta de um alerta acionado

Ao abrir um caso sobre um alerta acionado específico (como – se você não recebeu sua notificação por email), será necessário fornecer a ID do alerta. 

Para localizá-lo, siga estas etapas:

1. Na portal do Azure, navegue até a lista de alertas acionados e encontre esse alerta específico. Você pode usar os filtros para ajudá-lo a localizá-lo. 

1. Clique no alerta para abrir os detalhes do alerta. 

1. Role para baixo nos campos de alerta da primeira guia (a guia Resumo) até localizá-lo e copie-o. Esse campo também inclui um botão auxiliar "copiar para a área de transferência" que você pode usar.  

    ![Localizar ID do alerta](media/alerts-troubleshoot/get-alert-id.png)

## <a name="problem-creating-updating-or-deleting-action-rules-in-the-azure-portal"></a>Problema ao criar, atualizar ou excluir regras de ação no portal do Azure

Se você recebeu um erro ao tentar criar, atualizar ou excluir uma [regra de ação](../alerts/alerts-action-rules.md), siga estas etapas: 

1. **Você recebeu um erro de permissão?**  

    Você deve ter a [função interna de colaborador de monitoramento](../../role-based-access-control/built-in-roles.md#monitoring-contributor)ou as permissões específicas relacionadas a regras de ação e alertas.

1. **Você verificou os parâmetros da regra de ação?**  

    Verifique a [documentação da regra de ação](../alerts/alerts-action-rules.md)ou o comando [set-AzActionRule do PowerShell da regra de ação](/powershell/module/az.alertsmanagement/set-azactionrule) . 


## <a name="next-steps"></a>Próximas etapas
- Se estiver usando um alerta de log, consulte também [Solucionando problemas de alertas de log](./alerts-troubleshoot-log.md).
- Volte para a [portal do Azure](https://portal.azure.com) para verificar se você resolveu seu problema com as diretrizes acima