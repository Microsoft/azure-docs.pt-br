---
title: Conectar os logs de diagnóstico e auditoria do banco de dados SQL do Azure ao Azure Sentinel
description: Saiba como conectar logs de diagnóstico do banco de dados SQL do Azure e logs de auditoria de segurança ao Azure Sentinel.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/06/2021
ms.author: yelevin
ms.openlocfilehash: a3a09ceffc75e2d396d7bd7aeedd97b7f2b6ec2b
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807726"
---
# <a name="connect-azure-sql-database-diagnostics-and-auditing-logs"></a>Conectar logs de auditoria e diagnóstico do banco de dados SQL do Azure

O SQL do Azure é um mecanismo de banco de dados PaaS (plataforma como serviço) totalmente gerenciado que lida com a maioria das funções de gerenciamento de banco de dados, como atualização, aplicação de patches, backups e monitoramento, sem envolvimento do usuário. 

O conector do banco de dados SQL do Azure permite que você transmita os logs de auditoria e diagnóstico de seus bancos para o Azure Sentinel, permitindo que você monitore continuamente a atividade em todas as suas instâncias.

- A conexão de logs de diagnóstico permite que você envie logs de diagnóstico de banco de dados de tipos diferentes para seu espaço de trabalho do Azure Sentinel.

- Conectar logs de auditoria permite que você transmita logs de auditoria de segurança de todos os seus bancos de dados SQL do Azure no nível do servidor.

Saiba mais sobre como [monitorar bancos de dados SQL do Azure](../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter permissões de leitura e gravação no espaço de trabalho do Azure Sentinel.

- Para conectar os logs de auditoria, você deve ter permissões de leitura e gravação para as configurações de auditoria do Azure SQL Server.

## <a name="connect-to-azure-sql-database"></a>Conectar banco de dados SQL do Azure
    
1. No menu de navegação do Azure Sentinel, selecione **conectores de dados**.

1. Selecione **Azure SQL Database** na Galeria de conectores de dados e, em seguida, selecione **abrir a página conector**  no painel de visualização.

1. Na seção **configuração** da página conector, observe as duas categorias de logs que você pode conectar.

### <a name="connect-diagnostics-logs"></a>Conectar logs de diagnóstico

1. Em **logs de diagnóstico**, expanda **Habilitar logs de diagnóstico em cada um dos bancos de dados SQL do Azure manualmente**.

1. Selecione o link **abrir Azure sql >** para abrir a folha recursos **do SQL do Azure** .

1. **(Opcional)** Para localizar o recurso de banco de dados facilmente, selecione **Adicionar filtro** na barra filtros na parte superior.
    1. Na lista suspensa **filtro** , selecione **tipo de recurso**.
    1. Na lista suspensa **valor** , desmarque **selecionar tudo** e, em seguida, selecione **banco de dados SQL**.
    1. Clique em **Aplicar**.
    
1. Selecione o recurso de banco de dados cujos logs de diagnóstico você deseja enviar para o Azure Sentinel.

    > [!NOTE]
    > Para cada recurso de banco de dados cujos logs você deseja coletar, você deve repetir esse processo, começando nesta etapa.

1. Na página de recursos do banco de dados selecionado, em **monitoramento** no menu de navegação, selecione **configurações de diagnóstico**.

    1. Selecione o link **+ Adicionar configuração de diagnóstico** na parte inferior da tabela.

    1. Na tela de **configuração de diagnóstico** , insira um nome no campo nome da configuração de  **diagnóstico** .
    
    1. Na coluna **detalhes de destino** , marque a caixa de seleção **Enviar para log Analytics espaço de trabalho** . Dois novos campos serão exibidos abaixo dele. Escolha a **assinatura** relevante e **log Analytics espaço de trabalho** (onde o Azure Sentinel reside).

    1. Na coluna **detalhes da categoria** , marque as caixas de seleção dos tipos de log e métrica que você deseja ingerir. É recomendável selecionar todos os tipos disponíveis em **log** e **métrica**.

    1. Selecione **salvar** na parte superior da tela.

- Como alternativa, você pode usar o **script do PowerShell** fornecido para conectar seus logs de diagnóstico.
    1. Em **logs de diagnóstico**, expanda **habilitar por script do PowerShell**.

    1. Copie o bloco de código e cole-o no PowerShell.

### <a name="connect-audit-logs"></a>Conectar logs de auditoria

1. Em **logs de auditoria (versão prévia)**, expanda **Habilitar logs de auditoria em todos os bancos de dados SQL do Azure (no nível do servidor)**.

1. Selecione o link **abrir Azure SQL >** para abrir a folha de recursos **SQL Servers** .

1. Selecione o SQL Server cujos logs de auditoria você deseja enviar para o Azure Sentinel.

    > [!NOTE]
    > Para cada recurso de servidor cujos logs você deseja coletar, você deve repetir esse processo, começando nesta etapa.

1. Na página de recursos do servidor selecionado, em **segurança** no menu de navegação, selecione **auditoria**.

    1. Mova a alternância **habilitar auditoria do SQL do Azure** para **ativado**.

    1. Em **destino do log de auditoria**, selecione **log Analytics (versão prévia)**.
    
    1. Na lista de espaços de trabalho que aparece, escolha seu espaço de trabalho (onde o Azure Sentinel reside).

    1. Selecione **salvar** na parte superior da tela.

- Como alternativa, você pode usar o **script do PowerShell** fornecido para conectar seus logs de diagnóstico.
    1. Em **logs de auditoria**, expanda **habilitar por script do PowerShell**.

    1. Copie o bloco de código e cole-o no PowerShell.


> [!NOTE]
>
> Com esse conector de dados específico, os indicadores de status de conectividade (uma faixa de cores na Galeria de conectores de dados e os ícones de conexão ao lado dos nomes dos tipos de dados) serão mostrados como *conectados* (verde) somente se os dados tiverem sido ingeridos em algum momento nas duas últimas semanas. Depois que duas semanas tiverem passado sem ingestão de dados, o conector será exibido como sendo desconectado. O momento em que mais dados surgem, o status *conectado* retornará.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar o diagnóstico do banco de dados SQL do Azure e os logs de auditoria ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).