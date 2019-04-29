---
title: Requisitos de integração do SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Requisitos de integração do SAP HANA no Azure (Instâncias Grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/31/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 252c84bce2b70f6931593fe9410abe6cc146b5bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679044"
---
# <a name="onboarding-requirements"></a>Requisitos de integração

Estes são os requisitos para execução SAP HANA no Azure (Instâncias Grandes).

**Microsoft Azure**

- Uma assinatura do Azure que pode ser vinculada ao SAP HANA no Azure (Instâncias Grandes).
- Contrato do Suporte Premier do Microsoft Azure. Para obter informações específicas relacionadas à execução do SAP no Azure, confira [Nota de Suporte SAP nº 2015553 – SAP no Microsoft Azure: Pré-requisitos de suporte](https://launchpad.support.sap.com/#/notes/2015553). Se você usar unidades do HANA em Instâncias Grandes com 384 ou mais CPUs, também será necessário estender o contrato de suporte Premier para incluir o Azure Rapid Response.
- O reconhecimento das SKUs do HANA em Instâncias Grandes será necessário após executar um exercício de dimensionamento com o SAP.

**Conectividade de rede**

- ExpressRoute entre o local e o Azure: Para conectar o data center local ao Azure, não deixe de solicitar pelo menos uma conexão de 1 Gbps do seu ISP. A conectividade entre unidades de instância grande do HANA e o Azure também está usando a tecnologia do ExpressRoute. Essa conexão ExpressRoute entre as unidades de instância grande do HANA e o Azure está incluída no preço das unidades de instância grande do HANA, incluindo todos os encargos de entrada e saída de dados desse circuito específico do ExpressRoute. Portanto, como cliente, não ocorram custos adicionais além do seu link de ExpressRoute entre local e o Azure.

**Sistema operacional**

- Licenças para SUSE Linux Enterprise Server 12 para aplicativos SAP.

   > [!NOTE] 
   > O sistema operacional fornecido pela Microsoft não está registrado no SUSE. Ele não é conectado a uma instância da Ferramenta de Gerenciamento de Assinaturas.

- Ferramenta de Gerenciamento de Assinaturas do SUSE Linux implantada no Azure em uma VM. Essa ferramenta permite que o SAP HANA no Azure (Instâncias Grandes) seja registrado e atualizado, respectivamente, pelo SUSE. (Não há acesso à Internet no data center do HANA em Instâncias Grandes.) 
- Licenças para o Red Hat Enterprise Linux 6.7 ou 7.x para SAP HANA.

   > [!NOTE]
   > O sistema operacional fornecido pela Microsoft não é registrado na Red Hat. Ele não é conectado a uma instância do Gerenciador de Assinaturas do Red Hat.

- Gerenciador de Assinaturas do Red Hat implantado no Azure em uma VM. O Gerenciador de Assinaturas do Red Hat permite que o SAP HANA do Azure (Instâncias Grandes) seja registrado e atualizado pelo Red Hat. (Não há acesso direto à Internet de dentro do locatário implantado no carimbo da Instância Grande do Azure.)
- A SAP também exige que você tenha um contrato de suporte com seu provedor do Linux. Esse requisito não é removido pela solução do HANA em Instâncias Grandes ou por executar o Linux no Azure. Ao contrário de algumas das imagens da galeria do Linux Azure, a taxa de serviço *não* é incluída na oferta de solução do SAP HANA em Instâncias Grandes. É sua responsabilidade cumprir os requisitos do SAP em relação aos contratos de suporte com o distribuidor do Linux. 
   - Para o SUSE Linux, procure os requisitos do contrato de suporte em [Observação SAP n º 1984787 – SUSE Linux Enterprise Server 12: Notas de instalação](https://launchpad.support.sap.com/#/notes/1984787) e [Observação SAP n º 1056161 – suporte prioritário SUSE para aplicativos SAP](https://launchpad.support.sap.com/#/notes/1056161).
   - Para o Red Hat Linux, é necessário ter os níveis de assinatura corretos que incluem atualizações de suporte e serviço para os sistemas operacionais do SAP HANA em Instâncias Grandes. A Red Hat recomenda o Red Hat Enterprise Linux para a assinatura para a solução SAP. Veja https://access.redhat.com/solutions/3082481. 

Para a matriz de suporte das diferentes versões do SAP HANA com as diferentes versões do Linux, consulte [Nota SAP nº 2235581](https://launchpad.support.sap.com/#/notes/2235581).

Para a matriz de compatibilidade do sistema operacional e as versões de firmware/driver HLI, consulte [Atualização do sistema operacional para HLI](os-upgrade-hana-large-instance.md).


> [!IMPORTANT] 
> Para unidades do tipo II, somente a versão SLES 12 SP2 do sistema operacional tem suporte atualmente. 


**Banco de dados**

- As licenças e os componentes de instalação de software para SAP HANA (plataforma ou enterprise edition).

**Aplicativos**

- Licenças e componentes de instalação de software para quaisquer aplicativos do SAP que conectam-se ao SAP HANA e contratos de suporte do SAP relacionados.
- Licenças e os componentes de instalação de software para todos os aplicativos SAP não usado com o SAP HANA no ambiente do Azure (Instâncias Grandes) e relacionadas a contratos de suporte.

**Habilidades**

- Experiência com e conhecimento da IaaS do Azure e seus componentes.
- Experiência e conhecimento de como implantar uma carga de trabalho do SAP no Azure.
- Equipe certificada para instalação do SAP HANA.
- Habilidades de arquitetura do SAP para projetar alta disponibilidade e recuperação de desastre no SAP HANA.

**SAP**

- A expectativa é que você seja um cliente do SAP e tenha um contrato de suporte com o SAP.
- Especialmente para implementações da classe do Tipo II de SKUs do HANA em Instâncias Grandes, consulte o SAP em versões do SAP HANA e as eventuais configurações em hardware de escalonamento vertical de tamanho grande.

**Próximas etapas**
- Confira [Arquitetura do SAP HANA (Instâncias Grandes) no Azure](hana-architecture.md)