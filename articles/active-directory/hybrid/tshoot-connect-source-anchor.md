---
title: 'Azure AD Connect: solucionar problemas de âncora de origem durante a instalação | Microsoft Docs'
description: Este tópico fornece etapas para solucionar problemas com a âncora de origem durante a instalação.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/19/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 4e6460015430850c11fbf70a005d7440ce1b92fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91305997"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>Solucionando problemas de âncora de origem durante a instalação
Este artigo explica os diferentes problemas relacionados à âncora de origem que podem ocorrer durante a instalação e oferece maneiras de resolver esses problemas.

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Âncora de origem inválida no Azure Active Directory

### <a name="custom-installation"></a>Personalização Instalada

Durante a instalação personalizada, o Azure AD Connect lê a política de âncora de origem do Azure Active Directory. Se a política existir no Azure Active Directory, Azure AD Connect aplicará a mesma política, a menos que seja substituída pelo cliente. O Assistente informa qual atributo foi lido. Além disso, o assistente avisa se você tentar substituir a política de âncora de origem.

Durante essa operação de leitura, é possível que a política de ancoragem de origem em Azure Active Directory seja inesperada. Nesse caso, Azure AD Connect não sabe o que a âncora de origem deve usar e precisa de substituição manual.</br>
![Captura de tela que mostra onde substituir manualmente a âncora de origem.](media/tshoot-connect-source-anchor/source1.png)

Para resolver esse problema, você pode substituir manualmente a âncora de origem selecionando um atributo específico. Continue com esta opção se e somente se você tiver certeza de qual atributo deve ser selecionado. Se você não tiver certeza, entre em contato com [o suporte da Microsoft](https://support.microsoft.com/contactus/) para obter diretrizes. Se você alterar a política de âncora de origem, ela poderá interromper a associação entre os usuários locais e seus recursos do Azure associados.</br>
![Captura de tela que mostra o atributo especificado que substitui a âncora de origem.](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>Instalação Expressa
Durante a instalação expressa, o Azure AD Connect lê a política de âncora de origem do Azure Active Directory. Se a política existir no Azure Active Directory, Azure AD Connect aplicará a mesma política. Não há nenhuma opção para fazer a substituição manual.

Durante essa operação de leitura, é possível que a política de ancoragem de origem em Azure Active Directory seja inesperada. Nesse caso, Azure AD Connect não sabe qual deve ser a âncora de origem.</br>
![Captura de tela que mostra o que acontece quando a âncora de origem na Azure Active Directory é inesperada.](media/tshoot-connect-source-anchor/source3.png)

Para resolver esse problema, você precisa reinstalar o usando o modo personalizado e substituir manualmente a âncora de origem selecionando um atributo específico. Continue com esta opção se e somente se você tiver certeza de qual atributo deve ser selecionado. Se você não tiver certeza, entre em contato com [o suporte da Microsoft](https://support.microsoft.com/contactus/) para obter diretrizes. Se você alterar a política de âncora de origem, ela poderá interromper a associação entre os usuários locais e seus recursos do Azure associados.

### <a name="invalid-source-anchor-in-sync-engine"></a>Âncora de origem inválida no mecanismo de sincronização
Durante a instalação, é possível Azure AD Connect tentativas de configurar o mecanismo de sincronização usando uma âncora de origem inválida. Essa operação é provavelmente um problema de produto e a instalação do Azure AD Connect falhará. Contate o [suporte da Microsoft](https://support.microsoft.com/contactus/) se você executar o no problema.</br>
![unexpected](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).
