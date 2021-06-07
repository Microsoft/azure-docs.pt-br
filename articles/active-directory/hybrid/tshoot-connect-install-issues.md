---
title: Solucionar problemas de instalação do Azure AD Connect | Microsoft Docs
description: Este tópico fornece etapas para solucionar problemas com a instalação do Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25314b4a306678dc877a95194907b3d73979e4f8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89275854"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>Solucionar problemas: problemas de instalação do Azure AD Connect

## <a name="recommended-steps"></a>**Etapas Recomendadas**
Verifique o [tipo de instalação do Azure AD Connect](./how-to-connect-install-select-installation.md) adequado a você. Se você atende aos critérios de instalação expressa, recomendamos que você escolha a instalação expressa. A instalação expressa oferece as opções mínimas necessárias para concluir a instalação e, portanto, é menor a probabilidade de haver problemas. 

No entanto, se você não atende aos critérios de instalação expressa e precisa fazer a instalação personalizada, veja algumas melhores práticas a serem seguidas para evitar problemas comuns. Para fins de simplicidade, apenas as opções seletivas são mencionadas aqui:

* Verifique se você é um administrador no computador no qual está instalando o AAD Connect. Faça logon no computador com as mesmas credenciais de administrador.

* Deixe todas as opções padrão na página seguinte, exceto em “Usar um SQL Server existente”, caso queira usar o SQL Server existente. Veja [mais detalhes](./how-to-connect-install-custom.md) sobre como usar as opções de instalação personalizada. 

    ![Usar um SQL Server existente](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* Na página seguinte, escolha a opção “Criar nova conta do AD” para evitar problemas de permissão com a conta existente.

    ![Conta da floresta do AD](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**Problemas comuns**

* [Problemas de conectividade com Active Directory locais](./reference-connect-adconnectivitytools.md).

* [Problemas de conectividade com o Azure Active Directory online](./tshoot-connect-connectivity.md).

* [Problemas de permissão com Active Directory locais](./how-to-connect-configure-ad-ds-connector-account.md).

## <a name="recommended-documents"></a>**Documentos recomendados**
* [Pré-requisitos do Azure AD Connect](./how-to-connect-install-prerequisites.md)
* [Selecione o tipo de instalação para o Azure AD Connect](./how-to-connect-install-select-installation.md)
* [Introdução ao Azure AD Connect usando configurações expressas](./how-to-connect-install-express.md)
* [Instalação personalizada do Azure AD Connect](./how-to-connect-install-custom.md)
* [Azure AD Connect: atualização de uma versão anterior para a mais recente](./how-to-upgrade-previous-version.md)
* [Azure AD Connect: o que é o servidor de preparo?](./plan-connect-topologies.md#staging-server)
* [O que é o módulo PowerShell ADConnectivityTool?](./how-to-connect-adconnectivitytools.md)

## <a name="next-steps"></a>Próximas etapas
- [Sincronização de Azure ad Connect](how-to-connect-sync-whatis.md).
- [O que é identidade híbrida?](whatis-hybrid-identity.md)