---
title: Configurar logs de consulta lentos-portal do Azure-banco de dados do Azure para MySQL-servidor flexível
description: Este artigo descreve como configurar e acessar os logs de consulta lentos no banco de dados do Azure para MySQL servidor flexível do portal do Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: a61c8e3451d661dae2e5ad56a0d4a947252ec873
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94540056"
---
# <a name="configure-and-access-slow-query-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Configurar e acessar logs de consulta lentos para o banco de dados do Azure para MySQL-servidor flexível usando o portal do Azure

> [!IMPORTANT]
> O Banco de Dados do Azure para MySQL – Servidor Flexível está atualmente na versão prévia pública.

Você pode configurar, listar e baixar os [logs de consulta lenta](concepts-slow-query-logs.md) do banco de dados do Azure para MySQL do servidor flexível do portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos
As etapas neste artigo exigem que você tenha um [servidor flexível](quickstart-create-server-portal.md).

## <a name="configure-logging"></a>Configurar o registro em log
Configure o acesso aos logs de consulta lenta do MySQL. 

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Selecione seu servidor flexível.

1. Na seção **configurações** na barra lateral, selecione **parâmetros do servidor**.
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/server-parameters.png" alt-text="Página de parâmetros do servidor.":::

1. Atualize o parâmetro **slow_query_log** como **ativado**.
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/slow-query-log-enable.png" alt-text="Ative logs de consulta lentos.":::

1. Altere quaisquer outros parâmetros necessários (por exemplo, `long_query_time`, `log_slow_admin_statements`). Consulte os documentos de [logs de consulta lenta](./concepts-slow-query-logs.md#configure-slow-query-logging) para obter mais parâmetros.  
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/long-query-time.png" alt-text="Atualizar parâmetros relacionados ao log de consulta lento.":::

1. Clique em **Salvar**. 
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/save-parameters.png" alt-text="Salvar parâmetros de log de consulta lento.":::

Na página **parâmetros do servidor** , você pode retornar à lista de logs fechando a página.

## <a name="set-up-diagnostics"></a>Configurar o diagnóstico

Logs de consulta lentos são integrados com Azure Monitor configurações de diagnóstico para permitir que você direcione seus logs para Azure Monitor logs, hubs de eventos ou armazenamento do Azure.

1. Na seção **monitoramento** na barra lateral, selecione **configurações de diagnóstico**  >  **Adicionar configurações de diagnóstico**.

   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/add-diagnostic-setting.png" alt-text="Captura de tela das opções de configurações de diagnóstico":::

1. Forneça um nome de configuração de diagnóstico.

1. Especifique quais destinos enviarão os logs de consulta lentos (conta de armazenamento, Hub de eventos ou espaço de trabalho Log Analytics).

1. Selecione **MySqlSlowLogs** como o tipo de log.
    :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/configure-diagnostic-setting.png" alt-text="Captura de tela das opções de configuração das configurações de diagnóstico":::

1. Depois de configurar os coletores de dados para canalizar os logs de consulta lentos, selecione **salvar**.
    :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/save-diagnostic-setting.png" alt-text="Captura de tela das opções de configuração de configurações de diagnóstico, com salvar realçado":::

1. Acesse os logs de consulta lento explorando-os nos coletores de dados que você configurou. Pode levar até 10 minutos para que os logs sejam exibidos.

Se você canalizasse seus logs para Azure Monitor logs (Log Analytics), consulte algumas [consultas de exemplo](concepts-slow-query-logs.md#analyze-logs-in-azure-monitor-logs) que você pode usar para análise. 

## <a name="next-steps"></a>Próximas etapas
<!-- - See [Access slow query Logs in CLI](howto-configure-server-logs-in-cli.md) to learn how to download slow query logs programmatically.-->
- Saiba mais sobre [logs de consulta lentos](concepts-slow-query-logs.md)
- Para obter mais informações sobre as definições de parâmetro e o log do MySQL, consulte a documentação do MySQL em [logs](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).
- Saiba mais sobre [os logs de auditoria](concepts-audit-logs.md)
