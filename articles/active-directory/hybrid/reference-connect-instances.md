---
title: 'Azure AD Connect: instâncias do serviço de sincronização | Microsoft Docs'
description: Esta página documenta considerações especiais para instâncias do Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 06/18/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca481d50efb99d6e36c66388192e9f27cd66bf45
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62096089"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: Considerações especiais para instâncias
O Azure AD Connect é mais comumente usado com a instância mundial do Azure AD e o Office 365. Mas também existem outras instâncias que têm requisitos diferentes para URLs e outras considerações especiais.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Alemanha
O [Microsoft Cloud Alemanha](https://www.microsoft.de/cloud-deutschland) é uma nuvem soberana operada por uma administradora de dados alemã.

| URLs para abrir no servidor proxy |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| + Listas de revogação de certificados |

Ao entrar em seu locatário do Azure AD, você deverá usar uma conta com o domínio onmicrosoft.de.

Recursos atualmente indisponíveis no Microsoft Cloud Alemanha:

* O **Write-back de senha** está disponível em versão prévia no Azure AD Connect versão 1.1.570.0 e posteriores.
* Outros serviços do Azure AD Premium não estão disponíveis.

## <a name="microsoft-azure-government-cloud"></a>Nuvem do Microsoft Azure Governamental
A [Nuvem do Microsoft Azure Governamental](https://azure.microsoft.com/features/gov/) é uma nuvem para o governo dos EUA.

Esta nuvem teve suporte em versões mais antigas do DirSync. A partir da build 1.1.180 do Azure AD Connect, há suporte para a próxima geração da nuvem. Essa geração está usando pontos de extremidade com base somente nos EUA e tem uma lista de URLs diferente para abrir em seu servidor proxy.

| URLs para abrir no servidor proxy |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*.windows.net (necessário para a detecção automática do locatário governamental do Azure AD) |
| \*.gov.us.microsoftonline.com |
| + Listas de revogação de certificados |

> [!NOTE]
> A partir da versão AAD Connect 1.1.647.0, a definição do valor AzureInstance no registro não é mais necessário, desde que *.windows.net esteja aberto nos seus servidores proxy.

Recursos atualmente indisponíveis na nuvem do Microsoft Azure Governamental:

* O **Write-back de senha** está disponível em versão prévia no Azure AD Connect versão 1.1.570.0 e posterior.
* Outros serviços do Azure AD Premium não estão disponíveis.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).
