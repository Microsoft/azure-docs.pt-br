---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: c1cca99a04e78c40cdd1061e52e49f45a73c73eb
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361551"
---
### <a name="identity-tier"></a>Camada de identidade 

A parceria Microsoft-Oracle permite que você configure uma identidade unificada entre o Azure, o OCI e seu aplicativo Oracle. Para o JD Edwards EnterpriseOne ou PeopleSoft Application Suite, uma instância do Oracle HTTP Server (OHS) é necessária para configurar o logon único entre o Azure AD e o Oracle IDCS.

O OHS atua como um proxy reverso para a camada de aplicativo, o que significa que todas as solicitações para os aplicativos finais passam por ele. O WebGate do Oracle Access Manager é um plug-in do servidor Web OHS que intercepta todas as solicitações que vão para o aplicativo final. Se um recurso que está sendo acessado for protegido (requer uma sessão autenticada), o WebGate iniciará o fluxo de autenticação OIDC com o serviço de nuvem de identidade por meio do navegador do usuário. Para obter mais informações sobre os fluxos com suporte pelo WebGate do OpenID Connect, consulte a [documentação do Oracle Access Manager](https://docs.oracle.com/en/middleware/idm/access-manager/12.2.1.3/aiaag/integrating-webgate-oidc-server.html).

Com essa configuração, um usuário já conectado ao Azure AD pode navegar para o aplicativo JD Edwards EnterpriseOne ou PeopleSoft sem fazer logon novamente, por meio do serviço de nuvem de identidades da Oracle. Os clientes que implantam essa solução têm os benefícios do logon único, incluindo um único conjunto de credenciais, uma experiência de logon aprimorada, segurança aprimorada e custo de suporte técnico reduzido.

Para saber mais sobre como configurar o logon único para JD Edwards EnterpriseOne ou PeopleSoft com o Azure AD, confira o [White Paper](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf)associado da Oracle.