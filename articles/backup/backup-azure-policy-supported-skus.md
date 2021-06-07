---
title: SKUs de VM com suporte para o Azure Policy
description: Um artigo que descreve os SKUs de VM com suporte (por Publicador, oferta de imagem e SKU de imagem) que têm suporte para as políticas internas do Azure fornecidas pelo backup
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 4658d1beffe707682e173491edea1eac0db9c811
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "82183612"
---
# <a name="supported-vm-skus-for-azure-policy"></a>SKUs de VM com suporte para o Azure Policy

O backup do Azure fornece uma política interna (usando Azure Policy) que pode ser atribuída a **todas as VMs do Azure em um local especificado dentro de uma assinatura ou grupo de recursos**. Quando essa política é atribuída a um determinado escopo, todas as novas VMs criadas nesse escopo são automaticamente configuradas para backup em um **cofre existente no mesmo local e assinatura**. A tabela a seguir lista todos os SKUs de VM com suporte nesta política.

## <a name="supported-vms"></a>VMs com suporte *

**Nome da política:** Configurar o backup em VMs de um local para um cofre central existente no mesmo local

Publicador de imagem | Oferta de imagem | SKU de imagem
--- | --- | ---
MicrosoftWindowsServer | WindowsServer | Windows Server 2008 R2 SP1 (2008-R2-SP1)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2008 R2 SP (2008-R2-SP1-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 datacenter (2012 – Datacenter)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2012 datacenter (2012 – datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 R2 Datacenter (2012-R2 – Datacenter)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2012 R2 Datacenter (2012-R2-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 datacenter (2016 – Datacenter)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Datacenter – Server Core (2016-Datacenter-Server-Core)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2016 Datacenter – Server Core (2016-Datacenter-Server-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2016 datacenter (2016 – datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Núcleo do Windows Server 2019 Datacenter Server com contêineres (2016-datacenter-com-containers)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Host da Sessão da Área de Trabalho Remota 2016 (2016-datacenter-com-RDSH)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 datacenter (2019 – Datacenter)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core (2019 – datacenter-Core)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter Server Core (2019-datacenter-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | Núcleo do Windows Server 2019 Datacenter Server com contêineres (2019-datacenter-Core-with-containers)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Núcleo do Windows Server 2019 Datacenter Server com contêineres (2019-datacenter-Core-with-containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 datacenter (2019 – datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 datacenter com contêineres (2019-datacenter-com-containers)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 datacenter com contêineres (2019-datacenter-com-containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 datacenter (ZH-CN) (2019-datacenter-zhcn)
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1709-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1709-com-containers-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1803-com-containers-smalldisk
MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | Todas as SKUs de imagem
MicrosoftSQLServer | SQL2016SP1-WS2016 | Todas as SKUs de imagem
MicrosoftSQLServer | SQL2016-WS2016 | Todas as SKUs de imagem
MicrosoftSQLServer | SQL2016SP1-WS2016-BYOL | Todas as SKUs de imagem
MicrosoftSQLServer | SQL2012SP3-WS2012R2 | Todas as SKUs de imagem
MicrosoftSQLServer | SQL2016-WS2012R2 | Todas as SKUs de imagem
MicrosoftSQLServer | SQL2014SP2-WS2012R2 | Todas as SKUs de imagem
MicrosoftSQLServer | SQL2012SP3-WS2012R2-BYOL | Todas as SKUs de imagem
MicrosoftSQLServer | SQL2014SP1-WS2012R2-BYOL | Todas as SKUs de imagem
MicrosoftSQLServer | SQL2014SP2-WS2012R2-BYOL | Todas as SKUs de imagem
MicrosoftSQLServer | SQL2016-WS2012R2-BYOL | Todas as SKUs de imagem
MicrosoftRServer | MLServer-WS2016 | Todas as SKUs de imagem
MicrosoftVisualStudio | VisualStudio | Todas as SKUs de imagem
MicrosoftVisualStudio | Windows | Todas as SKUs de imagem
MicrosoftDynamicsAX | Dynamics | Pré-req-AX7-Onebox-U8
microsoft-ads | Windows-Data-Science-VM | Todas as SKUs de imagem
MicrosoftWindowsDesktop | Windows-10 | Todas as SKUs de imagem
RedHat | RHEL | 6,7, 6,8, 6,9, 6,10, 7,2, 7,3, 7,4, 7,5, 7,6, 7,7
RedHat | RHEL-SAP-HANA | 6,7, 7,2, 7,3
SUSE | SLES | 12. X
SUSE | SLES-HPC | 12. X
SUSE | SLES-HPC-Priority | 12. X
SUSE | SLES-SAP | 12. X
SUSE | SLES-SAP-BYOS | 12. X
SUSE | SLES-Priority | 12. X
SUSE | SLES-BYOS | 12. X
SUSE | SLES-SAPCAL | 12. X
SUSE | SLES-Standard | 12. X
Canônico | UbuntuServer | 14.04.0-LTS
Canônico | UbuntuServer | 14.04.1-LTS
Canônico | UbuntuServer | 14.04.2-LTS
Canônico | UbuntuServer | 14.04.3-LTS
Canônico | UbuntuServer | 14.04.4-LTS
Canônico | UbuntuServer | 14.04.5-LTS-DIÁRIO
Canônico | UbuntuServer | 14.04.5-LTS
Canônico | UbuntuServer | 16.04-LTS-DIÁRIO
Canônico | UbuntuServer | 16.04-LTS
Canônico | UbuntuServer | 16.04.0-LTS
Canônico | UbuntuServer | 18.04-DAILY-LTS
Canônico | UbuntuServer | 18.04-LTS
Oracle | Oracle-Linux | 6,8, 6,9, 6,10, 7,3, 7,4, 7,5, 7,6
OpenLogic | CentOS | 6. X, 7. X
OpenLogic | CentOS – LVM | 6. X, 7. X
OpenLogic | CentOS – SRIOV | 6. X, 7. X
cloudera | cloudera-centos-os | 7. X
