---
title: Matriz de suporte para o centro de backup
description: Este artigo resume os cenários aos quais o centro de backup dá suporte para cada tipo de carga de trabalho
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 672f0e127f173260f25978497198fd7b554aa390
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98893585"
---
# <a name="support-matrix-for-backup-center"></a>Matriz de suporte para o centro de backup

O centro de backup fornece um único painel de vidro para que as empresas [administrem, monitorem, operem e analisem backups em escala](backup-center-overview.md). Este artigo resume os cenários aos quais o centro de backup dá suporte para cada tipo de carga de trabalho.

## <a name="supported-scenarios"></a>Cenários com suporte

| **Categoria** | **Cenário**  | **Cargas de trabalho com suporte**  | **Limites** |
| -------------| ------------- | ----------------------- |------------|
| Monitoramento   | Exibir todos os trabalhos | <li> Máquina Virtual do Azure <br><br> <li> Banco de dados do Azure para servidor PostgreSQL <br><br> <li> SQL na VM do Azure <br><br> <li> SAP HANA na VM do Azure <br><br> <li> Arquivos do Azure | <li> 7 dias de trabalhos disponíveis prontos para uso. <br> <li> Cada filtro/lista suspensa dá suporte a um máximo de 1000 itens. Portanto, o centro de backup pode ser usado para monitorar um máximo de 1000 assinaturas e 1000 cofres entre locatários. |
| Monitoramento | Exibir todas as instâncias de backup | <li> Máquina Virtual do Azure <br><br> <li> Banco de dados do Azure para servidor PostgreSQL <br><br> <li> SQL na VM do Azure <br><br> <li> SAP HANA na VM do Azure <br><br> <li> Arquivos do Azure | O mesmo que o descrito acima |
| Monitoramento | Exibir todas as políticas de backup | <li> Máquina Virtual do Azure <br><br> <li> Banco de dados do Azure para servidor PostgreSQL <br><br> <li> SQL na VM do Azure <br><br> <li> SAP HANA na VM do Azure <br><br> <li> Arquivos do Azure | O mesmo que o descrito acima |
| Monitoramento | Exibir todos os cofres | <li> Máquina Virtual do Azure <br><br> <li> Banco de dados do Azure para servidor PostgreSQL <br><br> <li> SQL na VM do Azure <br><br> <li> SAP HANA na VM do Azure <br><br> <li> Arquivos do Azure | O mesmo que o descrito acima |
| Ações | Configurar backup | <li> Máquina Virtual do Azure <br><br> <li> Banco de dados do Azure para servidor PostgreSQL <br><br> <li> SQL na VM do Azure <br><br> <li> SAP HANA na VM do Azure <br><br> <li> Arquivos do Azure | Consulte matrizes de suporte para backup de [VM do Azure](./backup-support-matrix-iaas.md) e [banco de dados do Azure para backup de servidor PostgreSQL](backup-azure-database-postgresql.md#support-matrix) |
| Ações | Restaurar instância de backup | <li> Máquina Virtual do Azure <br><br> <li> Banco de dados do Azure para servidor PostgreSQL <br><br> <li> SQL na VM do Azure <br><br> <li> SAP HANA na VM do Azure <br><br> <li> Arquivos do Azure | Consulte matrizes de suporte para backup de [VM do Azure](./backup-support-matrix-iaas.md) e [banco de dados do Azure para backup de servidor PostgreSQL](backup-azure-database-postgresql.md#support-matrix) |
| Ações | Criar cofre | <li> Máquina Virtual do Azure <br><br> <li> Banco de dados do Azure para servidor PostgreSQL <br><br> <li> SQL na VM do Azure <br><br> <li> SAP HANA na VM do Azure <br><br> <li> Arquivos do Azure | Consulte matrizes de suporte para o [cofre dos serviços de recuperação](./backup-support-matrix.md#vault-support) |
| Ações | Criar política de backup | <li> Máquina Virtual do Azure <br><br> <li> Banco de dados do Azure para servidor PostgreSQL <br><br> <li> SQL na VM do Azure <br><br> <li> SAP HANA na VM do Azure <br><br> <li> Arquivos do Azure | Consulte matrizes de suporte para o [cofre dos serviços de recuperação](./backup-support-matrix.md#vault-support) |
| Ações | Executar backup sob demanda para uma instância de backup | <li> Máquina Virtual do Azure <br><br> <li> Banco de dados do Azure para servidor PostgreSQL <br><br> <li> SQL na VM do Azure <br><br> <li> SAP HANA na VM do Azure <br><br> <li> Arquivos do Azure | Consulte matrizes de suporte para backup de [VM do Azure](./backup-support-matrix-iaas.md) e [banco de dados do Azure para backup de servidor PostgreSQL](backup-azure-database-postgresql.md#support-matrix) |
| Ações | Parar o backup de uma instância de backup | <li> Máquina Virtual do Azure <br><br> <li> Banco de dados do Azure para servidor PostgreSQL <br><br> <li> SQL na VM do Azure <br><br> <li> SAP HANA na VM do Azure <br><br> <li> Arquivos do Azure | Consulte matrizes de suporte para backup de [VM do Azure](./backup-support-matrix-iaas.md) e [banco de dados do Azure para backup de servidor PostgreSQL](backup-azure-database-postgresql.md#support-matrix) |
| Insights | Exibir relatórios de backup | <li> Máquina Virtual do Azure <br><br> <li> SQL na máquina virtual do Azure <br><br> <li> SAP HANA na máquina virtual do Azure <br><br> <li> Arquivos do Azure <br><br> <li> System Center Data Protection Manager <br><br> <li> Agente de backup do Azure (MARS) <br><br> <li> Servidor de Backup do Azure (MABS) | Consulte [cenários com suporte para relatórios de backup](./configure-reports.md#supported-scenarios) |
| Governança | Exibir e atribuir políticas internas e personalizadas do Azure na categoria ' backup ' | N/D | N/D |
| Governança | Exibir fontes de fonte não configuradas para backup | <li> Máquina Virtual do Azure <br><br> <li> Banco de dados do Azure para servidor PostgreSQL | N/D |

## <a name="unsupported-scenarios"></a>Cenários sem suporte

| **Categoria** | **Cenário**  |
|--------------|---------------|
| Monitoramento | Exibir alertas em escala |
| Ações | Definir configurações de cofre em escala |
| Ações | Executar trabalho de restauração entre regiões do centro de backup |

## <a name="next-steps"></a>Próximas etapas

* [Examinar a matriz de suporte para o backup do Azure](./backup-support-matrix.md)
* [Examinar a matriz de suporte para o backup de VM do Azure](./backup-support-matrix-iaas.md)
* [Examine a matriz de suporte para o banco de dados do Azure para backup do servidor PostgreSQL](backup-azure-database-postgresql.md#support-matrix)