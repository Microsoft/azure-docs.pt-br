---
title: 'Identidade Híbrida: comparação de ferramentas de integração de diretório | Microsoft Docs'
description: Esta é a página que fornece uma tabela abrangente que compara as diversas ferramentas de integração de diretório que podem ser usadas para a integridade de diretório.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 1e62a4bd-4d55-4609-895e-70131dedbf52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/07/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9c8f5a75fea6ddd85026407fe4784c8a4589e33
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89278302"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Comparação de ferramentas de integração de diretório da Identidade Híbrida
Ao longo dos anos, as ferramentas de integração de diretório cresceram e evoluíram.  


- O [fim](/previous-versions/windows/desktop/forefront-2010/ff182370(v=vs.100)) e o [mim](/microsoft-identity-manager/microsoft-identity-manager-2016) ainda têm suporte e permitem principalmente a sincronização entre sistemas locais.   O [conector do Microsoft Azure AD do fim](/previous-versions/mim/dn511001(v=ws.10)) tem suporte no fim e no mim, mas não é recomendado para novas implantações – os clientes com fontes locais como observações ou SAP HCM devem usar o mim para popular Active Directory Domain Services (AD DS) e, em seguida, usar Azure ad Connect sincronização ou Azure ad Connect provisionamento de nuvem para sincronizar do AD DS com o Azure AD.
- [Azure ad Connect a sincronização](how-to-connect-sync-whatis.md) incorpora os componentes e a funcionalidade lançados anteriormente no DirSync e Azure ad Sync, para sincronizar entre AD DS florestas e o Azure AD.  
- [Azure ad Connect provisionamento de nuvem](../cloud-provisioning/what-is-cloud-provisioning.md) é um novo agente da Microsoft para sincronização de AD DS ao Azure AD, útil para cenários como fusão e aquisição, onde as florestas do AD da empresa adquiridas são isoladas das florestas do AD da empresa pai.

Para saber mais sobre as diferenças entre Azure AD Connect sincronização e Azure AD Connect provisionamento de nuvem, confira o artigo [o que é Azure ad Connect provisionamento de nuvem?](../cloud-provisioning/what-is-cloud-provisioning.md)

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).