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
ms.date: 05/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c4d8b0a33763a967550453d8a205258f7583084
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90015255"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: considerações especiais para instâncias
Azure AD Connect é mais comumente usado com a instância Mundial do Azure AD e Microsoft 365. Mas também existem outras instâncias que têm requisitos diferentes para URLs e outras considerações especiais.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Alemanha
O [Microsoft Cloud Alemanha](https://www.microsoft.de/cloud-deutschland) é uma nuvem soberana operada por uma administradora de dados alemã.

| URLs para abrir no servidor proxy |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| + Listas de revogação de certificados |

Ao entrar em seu locatário do Azure AD, você deverá usar uma conta com o domínio onmicrosoft.de.

Recursos atualmente indisponíveis no Microsoft Cloud Alemanha:

* O **write-back de senha** está disponível para visualização com Azure ad Connect versão 1.1.570.0 e posterior.
* Outros serviços do Azure AD Premium não estão disponíveis.

## <a name="microsoft-azure-government"></a>Microsoft Azure Governamental
A [Nuvem do Microsoft Azure Governamental](https://azure.microsoft.com/features/gov/) é uma nuvem para o governo dos EUA.

Esta nuvem teve suporte em versões mais antigas do DirSync. A partir da build 1.1.180 do Azure AD Connect, há suporte para a próxima geração da nuvem. Essa geração está usando pontos de extremidade com base somente nos EUA e tem uma lista de URLs diferente para abrir em seu servidor proxy.

| URLs para abrir no servidor proxy |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*. windows.net (necessário para a detecção automática de locatários do Azure governamental) |
| \*.gov.us.microsoftonline.com |
| + Listas de revogação de certificados |

> [!NOTE]
> A partir do Azure AD Connect versão 1.1.647.0, a definição do valor AzureInstance no registro não é mais necessária, desde que *. windows.net esteja aberto nos servidores proxy. No entanto, para clientes que não permitem conectividade com a Internet de seus servidores Azure AD Connect, a seguinte configuração manual pode ser usada.

### <a name="manual-configuration"></a>Configuração manual

As etapas de configuração manual a seguir são usadas para garantir que Azure AD Connect use os pontos de extremidade de sincronização do Azure governamental.

1. Inicie a instalação do Azure AD Connect.
2. Quando você consultar a primeira página na qual recebe uma solicitação para aceitar o EULA, não continue, mas deixe o assistente de instalação em execução.
3. Inicie o regedit e altere a chave do registro `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` para o valor `4`.
4. Vá para o assistente de instalação do Azure AD Connect, aceite o EULA e continue. Durante a instalação, certifique-se de usar o caminho de instalação de **configuração personalizada** (e não a instalação expressa) e, em seguida, continue a instalação como de costume.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).
