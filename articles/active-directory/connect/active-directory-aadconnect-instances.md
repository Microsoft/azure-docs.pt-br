---
title: "Azure AD Connect: instâncias do serviço de sincronização | Microsoft Docs"
description: "Esta página documenta considerações especiais para instâncias do Azure AD."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: e8321c3d16253226a5931cacbce6fa5d50b697bd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: considerações especiais para instâncias
O Azure AD Connect é mais comumente usado com a instância mundial do Azure AD e o Office 365. Mas também existem outras instâncias que têm requisitos diferentes para URLs e outras considerações especiais.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Alemanha
O [Microsoft Cloud Alemanha](http://www.microsoft.de/cloud-deutschland) é uma nuvem soberana operada por uma administradora de dados alemã.

| URLs para abrir no servidor proxy |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| + Listas de revogação de certificados |

Ao entrar em seu locatário do Azure AD, você deverá usar uma conta com o domínio onmicrosoft.de.

Recursos atualmente indisponíveis no Microsoft Cloud Alemanha:

* O **Azure AD Connect Health** não está disponível.
* As **Atualizações automáticas** não estão disponíveis.
* O **Write-back de senha** está disponível em versão prévia no Azure AD Connect versão 1.1.570.0 e posteriores.
* Outros serviços do Azure AD Premium não estão disponíveis.

## <a name="microsoft-azure-government-cloud"></a>Nuvem do Microsoft Azure Governamental
A [Nuvem do Microsoft Azure Governamental](https://azure.microsoft.com/features/gov/) é uma nuvem para o governo dos EUA.

Esta nuvem teve suporte em versões mais antigas do DirSync. A partir da build 1.1.180 do Azure AD Connect, há suporte para a próxima geração da nuvem. Essa geração está usando pontos de extremidade com base somente nos EUA e tem uma lista de URLs diferente para abrir em seu servidor proxy.

| URLs para abrir no servidor proxy |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*.gov.us.microsoftonline.com |
| + Listas de revogação de certificados |

O Azure AD Connect não é capaz de detectar automaticamente que o locatário do Azure AD está localizado na nuvem Governamental. Em vez disso, você precisa executar as seguintes ações ao instalar o Azure AD Connect.

1. Inicie a instalação do Azure AD Connect.
2. Quando você consultar a primeira página na qual recebe uma solicitação para aceitar o EULA, não continue, mas deixe o assistente de instalação em execução.
3. Inicie o regedit e altere a chave do registro `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` para o valor `2`.
4. Vá para o assistente de instalação do Azure AD Connect, aceite o EULA e continue. Durante a instalação, certifique-se de usar o caminho de instalação **configuração personalizada** (e não a instalação Expressa). Em seguida, continue a instalação como de costume.

Recursos atualmente indisponíveis na nuvem do Microsoft Azure Governamental:

* O **Azure AD Connect Health** não está disponível.
* As **Atualizações automáticas** não estão disponíveis.
* O **Write-back de senha** está disponível em versão prévia no Azure AD Connect versão 1.1.570.0 e posterior.
* Outros serviços do Azure AD Premium não estão disponíveis.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).
