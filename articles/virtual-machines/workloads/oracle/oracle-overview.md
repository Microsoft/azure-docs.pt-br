---
title: Soluções da Oracle no Microsoft Azure | Microsoft Docs
description: Saiba mais sobre as opções para implantar aplicativos Oracle e soluções no Microsoft Azure, incluindo executar totalmente na infraestrutura do Azure ou usar a conectividade entre a nuvem com nuvem infraestrutura OCI (Oracle).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.openlocfilehash: b8bfa0dfa82f73cad010a608150eac48c7f3d4c8
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707446"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Visão geral de aplicativos Oracle e soluções no Azure

Este artigo apresenta recursos para executar soluções da Oracle usando a infraestrutura do Azure. Consulte também detalhadas introduções a disponíveis [imagens de VM Oracle](oracle-vm-solutions.md) no Azure Marketplace e a capacidade de visualização [interconexão do Azure com infraestrutura de nuvem da Oracle (OCI)](oracle-oci-overview.md).

## <a name="oracle-databases-on-azure-infrastructure"></a>Bancos de dados Oracle na infraestrutura do Azure

Execute bancos de dados Oracle na infraestrutura do Azure usando imagens do Linux disponíveis no Azure Marketplace:

* Oracle Database 12.1, 12.2 e 18,3 Enterprise Edition 

* Oracle Database 18,3, 12.2 e 12.1 Standard Edition 

Você também pode optar por basear uma solução em uma imagem personalizada que você criar do zero no Azure ou carregar uma imagem personalizada do seu ambiente local.

Opcionalmente, configure com vários discos anexados e melhorar o desempenho do banco de dados ao instalar o Oracle Automated Storage Management (ASM).

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Aplicativos no Oracle Linux e WebLogic Server

Execute aplicativos empresariais no Azure em sistemas de operacionais com suporte do Oracle. As imagens a seguir estão disponíveis no Azure Marketplace:

* Oracle WebLogic Server 12.1.2

* Oracle Linux (UEK) 6.8, 6.9, 6.10, 7.3, 7.4, 7.5 e 7.6

## <a name="high-availability-and-disaster-recovery-options"></a>Opções de recuperação de desastres e disponibilidade alta

* Configurar o Oracle Data Guard, Active Data Guard ou GoldenGate na infraestrutura do Azure em conjunto com [zonas de disponibilidade](../../../availability-zones/az-overview.md) para alta disponibilidade.

* Use [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) para orquestrar e gerenciar a recuperação de desastres para suas VMs do Oracle Linux no Azure e seus locais ou servidores físicos. 

* Habilitar o Oracle Real Application Clusters (RAC) no Azure usando [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="integration-of-azure-with-oci-preview"></a>Integração do Azure com o OCI (visualização)

Execute aplicativos Oracle na infraestrutura do Azure, conectada a bancos de dados de back-end na nuvem infraestrutura OCI (Oracle). Essa solução usa os seguintes recursos: 

* **A rede entre nuvens** -uso direto de interconexão disponível entre FastConnect Oracle e o Azure ExpressRoute para estabelecer conexões de alta largura de banda, privadas e baixa latência entre o aplicativo e a camada de banco de dados.
* **Integrado a identidade** -configurar a identidade federada entre o Azure AD e IDCS Oracle para criar uma fonte de identidade única para as soluções. Habilite o logon único gerenciar recursos OCI e no Azure.

### <a name="deploy-oracle-applications-on-azure"></a>Implantar aplicativos Oracle no Azure

Use modelos do Terraform para configurar a infraestrutura do Azure e instalar aplicativos Oracle validada e com suporte para executar na configuração de nuvem:

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Aplicativos de varejo do Oracle
* Gerenciamento financeiro do Oracle Hyperion

Também implante aplicativos personalizados no Azure que se conectam com OCI e outros serviços do Azure.

### <a name="set-up-oracle-databases-in-oci"></a>Configurar bancos de dados Oracle OCI

Use serviços de nuvem do banco de dados Oracle (banco de dados autônomo, RAC, Exadata, DBaaS, único nó) em conjunto com aplicativos Oracle em execução no Azure. Saiba mais sobre [opções de banco de dados do OCI](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Licenciamento

Implantação de aplicativos Oracle no Azure é baseada em um modelo "Traga sua própria licença". Supõe-se que corretamente, você está licenciado para usar o software Oracle e que você tenha um contrato de suporte em vigor com a Oracle. A Oracle tem garantia de mobilidade de licença do local para o Azure. Veja como o Oracle-Azure [perguntas frequentes sobre](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a implantação [imagens de VM Oracle](oracle-vm-solutions.md) na infraestrutura do Azure.

* Saiba mais sobre como [interconexão do Azure com OCI](oracle-oci-overview.md).