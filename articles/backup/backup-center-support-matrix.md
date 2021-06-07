---
title: Matriz de suporte do centro de backup
description: Este artigo resume os cenários aos quais o centro de backup dá suporte para cada tipo de carga de trabalho
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: d6e5d34e201edda4fd1e9fda85f210fb88211e28
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102504500"
---
# <a name="support-matrix-for-backup-center"></a>Matriz de suporte do centro de backup

O centro de backup fornece apenas um painel de vidro para que as empresas [administrem, monitorem, operem e analisem backups em escala](backup-center-overview.md). Este artigo resume os cenários aos quais o centro de backup dá suporte para cada tipo de carga de trabalho.

## <a name="supported-scenarios"></a>Cenários com suporte

| **Categoria** | **Cenário**  | **Cargas de trabalho com suporte**  | **Limites** |
| -------------| ------------- | ----------------------- |------------|
| Monitoramento   | Exibir todos os trabalhos | <li> Máquina Virtual do Azure <br><br> <li> Servidor do Banco de Dados do Azure para PostgreSQL <br><br> <li> SQL na VM do Azure <br><br> <li> SAP HANA na VM do Azure <br><br> <li> Arquivos do Azure<br/><br/> <li>Blobs do Azure<br/><br/> <li>Azure Managed Disks | <li> 7 dias de trabalhos disponíveis prontos para uso. <br> <li> Cada filtro/lista suspensa dá suporte a um máximo de 1000 itens. Portanto, o centro de backup pode ser usado para monitorar um máximo de 1000 assinaturas e 1000 cofres entre locatários. |
| Monitoramento | Exibir todas as Instâncias de backup | <li> Máquina Virtual do Azure <br><br> <li> Servidor do Banco de Dados do Azure para PostgreSQL <br><br> <li> SQL na VM do Azure <br><br> <li> SAP HANA na VM do Azure <br><br> <li> Arquivos do Azure<br/><br/> <li>Blobs do Azure<br/><br/> <li>Azure Managed Disks | O mesmo que o descrito acima |
| Monitoramento | Exibir todas as políticas de backup | <li> Máquina Virtual do Azure <br><br> <li> Servidor do Banco de Dados do Azure para PostgreSQL <br><br> <li> SQL na VM do Azure <br><br> <li> SAP HANA na VM do Azure <br><br> <li> Arquivos do Azure<br/><br/> <li>Blobs do Azure<br/><br/> <li>Azure Managed Disks | O mesmo que o descrito acima |
| Monitoramento | Exibir todos os cofres | <li> Máquina Virtual do Azure <br><br> <li> Servidor do Banco de Dados do Azure para PostgreSQL <br><br> <li> SQL na VM do Azure <br><br> <li> SAP HANA na VM do Azure <br><br> <li> Arquivos do Azure<br/><br/> <li>Blobs do Azure<br/><br/> <li>Azure Managed Disks | O mesmo que o descrito acima |
| Ações | Configurar backup | <li> Máquina Virtual do Azure <br><br> <li> Servidor do Banco de Dados do Azure para PostgreSQL <br><br> <li> SQL na VM do Azure <br><br> <li> SAP HANA na VM do Azure <br><br> <li> Arquivos do Azure<br/><br/> <li>Blobs do Azure<br/><br/> <li>Azure Managed Disks | Confira as matrizes de suporte para [backup de VM do Azure](./backup-support-matrix-iaas.md) e [backup de Banco de Dados do Azure para PostgreSQL Server](backup-azure-database-postgresql.md#support-matrix) |
| Ações | Restaurar Instância de Backup | <li> Máquina Virtual do Azure <br><br> <li> Servidor do Banco de Dados do Azure para PostgreSQL <br><br> <li> SQL na VM do Azure <br><br> <li> SAP HANA na VM do Azure <br><br> <li> Arquivos do Azure<br/><br/> <li>Blobs do Azure<br/><br/> <li>Azure Managed Disks | Confira as matrizes de suporte para [backup de VM do Azure](./backup-support-matrix-iaas.md) e [backup de Banco de Dados do Azure para PostgreSQL Server](backup-azure-database-postgresql.md#support-matrix) |
| Ações | Criar cofre | <li> Máquina Virtual do Azure <br><br> <li> Servidor do Banco de Dados do Azure para PostgreSQL <br><br> <li> SQL na VM do Azure <br><br> <li> SAP HANA na VM do Azure <br><br> <li> Arquivos do Azure<br/><br/> <li>Blobs do Azure<br/><br/> <li>Azure Managed Disks | Consultar matrizes de suporte para o [cofre dos Serviços de Recuperação](./backup-support-matrix.md#vault-support) |
| Ações | Criar política de backup | <li> Máquina Virtual do Azure <br><br> <li> Servidor do Banco de Dados do Azure para PostgreSQL <br><br> <li> SQL na VM do Azure <br><br> <li> SAP HANA na VM do Azure <br><br> <li> Arquivos do Azure<br/><br/> <li>Blobs do Azure<br/><br/> <li>Azure Managed Disks | Consultar matrizes de suporte para o [cofre dos Serviços de Recuperação](./backup-support-matrix.md#vault-support) |
| Ações | Executar backup sob demanda para uma instância de backup | <li> Máquina Virtual do Azure <br><br> <li> Servidor do Banco de Dados do Azure para PostgreSQL <br><br> <li> SQL na VM do Azure <br><br> <li> SAP HANA na VM do Azure <br><br> <li> Arquivos do Azure<br/><br/> <li>Blobs do Azure<br/><br/> <li>Azure Managed Disks | Confira as matrizes de suporte para [backup de VM do Azure](./backup-support-matrix-iaas.md) e [backup de Banco de Dados do Azure para PostgreSQL Server](backup-azure-database-postgresql.md#support-matrix) |
| Ações | Parar o backup de uma instância de backup | <li> Máquina Virtual do Azure <br><br> <li> Servidor do Banco de Dados do Azure para PostgreSQL <br><br> <li> SQL na VM do Azure <br><br> <li> SAP HANA na VM do Azure <br><br> <li> Arquivos do Azure<br/><br/> <li>Blobs do Azure<br/><br/> <li>Azure Managed Disks | Confira as matrizes de suporte para [backup de VM do Azure](./backup-support-matrix-iaas.md) e [backup de Banco de Dados do Azure para PostgreSQL Server](backup-azure-database-postgresql.md#support-matrix) |
| Insights | Exibir Relatórios de Backup | <li> Máquina Virtual do Azure <br><br> <li> SQL em Máquina Virtual do Azure <br><br> <li> SAP HANA em Máquina Virtual do Azure <br><br> <li> Arquivos do Azure <br><br> <li> System Center Data Protection Manager <br><br> <li> MARS (Agente do Backup do Azure) <br><br> <li> Servidor de Backup do Azure (MABS) | Confira os [cenários com suporte para Relatórios de Backup](./configure-reports.md#supported-scenarios) |
| Governança | Exibir e atribuir Políticas do Azure internas e personalizadas na categoria 'Backup' | N/D | N/D |
| Governança | Exibir fontes de fonte não configuradas para backup | <li> Máquina Virtual do Azure <br><br> <li> Servidor do Banco de Dados do Azure para PostgreSQL | N/D |

## <a name="unsupported-scenarios"></a>Cenários sem suporte

| **Categoria** | **Cenário**  |
|--------------|---------------|
| Monitoramento | Exibir alertas em escala |
| Ações | Configurar definições de cofre em escala |
| Ações | Executar trabalho de restauração entre regiões do centro de backup |

## <a name="next-steps"></a>Próximas etapas

* [Examinar a matriz de suporte para Backup do Azure](./backup-support-matrix.md)
* [Examinar a matriz de suporte para backup de VM do Azure](./backup-support-matrix-iaas.md)
* [Examinar a matriz de suporte para backup do Banco de Dados do Azure para PostgreSQL Server](backup-azure-database-postgresql.md#support-matrix)