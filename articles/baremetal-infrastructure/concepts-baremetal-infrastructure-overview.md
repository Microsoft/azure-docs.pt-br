---
title: Visão geral da visualização da infraestrutura do BareMetal no Azure
description: Visão geral da infraestrutura do BareMetal no Azure.
ms.custom: references_regions
ms.topic: conceptual
ms.date: 1/4/2021
ms.openlocfilehash: 603aa6504531ef8a75fccbc9d9cc6de648b42373
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954625"
---
#  <a name="what-is-baremetal-infrastructure-preview-on-azure"></a>O que é a visualização de infraestrutura do BareMetal no Azure?

A infraestrutura BareMetal do Azure fornece uma solução segura para migrar cargas de trabalho personalizadas da empresa. As instâncias de BareMetal são hardware de host/servidor não compartilhado atribuído a você. Ele desbloqueia a portabilidade de sua solução local com cargas de trabalho especializadas que exigem hardware certificado, licenciamento e contratos de suporte. O Azure lida com o monitoramento e a manutenção da infraestrutura para você, enquanto o monitoramento do sistema operacional (SO) e o monitoramento de aplicativos estão dentro de sua propriedade.

A infraestrutura do BareMetal fornece um caminho para modernizar seu panorama de infraestrutura e, ao mesmo tempo, manter seus investimentos e arquitetura existentes. Com a infraestrutura do BareMetal, você pode colocar cargas de trabalho especializadas no Azure, permitindo o acesso e a integração com os serviços do Azure com baixa latência.

## <a name="sku-availability-in-azure-regions"></a>Disponibilidade de SKU em regiões do Azure
A infraestrutura BareMetal para cargas de trabalho especializadas e de uso geral está disponível, começando com quatro regiões baseadas em carimbos de revisão 4,2 (Rev 4,2):
- Europa Ocidental
- Norte da Europa
- Leste dos EUA 2
- Centro-Sul dos Estados Unidos

>[!NOTE]
>A **Rev 4,2** é a mais recente estrutura de BareMetal de marca que usa a arquitetura Rev 4 existente.  A Rev 4 fornece mais proximidade com os hosts da VM (máquina virtual) do Azure. Ele tem melhorias significativas na latência de rede entre as VMs do Azure e as unidades de instância BareMetal implantadas em carimbos de Rev 4 ou linhas.  Você pode acessar e gerenciar suas instâncias do BareMetal por meio do portal do Azure. 

## <a name="support"></a>Suporte
A infraestrutura BareMetal é compatível com ISO 27001, ISO 27017, SOC 1 e SOC 2.  Ele também usa um modelo BYOL (traga sua própria licença): sistema operacional, carga de trabalho especializada e aplicativos de terceiros.  

Assim que você receber acesso à raiz e controle total, assumirá a responsabilidade por:
- Projetando e implementando soluções de backup e recuperação, alta disponibilidade e recuperação de desastre
- Licenciamento, segurança e suporte para so e software de terceiros

A Microsoft é responsável por:
- Fornecendo o hardware para cargas de trabalho especializadas 
- Provisionando o sistema operacional

:::image type="content" source="media/baremetal-support-model.png" alt-text="Modelo de suporte de infraestrutura do BareMetal" border="false":::

## <a name="compute"></a>Computação
A infraestrutura do BareMetal oferece várias SKUs para cargas de trabalho especializadas. Os SKUs disponíveis variam desde o sistema menor de dois soquetes até o sistema de 24 soquetes. Use os SKUs específicos da carga de trabalho para sua carga de trabalho especializada.

O selo da instância BareMetal em si combina os seguintes componentes:

- **Computação:** Servidores baseados em uma geração diferente de processadores Intel Xeon que fornecem a capacidade de computação necessária e são certificados para a carga de trabalho especializada.

- **Rede:** Uma malha de rede unificada de alta velocidade interconecta os componentes de computação, armazenamento e LAN.

- **Armazenamento:** Uma infraestrutura acessada por meio de uma malha de rede unificada.

Na infraestrutura multilocatário do carimbo BareMetal, os clientes são implantados em locatários isolados. Ao implantar um locatário, você nomeia uma assinatura do Azure em seu registro do Azure. Essa assinatura do Azure é aquela que as instâncias BareMetal são cobradas.

>[!NOTE]
>Um cliente implantado na instância BareMetal é isolado em um locatário. Um locatário é isolado de outros locatários na camada de rede, de armazenamento e de computação. As unidades de armazenamento e de computação atribuídas aos diferentes locatários não podem ver um ao outro ou se comunicar umas com as outras nas instâncias de BareMetal.

## <a name="os"></a>Sistema operacional
Durante o provisionamento da instância do BareMetal, você pode selecionar o sistema operacional que deseja instalar nos computadores. 

>[!NOTE]
>Lembre-se de que a infraestrutura BareMetal é um modelo BYOL.

As versões disponíveis do sistema operacional Linux são:
- Red Hat Enterprise Linux (RHEL) 7,6
- SUSE Linux Enterprise Server (SLES)
   - SLES 12 SP2
   - SLES 12 SP3
   - SLES 12 SP4
   - SLES 12 SP5
   - SLES 15 SP1

## <a name="storage"></a>Armazenamento
Instâncias de BareMetal com base em tipo de SKU específico vêm com armazenamento NFS predefinido para o tipo de carga de trabalho específico. Ao provisionar o BareMetal, você pode provisionar mais armazenamento com base em seu crescimento estimado enviando uma solicitação de suporte. Todo o armazenamento é fornecido com um disco flash na revisão 4,2 com suporte para NFSv3 e NFSv4. A revisão mais recente 4,5 NVMe SSD estará disponível. Para obter mais informações sobre o dimensionamento de armazenamento, consulte a seção [tipo de carga de trabalho BareMetal](../virtual-machines/workloads/sap/get-started.md) .

>[!NOTE]
>O armazenamento usado para BareMetal atende [padrão FIPS (FIPS)](/microsoft-365/compliance/offering-fips-140-2) os requisitos de publicação 140-2 que oferecem criptografia em repouso por padrão. Os dados são armazenados com segurança nos discos.

## <a name="networking"></a>Rede
A arquitetura dos serviços de rede do Azure é um componente fundamental para uma implantação bem-sucedida de cargas de trabalho especializadas em instâncias do BareMetal. É provável que nem todos os sistemas de ti estejam localizados no Azure já. O Azure oferece tecnologia de rede para que o Azure pareça uma data center virtual para suas implantações de software local. A funcionalidade de rede do Azure necessária para instâncias de BareMetal é:

- As redes virtuais do Azure são conectadas ao circuito do ExpressRoute que se conecta aos seus ativos de rede local.
- Um circuito do ExpressRoute que se conecta no local para o Azure deve ter uma largura de banda mínima de 1 Gbps ou superior.
- Active Directory estendido e DNS no Azure ou completamente em execução no Azure.

O uso do ExpressRoute permite que você estenda sua rede local para o Microsoft Cloud por uma conexão privada com a ajuda de um provedor de conectividade. Você pode habilitar o **Expressroute Premium** para estender a conectividade entre limites de geopolítica ou usar o **local do expressroute** para transferência de dados econômica entre o local próximo à região do Azure que você deseja.

As instâncias de BareMetal são provisionadas no intervalo de endereços IP do servidor de VNET do Azure.

:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-infrastructure-diagram.png" alt-text="Diagrama de infraestrutura BareMetal do Azure" lightbox="media/baremetal-infrastructure-portal/baremetal-infrastructure-diagram.png" border="false":::

A arquitetura mostrada é dividida em três seções:
- **À esquerda:** mostra a infraestrutura local do cliente que executa diferentes aplicativos, conectando-se por meio do parceiro ou roteador de borda locais como Equinix. Para obter mais informações, consulte [provedores e locais de conectividade: Azure ExpressRoute](../expressroute/expressroute-locations.md).
- **Center:** mostra o [ExpressRoute](../expressroute/expressroute-introduction.md) provisionado usando sua assinatura do Azure oferecendo conectividade com a rede do Azure Edge.
- **Direita:** mostra o IaaS do Azure e, nesse caso, o uso de VMs para hospedar seus aplicativos, que são provisionados em sua rede virtual do Azure.
- **Inferior:** mostra o uso do gateway de expressroute habilitado com o [expressroute FastPath](../expressroute/about-fastpath.md) para conectividade BareMetal que oferece baixa latência.   
   >[!TIP]
   >Para dar suporte a isso, o gateway de ExpressRoute deve ser UltraPerformance.  Para obter mais informações, consulte [sobre gateways de rede virtual do ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md).

## <a name="next-steps"></a>Próximas etapas

A próxima etapa é aprender a identificar e interagir com as unidades de instância do BareMetal por meio do portal do Azure.

> [!div class="nextstepaction"]
> [Gerenciar Instâncias de BareMetal por meio do portal do Azure](connect-baremetal-infrastructure.md)