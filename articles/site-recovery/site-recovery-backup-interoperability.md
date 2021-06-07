---
title: Suporte para o uso de Azure Site Recovery com o backup do Azure
description: Fornece uma visão geral de como o Azure Site Recovery e o backup do Azure podem ser usados juntos.
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: sideeksh
ms.openlocfilehash: c334eee34eb878135d3d81ab15d03618c6604846
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86135173"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Suporte para o uso de Site Recovery com o backup do Azure

Este artigo resume o suporte para usar o [serviço de site Recovery](site-recovery-overview.md) junto com o [serviço de backup do Azure](../backup/backup-overview.md).

**Ação** | **Suporte do Site Recovery** | **Detalhes**
--- | --- | ---
**Implantar serviços juntos** | Compatível | Os serviços são interoperáveis e podem ser configurados juntos.
**Backup/restauração de arquivo** | Compatível | Quando o backup e a replicação estão habilitados para uma VM e os backups são feitos, não há nenhum problema na restauração de arquivos nas VMs do lado da origem ou no grupo de VMs. A replicação continua normalmente sem alteração na integridade da replicação.
**Restauração de disco** | Não há suporte atual | Se você restaurar um disco de backup, você precisará desabilitar e reabilitar novamente a replicação para a VM.
**Restauração da VM** | Não há suporte atual | Se você restaurar uma VM ou um grupo de VMs, será necessário desabilitar e reabilitar a replicação para a VM.  


