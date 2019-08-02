---
title: Configurar e acessar os logs de auditoria do banco de dados do Azure para MariaDB no portal do Azure
description: Este artigo descreve como configurar e acessar os logs de auditoria no banco de dados do Azure para MariaDB do portal do Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: 8fadcc8cb418063662df45d4134bef0578ffccda
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444809"
---
# <a name="configure-and-access-audit-logs-in-the-azure-portal"></a>Configurar e acessar logs de auditoria no portal do Azure

Você pode configurar o [banco de dados do Azure para logs de auditoria do MariaDB](concepts-audit-logs.md) e configurações de diagnóstico do portal do Azure.

> [!IMPORTANT]
> Funcionalidade de log de auditoria está atualmente em visualização.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia de instruções, você precisa:

- [Banco de dados do Azure para MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Configurar o log de auditoria

Habilitar e configurar o log de auditoria.

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. Selecione o servidor do Banco de Dados do Azure para MariaDB.

1. Sob o **as configurações** seção na barra lateral, selecione **parâmetros de servidor**.
    ![Parâmetros de servidor](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Atualizar o **audit_log_enabled** parâmetro como ON.
    ![Habilitar logs de auditoria](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Selecione o [tipos de evento](concepts-audit-logs.md#configure-audit-logging) a ser registrada, atualizando o **audit_log_events** parâmetro.
    ![Eventos de log de auditoria](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Adicionar usuários a serem excluídos do log atualizando MariaDB a **audit_log_exclude_users** parâmetro. Especifique os usuários, fornecendo seu nome de usuário do MariaDB.
    ![Excluir usuários de log de auditoria](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. Depois que você alterar os parâmetros, clique em **Salvar**. Ou então, você pode **Descartar** suas alterações.
    ![Salvar](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Configuração dos logs de diagnóstico

1. Sob o **Monitoring** seção na barra lateral, selecione **configurações de diagnóstico**.

1. Clique em "+ Adicionar configuração de diagnóstico" ![Adicionar configuração de diagnóstico](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Forneça um nome de configuração de diagnóstico.

1. Especifique quais dados coletores para enviar os logs de auditoria (conta de armazenamento, hub de eventos e/ou espaço de trabalho do Log Analytics).

1. Selecione "MySqlAuditLogs" como o tipo de log.
![Definir configuração de diagnóstico](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. Depois de ter configurado os coletores de dados para redirecionar os logs de auditoria, você pode clicar em **salvar**.
![Salvar configuração de diagnóstico](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. Acesse os logs de auditoria, explorando-os no Coletores de dados que você configurou. Ele pode levar até 10 minutos para que os logs sejam exibidos.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [logs de auditoria](concepts-audit-logs.md) no banco de dados do Azure para MariaDB.