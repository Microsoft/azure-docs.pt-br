---
author: tanmaygore
ms.author: tagore
ms.service: cloud-services
ms.topic: include
ms.date: 11/25/2018
ms.openlocfilehash: 5deba9d8968f71ef1b21517e74e6af4c39aa9271
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "76279336"
---
> [!WARNING]
> Quando você habilita o diagnóstico para uma função existente, qualquer extensão que você já tenha definido será desabilitado quando o pacote for implantado. Estão incluídos:
>
> * Diagnóstico do Microsoft Monitoring Agent
> * Monitoramento de segurança do Microsoft Azure
> * Microsoft Antimalware                 
> * Agente de monitoramento da Microsoft
> * Agente Criador de Perfil de Serviço da Microsoft      
> * Extensão de Domínio do Azure        
> * Extensão de Diagnóstico do Azure   
> * Extensão de Área de Trabalho Remota do Azure
> * Coletor de Logs do Azure
>
> Depois de implantar a função atualizada, você pode redefinir as extensões por meio do portal do Azure ou do PowerShell.
>
