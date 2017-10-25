---
title: "Visão geral e arquitetura do SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs"
description: "Visão geral da arquitetura de como implantar SAP HANA no Azure (Instâncias Grandes)."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/02/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 63e1820033e051b72601291c5206772192e68769
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sap-hana-large-instances-overview-and-architecture-on-azure"></a>Visão geral e arquitetura do SAP HANA (Instâncias Grandes) no Azure

## <a name="what-is-sap-hana-on-azure-large-instances"></a>O que é SAP HANA no Azure (Instâncias Grandes)?

SAP HANA no Azure (Instâncias Grandes) é uma solução exclusiva para o Azure. Além de fornecer Máquinas Virtuais do Azure para fins de implantação e execução de SAP HANA, o Azure oferece a possibilidade de executar e implantar o SAP HANA em servidores bare-metal que são dedicados a você como um cliente. O SAP HANA na solução do Azure (Instâncias Grandes) se baseia em hardware bare-metal de host/servidor não compartilhado que está atribuído a você como um cliente. O hardware de servidor é inserido em selos maiores que contêm infraestrutura de computação/servidor, de rede e de armazenamento. Isso, enquanto combinação, é certificado pela TDI do HANA. A oferta de serviço do SAP HANA no Azure (Instâncias Grandes) oferece vários tamanhos ou SKUs de servidor diferentes, começando com unidades de 72 CPUs e 768 GB de memória até unidades com 960 CPUs e 20 TB de memória.

O isolamento de cliente dentro do selo de infraestrutura é executado em locatários, o que detalhadamente se parece com:

- Rede: isolamento dos clientes dentro da pilha de infraestrutura por meio de redes virtuais por locatário atribuído pelo cliente. Um locatário é atribuído a um único cliente. Um cliente pode ter vários locatários. O isolamento de rede de locatários impede a comunicação de rede entre locatários no nível de selo de infraestrutura. Isso ocorre mesmo se os locatários pertencem ao mesmo cliente.
- Componentes de armazenamento: isolamento por meio de máquinas virtuais de armazenamento que têm volumes de armazenamento atribuídos a elas. Volumes de armazenamento podem ser atribuídos a somente uma máquina virtual de armazenamento. Uma máquina virtual de armazenamento é atribuída exclusivamente a um único locatário na pilha de infraestrutura certificada pela TDI do SAP HANA. Como resultado, volumes de armazenamento atribuídos a uma máquina virtual de armazenamento podem ser acessados somente em um locatário específico e relacionado. Além disso, eles não ficam visíveis entre os outros locatários implantados.
- Host ou servidor: uma unidade de host ou servidor não é compartilhada entre os clientes ou locatários. Um servidor ou host, implantado para um cliente, é uma unidade de computação bare-metal atômica que é atribuída a um único locatário. **Não** é usado nenhum particionamento de hardware ou de software que possa resultar em você, enquanto cliente, compartilhar um host ou um servidor com outro cliente. Volumes de armazenamento que são atribuídos à máquina virtual de armazenamento do locatário específico são montados para um servidor desse tipo. Um locatário pode ter unidades de servidor de um para muitos de diferentes SKUs atribuídos de modo exclusivo.
- Dentro de um SAP HANA no selo de infraestrutura do Azure (Instância Grande), vários locatários diferentes são implantados e isolados entre si por meio dos conceitos de locatário em nível de rede, de armazenamento e de computação. 


Essas unidades de servidor bare-metal têm suporte para executar apenas o SAP HANA. A camada de aplicativo SAP ou camada de middleware de carga de trabalho está em execução em Máquinas Virtuais do Microsoft Azure. Os selos de infraestrutura executando as unidades do SAP HANA no Azure (Instância Grande) são conectados as backbones de rede do Azure, portanto, essa conectividade de baixa latência entre as Máquinas Virtuais do Azure e as unidades do SAP HANA no Azure (Instância Grande) é fornecida.

Este documento é um dos cinco documentos que abordam o tópico de SAP HANA no Azure (Instância Grande). Neste documento, estudamos a arquitetura básica, as responsabilidades, os serviços fornecidos e, em um alto nível, os recursos da solução. Para a maioria das áreas, tais como sistema de rede e conectividade de quatro outros documentos abordam detalhes e análises aprofundadas. A documentação do SAP HANA no Azure (Instância Grande) não aborda aspectos de instalação do SAP NetWeaver ou implantações do SAP NetWeaver em VMs do Azure. Este tópico é abordado em documentação separada localizada no mesmo contêiner de documentação. 


As cinco partes deste guia abrangem os seguintes tópicos:

- [Visão geral e arquitetura do SAP HANA (instâncias grandes) no Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Infraestrutura e conectividade do SAP HANA (instâncias grandes) no Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Como instalar e configurar o SAP HANA (instâncias grandes) no Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Alta disponibilidade e recuperação de desastre do SAP HANA (instâncias grandes) no Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Solução de problemas e monitoramento do SAP HANA (instâncias grandes) no Azure](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="definitions"></a>Definições

Várias definições comuns são amplamente usadas no guia de implantação técnica e arquitetura. Observe os seguintes termos e seus significados:

- **IaaS:** Infraestrutura como Serviço
- **PaaS:** Plataforma como Serviço
- **SaaS:** Software como Serviço
- **Componente SAP:** um aplicativo SAP individual como ECC, BW, Solution Manager ou EP. Os componentes SAP podem ser baseados em tecnologias ABAP ou Java tradicionais ou em um aplicativo não baseado em NetWeaver, como o Business Objects.
- **Ambiente SAP:** um ou mais componentes SAP agrupados logicamente para executar uma função de negócios, como Desenvolvimento, QAS, Treinamento, DR ou Produção.
- **Paisagem SAP:** refere-se à totalidade dos ativos SAP na estrutura de TI de um cliente. A estrutura da SAP inclui todos os ambientes de produção e de não produção.
- **Sistema SAP:** a combinação de camada DBMS e camada de aplicativo de um sistema de desenvolvimento SAP ERP, sistema de teste SAP BW, sistema de produção SAP CRM etc. Implantações do Azure não dão suporte a divisão essas duas camadas entre local e o Azure. Isso significa que um sistema SAP é implantado localmente ou no Azure. No entanto, você pode implantar os diferentes sistemas de uma estrutura da SAP no Azure ou localmente. Por exemplo, você poderia implantar os sistemas de desenvolvimento e teste SAP CRM no Azure, enquanto implanta o sistema de produção CRM SAP localmente. No caso do SAP HANA no Azure (Instâncias Grandes), pretende-se que você hospede a camada do aplicativo SAP de sistemas SAP em VMs do Azure e a instância do SAP HANA relacionada em uma unidade no selo de Instância Grande do HANA.
- **Grande carimbo da instância:** uma pilha de infraestrutura de hardware é TDI do SAP HANA certificados e dedicado para executar instâncias do SAP HANA no Azure.
- **SAP HANA no Azure (Instâncias Grandes):** nome oficial para a oferta no Azure para executar instâncias do HANA em hardware certificado por TDI do SAP HANA implantado em carimbos de Instância Grande em diferentes regiões do Azure. O termo relacionado **Instância Grande do HANA** é a abreviação de SAP HANA no Azure (Instâncias Grandes) e é amplamente usado neste guia de implantação técnica.
- **Entre instalações**: descreve um cenário em que as VMs são implantadas em uma assinatura do Azure com conectividade site a site, multissite ou de ExpressRoute entre os datacenters locais e o Azure. Na documentação comum do Azure, esses tipos de implantações também são descritas como cenários entre instalações. O motivo para a conexão é estender domínios locais, Active Directory/OpenLDAP local e DNS local para o Azure. A estrutura local é estendida para os ativos do Azure das assinaturas do Azure. Com esta extensão, as VMs podem ser parte do domínio local. Usuários de domínio do domínio local podem acessar os servidores e podem executar serviços nessas VMs (como serviços DBMS). A comunicação e resolução de nomes entre máquinas virtuais implantadas localmente e VMs implantadas no Azure são possíveis. Esse é o cenário típico no qual a maioria dos SAP ativos são implantados. Consulte os guias [Planejamento e design para o Gateway de VPN](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e [Criar uma VNet com uma conexão Site a Site usando o Portal do Azure](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter informações mais detalhadas.
- **Locatário:** um cliente implantado no selo de Instâncias Grandes do HANA é isolado em um "locatário". Um locatário é isolado de outros locatários na camada de rede, de armazenamento e de computação. Portanto, essas unidades de computação e de armazenamento atribuídas aos locatários diferentes não podem ver umas às outras nem se comunicar entre si no nível de selo de Instância Grande do HANA. Um cliente pode escolher ter implantações em diferentes locatários. Mesmo assim, não há nenhuma comunicação entre locatários no nível de selo de Instância Grande do HANA.

Há uma variedade de recursos adicionais que foram publicadas no tópico de implantação SAP a carga de trabalho na nuvem pública do Microsoft Azure. É altamente recomendável que qualquer pessoa planejando e executando uma implantação do SAP HANA no Azure seja experiente e ciente das entidades de IaaS do Azure e da implantação de cargas de trabalho do SAP no Azure IaaS. Os recursos a seguir fornecem mais informações e devem ser consultados antes de continuar:


- [Uso das soluções SAP em máquinas virtuais do Microsoft Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="certification"></a>Certificação

Além de certificação NetWeaver, SAP exige uma certificação especial para SAP HANA dar suporte a SAP HANA em determinadas infraestruturas, como IaaS do Azure.

O SAP Note principal no NetWeaver e, até certo grau, na certificação do SAP HANA, é [SAP Note nº 1928533 – aplicativos SAP no Azure: produtos com suporte e tipos de VM do Azure](https://launchpad.support.sap.com/#/notes/1928533).

Este [SAP Note nº 2316233 - SAP HANA no Microsoft Azure (Instâncias Grandes)](https://launchpad.support.sap.com/#/notes/2316233/E) também é significativo. Ele aborda a solução descrita neste guia. Além disso, você têm suporte para executar SAP HANA no tipo GS5 VM do Azure. [Informações para esse caso são publicadas no site da SAP](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).

O SAP HANA na solução do Azure (Instâncias Grandes) mencionada no SAP Note nº 2316233 fornece aos clientes Microsoft e SAP a capacidade de implantar grandes cargas de trabalho do SAP Business Suite, do SAP Business Warehouse (BW), do S/4 HANA, do BW/4HANA ou outras do SAP HANA no Azure. A solução se baseia no selo de hardware dedicado certificado do SAP HANA ([TDI – Tailored Datacenter Integration do SAP HANA](https://scn.sap.com/docs/DOC-63140)). A execução como uma solução configurada de TDI do SAP HANA oferece a confiança de saber que todos os aplicativos baseados no SAP HANA (incluindo o SAP Business Suite no SAP HANA, SAP Business Warehouse (BW) SAP HANA, S4/HANA e BW4/HANA) funcionarão na infraestrutura de hardware.

Comparado à execução SAP HANA em máquinas virtuais Azure essa solução tem um benefício — fornece para volumes muito maiores de memória. Se você deseja habilitar esta solução, há alguns aspectos fundamentais para entender:

- Os aplicativos de camada e os aplicativos não SAP executados em VMs (Máquinas Virtuais) do Azure hospedados nos carimbos de hardware do Azure usuais.
- Implantações de infraestrutura, data centers e aplicativos de cliente locais são conectadas à plataforma de nuvem do Microsoft Azure por meio do Azure ExpressRoute (recomendado) ou VPN (rede virtual privada). Active Directory (AD) e DNS também se estendem para o Azure.
- A instância de banco de dados do SAP HANA para carga de trabalho do HANA é executada no SAP HANA no Azure (Instâncias Grandes). O carimbo de Instância Grande está conectado à rede do Azure para que o software em execução nas VMs do Azure possa interagir com a instância do HANA em execução no HANA em Instâncias Grandes.
- Hardware do SAP HANA no Azure (Instâncias Grandes) é fornecido em uma infraestrutura como serviço (IaaS) com o SUSE Linux Enterprise Server de hardware dedicado ou Red Hat Enterprise Linux, pré-instalado. Como com as máquinas virtuais do Azure, ainda mais as atualizações e manutenção para o sistema operacional é sua responsabilidade.
- Instalação do HANA ou todos os componentes adicionais necessários para executar SAP HANA em unidades de instâncias HANA grandes é sua responsabilidade, assim como todos os respectivos operações contínuas e administração do SAP HANA no Azure.
- Além das soluções descritas aqui, você pode instalar outros componentes na sua assinatura do Azure que se conecta ao SAP HANA no Azure (Instâncias Grandes).  Por exemplo, os componentes que permitem a comunicação com e/ou diretamente para o banco de dados do SAP HANA (servidores de salto, RDP servidores SAP HANA Studio, serviços de dados do SAP para cenários SAP BI, ou soluções de monitoramento de rede).
- Assim como no Azure, as Instâncias Grandes do HANA dão suporte às funcionalidades de Alta Disponibilidade e de Recuperação de Desastre.

## <a name="architecture"></a>Arquitetura

Em um alto nível, a solução do SAP HANA no Azure (Instâncias Grandes) tem a camada do aplicativo SAP que reside nas VMs do Azure e a camada de banco de dados que reside em hardware TDI SAP configurado localizado em um carimbo de Instância Grande na mesma região do Azure que está conectado ao Azure IaaS.

> [!NOTE]
> Você precisa implantar a camada do aplicativo SAP na mesma região do Azure que a camada de DBMS SAP. Essa regra é bem documentada em informações publicadas sobre carga de trabalho do SAP no Azure. 

A arquitetura geral do SAP HANA no Azure (Instâncias Grandes) fornece que uma configuração de hardware TDI SAP certificada (não virtualizada, bare metal, servidor de alto desempenho para o banco de dados do SAP HANA) e a capacidade e a flexibilidade do Azure para dimensionar os recursos da camada de aplicativo SAP para atender às suas necessidades.

![Visão geral da arquitetura do SAP HANA no Azure (Instâncias Grandes)](./media/hana-overview-architecture/image1-architecture.png)

A arquitetura mostrada é dividida em três seções:

- **Direita:** uma infraestrutura local executando aplicativos diferentes em data centers com usuários finais acesso a aplicativos de LOB (como SAP). Idealmente, esse local infraestrutura é conectada ao Azure com o Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Centro:** mostra Azure IaaS e, nesse caso, o uso de VMs do Azure para hospedar SAP ou outros aplicativos que usam SAP HANA como um sistema DBMS. Instâncias HANA menores que fornecem função com a memória VMs do Azure são implantadas em VMs do Azure junto com sua camada de aplicativo. Saiba mais sobre [Máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/).
<br />Rede do Azure é usada para agrupar os sistemas SAP junto com outros aplicativos em redes virtuais do Azure (VNets). Essas redes virtuais se conectar a sistemas locais, bem como SAP HANA no Azure (Instâncias Grandes).
<br />Para aplicativos SAP NetWeaver e bancos de dados que têm suporte para executar no Microsoft Azure, consulte [1928533 de n º de observação de suporte do SAP – aplicativos SAP no Azure: tipos de produtos com suporte e VM do Azure](https://launchpad.support.sap.com/#/notes/1928533). Para obter a documentação sobre como implantar soluções SAP no Azure, examine:

  -  [Uso do SAP em VMs (máquinas virtuais) do Windows](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Uso das soluções SAP em máquinas virtuais do Microsoft Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **À esquerda:** mostra o hardware certificado por TDI do SAP HANA no carimbo de Instância Grande do Azure. As unidades do HANA em Instância Grande estão conectadas a redes virtuais do Azure de sua assinatura usando a mesma tecnologia como a conectividade do local para o Azure.

O carimbo de Instância Grande do Azure em si combina os seguintes componentes:

- **Computação:** servidores baseados em processadores Intel Xeon E7-8890v3 ou Intel Xeon E7-8890v4 que fornecem a capacidade de computação necessária e são certificados pelo SAP HANA.
- **Rede:** A Unificação da malha de rede de alta velocidade que interconexões de computação, armazenamento e componentes de LAN.
- **Armazenamento:** uma infraestrutura de armazenamento que é acessada por meio de uma malha de rede unificada. Capacidade de armazenamento específica é fornecida dependendo do SAP HANA específico na configuração do Azure (Instâncias Grandes) que está sendo implantada (mais capacidade de armazenamento está disponível a um custo mensal adicional).

Na infraestrutura multilocatária do carimbo de Instância Grande, os clientes são implantados como locatários isolados. Na implantação do locatário, é necessário o nome de uma assinatura do Azure dentro de seu registro do Azure. Essa será a assinatura do Azure na qual as Instâncias Grandes do HANA serão cobradas. Esses locatários têm uma relação de 1:1 para a assinatura do Azure. Em termos de rede, é possível acessar uma unidade de Instância Grande do HANA implantada em um locatário em uma região do Azure de VNets do Azure diferentes, pertencentes a assinaturas do Azure diferentes. Será necessário, no entanto, que essas assinaturas do Azure pertençam ao mesmo registro do Azure. 

Assim como acontece com VMs do Azure, SAP HANA no Azure (Instâncias Grandes) é oferecido em várias regiões do Azure. Para oferecer recursos de Recuperação de Desastre, você pode optar por aceitar. Os carimbos de Instância Grande contidos em uma região geopolítica do Azure são conectados uns aos outros. Por exemplo, carimbos de Instância Grande do HANA no Oeste dos EUA e no Leste dos EUA são conectados por meio de um link de rede dedicado para fins de recuperação de desastre de replicação. 

Assim como você pode escolher entre diferentes tipos de VM com as máquinas virtuais do Azure, você pode escolher entre diferentes SKUs do HANA em Instâncias Grandes que são personalizados para tipos diferentes de carga de trabalho do SAP HANA. SAP se aplica a memória para taxas de soquete de processador para diferentes cargas de trabalho com base nas gerações de processadores Intel, há quatro tipos diferentes de SKU oferecidos:

A partir de julho de 2017, o SAP HANA no Azure (Instâncias Grandes) está disponível em várias configurações nas regiões do Azure Oeste dos EUA e Leste dos EUA, Leste da Austrália, Sudeste da Austrália, Europa Ocidental e Europa Setentrional:

| Solução SAP | CPU | Memória | Armazenamento | Disponibilidade |
| --- | --- | --- | --- | --- |
| Otimizado para OLAP: SAP BW, BW/4HANA<br /> ou SAP HANA para carga de trabalho OLAP genérica | SAP HANA no Azure S72<br /> – 2 x processadores Intel® Xeon® E7-8890 v3<br /> 36 núcleos de CPU e 72 threads de CPU |  768 GB |  3 TB | Disponível |
| --- | SAP HANA no Azure S144<br /> – 4 x processadores Intel® Xeon® E7-8890 v3<br /> 72 núcleos de CPU e 144 threads de CPU |  1,5 TB |  6 TB | Não é mais oferecido |
| --- | SAP HANA no Azure S192<br /> – 4 x processadores Intel® Xeon® E7-8890 v4<br /> 96 núcleos de CPU e 192 threads de CPU |  2,0 TB |  8 TB | Disponível |
| --- | SAP HANA no Azure S384<br /> – 8 x processadores Intel® Xeon® E7-8890 v4<br /> 192 núcleos de CPU e 384 threads de CPU |  4,0 TB |  16 TB | Pronto para ordem |
| Otimizado para OLTP: SAP Business Suite<br /> no SAP HANA ou S/4HANA (OLTP),<br /> OLTP genérico | SAP HANA no Azure S72m<br /> – 2 x processadores Intel® Xeon® E7-8890 v3<br /> 36 núcleos de CPU e 72 threads de CPU |  1,5 TB |  6 TB | Disponível |
|---| SAP HANA no Azure S144m<br /> – 4 x processadores Intel® Xeon® E7-8890 v3<br /> 72 núcleos de CPU e 144 threads de CPU |  3,0 TB |  12 TB | Não é mais oferecido |
|---| SAP HANA no Azure S192m<br /> – 4 x processadores Intel® Xeon® E7-8890 v4<br /> 96 núcleos de CPU e 192 threads de CPU  |  4,0 TB |  16 TB | Disponível |
|---| SAP HANA no Azure S384m<br /> – 8 x processadores Intel® Xeon® E7-8890 v4<br /> 192 núcleos de CPU e 384 threads de CPU |  6,0 TB |  18 TB | Pronto para ordem |
|---| SAP HANA no Azure S384xm<br /> – 8 x processadores Intel® Xeon® E7-8890 v4<br /> 192 núcleos de CPU e 384 threads de CPU |  8,0 TB |  22 TB |  Pronto para ordem |
|---| SAP HANA no Azure S576<br /> – 12 x processadores Intel® Xeon® E7-8890 v4<br /> 288 núcleos de CPU e 576 threads de CPU |  12,0 TB |  28 TB | Pronto para ordem |
|---| SAP HANA no Azure S768<br /> – 16 x processadores Intel® Xeon® E7-8890 v4<br /> 384 núcleos de CPU e 768 threads de CPU |  16,0 TB |  36 TB | Pronto para ordem |
|---| SAP HANA no Azure S960<br /> – 20 x processadores Intel® Xeon® E7-8890 v4<br /> 480 núcleos de CPU e 960 threads de CPU |  20,0 TB |  46 TB | Pronto para ordem |

- Núcleos de CPU = soma de núcleos de CPU não-hyper-threading da soma dos processadores da unidade do servidor.
- Threads de CPU = soma de threads de computação fornecidos pelos núcleos de CPU não-hyper-threading da soma dos processadores da unidade do servidor. Todas as unidades são configuradas por padrão para usar o Hyper-Threading.


As diferentes configurações acima, que estão em estado Disponível ou 'Não é mais oferecido', são referenciadas na [Observação de Suporte do SAP No. 2316233 – SAP HANA no Microsoft Azure (Instâncias Grandes)](https://launchpad.support.sap.com/#/notes/2316233/E). As configurações que são marcadas como 'Pronto para Ordem' encontrarão a respectiva entrada na Nota SAP em breve. No entanto, esses SKUs de instância já podem ser solicitados para as seis diferentes regiões do Azure nas quais o serviço de Instância Grande do HANA está disponível.

As configurações específicas de escolhido dependem da carga de trabalho, recursos de CPU e memória desejada. É possível que a carga de trabalho OLTP use os SKUs otimizados para carga de trabalho OLAP. 

A base de hardware para todas as ofertas é certificada pelo TDI do SAP HANA. No entanto, fazemos distinção entre duas classes diferentes de hardware, as quais dividem os SKUs em:

- S72, S72m, S144, S144m, S192 e S192m, que chamamos de 'classe do Tipo I' de SKUs.
- S384, S384m, S384xm, S576, S768 e S960, que chamamos de 'classe do Tipo II' de SKUs.

É importante observar que uma selo de Instância Grande do HANA não está alocada exclusivamente para uso de um único cliente. Esse fato se aplica aos racks de recursos de computação e armazenamento conectados por meio de uma malha de rede implantada no Azure também. A infraestrutura do HANA em Instâncias Grandes, como o Azure, implanta &quot;locatários&quot; diferentes do cliente isolados uns dos outros nos três níveis a seguir:

- Rede: isolamento por meio de redes virtuais dentro do selo de Instância Grande do HANA.
- Armazenamento: isolamento por meio de máquinas virtuais de armazenamento que têm volumes de armazenamento atribuídos e isolam volumes de armazenamento entre locatários.
- Computação: atribuição dedicada de unidades de servidor para um único locatário. Nenhum particionamento de hardware ou de software das unidades de servidor. Nenhum compartilhamento de uma única unidade de host ou de servidor entre locatários. 

Dessa forma, as implantações de unidades de Instâncias Grandes do HANA entre locatários diferentes não são visíveis ente si. Tampouco as unidades de Instância Grande do HANA implantadas em diferentes locatários podem se comunicar diretamente entre si no nível de selo de Instância Grande do HANA. Somente unidades de Instância Grande do HANA dentro de um locatário podem se comunicar entre si no nível de selo de Instância Grande do HANA.
Um locatário implantado no selo de Instância Grande é atribuído, com relação à cobrança, a uma assinatura do Azure. No entanto, em termos de rede, elas pode ser acessadas por meio de VNets do Azure de outras assinaturas do Azure contidas no mesmo registro do Azure. Se você implantar com outra assinatura do Azure na mesma região do Azure, você também poderá escolher solicitar um locatário de Instância Grande do HANA separado.

Existem diferenças significativas entre executar o SAP HANA em Instâncias Grandes do HANA e executar o SAP HANA em VMs do Azure implantadas no Azure:

- Não há nenhuma camada de virtualização para o SAP HANA no Azure (Instâncias Grandes). Você obtém o desempenho do hardware bare-metal subjacente.
- Ao contrário do Azure, o servidor do servidor do SAP HANA no Azure (Instâncias Grandes) é dedicado a um cliente específico. Não há nenhuma possibilidade de uma unidade de host ou de servidor ter hardware ou software particionado. Como resultado, uma unidade de Instância Grande do HANA é usada conforme atribuída como um todo para um locatário e, com isso, para você enquanto cliente. Uma reinicialização ou desligamento do servidor não leva automaticamente para a implantação do sistema operacional e do SAP HANA em outro servidor. (Para SKUs de classe do Tipo I, a única exceção se um servidor encontrar problemas e a reimplantação precisar ser executada em outro servidor).
- Ao contrário do Azure, em que tipos de host do processador são selecionados para melhor taxa de preço e desempenho, os tipos de processador escolhidos para o SAP HANA no Azure (Instâncias Grandes) têm o melhor desempenho da linha de processadores Intel E7v3 e E7v4.


### <a name="running-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Executando várias instâncias do SAP HANA em uma unidade de Instância Grande do HANA
É possível hospedar mais de uma instância ativa do SAP HANA das unidades de Instância Grande do HANA. Para ainda fornecer os recursos de instantâneos de armazenamento e recuperação de desastre, essa configuração requer um conjunto de volumes por instância. A partir de agora, as unidades de Instância Grande do HANA podem ser subdivididas da seguinte maneira:

- S72, S72m, S144, S192: em incrementos de 256 GB, com a menor unidade inicial tendo 256 GB. Incrementos diferentes como 256 GB, 512 GB e assim por diante podem ser combinados ao máximo da memória da unidade.
- S144m e S192m: em incrementos de 256 GB com a menor unidade tendo 512 GB. Incrementos diferentes como 512 GB, 768 GB e assim por diante podem ser combinados ao máximo da memória da unidade.
- Classe do Tipo II: em incrementos de 512 GB, com a menor unidade inicial de 2 TB. Incrementos diferentes como 512 GB, 1 TB, 1,5 TB e assim por diante podem ser combinados ao máximo da memória da unidade.

Alguns exemplos de como executar várias instâncias do SAP HANA seriam semelhantes ao seguinte:

| SKU | Tamanho da memória | Tamanho do Armazenamento | Tamanhos com vários bancos de dados |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1x instância do HANA de 768 GB<br /> ou 1x instância de 512 GB + 1x instância de 256 GB<br /> ou 3x instâncias de 256 GB | 
| S72m | 1,5 TB | 6 TB | 3x instâncias do HANA de 512GB<br />ou 1x instância de 512 GB + 1x instância de 1 TB<br />ou 6x instâncias de 256 GB<br />ou 1x instância de 1,5 TB | 
| S192m | 4 TB | 16 TB | 8x instâncias de 512 GB<br />ou 4x instâncias de 1 TB<br />ou 4x instâncias de 512 GB + 2x instâncias de 1 TB<br />ou 4x instâncias de 768 GB + 2x instâncias de 512 GB<br />ou 1x instância de 4 TB |
| S384xm | 8 TB | 22 TB | 4x instâncias de 2 TB<br />ou 2x instâncias de 4 TB<br />ou 2x instâncias de 3 TB + 1x instância de 2 TB<br />ou 2x instâncias de 2,5 TB + 1x instância de 3 TB<br />ou 1x instância de 8 TB |


Já é suficiente pra entender o princípio. Certamente, há também outras variações. 

### <a name="using-sap-hana-data-tiering-and-extension-nodes"></a>Usando nós de extensão e camadas de dados do SAP HANA
O SAP dá suporte a um modelo de camadas de dados para SAP BW de diferentes versões do SAP NetWeaver e SAP BW/4HANA. Detalhes sobre o modelo de hierarquia de dados podem ser encontrados neste documento e blog mencionados neste documento pelo SAP: [SAP BW/4HANA e SAP BW EM HANA COM NÓS DE EXTENSÃO SAP HANA](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).
Com instâncias do HANA grandes, você pode usar a configuração de opção 1 de nós de extensão do SAP HANA, conforme detalhado nestas Perguntas Frequentes e documentos de blog de SAP. Configurações de opção 2 podem ser definidas com os SKUs de instâncias do HANA grandes: S72m, S192, S192m, S384 e S384m.  
Ao examinar a documentação, a vantagem pode não ser aparente à primeira vista. Mas observando as diretrizes de dimensionamento do SAP, você pode ver uma vantagem usando nós de extensão do SAP HANA de opção-1 e opção 2. Aqui está um exemplo:

- Diretrizes de dimensionamento do SAP HANA geralmente exigem duas vezes mais volume de dados do que memória. Portanto, quando você estiver executando a instância do SAP HANA com os dados ativos, você só tem 50% ou menos da memória preenchida com os dados. Idealmente, o restante da memória é mantido para o SAP HANA fazer seu trabalho.
- Isso significa que, em uma unidade de instância do HANA grande S192 com 2 TB de memória executando um banco de dados do SAP BW, você tem apenas 1 TB como volume de dados.
- Se você usar um nó de extensão SAP HANA adicional da opção 1, bem como um SKU de instância do HANA grande S192, ele oferecerá a você uma capacidade adicional de 2 TB para o volume de dados. Na configuração de opção 2, oferecerá até mesmo 4 TB adicionais para o volume de dados mornos. Em comparação com o nó quente, a capacidade total de memória do nó de extensão 'morno' pode ser usada para armazenamento de dados para a opção 1 e o dobro da memória pode ser usada para o volume de dados na configuração de nó da extensão de SAP HANA da opção 2.
- Como resultado, você acabará com uma capacidade de 3 TB para seus dados e uma taxa de quente para morno de 1:2 para a opção 1 e 5 TB de dados e uma taxa de 1:4 na configuração de nó de extensão da opção 2.

No entanto, quanto maior o volume de dados em comparação com a memória, maior a probabilidade de que os dados mornos que você está solicitando sejam armazenados no armazenamento em disco.


## <a name="operations-model-and-responsibilities"></a>Responsabilidades e modelo de operações

O serviço fornecido com SAP HANA no Azure (Instâncias Grandes) é alinhado com os serviços do Azure IaaS. Você obtém uma instância do HANA em Instâncias Grandes com um sistema operacional instalado otimizado para o SAP HANA. Assim como acontece com as VMs do Azure IaaS, a maioria das tarefas de proteção de sistema operacional, instalar software adicional é necessário, instalando HANA, o sistema operacional e HANA, operacionais e atualizar o sistema operacional e HANA é sua responsabilidade. A Microsoft não força atualizações do sistema operacional ou HANA para você.

![Responsabilidades do SAP HANA no Azure (Instâncias Grandes)](./media/hana-overview-architecture/image2-responsibilities.png)

Como você pode ver no diagrama acima, SAP HANA no Azure (Instâncias Grandes) é um infraestrutura de multilocatário como uma oferta de serviço. E como resultado, a divisão de responsabilidade é o limite de infraestrutura de sistema operacional, na maior parte. A Microsoft é responsável por todos os aspectos do serviço abaixo da linha do sistema operacional e é responsável acima da linha, incluindo o sistema operacional. Assim, métodos locais mais recentes que você pode estar aplicando para conformidade, segurança, gerenciamento de aplicativos, base e gerenciamento de SO podem continuar a ser usados. Os sistemas aparecem como se estivessem em sua rede em todos os aspectos.

No entanto, esse serviço é otimizado para SAP HANA, portanto, há áreas onde você e a Microsoft precisam trabalhar juntos para usar os recursos de infraestrutura subjacente para obter melhores resultados.

A lista a seguir fornece mais detalhes sobre cada uma das camadas e suas responsabilidades:

**Rede:** todas as redes internas do carimbo de Instância Grande que executa o SAP HANA, seu acesso ao armazenamento, a conectividade entre as instâncias (para expansão e outras funções), a conectividade com o cenário e a conectividade com o Azure no qual a camada do aplicativo SAP é hospedada em Máquinas Virtuais do Azure. Ele também inclui conectividade WAN entre Data Centers do Azure para replicação de objetivos de Recuperação de Desastre. Todas as redes são particionadas pelo locatário e aplicou QOS.

**Armazenamento:** o virtualizado particionada armazenamento para todos os volumes necessários pelos servidores do SAP HANA, bem como para instantâneos. 

**Servidores:** os servidores físicos dedicados para executar os bancos de dados SAP HANA atribuídos aos locatários. Os servidores de SKUs de classe do Tipo I são abstraídos do hardware. Com esses tipos de servidores, a configuração do servidor é coletada e mantida em perfis, que podem ser movidos de um hardware físico para outro hardware físico. Uma movimentação desse tipo (manual) de um perfil por operações pode ser comparada um pouco à recuperação de serviço do Azure. Os servidores de SKUs de classe do Tipo II não oferecem essa funcionalidade.

**SDDC:** centraliza o software de gerenciamento que é usado para gerenciar os dados como entidades de software definidas. Ele permite que a Microsoft reserve recursos em pool por motivos de desempenho, disponibilidade e escala.

**Sistema operacional:** o sistema operacional que você escolher (SUSE Linux ou Red Hat Linux) que é executado em servidores. As imagens do SO que você recebe serão aquelas fornecidas pelo fornecedor individual do Linux à Microsoft para executar o SAP HANA. Você precisa ter uma assinatura com o fornecedor do Linux para receber a imagem específica otimizada para SAP HANA. Suas responsabilidades incluem o registro das imagens com o fornecedor do SO. Depois da entrega feita pela Microsoft, você também é responsável por qualquer aplicação de patch no sistema operacional Linux. Essa aplicação de patch inclui pacotes adicionais que podem ser necessários para uma instalação bem-sucedida do SAP HANA (consulte a documentação de instalação do SAP HANA e as Notas SAP) e que não foram incluídos pelo fornecedor específico do Linux nas respectivas imagens de SO otimizadas para SAP HANA. A responsabilidade do cliente também inclui a aplicação de patch do SO que está relacionado ao mau funcionamento/otimização do SO e seus drivers em relação ao hardware de servidor específico. Ou qualquer aplicação de patch de segurança ou funcional do SO. Também é responsabilidade do cliente monitorar e planejar a capacidade do(s):

- Consumo de recursos de CPU
- Consumo de memória
- Volumes de disco relacionados ao espaço livre, IOPS e latência
- Tráfego de volume de rede entre o Hana Instância Grande e a camada do aplicativo SAP

A infraestrutura subjacente do HANA em Instâncias Grandes fornece a funcionalidade de backup e restauração do volume do sistema operacional. Usar essa funcionalidade também é sua responsabilidade.

**Middleware:** instância do SAP HANA, principalmente. Administração, operações e monitoramento são de sua responsabilidade. Não há funcionalidade fornecida que permite que você use instantâneos de armazenamento para fins de Recuperação de Desastre e backup/restauração. Essas funcionalidades são fornecidas pela infraestrutura. No entanto, suas responsabilidades incluem também a criação de um design de Alta Disponibilidade ou Recuperação de Desastre com essas funcionalidades, aproveitando-as e monitorando para conferir que instantâneos de armazenamento tenham sido executados com êxito.

**Dados:** seus dados gerenciados pelo SAP HANA e outros dados, como backups de arquivos localizados em volumes ou compartilhamentos de arquivos. Suas responsabilidades incluem monitoramento espaço livre em disco e gerenciamento de conteúdo em volumes e a execução bem-sucedida de backups de volumes de disco e instantâneos de armazenamento. No entanto, a execução bem-sucedida da replicação de dados para locais de DR é a responsabilidade da Microsoft.

**Aplicativos:** as instâncias do aplicativo SAP ou, no caso de aplicativos não-SAP, a camada de aplicativo desses aplicativos. Suas responsabilidades incluem a implantação, administração, operações e monitoramento dos aplicativos relacionados ao planejamento de capacidade de consumo de recursos de CPU, consumo de memória, consumo de armazenamento do Azure e consumo de largura de banda de rede nas VNets do Azure e de VNets do Azure para SAP HANA no Azure (Instâncias Grandes).

**WANs:** as conexões que você estabelece do local para implantações do Azure para cargas de trabalho. Todos os nossos clientes de Instâncias Grandes do HANA usam o Azure ExpressRoute para conectividade. Esta conexão não é parte do SAP HANA na solução do Azure (Instâncias Grandes), você é responsável pela instalação desta conexão.

**Arquivo:** talvez você prefira arquivar cópias dos dados usando seus próprios métodos em contas de armazenamento. O arquivamento requer gerenciamento, conformidade, custos e operações. Você é responsável por gerar cópias de arquivos e backups no Azure e armazená-los de uma maneira em conformidade.

Configurações para o SAP HANA Veja o [SLA para o SAP HANA no Azure (Instâncias Grandes)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/).

## <a name="sizing"></a>Dimensionamento

O dimensionamento do HANA em Instâncias Grandes não é diferente de dimensionamento para o HANA em geral. Existentes e a implantação de sistemas, você deseja mover de outros RDBMS para HANA, SAP fornece vários relatórios que são executados em seus sistemas SAP existentes. Se o banco de dados é movido para o HANA, esses relatórios verificam os dados e calculam os requisitos de memória para a instância do HANA. Leia as seguintes observações do SAP para obter mais informações sobre como executar esses relatórios e como obter seus patches/versões mais recentes:

- [Nota SAP nº 1793345 – Dimensionamento do SAP Suite no HANA](https://launchpad.support.sap.com/#/notes/1793345)
- [Nota SAP nº 1872170 – Relatório de dimensionamento do Suite no HANA e S/4 HANA](https://launchpad.support.sap.com/#/notes/1872170)
- [Nota SAP nº 2121330 – Perguntas frequentes: relatório de dimensionamento do SAP BW no HANA](https://launchpad.support.sap.com/#/notes/2121330)
- [Nota SAP nº 1736976 – Relatório de dimensionamento do BW no HANA](https://launchpad.support.sap.com/#/notes/1736976)
- [Nota SAP nº 2296290 – Novo relatório de dimensionamento do BW no HANA](https://launchpad.support.sap.com/#/notes/2296290)

Para implementações de campo verde Sizer rápida SAP está disponível para calcular os requisitos de memória da implementação de software SAP sobre HANA.

Requisitos de memória para HANA aumentarão à medida que o volume de dados crescer, portanto, você deve estar atento agora o consumo de memória e ser capaz de prever o que será no futuro. Com base nos requisitos de memória, você pode mapear a demanda em uma das SKUs do Hana Instância Grande.

## <a name="requirements"></a>Requisitos

Estes são os requisitos para execução SAP HANA no Azure (Instâncias Grandes).

**Microsoft Azure:**

- Uma assinatura do Azure que pode ser vinculada ao SAP HANA no Azure (Instâncias Grandes).
- Contrato do Suporte Premier da Microsoft. Consulte [2015553 de n º de observação de suporte do SAP – SAP no Microsoft Azure: pré-requisitos de suporte](https://launchpad.support.sap.com/#/notes/2015553) para obter informações específicas relacionadas à execução de SAP no Azure. Com unidades de instância grande HANA 384 e mais CPUs, você também precisa estender o contrato de Suporte Premier para incluir ARR (resposta rápida do Azure).
- O reconhecimento dos SKUs de instâncias do HANA em Instâncias Grandes será necessário após a realização de um exercício de dimensionamento com o SAP.

**Conectividade de rede:**

- Azure ExpressRoute entre o local para o Azure: para conectar seu datacenter local ao Azure, solicite pelo menos uma conexão de 1 Gbps do ISP. 

**Sistema operacional:**

- Licenças para SUSE Linux Enterprise Server 12 para aplicativos SAP.

> [!NOTE] 
> O sistema operacional fornecido pela Microsoft não está registrado no SUSE, nem está conectado a uma instância SMT.

- O SMT da assinatura do SUSE Linux é implantado no Azure em uma VM do Azure. Isso fornece a capacidade de o SAP HANA no Azure (Instâncias Grandes) ser registrado e respectivamente atualizado pelo SUSE (pois não há nenhum acesso à internet no data center do HANA em Instâncias Grandes). 
- Licenças para Red Hat Enterprise Linux 6.7 ou 7.2 para SAP HANA.

> [!NOTE]
> O sistema operacional fornecido pela Microsoft não está registrado no Red Hat, nem está conectado a uma instância do gerenciador de assinaturas do Red Hat.

- Gerenciador do Red Hat assinatura implantado no Azure em uma VM do Azure. O gerenciador de assinaturas do Red Hat fornece a capacidade para o SAP HANA no Azure (Instâncias Grandes) ser registrado e atualizado respectivamente pelo Red Hat (já que não há nenhum acesso direto à Internet de dentro do locatário implantado no selo de Instância Grande do Azure).
- A SAP também exige que você tenha um contrato de suporte com seu provedor do Linux. Esse requisito não é apagado pela solução de Grandes Instâncias do HANA nem pelo fato de você executar o Linux no Azure. Ao contrário de algumas das imagens da galeria do Azure no Linux, a taxa de serviço NÃO está incluída na oferta da solução das Grandes Instâncias do HANA. Como cliente, cabe a você atender aos requisitos da SAP no que diz respeito aos contratos de suporte com o distribuidor do Linux.   
   - Para o SUSE Linux, procure os requisitos do contrato de suporte em [Nota SAP n. 1984787 – SUSE LINUX Enterprise Server 12: Notas de instalação](https://launchpad.support.sap.com/#/notes/1984787) e [Nota SAP n. 1056161 – Suporte de prioridade do SUSE para aplicativos SAP](https://launchpad.support.sap.com/#/notes/1056161).
   - Para o Red Hat Linux, é necessário ter os níveis de assinatura corretos que incluem suporte e serviço (atualizações nos sistemas operacionais das Instâncias Grandes do HANA). O Red Hat recomenda obter uma assinatura "RHEL for SAP Business Applications". Com relação a suporte e serviços, confira [Nota SAP nº 2002167 – Red Hat Enterprise Linux 7.x: Instalação e atualização](https://launchpad.support.sap.com/#/notes/2002167) e [Nota SAP nº 1496410 – Red Hat Enterprise Linux 6.x: Instalação e atualização](https://launchpad.support.sap.com/#/notes/1496410) para obter detalhes.

**Banco de dados:**

- As licenças e os componentes de instalação de software para SAP HANA (plataforma ou enterprise edition).

**Aplicativos:**

- Contratos de suporte de licenças e os componentes de instalação de software para todos os aplicativos SAP conexão SAP HANA e relacionados ao SAP.
- Licenças e os componentes de instalação de software para todos os aplicativos SAP não usado em relação a SAP HANA no ambiente do Azure (Instâncias Grandes) e relacionadas a contratos de suporte.

**Habilidades:**

- Experiência e conhecimento sobre IaaS do Azure e seus componentes.
- Experiência e conhecimento sobre a implantação da carga de trabalho do SAP no Azure.
- Instalação do SAP HANA certificados pessoal.
- As habilidades de arquiteto do SAP para design de Alta Disponibilidade e Recuperação de Desastre no SAP HANA.

**SAP:**

- Expectativa é que você seja um cliente do SAP e tenha suporte de um contrato com o SAP
- Especialmente para implementações na classe do Tipo II dos SKUs de Instância Grande do HANA, é altamente recomendável consultar com a SAP sobre versões do SAP HANA e eventuais configurações em hardware de escalonamento vertical de tamanho grande.


## <a name="storage"></a>Armazenamento

O layout de armazenamento do SAP HANA no Azure (Instâncias Grandes) é configurado pelo Gerenciamento de Serviços do SAP HANA no Azure por meio das diretrizes recomendadas pela SAP, documentadas no white paper [Requisitos de armazenamento do SAP HANA](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

As Instâncias Grandes do HANA da classe do Tipo I são fornecidas com um volume de armazenamento equivalente a quatro vezes o volume de memória. Para as Instâncias Grandes do HANA da classe do Tipo II, o armazenamento não será de quatro vezes mais. As unidades são fornecidas com um volume que deve ser usado para armazenar os backups de log de transações do HANA. Encontre mais detalhes em [Como instalar e configurar o SAP HANA (instâncias grandes) no Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Consulte a tabela a seguir em termos de alocação de armazenamento. A tabela lista aproximadamente a capacidade dos diferentes volumes fornecidos com as diferentes unidades de Instância Grande do HANA.

| SKU de Instância Grande do HANA | hana/data | Hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| S72 | 1280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3.328 GB | 768 GB |1280 GB | 768 GB |
| S192 | 4.608 GB | 1024 GB | 1536 GB | 1024 GB |
| S192m | 11.520 GB | 1536 GB | 1.792 GB | 1536 GB |
| S384 | 11.520 GB | 1536 GB | 1.792 GB | 1536 GB |
| S384m | 12.000 GB | 2.050 GB | 2.050 GB | 2.040 GB |
| S384xm | 16.000 GB | 2.050 GB | 2.050 GB | 2.040 GB |
| S576 | 20.000 GB | 3.100 GB | 2.050 GB | 3.100 GB |
| S768 | 28.000 GB | 3.100 GB | 2.050 GB | 3.100 GB |
| S960 | 36.000 GB | 4.100 GB | 2.050 GB | 4.100 GB |


Volumes implantados reais podem variar um pouco com base na implantação e na ferramenta que é usada para mostrar os tamanhos do volume.

Se você subdividir um SKU de Instância Grande do HANA, alguns exemplos de partes de divisão possíveis pareceriam com:

| Partição de memória em GB | hana/data | Hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1280 GB | 512 GB | 768 GB | 512 GB |
| 1024 | 1.792 GB | 640 GB | 1024 GB | 640 GB |
| 1536 | 3.328 GB | 768 GB | 1280 GB | 768 GB |


Esses tamanhos são números de volume aproximados que podem variar ligeiramente dependendo de implantação e das ferramentas usadas para examinar os volumes. Também há outros tamanhos de partição possíveis, como 2,5 TB. Esses tamanhos de armazenamento serão calculados com uma fórmula semelhante à usada para as partições acima. O termo 'partições' não indica que o sistema operacional, memória ou recursos de CPU estejam particionados em nenhum sentido. Ele indica apenas partições de armazenamento para as instâncias diferentes do HANA que talvez você queira implantar em uma única unidade de Instância Grande do HANA. 

Você, na condição de cliente, talvez precise de mais armazenamento; existe a possibilidade de adicionar armazenamento adquirindo armazenamento adicional em unidades de 1 TB. Esse armazenamento adicional pode ser adicionado como um volume adicional ou pode ser usado para estender um ou mais dos volumes existentes. Não é possível diminuir os tamanhos dos volumes conforme originalmente implantados e conforme documentado em grande parte pelas tabelas acima. Também não é possível alterar os nomes dos volumes ou nomes de montagem. Os volumes de armazenamento, conforme descrito acima, são anexados às unidades de Instância Grande do HANA como volumes NFS4.

Como cliente, você pode optar por usar instantâneos de armazenamento para fins de backup/restauração e recuperação de desastre. Mais detalhes sobre esse tópico são fornecidos em [Alta disponibilidade e recuperação de desastre do SAP HANA (instâncias grandes) no Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="encryption-of-data-at-rest"></a>Criptografia de dados em repouso
O armazenamento usado para as Instâncias Grandes do HANA permite uma criptografia transparente dos dados, conforme eles são armazenados nos discos. No momento da implantação de uma Unidade de Instância Grande do HANA, você tem a opção de habilitar esse tipo de criptografia. Você também pode optar por alterar para volumes criptografados logo após a implantação. A movimentação de volumes não criptografados para criptografados é transparente e não exige nenhum tempo de inatividade. 

Com os SKUs de classe do Tipo I, o volume no qual o LUN de inicialização é armazenado é criptografado. No caso de SKUs de classe do Tipo II de Instâncias Grandes do HANA, você precisa criptografar o LUN de inicialização com métodos de sistema operacional. 


## <a name="networking"></a>Rede

A arquitetura de Rede do Azure é um componente-chave para a implantação bem-sucedida de aplicativos SAP em Instâncias Grandes do HANA. Normalmente, as implantações do SAP HANA no Azure (Instâncias Grandes) têm uma estrutura SAP maior com diversas soluções SAP com vários tamanhos de bancos de dados, consumo de recursos de CPU e a utilização de memória. É muito provável que nem todos esses sistemas SAP se baseiem no SAP HANA, de forma que seu cenário SAP provavelmente seja um híbrido que utiliza:

- Sistemas SAP implantados no local. Devido a seus tamanhos, esses sistemas não podem ser hospedados atualmente no Azure; um exemplo clássico seria um sistema SAP ERP de produção executado no Microsoft SQL Server (como o banco de dados) que exige mais recursos de CPU ou de memória do que as VMs do Azure podem fornecer.
- Sistemas SAP baseados em SAP HANA implantados localmente.
- Sistemas SAP implantados em VMs do Azure. Esses sistemas podem ser instâncias de desenvolvimento, teste, área restrita ou produção para qualquer um dos aplicativos baseados no SAP NetWeaver que podem implantar com êxito no Azure (em VMs), com base na demanda de memória e no consumo de recursos. Esses sistemas também pode ser baseados em bancos de dados como o SQL Server (consulte [Nota de Suporte SAP n. 1928533 – Aplicativos SAP no Azure: tipos de VM do Azure e produtos com suporte](https://launchpad.support.sap.com/#/notes/1928533/E)) ou SAP HANA (consulte [Plataformas IaaS certificadas do SAP HANA](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)).
- Servidores de aplicativos SAP implantados no Azure (em VMs) que aproveitam o SAP HANA no Azure (Instância Grande) em carimbos de Instância Grande do Azure.

Enquanto um cenário de SAP (com quatro ou mais diferentes cenários de implantação) híbrido é típico, há muitos casos de cliente com cenário de SAP completo em execução no Azure. Já que as VMs do Microsoft Azure estão se tornando mais poderosas, o número de clientes movendo todas as suas soluções do SAP para o Azure está aumentando.

Rede no contexto de sistemas SAP implantados no Azure o Azure não é complicado. Ele se baseia em princípios a seguir:

- Redes virtuais do Azure (VNets) precisa estar conectado ao circuito do ExpressRoute do Azure que se conecta à rede local.
- Geralmente, um circuito do ExpressRoute conectando-se localmente ao Azure deve ter uma largura de banda de 1 Gbps ou superior. Essa largura de banda mínima permite uma largura de banda adequada para transferir dados entre sistemas locais e em execução em VMs do Azure (bem como conexão com sistemas do Azure de usuários finais locais).
- Todos os sistemas SAP no Azure precisam ser configurado em redes virtuais do Azure para se comunicar entre si.
- O Active Directory e o DNS hospedados localmente são estendidos para o Azure por meio do ExpressRoute por meio do local.


> [!NOTE] 
> Do ponto de vista de cobrança, uma única assinatura do Azure pode ser vinculada somente a um único locatário em um selo de Instância Grande em uma determinada região do Azure e, por outro lado, um único locatário de selo de Instância Grande pode ser vinculado somente a uma assinatura do Azure. Esse fato não é diferente para nenhum outro objeto faturável no Azure

A implantação do SAP HANA no Azure (Instâncias Grandes) em várias regiões diferentes do Azure fará com que um locatário separado seja implantado selo de Instância Grande. No entanto, você pode executar ambos na mesma assinatura do Azure, desde que essas instâncias façam parte do mesmo cenário SAP. 

> [!IMPORTANT] 
> Implantação de gerenciamento de recursos do Azure só é compatível com SAP HANA no Azure (Instâncias Grandes).

 

### <a name="additional-azure-vnet-information"></a>Informações adicionais de rede virtual do Azure

Para conectar uma rede virtual do Azure a ExpressRoute, um gateway do Azure deve ser criado (consulte [sobre gateways de rede virtual para ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Um gateway do Azure pode ser usado com a ExpressRoute para uma infraestrutura fora do Azure (ou um carimbo da instância do Azure grande), ou para se conectar entre redes virtuais do Azure (consulte [configurar uma conexão de VNet para VNet para o Gerenciador de recursos usando o PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Você pode conectar o gateway do Azure com um máximo de quatro conexões diferentes do ExpressRoute, desde que essas conexões sejam provenientes de diferentes roteadores MSEE (MS Enterprise Edges).  Para obter mais informações, consulte [Infraestrutura e conectividade do SAP HANA (instâncias grandes) no Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> A taxa de transferência fornece um gateway do Azure é diferente para os dois casos de uso (consulte [sobre o Gateway de VPN](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). A taxa de transferência máxima que podemos conseguir com um gateway de rede virtual é de 10 Gbps, usando uma conexão do ExpressRoute. Tenha em mente que copiar arquivos entre uma VM do Azure que residem em uma VNet do Azure e um sistema local (como um fluxo de cópia única) não atingirá a taxa de transferência total de SKUs do gateway diferente. Para aproveitar a largura de banda completa do gateway de rede virtual, você deve usar vários fluxos ou copiar arquivos diferentes fluxos paralelos de um único arquivo.


### <a name="networking-architecture-for-hana-large-instances"></a>Arquitetura de rede para Instâncias Grandes do HANA
A arquitetura de rede para Instâncias Grandes do HANA, conforme mostrado abaixo, pode ser separada em quatro partes diferentes:

- Rede local e conexão do ExpressRoute do Azure. Esta parte é o domínio de clientes e é conectado ao Azure através do ExpressRoute. Essa é a parte no canto inferior direito dos gráficos abaixo.
- Rede do Azure conforme discutido acima com VNets do Azure, que novamente têm gateways. Essa é uma área em que você precisa localizar os designs apropriados para seus requisitos de aplicativos, de segurança e de conformidade. Usar as Instâncias Grandes do HANA é outro ponto de consideração em termos de número de VNets e de SKUs de gateway do Azure entre os quais escolher. Essa é a parte no canto superior direito dos gráficos.
- Conectividade de Instâncias Grandes do HANA por meio da tecnologia do ExpressRoute no Azure. Essa parte é implantada e feita pela Microsoft. Tudo que você precisa fazer como cliente é fornecer alguns intervalos de endereços IP e, após a implantação dos ativos em Instâncias Grandes do HANA, conectar o circuito do ExpressRoute às VNets do Azure (consulte [Infraestrutura do SAP HANA (instâncias grandes) e conectividade no Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). 
- Rede em Instâncias Grandes do HANA, o que geralmente é transparente para você como um cliente.

![Rede virtual do Azure conectado ao SAP HANA no Azure (Instâncias Grandes) e local](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

O fato de que você usa Instâncias Grandes do HANA não altera a necessidade de conectar seus ativos locais por meio do ExpressRoute ao Azure. Ele também não altera a necessidade de ter uma ou várias VNets que executam as VMs do Azure que hospedam a camada de aplicativo que se conecta às instâncias do HANA hospedadas em unidades de Instância Grande do HANA. 

A diferença para implantações do SAP no Azure puro acompanha os seguintes fatos:

- As unidades de Instância Grande do HANA do seu locatário do cliente são conectadas por meio de outro circuito do ExpressRoute nas VNets do Azure. Para separar as condições de carga, os links do ExpressRoute do local para VNets do Azure e links entre VNets do Azure e Instâncias Grandes do HANA não compartilham os mesmo roteadores.
- O perfil de carga de trabalho entre a camada de aplicativo SAP e a instância do HANA é de uma natureza diferente de muitas pequenas solicitações e fica em intermitência como transferências de dados (conjuntos de resultados) do SAP HANA para a camada de aplicativo.
- A arquitetura do aplicativo SAP é mais sensível à latência de rede do que os cenários típicos em que dados são trocados entre o local e o Azure.
- O gateway de VNet tem pelo menos duas conexões do ExpressRoute e ambas compartilham a largura de banda máxima para dados de entrada do gateway de VNet.

A latência de rede experimentada entre VMs do Azure e unidades de Instância Grande do HANA pode ser maior que uma latência típica de ida e volta da rede de VM para VM. Dependendo da região do Azure, os valores medidos podem exceder a latência de ida e volta de 0,7 ms, classificada como abaixo da média em [Nota SAP 1100926 – Perguntas frequentes: desempenho de rede](https://launchpad.support.sap.com/#/notes/1100926/E). Mesmo assim, os clientes implantaram aplicativos SAP de produção com base no SAP HANA muito com êxito em Instâncias Grandes do SAP HANA. Os clientes que implantaram relataram grandes melhorias ao executar seus aplicativos SAP no SAP HANA usando unidades de Instância Grande do HANA. No entanto, você deve testar os processos de negócios integralmente em Instâncias Grandes do HANA no Azure.
 
Para fornecer a latência de rede determinística entre VMs do Azure e Instância Grande do HANA, a escolha do SKU do Gateway de VNet do Azure é essencial. Ao contrário de padrões de tráfego entre o local e VMs do Azure, o padrão de tráfego entre VMs do Azure e Instâncias Grandes do HANA pode desenvolver pequenas, porém altas intermitências de solicitações e volumes de dados a serem transmitidos. Para que tais intermitências sejam bem administradas, é altamente recomendável o uso do SKU de Gateway UltraPerformance. Para a classe de Tipo II de SKUs de Instância Grande do HANA, o uso do SKU de gateway UltraPerformance como Gateway de VNet do Azure é obrigatório.  

> [!IMPORTANT] 
> Dado o tráfego de rede geral entre as camadas de banco de dados e aplicativos SAP, somente o HighPerformance ou SKUs de gateway UltraPerformance para redes virtuais tem suporte para conectar-se ao SAP HANA no Azure (Instâncias Grandes). Para SKUs de Tipo II de Instância Grande do HANA, apenas o SKU de Gateway UltraPerformance tem suporte como Gateway de VNet do Azure.



### <a name="single-sap-system"></a>Único sistema SAP

A infraestrutura no local mostrada acima é conectada por meio do ExpressRoute no Azure e o circuito do ExpressRoute se conecta em um roteador de borda do Microsoft Enterprise (MSEE) (consulte [visão geral técnica da ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Uma vez estabelecida, rota, que se conecta o backbone do Microsoft Azure e todas as regiões do Azure são acessíveis.

> [!NOTE] 
> Para executar cenários SAP no Azure, conecte-se para o MSEE mais próximo para a região do Azure no cenário SAP. Os carimbos de Instância Grande do Azure são conectados por meio de dispositivos MSEE dedicados para minimizar a latência da rede entre VMs do Azure no Azure IaaS e os carimbos de Instância Grande.

O gateway de rede virtual para VMs do Azure hospedam instâncias do aplicativo SAP está conectado a esse circuito do ExpressRoute e a mesma rede virtual está conectada a um roteador MSEE separado dedicado à conexão com carimbos de Instância Grande.

Este é um exemplo simples de um sistema SAP, onde a camada de aplicativo SAP é hospedada no Azure e o banco de dados do SAP HANA é executado em SAP HANA no Azure (Instâncias Grandes). A suposição é que a largura de banda de gateway de VNet com taxa de transferência de 2 Gbps ou 10 Gbps não representa um afunilamento.

### <a name="multiple-sap-systems-or-large-sap-systems"></a>Vários sistemas SAP ou grandes sistemas SAP

É razoável presumir que, se vários sistemas SAP ou grandes sistemas SAP forem implantados e se conectarem ao SAP HANA no Azure (Instâncias Grandes), a taxa de transferência do gateway de VNet poderá se tornar um afunilamento. Nesse caso, você precisa dividir as camadas do aplicativo em múltiplas VNets do Azure. Também é recomendável criar VNets especiais que se conectam às Instâncias Grandes do HANA em casos como:

- Execução de backups diretamente por meio das Instâncias do HANA nas Instâncias Grandes do HANA para uma VM no Azure que hospeda compartilhamentos NFS
- Cópia de backups grandes ou de outros arquivos de unidades de Instância Grande do HANA com o espaço em disco gerenciado no Azure.

O uso de VNets separadas que hospedam VMs que gerenciam o armazenamento evita o impacto da transferência de dados ou arquivos grandes das Instâncias Grandes do HANA para o Azure no Gateway da VNet que atende às VMs que executam a camada do aplicativo SAP. 

Uma arquitetura de rede mais escalonável:

- Aproveite diversas redes virtuais do Azure para uma camada de aplicativo SAP única e maior.
- Implante uma rede virtual do Azure separado para cada sistema SAP, em comparação com a combinação desses sistemas SAP em sub-redes separadas na mesma rede virtual.

 Uma arquitetura de rede mais escalonável para o SAP HANA no Azure (Instâncias Grandes):

![Implantação de camada do aplicativo SAP em várias redes virtuais do Azure](./media/hana-overview-architecture/image4-networking-architecture.png)

A implantação da camada de aplicativos ou componentes, SAP em várias VNets do Azure, como mostrado acima, introduziu uma sobrecarga de latência inevitável que ocorreu durante a comunicação entre os aplicativos hospedados nessas VNets do Azure. Por padrão, o tráfego de rede entre VMs do Azure localizadas em diferentes VNets é roteado por meio de roteadores MSEE nessa configuração. No entanto, desde setembro de 2016, esse roteamento pode ser otimizado. Para otimizar e reduzir a latência na comunicação entre duas redes virtuais basta emparelhar as redes virtuais do Azure na mesma região. Mesmo que essas VNets estejam em assinaturas diferentes. Com o emparelhamento VNet do Azure, a comunicação entre VMs em duas VNets diferentes do Azure pode usar o backbone da rede do Azure para se comunicar diretamente entre si. Desse modo, a latência observada é semelhante a de VMs que estão na mesma VNet. Enquanto isso, o tráfego que inclui intervalos de endereço IP que são conectados por meio do gateway da VNet do Azure é roteado pelo gateway da VNet individual da VNet. Você pode obter detalhes sobre o emparelhamento de rede virtual do Azure no artigo [Emparelhamento VNet](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


### <a name="routing-in-azure"></a>Roteamento no Azure

Há duas considerações de roteamento de rede importantes para SAP HANA no Azure (Instâncias Grandes):

1. O SAP HANA no Azure (Instâncias Grandes) só pode ser acessado pelas VMs do Azure na conexão do ExpressRoute dedicada; não diretamente do local. Alguns clientes de administração e os aplicativos que precisam de acesso direto, como o SAP Solution Manager em execução local, não conseguem se conectar ao banco de dados do SAP HANA.

2. As unidades do SAP HANA no Azure (Instâncias Grandes) têm um endereço IP atribuído do intervalo de endereços do Pool de IPs do Servidor como enviado pelo cliente (consulte [Infraestrutura e conectividade do SAP HANA (instâncias grandes) no Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter detalhes).  Esse endereço IP é acessível por meio das assinaturas do Azure e do ExpressRoute que conecta as VNets do Azure ao HANA no Azure (Instâncias Grandes). O endereço IP atribuído fora desse intervalo de endereços do Pool de IPs do Servidor é diretamente atribuído à unidade de hardware e não usa mais o NAT como era o caso nas primeiras implantações dessa solução. 

> [!NOTE] 
> Se você precisar se conectar ao SAP HANA no Azure (Instâncias Grandes) em um _do data warehouse_ cenário, onde aplicativos e/ou usuários finais precisam se conectar no banco de dados SAP HANA (executando diretamente), outro componente de rede deve ser usado: um proxy reverso para dados da rota, de e para. Por exemplo, F5 BIG-IP, NGINX com o Gerenciador de Tráfego implantado no Azure como uma solução de roteamento de tráfego/firewall virtual.

### <a name="internet-connectivity-of-hana-large-instances"></a>Conectividade com a Internet das Instâncias Grandes do HANA
As Instâncias Grandes do HANA NÃO têm conectividade direta com a Internet. Isso restringe sua capacidade de, por exemplo, registrar a imagem do sistema operacional diretamente no fornecedor do sistema operacional. Portanto, talvez você precise trabalhar com o servidor SMT SLES local ou o Gerenciador de Assinaturas RHEL

### <a name="data-encryption-between-azure-vms-and-hana-large-instances"></a>Criptografia de dados entre VMs do Azure e Instâncias Grandes do HANA
Os dados transferidos entre as Instâncias Grandes do HANA e as VMs do Azure não são criptografados. No entanto, apenas para a troca entre o lado do DBMS do HANA e aplicativos baseados em JDBC/ODBC, é possível habilitar criptografia do tráfego. Consulte [esta documentação da SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false)

### <a name="using-hana-large-instance-units-in-multiple-regions"></a>Usando unidades de Instância Grande do HANA em várias regiões

Você pode ter outros motivos para implantar SAP HANA no Azure (Instâncias Grandes) em várias regiões do Azure, além de DR. Talvez você queira acessar o HANA em Instâncias Grandes de cada uma das máquinas virtuais implantadas nas redes virtuais diferentes nas regiões. Como os endereços IP atribuídos às diferentes unidades de Instâncias Grandes do HANA não são propagados além das VNets do Azure (que estão diretamente conectadas por meio de seu gateway às instâncias), há uma pequena alteração no design da VNet apresentado acima: um gateway de VNet do Azure pode manipular quatro circuitos diferentes do ExpressRoute de MSEEs diferentes e cada VNet conectada a um dos carimbos de Instância Grande pode ser conectada ao carimbo de Instância Grande em outra região do Azure.

![Redes virtuais do Azure conectadas ao carimbos de Instância Grande do Azure em diferentes regiões do Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

A figura acima mostra como as diferentes redes virtuais do Azure em ambas as regiões estão conectadas a dois circuitos do ExpressRoute diferentes que são usados para se conectar ao SAP HANA no Azure (Instâncias Grandes) em ambas as regiões do Azure. As conexões recém-introduzidas são as linhas vermelhas retangulares. Com essas conexões, fora das VNets do Azure, as VMs em execução em uma dessas VNets podem acessar cada uma das diferentes unidades de Instâncias Grandes do HANA implantadas nas duas regiões. Como você pode ver nos gráficos acima, presume-se que você tenha duas conexões do ExpressRoute do local em duas regiões do Azure; recomendada por motivos de Recuperação de Desastre.

> [!IMPORTANT] 
> Se forem usados vários circuitos do ExpressRoute, as configurações de prefixação do AS Path e de BGP de Preferência Local deverão ser usadas para garantir o roteamento apropriado do tráfego.


