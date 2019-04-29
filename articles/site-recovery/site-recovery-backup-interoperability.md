---
title: Suporte para o Azure Site Recovery com o Backup do Azure | Microsoft Docs
description: Fornece uma visão geral de como do Azure Site Recovery e Backup do Azure podem ser usados juntos.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2019
ms.author: sideeksh
ms.openlocfilehash: e902f70225ec0eb0caa98f7e19a16c87220cb6f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035708"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Suporte para usar o Site Recovery com o Backup do Azure

Este artigo resume o suporte para usar o [serviço Site Recovery](site-recovery-overview.md) junto com o [serviço de Backup do Azure](https://docs.microsoft.com/azure/backup/backup-overview).

**Ação** | **Suporte à recuperação de site** | **Detalhes**
--- | --- | ---
**Implantar os serviços juntos** | Com suporte | Os serviços são interoperáveis e podem ser configurados juntos.
**Backup/restauração de arquivo** | Com suporte | Quando o backup e replicação estiverem habilitadas para uma VM e os backups são realizados, há nenhum problema de restauração de arquivos no lado do código-fonte VMs ou grupo de VMs. Como de costume, a replicação continuará sem alterações na integridade da replicação.
**Backup/restauração de disco** | Não há suporte atual | Se você restaurar um backup de disco, você precisará desabilitar e reabilitar a replicação para a VM novamente.
**Backup/restauração de VM** | Não há suporte atual | Se você fazer backup ou restaurar uma VM ou um grupo de VMs, você precisará desabilitar e reabilitar a replicação para a VM.  


