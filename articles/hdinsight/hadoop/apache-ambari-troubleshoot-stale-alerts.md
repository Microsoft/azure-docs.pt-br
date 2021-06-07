---
title: Alertas obsoletos do Apache Ambari no Azure HDInsight
description: Discussão e análise de possíveis razões e soluções para alertas obsoletos do Apache Ambari no HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 22985f295e504a2d73015f1dc96064f89be47bd7
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866923"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>Cenário: alertas obsoletos do Apache Ambari no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Na interface do usuário do Apache Ambari, você pode ver um alerta como este:

:::image type="content" source="./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png" alt-text="Exemplo de alerta do Apache Ambari obsoleto" border="true":::

## <a name="cause"></a>Causa

Os agentes do Ambari monitoram continuamente a integridade de muitos recursos. Os *alertas* podem ser configurados para notificá-lo se as propriedades específicas do cluster estão dentro dos limites predeterminados. Após a execução de cada verificação de recurso, se a condição de alerta for atendida, os agentes de Ambari relatarão o status de volta para o servidor Ambari e dispararão um alerta. Se um alerta não estiver marcado de acordo com o intervalo em seu perfil de alerta, o servidor disparará um alerta de *alertas obsoletos do servidor Ambari* .

Há várias razões pelas quais uma verificação de integridade pode não ser executada em seu intervalo definido:

* Os hosts estão sob uso intenso (alto uso da CPU), para que o agente do Ambari não possa obter recursos do sistema suficientes para executar os alertas no prazo.

* O cluster está ocupado executando muitos trabalhos ou serviços durante um período de carga pesada.

* Um pequeno número de hosts no cluster hospeda vários componentes e, portanto, é necessário para executar muitos alertas. Se o número de componentes for grande, os trabalhos de alerta poderão perder seus intervalos agendados.

## <a name="resolution"></a>Resolução

Experimente os métodos a seguir para resolver problemas com alertas obsoletos do Ambari.

### <a name="increase-the-alert-interval-time"></a>Aumentar a hora do intervalo de alerta

Você pode aumentar o valor de um intervalo de alerta individual, com base no tempo de resposta e na carga do seu cluster:

1. Na interface do usuário do Apache Ambari, selecione a guia **alertas** .
1. Selecione o nome de definição de alerta desejado.
1. Na definição, selecione **Editar**.
1. Aumente o valor do **intervalo de verificação** e, em seguida, selecione **salvar**.

### <a name="increase-the-alert-interval-time-for-ambari-server-alerts"></a>Aumentar o tempo de intervalo de alerta para alertas do servidor Ambari

1. Na interface do usuário do Apache Ambari, selecione a guia **alertas** .
1. Na lista suspensa **grupos** , selecione **AMBARI padrão**.
1. Selecione o alerta de **alertas do servidor Ambari** .
1. Na definição, selecione **Editar**.
1. Aumente o valor do **intervalo de verificação** .
1. Aumente o valor **multiplicador de intervalo** e, em seguida, selecione **salvar**.

### <a name="disable-and-reenable-the-alert"></a>Desabilitar e reabilitar o alerta

Para descartar um alerta obsoleto, desabilite-o e habilite-o novamente:

1. Na interface do usuário do Apache Ambari, selecione a guia **alertas** .
1. Selecione o nome de definição de alerta desejado.
1. Na definição, selecione **habilitado** na parte extrema direita da interface do usuário.
1. Na janela pop-up de **confirmação** , selecione **confirmar desabilitar**.
1. Aguarde alguns segundos para que todas as "instâncias" de alerta mostradas na página sejam limpas.
1. Na definição, selecione **desabilitado** na parte extrema direita da interface do usuário.
1. Na janela pop-up de **confirmação** , selecione **confirmar habilitar**.

### <a name="increase-the-alert-grace-period"></a>Aumentar o período de carência do alerta

Há um período de carência antes que um agente Ambari relate que um alerta configurado perdeu sua agenda. Se o alerta perdeu sua hora agendada, mas foi executado dentro do período de carência, o alerta obsoleto não é gerado.

O `alert_grace_period` valor padrão é 5 segundos. Você pode definir essa configuração no/etc/ambari-Agent/conf/ambari-agent.ini. Para hosts nos quais os alertas obsoletos ocorrem em intervalos regulares, tente aumentar o valor para 10. Em seguida, reinicie o agente Ambari.

## <a name="next-steps"></a>Próximas etapas

Se o problema não tiver sido mencionado aqui ou se você não puder solucioná-lo, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure no [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se ao [@AzureSupport](https://twitter.com/azuresupport) no Twitter. Essa é a conta de Microsoft Azure oficial para melhorar a experiência do cliente. Ele conecta a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, envie uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Para chegar lá, selecione ajuda (**?**) no menu do portal ou abra o painel **ajuda + suporte** . Para obter mais informações, consulte [como criar uma solicitação de suporte do Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). 

  O suporte para gerenciamento de assinaturas e cobrança está incluído na sua assinatura do Microsoft Azure. O suporte técnico está disponível por meio dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).