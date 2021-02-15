---
title: Obtenha informações usando o centro de backup
description: Saiba como analisar tendências históricas e obter informações mais aprofundadas sobre seus backups com o centro de backup.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: c5f707ce3bc8d1172805ac14958a50bf59d1412d
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98893686"
---
# <a name="obtain-insights-using-backup-center-preview"></a>Obter informações usando o centro de backup (versão prévia)

Para analisar as tendências históricas e obter informações mais aprofundadas sobre seus backups, o centro de backup fornece uma interface para os [relatórios de backup (versão prévia)](configure-reports.md), que usa [logs de Azure monitor](../azure-monitor/platform/data-platform-logs.md) e [pastas de trabalho do Azure](../azure-monitor/platform/workbooks-overview.md). Os relatórios de backup oferecem os seguintes recursos:

- Alocação e previsão do armazenamento em nuvem consumido.

- Auditoria de backups e restaurações.

- Identificação das principais tendências em diferentes níveis de granularidade.

- Obter visibilidade e informações sobre as oportunidades de otimização de custos para seus backups.

## <a name="supported-scenarios"></a>Cenários com suporte

- Os relatórios de backup (visualização) não estão disponíveis no momento para o backup do servidor do banco de dados do Azure para PostgreSQL.

- Consulte a [matriz de suporte](backup-center-support-matrix.md) para obter uma lista detalhada de cenários com e sem suporte.

## <a name="get-started"></a>Introdução

### <a name="configure-your-vaults-to-send-data-to-a-log-analytics-workspace"></a>Configurar seus cofres para enviar dados para um espaço de trabalho Log Analytics

[Saiba como definir as configurações de diagnóstico em escala para seus cofres](./configure-reports.md#get-started)

### <a name="view-backup-reports-in-the-backup-center-portal"></a>Exibir relatórios de backup no portal do centro de backup

A seleção do item de menu **relatórios de backup** no centro de backup abre os relatórios. Escolha um ou mais espaços de trabalho do Log Analytics para exibir e analisar informações de chave em seus backups.

![Relatórios de backup no centro de backup](./media/backup-center-obtain-insights/backup-center-backup-reports.png)

A seguir estão as exibições disponíveis:

1. **Resumo** – Use esta guia para obter uma visão geral de alto nível do seu espaço de backup. [Saiba mais](./configure-reports.md#summary)

1. **Itens de backup** -Use esta guia para ver informações e tendências sobre o armazenamento em nuvem consumidos em um nível de item de backup. [Saiba mais](./configure-reports.md#backup-items)

1. **Uso** -Use essa guia para exibir os parâmetros de cobrança de chave para seus backups. [Saiba mais](./configure-reports.md#usage)

1. **Trabalhos** – Use essa guia para exibir tendências de longa execução em trabalhos, como o número de trabalhos com falha por dia e as principais causas de falha do trabalho. [Saiba mais](./configure-reports.md#jobs)

1. **Políticas** – Use essa guia para exibir informações sobre todas as suas políticas ativas, como o número de itens associados e o armazenamento em nuvem total consumido por itens de backup em uma determinada política. [Saiba mais](./configure-reports.md#policies)

1. **Otimizar** – Use essa guia para obter visibilidade de possíveis oportunidades de otimização de custos para seus backups. [Saiba mais](./configure-reports.md#optimize)

## <a name="next-steps"></a>Próximas etapas

- [Monitorar e operar backups](backup-center-monitor-operate.md)
- [Governar seu espaço de backup](backup-center-govern-environment.md)
- [Executar ações usando o centro de backup](backup-center-actions.md)