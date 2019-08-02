---
author: jpconnock
ms.service: cloud-services
ms.topic: include
ms.date: 11/25/2018
ms.author: jeconnoc
ms.openlocfilehash: f23d5246401f23694755e63a51c52da5dbf5ac1b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "67172264"
---
> [!WARNING]
> Quando você habilita o diagnóstico para uma função existente, qualquer extensão que você já tenha definido será desabilitado quando o pacote for implantado. Elas incluem:
>
> * Diagnóstico do Microsoft Monitoring Agent
> * Monitoramento de segurança do Microsoft Azure
> * Microsoft Antimalware                 
> * Microsoft Monitoring Agent
> * Agente Criador de Perfil de Serviço da Microsoft      
> * Extensão de Domínio do Azure        
> * Extensão de Diagnóstico do Azure   
> * Extensão de Área de Trabalho Remota do Azure
> * Coletor de Logs do Azure
>
> Depois de implantar a função atualizada, você pode redefinir as extensões por meio do portal do Azure ou do PowerShell.
>
