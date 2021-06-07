---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 2b28cc742bb981e0db535d39cb0c7fbf027470ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "83673757"
---
### <a name="identity-tier"></a>Camada de identidade 

A parceria entre Microsoft e Oracle permite que você configure uma identidade unificada entre o Azure, o OCI e seu aplicativo Oracle. Para o JD Edwards EnterpriseOne ou o PeopleSoft Application Suite, uma instância do Oracle HTTP Server (OHS) é necessária para configurar o logon único entre o Microsoft Azure Active Directory e o Oracle IDCS.

O OHS atua como um proxy reverso para a camada de aplicativo, o que significa que todas as solicitações para os aplicativos finais passam por ele. O WebGate do Oracle Access Manager é um plug-in do servidor Web OHS que intercepta todas as solicitações que vão para o aplicativo final. Se um recurso que está sendo acessado estiver protegido (precisar de uma sessão autenticada), o WebGate iniciará o fluxo de autenticação OIDC com o serviço de nuvem de identidade por meio do navegador do usuário. Para obter mais informações sobre os fluxos compatíveis com o WebGate do OpenID Connect, veja a [documentação do Oracle Access Manager](https://docs.oracle.com/cd/E52734_01/oam/AIAAG/GUID-1E927D1B-FB83-425B-8768-85DB441821A4.htm#AIAAG7327).

Com essa configuração, um usuário já conectado ao Azure Active Directory consegue navegar até o aplicativo JD Edwards EnterpriseOne ou PeopleSoft sem fazer logon novamente, por meio do Oracle Identity Cloud Service. Os clientes que implantam essa solução têm os benefícios do logon único, incluindo um único conjunto de credenciais, uma melhor experiência de logon, segurança aprimorada e custo de suporte técnico reduzido.

Para saber mais sobre como configurar o logon único para o JD Edwards EnterpriseOne ou o PeopleSoft com o Azure Active Directory, veja o [whitepaper da Oracle](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf) associado.