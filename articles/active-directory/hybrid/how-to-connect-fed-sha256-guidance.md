---
title: Alterar o algoritmo de hash de assinatura para Microsoft 365 confiança de terceira parte confiável-Azure
description: Esta página fornece diretrizes para alterar o algoritmo SHA para confiança de Federação com Microsoft 365.
keywords: SHA1, SHA256, M365, Federação, aadconnect, ADFS, AD FS, alterar Sha, confiança de Federação, confiança de terceira parte confiável
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: cf6880e2-af78-4cc9-91bc-b64de4428bbd
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6bf9347d4d14e6583febd4ffaf0447e912133b80
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89660916"
---
# <a name="change-signature-hash-algorithm-for-microsoft-365-relying-party-trust"></a>Alterar o algoritmo de hash de assinatura para Microsoft 365 confiança de terceira parte confiável
## <a name="overview"></a>Visão geral
O AD FS (Serviços de Federação do Azure Active Directory) assina seus tokens para o Microsoft Azure Active Directory para garantir que eles não possam ser violados. Essa assinatura pode ser baseada em SHA1 ou em SHA256. O Azure Active Directory agora dá suporte para tokens assinados com um algoritmo SHA256, e recomendamos configurar o algoritmo de assinatura do token para SHA256 no nível mais alto de segurança. Este artigo descreve as etapas necessárias para definir o algoritmo de assinatura de token para o nível SHA256 mais seguro.

>[!NOTE]
>A Microsoft recomenda o uso do SHA256 como o algoritmo de assinatura de tokens, pois ele é mais seguro do que o SHA1; no entanto, o SHA1 ainda é uma opção com suporte.

## <a name="change-the-token-signing-algorithm"></a>Alterar o algoritmo de assinatura de token
Depois de definir o algoritmo de assinatura com um dos dois processos abaixo, AD FS assina os tokens para Microsoft 365 confiança de terceira parte confiável com SHA256. Você não precisa fazer nenhuma alteração de configuração extra, e essa alteração não tem impacto sobre sua capacidade de acessar Microsoft 365 ou outros aplicativos do Azure AD.

### <a name="ad-fs-management-console"></a>Console de gerenciamento do AD FS
1. Abra o console de gerenciamento do AD FS no servidor primário do AD FS.
2. Expanda o nó do AD FS e clique em **Objetos de Confiança de Terceira Parte Confiável**.
3. Clique com o botão direito do mouse em seu Microsoft 365/confiança de terceira parte confiável do Azure e selecione **Propriedades**.
4. Selecione a guia **Avançado** e o Secure Hash Algorithm SHA256.
5. Clique em **OK**.

![Algoritmo de assinatura SHA256 - MMC](./media/how-to-connect-fed-sha256-guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>Cmdlets do PowerShell do AD FS
1. Em qualquer servidor do AD FS, abra o PowerShell com privilégios de administrador.
2. Definir o algoritmo de hash seguro usando o cmdlet **Set-AdfsRelyingPartyTrust** .
   
   <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'https://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>Leia também
* [Reparar Microsoft 365 confiança com Azure AD Connect](how-to-connect-fed-management.md#repairthetrust)

