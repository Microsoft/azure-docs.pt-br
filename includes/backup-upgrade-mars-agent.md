---
title: Atualizar o agente de backup do Azure
description: Essas informações explicam por que você deve atualizar o agente de backup do Azure e onde baixar a atualização.
services: backup
cloud: ''
suite: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 03/03/2020
ms.author: dacurwin
ms.openlocfilehash: bd298f758d6109b908db01dd2ae3b97e5e2f714a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "78673211"
---
## <a name="upgrade-the-mars-agent"></a>Atualizar o agente MARS

As versões do agente de Serviços de Recuperação do Microsoft Azure (MARS) abaixo do 2.0.9083.0 têm uma dependência do serviço de controle de acesso do Azure. O agente MARS também é conhecido como o agente de backup do Azure.

No 2018, [a Microsoft preteriu o serviço de controle de acesso do Azure](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). A partir de 19 de março de 2018, todas as versões do agente MARS abaixo de 2.0.9083.0 apresentarão falhas de backup. Para evitar ou resolver falhas de backup, [atualize seu agente Mars para a versão mais recente](https://support.microsoft.com/help/4538314/update-for-azure-backup-for-microsoft-azure-recovery-services-agent). Para identificar os servidores que exigem uma atualização de agente MARS, siga as etapas em [atualizar o agente de serviços de recuperação do Microsoft Azure (MARS)](../articles/backup/upgrade-mars-agent.md).

O agente MARS é usado para fazer backup de arquivos e pastas e dados de estado do sistema no Azure. O System Center DPM e o Servidor de Backup do Azure usam o agente MARS para fazer backup de dados no Azure.
