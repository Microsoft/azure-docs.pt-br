---
title: Conectar Azure AD Identity Protection dados ao Azure Sentinel
description: Saiba como transmitir logs e alertas de Azure AD Identity Protection para o Azure Sentinel para exibir painéis, criar alertas personalizados e melhorar a investigação.
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 06/24/2020
ms.author: yelevin
ms.openlocfilehash: f420e80f10072c440f5401c042d6370a061b1a7f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98632234"
---
# <a name="connect-data-from-azure-active-directory-azure-ad-identity-protection"></a>Conectar dados da proteção de identidade do Azure Active Directory (AD do Azure)

Você pode transmitir logs de [Azure ad Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) para o Azure Sentinel para transmitir alertas para o Azure Sentinel para exibir painéis, criar alertas personalizados e melhorar a investigação. O Azure Active Directory Identity Protection fornece uma exibição consolidada em risco de usuários, detecções de risco e vulnerabilidades, com a capacidade de corrigir o risco imediatamente e definir políticas para corrigir automaticamente eventos futuros. O serviço se baseia na experiência da Microsoft em proteger as identidades dos consumidores e ganha uma considerável precisão do sinal de mais de 13.000.000.000 logons por dia. 

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter uma [assinatura Azure ad Premium P2](https://azure.microsoft.com/pricing/details/active-directory/).
- Você deve ter um usuário com permissões de administrador global ou de administrador de segurança.

## <a name="connect-to-azure-ad-identity-protection"></a>Conectar-se ao Azure AD Identity Protection

Se você tiver uma assinatura Azure AD Premium P2, Azure AD Identity Protection será incluído. Se alguma [política estiver habilitada](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) e gerando alertas, os dados do alerta poderão ser facilmente transmitidos para o Azure Sentinel.

1. No Azure Sentinel, selecione **conectores de dados** e, em seguida, clique no bloco **Azure ad Identity Protection** .

1. Clique em **conectar** para iniciar o streaming de Azure ad Identity Protection eventos no Azure Sentinel.

1. Para usar o esquema relevante em Log Analytics para os alertas de Azure AD Identity Protection, procure **SecurityAlert**.

Se você quiser testar o conector, poderá [simular detecções](../active-directory/identity-protection/howto-identity-protection-simulate-risk.md) para gerar alertas de exemplo que serão transmitidos para o Azure Sentinel.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar Azure AD Identity Protection ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
