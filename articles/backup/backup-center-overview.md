---
title: Visão geral do centro de backup
description: Este artigo fornece uma visão geral do centro de backup do Azure.
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: b42bb2719d03108212f62428dc97ed814899c63c
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102506031"
---
# <a name="overview-of-backup-center"></a>Visão geral do centro de backup

O centro de backup fornece uma **única experiência de gerenciamento unificada** no Azure para empresas para controlar, monitorar, operar e analisar backups em grande escala. Como tal, é consistente com as experiências de gerenciamento nativo do Azure.

Alguns dos principais benefícios do centro de backup incluem:

* **Painel único de vidro para gerenciar backups** – o centro de backup foi projetado para funcionar bem em um ambiente grande e distribuído do Azure. Você pode usar o centro de backup para gerenciar com eficiência os backups que abrangem vários tipos de carga de trabalho, cofres, assinaturas, regiões e locatários [Lighthouse do Azure](../lighthouse/overview.md) .
* **Gerenciamento centrado em fonte de** dados – o centro de backup fornece exibições e filtros que são centralizados nas fontes de dados das quais você está fazendo backup (por exemplo, VMS e bancos de dados). Isso permite que um proprietário de recurso ou um administrador de backup monitore e opere backups de itens sem a necessidade de se concentrar em qual cofre um item é submetido a backup. Um recurso importante desse design é a capacidade de filtrar modos de exibição por propriedades específicas de DataSource, como assinatura de DataSource, grupo de recursos DataSource e marcas DataSource. Por exemplo, se a sua organização seguir uma prática de atribuir marcas diferentes a VMs que pertencem a departamentos diferentes, você poderá usar o centro de backup para filtrar as informações de backup com base nas marcas das VMs subjacentes cujo backup está sendo feito sem a necessidade de se concentrar na marca do cofre.
* **Experiências conectadas** – o centro de backup fornece integrações nativas aos serviços existentes do Azure que habilitam o gerenciamento em escala. Por exemplo, o centro de backup usa a experiência de [Azure Policy](../governance/policy/overview.md) para ajudá-lo a governar seus backups. Ele também aproveita [pastas de trabalho do Azure](../azure-monitor/visualize/workbooks-overview.md) e [logs de Azure monitor](../azure-monitor/logs/data-platform-logs.md) para ajudá-lo a exibir relatórios detalhados sobre backups. Portanto, você não precisa aprender novos princípios para usar os diversos recursos oferecidos pelo centro de backup. Você também pode descobrir os recursos da Comunidade no centro de backup.

## <a name="supported-scenarios"></a>Cenários com suporte

* O centro de backup tem suporte no momento para backup de VM do Azure, SQL no backup de VM do Azure, SAP HANA no backup de VM do Azure, backup de arquivos do Azure, backup de BLOBs do Azure, backup do Azure Managed Disks e banco de dados do Azure para backup do servidor PostgreSQL.
* Consulte a [matriz de suporte](backup-center-support-matrix.md) para obter uma lista detalhada de cenários com e sem suporte.

## <a name="get-started"></a>Introdução

Para começar a usar o centro de backup, procure **centro de backup** na portal do Azure e navegue até o painel do **centro de backup** .

![Pesquisa do centro de backup](./media/backup-center-overview/backup-center-search.png)

A primeira tela que você vê é a **visão geral**. Ele contém dois blocos – **trabalhos** e **instâncias de backup**.

![Blocos do centro de backup](./media/backup-center-overview/backup-center-overview-widgets.png)

No bloco **trabalhos** , você obtém uma exibição resumida de todos os trabalhos relacionados a backup e restauração que foram disparados em todo o seu espaço de backup nas últimas 24 horas. Você pode exibir informações sobre o número de trabalhos que foram concluídos, com falha e que estão em andamento. A seleção de qualquer um dos números neste bloco permite que você exiba mais informações sobre os trabalhos para um tipo de fonte de dados específico, tipo de operação e status.

No bloco **instâncias de backup** , você obtém uma exibição resumida de todas as instâncias de backup em seu espaço de backup. Por exemplo, você pode ver o número de instâncias de backup que estão no estado de exclusão reversível em comparação com o número de instâncias que ainda estão configuradas para proteção. A seleção de qualquer um dos números neste bloco permite que você exiba mais informações sobre instâncias de backup para um tipo de fonte de dados e um estado de proteção específicos. Você também pode exibir todas as instâncias de backup cuja fonte de base subjacente não foi encontrada (a fonte de origem pode ser excluída ou você pode não ter acesso à fonte de fontes).

Assista ao vídeo a seguir para entender os recursos do centro de backup:

> [!VIDEO https://www.youtube.com/embed/pFRMBSXZcUk?t=497]

Siga as [próximas etapas](#next-steps) para entender os diferentes recursos que o centro de backup fornece e como você pode usar esses recursos para gerenciar seu espaço de backup com eficiência.

## <a name="next-steps"></a>Próximas etapas

* [Monitorar e operar backups](backup-center-monitor-operate.md)
* [Governar seu espaço de backup](backup-center-govern-environment.md)
* [Obtenha informações sobre seus backups](backup-center-obtain-insights.md)
* [Executar ações usando o centro de backup](backup-center-actions.md)