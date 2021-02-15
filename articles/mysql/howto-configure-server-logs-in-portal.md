---
title: Acessar logs de consulta lentos-portal do Azure-banco de dados do Azure para MySQL
description: Este artigo descreve como configurar e acessar os logs lentos no banco de dados do Azure para MySQL no portal do Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 4/13/2020
ms.openlocfilehash: 5ad4ffa99a7af592e3e93e53673d254956807c40
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541616"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>Configurar e acessar logs de consulta lentos no portal do Azure

Você pode configurar, listar e baixar os [logs de consulta lenta do banco de dados do Azure para MySQL](concepts-server-logs.md) do portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos
As etapas neste artigo exigem que você tenha o [banco de dados do Azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md).

## <a name="configure-logging"></a>Configurar o registro em log
Configure o acesso aos logs de consulta lenta do MySQL. 

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Selecione seu servidor de Banco de Dados do Azure para MySQL.

3. Na seção **monitoramento** na barra lateral, selecione **logs do servidor**. 
   :::image type="content" source="./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png" alt-text="Captura de tela de opções de logs do servidor":::

4. Para ver os parâmetros do servidor, selecione **clique aqui para habilitar logs e configurar parâmetros de log**.

5. Ative **slow_query_log** para **ativado**.

6. Selecione para onde os logs são gerados usando **log_output**. Para enviar logs para o armazenamento local e Azure Monitor logs de diagnóstico, selecione **arquivo**. 

7. Altere quaisquer outros parâmetros necessários. 

8. Selecione **Salvar**. 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="Captura de tela de parâmetros de log de consulta lentos e salvar.":::

Na página **parâmetros do servidor** , você pode retornar à lista de logs fechando a página.

## <a name="view-list-and-download-logs"></a>Exibir a lista e baixar os logs
Após o início do log, você pode exibir uma lista de logs de consultas lentas disponíveis e baixar arquivos de log individuais.

1. Abra o portal do Azure.

2. Selecione seu servidor de Banco de Dados do Azure para MySQL.

3. Na seção **monitoramento** na barra lateral, selecione **logs do servidor**. A página mostra uma lista de seus arquivos de log.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/4-server-logs-list.png" alt-text="Captura de tela da página de logs do servidor, com a lista de logs realçada":::

   > [!TIP]
   > A convenção de nomenclatura do log é **mysql-slow-< nome do seu servidor>-aaaammddhh.log**. A data e a hora usadas no nome de arquivo são a hora em que o log foi emitido. Os arquivos de log são girados a cada 24 horas ou 7,5 GB, o que vier primeiro. 

4. Se necessário, use a caixa de pesquisa para restringir rapidamente um log específico, com base na data e hora. A pesquisa busca o nome do log.

5. Para baixar arquivos de log individuais, selecione o ícone de seta para baixo ao lado de cada arquivo de log na linha da tabela.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/5-download.png" alt-text="Captura de tela da página de logs do servidor, com o ícone de seta para baixo realçado":::

## <a name="set-up-diagnostic-logs"></a>Configuração dos logs de diagnóstico

1. Na seção **monitoramento** na barra lateral, selecione **configurações de diagnóstico**  >  **Adicionar configurações de diagnóstico**.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png" alt-text="Captura de tela das opções de configurações de diagnóstico":::

1. Forneça um nome de configuração de diagnóstico.

1. Especifique quais coletores de dados enviarão os logs de consulta lentos (conta de armazenamento, Hub de eventos ou espaço de trabalho Log Analytics).

1. Selecione **MySqlSlowLogs** como o tipo de log.
:::image type="content" source="./media/howto-configure-server-logs-in-portal/configure-diagnostic-setting.png" alt-text="Captura de tela das opções de configuração das configurações de diagnóstico":::

1. Depois de configurar os coletores de dados para canalizar os logs de consulta lentos, selecione **salvar**.
:::image type="content" source="./media/howto-configure-server-logs-in-portal/save-diagnostic-setting.png" alt-text="Captura de tela das opções de configuração de configurações de diagnóstico, com salvar realçado":::

1. Acesse os logs de consulta lento explorando-os nos coletores de dados que você configurou. Pode levar até 10 minutos para que os logs sejam exibidos.

## <a name="next-steps"></a>Próximas etapas
- Consulte [acessar logs de consulta lentos na CLI](howto-configure-server-logs-in-cli.md) para saber como baixar logs de consulta lentos programaticamente.
- Saiba mais sobre [logs de consulta lentos](concepts-server-logs.md) no banco de dados do Azure para MySQL.
- Para obter mais informações sobre as definições de parâmetro e o log do MySQL, consulte a documentação do MySQL em [logs](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).