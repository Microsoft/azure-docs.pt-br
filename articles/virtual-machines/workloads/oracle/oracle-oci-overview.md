---
title: Integre o Microsoft Azure com o Oracle Cloud Infrastructure | Microsoft Docs
description: Saiba mais sobre soluções que integram aplicativos Oracle em execução no Microsoft Azure com bancos de dados no Oracle Cloud Infrastructure (OCI).
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 06/01/2020
ms.author: kegorman
ms.openlocfilehash: b27db94775e2ba8275dbdab4bf5bd61cc0fdf5c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101666868"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure"></a>Soluções de aplicativos Oracle que integram o Microsoft Azure e o Oracle Cloud Infrastructure

A Microsoft e a Oracle fizeram uma parceria para fornecer conectividade entre nuvens de baixa latência e alta taxa de transferência, permitindo que você aproveite o melhor das duas nuvens. 

Usando essa conectividade entre nuvens, você pode particionar um aplicativo multicamadas para executar sua camada de banco de dados no Oracle Cloud Infrastructure (OCI) e o aplicativo e outras camadas no Microsoft Azure. A experiência é semelhante à execução de toda a pilha de solução em uma única nuvem. 

Se você estiver interessado em executar seu middleware, incluindo o WebLogic Server, na infraestrutura do Azure, mas tiver o banco de dados Oracle em execução no OCI, confira [Aplicativos do WebLogic Server](oracle-weblogic.md).

Se você estiver interessado em implantar soluções Oracle totalmente na infraestrutura do Azure, confira [Imagens de VM do Oracle e sua implantação no Microsoft Azure](oracle-vm-solutions.md).

## <a name="scenario-overview"></a>Visão geral do cenário

A conectividade entre nuvens fornece uma solução para que você execute aplicativos líderes do setor da Oracle e seus aplicativos personalizados em máquinas virtuais do Azure, aproveitando os benefícios dos serviços de banco de dados hospedados no OCI. 

A partir de maio de 2020, os seguintes aplicativos são certificados em uma configuração de nuvem cruzada:

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Aplicativos Oracle Retail
* Oracle Hyperion Financial Management

O diagrama a seguir é uma visão geral de alto nível da solução conectada. Para simplificar, o diagrama mostra apenas uma camada de aplicativo e uma camada de dados. Dependendo da arquitetura do aplicativo, sua solução pode incluir camadas adicionais, como um cluster do WebLogic Server ou uma camada da Web no Azure. Para obter mais informações, consulte as próximas seções.

![Visão geral da solução Azure OCI](media/oracle-oci-overview/crosscloud.png)

## <a name="region-availability"></a>Disponibilidade de região 

A conectividade entre nuvens é limitada às seguintes regiões:
* Azure Leste dos EUA (EastUS) e OCI Ashburn, VA (leste dos EUA)
* Azure Sul do Reino Unido (UKSouth) e OCI Londres (sul do Reino Unido)
* Azure Central do Canadá (CanadaCentral) e OCI Toronto (sudeste do Canadá)
* Azure Europa Ocidental (WestEurope) e OCI Amsterdam (Noroeste dos Países Baixos)
* Azure Leste do Japão (JapanEast) e OCI Tóquio (leste do Japão)
* Azure oeste dos EUA (Westus) & OCI San José (oeste dos EUA)

## <a name="networking"></a>Rede

Os clientes corporativos geralmente optam por diversificar e implantar cargas de trabalho em várias nuvens por vários motivos operacionais e de negócios. Para diversificar, os clientes interconectam redes em nuvem usando a Internet, VPN IPSec ou usando a solução de conectividade direta do provedor de nuvem por meio de sua rede local. A interconexão de redes em nuvem pode exigir investimentos significativos em tempo, dinheiro, design, aquisição, instalação, teste e operações. 

Para resolver esses desafios do cliente, a Oracle e a Microsoft habilitaram uma experiência de várias nuvens integradas. A rede entre nuvens é estabelecida conectando um circuito de [ExpressRoute](../../../expressroute/expressroute-introduction.md) no Microsoft Azure com um circuito de [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) em OCI. Essa conectividade é possível quando um local de emparelhamento do ExpressRoute do Azure está em proximidade com ou no mesmo local de emparelhamento que o FastConnect de OCI. Essa configuração permite uma conectividade segura e rápida entre as duas nuvens sem a necessidade de um provedor de serviços intermediário.

Com o ExpressRoute e o FastConnect, os clientes podem emparelhar uma rede virtual no Azure com uma rede de nuvem virtual no OCI, desde que o espaço de endereço IP privado não se sobreponha. O emparelhamento das duas redes permite que um recurso na rede virtual se comunique com um recurso na rede de nuvem virtual do OCI como se eles estivessem na mesma rede.

## <a name="network-security"></a>Segurança de rede

A segurança de rede é um componente crucial de qualquer aplicativo empresarial e é fundamental para essa solução de várias nuvens. Qualquer tráfego que passe pelo ExpressRoute e pelo FastConnect passará por uma rede privada. Essa configuração permite a comunicação segura entre uma rede virtual do Azure e uma rede de nuvem virtual da Oracle. Você não precisa fornecer um endereço IP público para nenhuma máquina virtual no Azure. Da mesma forma, você não precisa de um gateway de Internet no OCI. Toda a comunicação ocorre por meio do endereço IP privado dos computadores.

Além disso, você pode configurar [listas de segurança](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) em sua rede de nuvem virtual do OCI e regras de segurança (conectadas aos [grupos de segurança de rede](../../../virtual-network/network-security-groups-overview.md) do Azure). Use essas regras para controlar o fluxo de tráfego entre as máquinas nas redes virtuais. As regras de segurança de rede podem ser adicionadas em um nível de máquina, em um nível de sub-rede, bem como no nível de rede virtual.

Os [aplicativos WebLogic Server do Azure](oracle-weblogic.md) criam um grupo de segurança de rede pré-configurado para funcionar com as configurações de porta do WebLogic Server.
 
## <a name="identity"></a>Identidade

A identidade é um dos pilares principais da parceria entre a Microsoft e a Oracle. Foi feito um trabalho significativo para integrar o [IDCS (serviço de nuvem de identidade da Oracle](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html)) com o [Azure Active Directory](../../../active-directory/index.yml) (Azure AD). O Azure AD é o serviço de gerenciamento de acesso e identidade baseado em nuvem da Microsoft. Os usuários podem entrar e acessar vários recursos com a ajuda do Azure AD. O Azure AD também permite que você gerencie seus usuários e suas permissões.

Atualmente, essa integração permite que você gerencie em um local central, que é o Azure Active Directory. O Azure AD sincroniza todas as alterações no diretório com o diretório Oracle correspondente e é usado para logon único em soluções da Oracle entre nuvens.

## <a name="next-steps"></a>Próximas etapas

Introdução a uma [rede entre nuvens](configure-azure-oci-networking.md) entre o Azure e o OCI. 

Para saber mais e obter white papers sobre o OCI, confira a documentação do [Oracle Cloud](https://docs.cloud.oracle.com/iaas/Content/home.htm).
