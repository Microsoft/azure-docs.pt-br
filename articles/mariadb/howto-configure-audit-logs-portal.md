---
title: Acessar logs de auditoria-portal do Azure-banco de dados do Azure para MariaDB
description: Este artigo descreve como configurar e acessar os logs de auditoria no banco de dados do Azure para MariaDB no portal do Azure.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: how-to
ms.date: 6/24/2020
ms.openlocfilehash: ec0791b169f667a2828087c37ea8f01fc3351bf7
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98663138"
---
# <a name="configure-and-access-audit-logs-in-the-azure-portal"></a>Configurar e acessar os logs de auditoria no portal do Azure

Você pode configurar o [banco de dados do Azure para MariaDB logs de auditoria](concepts-audit-logs.md) e configurações de diagnóstico do portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia de instruções, você precisa:

- [Banco de dados do Azure para servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Configurar o log de auditoria

>[!IMPORTANT]
> É recomendável registrar apenas os tipos de eventos e os usuários necessários para fins de auditoria para garantir que o desempenho do servidor não seja muito afetado.

Habilite e configure o log de auditoria.

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Selecione o servidor do Banco de Dados do Azure para MariaDB.

1. Na seção **configurações** na barra lateral, selecione **parâmetros do servidor**.
    ![Parâmetros do servidor](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Atualize o parâmetro **audit_log_enabled** como ativado.
    ![Habilitar logs de auditoria](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Selecione os [tipos de eventos](concepts-audit-logs.md#configure-audit-logging) a serem registrados atualizando o parâmetro **audit_log_events** .
    ![Eventos do log de auditoria](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Adicione os usuários do MariaDB a serem excluídos do log atualizando o parâmetro **audit_log_exclude_users** . Especifique os usuários fornecendo seu nome de usuário MariaDB.
    ![Log de auditoria excluir usuários](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. Depois que você alterar os parâmetros, clique em **Salvar**. Ou então, você pode **Descartar** suas alterações.
    ![Salvar](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Configuração dos logs de diagnóstico

1. Na seção **monitoramento** na barra lateral, selecione **configurações de diagnóstico**.

1. Clique em "+ adicionar configuração de diagnóstico" ![ Adicionar configuração de diagnóstico](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Forneça um nome de configuração de diagnóstico.

1. Especifique quais coletores de dados enviar os logs de auditoria (conta de armazenamento, Hub de eventos e/ou espaço de trabalho Log Analytics).

1. Selecione "MySqlAuditLogs" como o tipo de log.
![Definir configuração de diagnóstico](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. Depois de configurar os coletores de dados para canalizar os logs de auditoria, você pode clicar em **salvar**.
![Salvar configuração de diagnóstico](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. Acesse os logs de auditoria explorando-os nos coletores de dados que você configurou. Pode levar até 10 minutos para que os logs sejam exibidos.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [os logs de auditoria](concepts-audit-logs.md) no banco de dados do Azure para MariaDB
- Saiba como configurar logs de auditoria no [CLI do Azure](howto-configure-audit-logs-cli.md)