---
title: Integrar o Microsoft Azure com a infraestrutura de nuvem da Oracle | Microsoft Docs
description: Saiba mais sobre as soluções que integram aplicativos Oracle em execução no Microsoft Azure com bancos de dados na nuvem infraestrutura OCI (Oracle).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: fcdd46ea60ea53088ffacd7d13693b16a208d527
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707462"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure-preview"></a>Soluções de aplicativos Oracle a integração do Microsoft Azure e infraestrutura de nuvem da Oracle (versão prévia)

A Microsoft e Oracle fizeram uma parceria para oferecer baixa latência, conectividade de nuvem de alta taxa de transferência, permitindo que você aproveite o melhor de ambas as nuvens. 

Usando essa conectividade entre a nuvem, você pode particionar um aplicativo de várias camadas para executar sua camada de banco de dados na nuvem infraestrutura OCI (Oracle) e o aplicativo e outras camadas no Microsoft Azure. A experiência é semelhante à execução de pilha de toda a solução em uma única nuvem. 

> [!IMPORTANT]
> Esse recurso de nuvem está atualmente em versão prévia e algumas [limitações se aplicam](#preview-limitations). As versões prévias são disponibilizadas com a condição de que você concorde com os [termos de uso complementares](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

Se você estiver interessado na implantação de soluções da Oracle inteiramente na infraestrutura do Azure, consulte [imagens de VM Oracle e sua implantação no Microsoft Azure](oracle-vm-solutions.md).

## <a name="scenario-overview"></a>Visão geral do cenário

Conectividade de nuvem fornece uma solução para executar aplicativos de líderes do setor da Oracle e seus próprios aplicativos personalizados, em máquinas virtuais do Azure enquanto aproveita os benefícios dos serviços de banco de dados hospedado no OCI. 

Você pode executar em uma configuração de nuvem entre os aplicativos incluem:

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Aplicativos de varejo do Oracle
* Gerenciamento financeiro do Oracle Hyperion

O diagrama a seguir é uma visão geral da solução conectada. Para simplificar, o diagrama mostra apenas uma camada de aplicativo e uma camada de dados. Dependendo da arquitetura do aplicativo, sua solução pode incluir camadas adicionais, como uma camada da web no Azure. Para obter mais informações, consulte as próximas seções.

![Visão geral da solução do Azure OCI](media/oracle-oci-overview/crosscloud.png)

## <a name="preview-limitations"></a>Limitações de visualização

* Conectividade de nuvem na visualização é limitada para a região do Azure Leste dos Estados Unidos (eastus) e o Ashburn OCI (us-ashburn-1).

## <a name="networking"></a>Rede

Os clientes corporativos normalmente optam por Diversificar e implantar cargas de trabalho ao longo de várias nuvens para vários comerciais e operacionais motivos. Para Diversificar, os clientes interconexão de redes em nuvem usando a internet, a VPN IPSec, ou usando a solução de conectividade direta do provedor de nuvem por meio de sua rede local. Interconexão de redes em nuvem pode exigir o investimentos significativos em tempo, dinheiro, design, aquisição, instalação, teste e operações. 

Para enfrentar esses desafios do cliente, a Oracle e Microsoft habilitou uma experiência integrada de várias nuvens. A rede entre nuvens é estabelecida por conectar-se um [ExpressRoute](../../../expressroute/expressroute-introduction.md) circuito no Microsoft Azure com um [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) circuito no OCI. Essa conectividade está possíveis em que um local de emparelhamento do ExpressRoute do Azure em proximidade com ou no mesmo local de emparelhamento FastConnect o OCI. Essa configuração permite a conectividade segura e rápida entre duas nuvens sem a necessidade de um provedor de serviços intermediária.

Usando o ExpressRoute e FastConnect, os clientes podem emparelhar uma rede virtual no Azure com uma rede virtual de nuvem no OCI, desde que o espaço de endereço IP privado não se sobreponha. Emparelhar duas redes permite que um recurso na rede virtual para se comunicar com um recurso na rede de nuvem virtual OCI como se eles são ambos na mesma rede.

## <a name="network-security"></a>Segurança de rede

Segurança de rede é um componente crucial de qualquer aplicativo empresarial e é central para essa solução de várias nuvens. Qualquer tráfego que passa pela ExpressRoute e FastConnect passa de uma rede privada. Essa configuração permite uma comunicação segura entre uma rede virtual do Azure e uma rede de nuvem virtual do Oracle. Você não precisa fornecer um endereço IP a máquinas virtuais no Azure. Da mesma forma, não é necessário um gateway de internet OCI. Toda comunicação ocorre via o endereço IP privado das máquinas.

Além disso, você pode configurar [listas de segurança](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) em suas regras de segurança de rede e de nuvem virtual OCI (anexado para o Azure [grupos de segurança de rede](../../../virtual-network/security-overview.md)). Use essas regras para controlar o tráfego que flui entre as máquinas nas redes virtuais. Regras de segurança de rede podem ser adicionadas em um nível de máquina, no nível da sub-rede, bem como no nível da rede virtual.
 
## <a name="identity"></a>Identidade

Identidade é um dos principais pilares da parceria entre a Microsoft e Oracle. Um trabalho significativo foi feito para integrar [serviço de nuvem de identidade do Oracle](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDCS) com [Azure Active Directory](../../../active-directory/index.yml) (Azure AD). Azure AD é baseados em nuvem acesso e identidade do serviço de gerenciamento da Microsoft. Ele ajuda os usuários a entrar e acessar vários recursos. Azure AD também permite que você gerencie seus usuários e suas permissões.

Atualmente, essa integração permite que você gerencie em um local central, que é o Azure Active Directory. Azure AD sincroniza as alterações no diretório com o diretório correspondente do Oracle e é usado para logon único para a nuvem soluções da Oracle.

## <a name="next-steps"></a>Próximas etapas

Comece com um [rede entre nuvens](configure-azure-oci-networking.md) entre o Azure e OCI. 

Para obter mais informações e white papers sobre OCI, consulte o [Oracle Cloud](https://docs.cloud.oracle.com/iaas/Content/home.htm) documentação.
