---
title: Monitorar e operar backups usando o Centro de Backup
description: Este artigo explica como monitorar e operar backups em escala usando o centro de backup
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 86b81110d6abeb1425e18ee45dfe65a96f69687d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102506099"
---
# <a name="monitor-and-operate-backups-using-backup-center"></a>Monitorar e operar backups usando o centro de backup

Como administrador de backup, você pode usar o centro de backup como um único painel de vidro para monitorar seus trabalhos e o inventário de backup diariamente. Você também pode usar o centro de backup para executar suas operações regulares, como responder a solicitações de backup sob demanda, restaurar backups, criar políticas de backup e assim por diante.

## <a name="supported-scenarios"></a>Cenários com suporte

* O centro de backup atualmente tem suporte para backup de VM do Azure, SQL no backup de VM do Azure, SAP HANA no backup de VM do Azure, backup de arquivos do Azure, backup de BLOBs do Azure, backup do Azure Managed Disks e banco de dados do Azure para backup do servidor PostgreSQL.
* Consulte a [matriz de suporte](backup-center-support-matrix.md) para obter uma lista detalhada de cenários com e sem suporte.

## <a name="backup-instances"></a>Instâncias de backup

O centro de backup permite a fácil pesquisa e a descoberta de instâncias de backup em todo o seu espaço de backup.

A seleção da guia **instâncias de backup** no centro de backup permite exibir detalhes de todas as instâncias de backup às quais você tem acesso.

 Você pode exibir as seguintes informações sobre cada uma de suas instâncias de backup:

* Assinatura de DataSource
* Grupo de recursos DataSource
* Último ponto de recuperação
* Cofre associado à instância de backup
* Status de proteção

 Você também pode filtrar a lista de instâncias de backup nos seguintes parâmetros:

* Assinatura de DataSource
* Grupo de recursos DataSource
* Local da fonte de origem
* Tipo de fonte de tipos
* Cofre
* Status de proteção
* Marcas DataSource

Clicar com o botão direito do mouse em qualquer um dos itens na grade permite executar ações na instância de backup específica, como navegar até o recurso, disparar backups e restaurações sob demanda, ou interromper o backup.

![Centro de backup-instâncias](./media/backup-center-monitor-operate/backup-center-instances.png)

## <a name="backup-jobs"></a>Trabalhos de backup

O centro de backup permite exibir informações detalhadas sobre todos os trabalhos que foram criados em seu espaço de backup e tomar as devidas medidas para trabalhos com falha.

A seleção do item de menu **trabalhos de backup** no centro de backup fornece uma exibição de todos os seus trabalhos. Cada trabalho contém as seguintes informações:

* Instância de backup associada ao trabalho
* Assinatura de DataSource
* Grupo de recursos DataSource
* Local da fonte de origem
* Operação de trabalho
* Status do trabalho
* Hora de início do trabalho
* Duração do trabalho

A seleção de um item na grade permite que você exiba mais detalhes sobre o trabalho fornecido. Clicar com o botão direito do mouse em um item ajuda você a navegar até o recurso para executar a ação necessária.

![Centro de backup-trabalhos](./media/backup-center-monitor-operate/backup-center-jobs.png)

Usando a guia **trabalhos de backup** , você pode exibir trabalhos até os últimos sete dias. Para exibir os trabalhos mais antigos, use os [relatórios de backup](backup-center-obtain-insights.md).

## <a name="vaults"></a>Cofres

A seleção do item de menu **cofres** no centro de backup permite que você veja uma lista de todos os [cofres de serviços de recuperação](backup-azure-recovery-services-vault-overview.md) e [cofres de backup](backup-vault-overview.md) aos quais você tem acesso. Você pode filtrar a lista com os seguintes parâmetros:

* Assinatura do cofre
* Grupo de recursos do cofre
* Nome do cofre
* Tipo de DataSource associado à política

Selecionar qualquer item na lista permite que você navegue para um determinado cofre.

![Centro de backup-cofres](./media/backup-center-monitor-operate/backup-center-vaults.png)

## <a name="backup-policies"></a>Políticas de backup

O centro de backup permite exibir e editar informações de chave para qualquer uma de suas políticas de backup.

A seleção do item de menu **políticas de backup** permite exibir todas as políticas que você criou em todo o seu espaço de backup. Você pode filtrar a lista por assinatura do cofre, grupo de recursos, tipo de fonte de fontes e cofre. Clicar com o botão direito do mouse em um item na grade permite exibir itens associados para essa política, editar a política ou até mesmo excluí-la, se necessário.

![Centro de backup-políticas](./media/backup-center-monitor-operate/backup-center-policies.png)

## <a name="next-steps"></a>Próximas etapas

* [Governar seu espaço de backup](backup-center-govern-environment.md)
* [Executar ações usando o centro de backup](backup-center-actions.md)
* [Obtenha informações sobre seus backups](backup-center-obtain-insights.md)
