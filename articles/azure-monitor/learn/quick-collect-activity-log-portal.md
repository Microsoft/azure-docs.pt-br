---
title: Enviar o log de atividades do Azure para o workspace do Log Analytics usando o portal do Azure
description: Use o portal do Azure para criar um workspace do Log Analytics e uma configuração de diagnóstico para enviar o log de atividades para os Logs do Azure Monitor.
ms.subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: 47cf0a1f1d40b9e8358d6bdb4a3ea6a501b67da2
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91439212"
---
# <a name="send-azure-activity-log-to-log-analytics-workspace-using-azure-portal"></a>Enviar o log de atividades do Azure para o workspace do Log Analytics usando o portal do Azure
O log de atividades é um log de plataforma no Azure que fornece insights sobre eventos no nível de assinatura. Isso inclui informações como quando um recurso é modificado ou quando uma máquina virtual é iniciada. Veja o log de atividades no portal do Azure ou recupere as entradas com o PowerShell e a CLI. Este guia de início rápido mostra como usar o portal do Azure para criar um workspace do Log Analytics e uma configuração de diagnóstico para enviar o log de atividades para os Logs do Azure Monitor, em que você poderá analisá-los usando [consultas de log](../log-query/log-query-overview.md) e habilitar outros recursos, como [alertas de log](../platform/alerts-log-query.md) e [pastas de trabalho](../platform/workbooks-overview.md). 

## <a name="sign-in-to-azure-portal"></a>Entre no portal do Azure
Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com). 



## <a name="create-a-log-analytics-workspace"></a>Criar um espaço de trabalho do Log Analytics
No portal do Azure, procure e selecione **Workspaces do Log Analytics**. 

![Captura de tela do portal do Azure que mostra "workspaces do log analytics" na caixa de pesquisa e os workspaces do Log Analytics realçados em Serviços.](media/quick-create-workspace/azure-portal-01.png)
  
Clique em **Adicionar** e forneça valores para o **Grupo de recursos**, o workspace **Nome** e a **Localização**. O nome do workspace precisa ser exclusivo em todas as assinaturas do Azure.

![Criar workspace](media/quick-collect-activity-log/create-workspace.png)

Clique em **Examinar + criar** para examinar as configurações e em **Criar** para criar o workspace. Isso selecionará o tipo de preço padrão **Pagamento Conforme o Uso**, o que não vai gerar nenhum custo até você começar a coletar um volume suficiente de dados. Não há nenhum custo para a coleta do log de atividades.


## <a name="create-diagnostic-setting"></a>Criar configuração de diagnóstico
No portal do Azure, procure e selecione **Monitor**. 

![Captura de tela do portal do Azure com "monitor" na caixa de pesquisa e Monitor realçado em Serviços.](media/quick-collect-activity-log/azure-portal-monitor.png)

Selecione **Log de atividades**. Você verá os eventos recentes da assinatura atual. Clique em **Configurações de diagnóstico** para ver as configurações de diagnóstico da assinatura.

![Log de atividades](media/quick-collect-activity-log/activity-log.png)

Clique em **Adicionar configuração de diagnóstico** para criar uma configuração. 

![Criar configuração de diagnóstico](media/quick-collect-activity-log/create-diagnostic-setting.png)

Digite um nome como *Enviar Log de atividades para o workspace*. Selecione cada uma das categorias. Escolha **Enviar para o Log Analytics** como o único destino e especifique o workspace criado. Clique em **Salvar** para criar a configuração de diagnóstico e feche a página.

![Nova configuração de diagnóstico](media/quick-collect-activity-log/new-diagnostic-setting.png)

## <a name="generate-log-data"></a>Gerar dados de log
Somente as novas entradas do log de atividades serão enviadas ao workspace do Log Analytics; portanto, execute algumas ações na sua assinatura que serão registradas em log, como iniciar ou parar uma máquina virtual ou criar ou modificar outro recurso. Talvez seja necessário aguardar alguns minutos para que a configuração de diagnóstico seja criada e os dados sejam gravados inicialmente no workspace. Após esse atraso, todos os eventos gravados no log de atividades serão enviados ao workspace em alguns segundos.

## <a name="retrieve-data-with-a-log-query"></a>Recuperar dados com uma consulta de log

Selecione **Logs** no menu do **Azure Monitor**. Feche a página **Consultas de exemplo**. Se o escopo não estiver definido como o workspace criado, clique em **Selecionar escopo** e localize-o.

![Escopo do Log Analytics](media/quick-collect-activity-log/log-analytics-scope.png)

Na janela de consulta, digite `AzureActivity` e clique em **Executar**. Essa é uma consulta simples que retorna todos os registros da tabela *AzureActivity*, que contém todos os registros enviados do log de atividades.

![Consulta simples](media/quick-collect-activity-log/query-01.png)

Expanda um dos registros para ver as propriedades detalhadas.

![Expandir propriedades](media/quick-collect-activity-log/expand-properties.png)

Experimente usar uma consulta mais complexa, como `AzureActivity | summarize count() by CategoryValue`, que fornece uma contagem de eventos resumidos pela categoria.

![Consulta complexa](media/quick-collect-activity-log/query-02.png)


## <a name="next-steps"></a>Próximas etapas
Neste guia de início rápido, você configurou o log de atividades para ser enviado a um workspace do Log Analytics. Agora você pode configurar outros dados a serem coletados no workspace, em que você poderá analisá-los juntos usando [consultas de log](../log-query/log-query-overview.md) no Azure Monitor e aproveitar recursos como [alertas de log](../platform/alerts-log-query.md) e [pastas de trabalho](../platform/workbooks-overview.md). Em seguida, reúna os [logs de recursos](../platform/resource-logs.md) dos seus recursos do Azure, que complementam os dados do log de atividades, fornecendo insights sobre as operações que foram executadas em cada recurso.


> [!div class="nextstepaction"]
> [Coletar e analisar logs de recursos com o Azure Monitor](tutorial-resource-logs.md)
