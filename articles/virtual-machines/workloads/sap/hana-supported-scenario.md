---
title: Cenários com suporte para SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: Cenários suportados e seus detalhes de arquitetura para o SAP HANA no Azure (Instâncias Grandes)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/26/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7f33ef81282dd8e60eba64cc77cbd95ab8dbc557
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967560"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Cenários suportados para instâncias grandes do HANA
Este artigo descreve os cenários com suporte e os detalhes de arquitetura para o HLI (HANA Large instances).

>[!NOTE]
>Se seu cenário necessário não for mencionado neste artigo, entre em contato com a equipe de gerenciamento de serviços da Microsoft para avaliar suas necessidades.
Antes de configurar a unidade HLI, valide o design com o SAP ou com seu parceiro de implementação de serviço.

## <a name="terms-and-definitions"></a>Termos e definições
Vamos entender os termos e as definições que são usados neste artigo:

- **Sid**: um identificador de sistema para o sistema Hana
- **HLI**: instâncias grandes do Hana
- **Dr**: recuperação de desastre
- **Dr normal**: uma configuração do sistema com um recurso dedicado somente para fins de recuperação de desastre
- **Dr multipropósito**: um sistema de site de recuperação de desastre que está configurado para usar um ambiente de não produção junto com uma instância de produção configurada para um evento de recuperação de desastre 
- **Sid único**: um sistema com uma instância instalada
- **Multi-Sid**: um sistema com várias instâncias configuradas; também chamado de ambiente MCOS
- **HSR**: replicação de sistema de SAP Hana

## <a name="overview"></a>Visão geral
O HANA em instâncias grandes dá suporte a uma variedade de arquiteturas para ajudá-lo a atingir seus requisitos de negócios. As seções a seguir abrangem os cenários arquitetônicos e seus detalhes de configuração. 

O design de arquitetura derivada é puramente de uma perspectiva de infraestrutura e você deve consultar o SAP ou seus parceiros de implementação para a implantação do HANA. Se seus cenários não estiverem listados neste artigo, entre em contato com a equipe de conta Microsoft para examinar a arquitetura e derivar uma solução para você.

> [!NOTE]
> Essas arquiteturas são totalmente compatíveis com o design de TDI (integração de dados personalizada) e com suporte da SAP.

Este artigo descreve os detalhes dos dois componentes em cada arquitetura com suporte:

- Ethernet
- Armazenamento

### <a name="ethernet"></a>Ethernet

Cada servidor provisionado vem pré-configurado com conjuntos de interfaces Ethernet. As interfaces Ethernet configuradas em cada unidade de HLI são categorizadas em quatro tipos:

- **A**: usada para o ou por acesso do cliente.
- **B**: usado para comunicação de nó para nó. Essa interface é configurada em todos os servidores (independentemente da topologia solicitada), mas usada somente para cenários de expansão.
- **C**: usado para conectividade de nó para armazenamento.
- **D**: usado para conexão de dispositivo de nó para iSCSI para a instalação do STONITH. Essa interface é configurada somente quando uma configuração HSR é solicitada.  

| Interface lógica NIC | Tipo de SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| Um | TIPO I | eth0.tenant | eno1.tenant | Cliente a HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Nó para nó|
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | STONITH |
| Um | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente a HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Nó para nó|
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | STONITH |

Você escolhe a interface com base na topologia configurada na unidade HLI. Por exemplo, a interface "B" é configurada para comunicação de nó para nó, que é útil quando você tem uma topologia de expansão configurada. Essa interface não é usada para configurações de expansão de nó único. Para obter mais informações sobre o uso da interface, examine os cenários necessários (mais adiante neste artigo). 

Se necessário, você pode definir placas NIC adicionais por conta própria. No entanto, as configurações de NICs existentes *não podem* ser alteradas.

>[!NOTE]
>Você pode encontrar interfaces adicionais que são interfaces físicas ou acoplamento. Você deve considerar apenas as interfaces mencionadas anteriormente para seu caso de uso. Qualquer outra pessoa pode ser ignorada.

A distribuição de unidades com dois endereços IP atribuídos deve ser semelhante a:

- A Ethernet "A" deve ter um endereço IP atribuído que esteja dentro do intervalo de endereços do pool de IPS do servidor que você enviou para a Microsoft. Esse endereço IP deve ser mantido no diretório */etc/hosts* do sistema operacional.

- A Ethernet "C" deve ter um endereço IP atribuído que é usado para comunicação com o NFS. Esse endereço *não* precisa ser mantido no diretório *etc/hosts* para permitir o tráfego de instância para instância dentro do locatário.

Para replicação de sistema do HANA ou implantação em expansão do HANA, uma configuração de folha com dois endereços IP atribuídos não é adequada. Se você tiver apenas dois endereços IP atribuídos e desejar implantar essa configuração, entre em contato com SAP HANA no gerenciamento de serviços do Azure. Eles podem atribuir um terceiro endereço IP em uma terceira VLAN. Para unidades do HANA em instâncias grandes com três endereços IP atribuídos em três portas NIC, as seguintes regras de uso se aplicam:

- A Ethernet "A" deve ter um endereço IP atribuído que está fora do intervalo de endereços do pool de IPS do servidor que você enviou à Microsoft. Esse endereço IP não deve ser mantido no diretório *etc/hosts* do sistema operacional.

- A Ethernet "B" deve ser mantida exclusivamente no diretório *etc/hosts* para comunicação entre as várias instâncias. Esses são os endereços IP a serem mantidos em configurações do HANA de expansão como os endereços IP que o HANA usa para a configuração entre nós.

- A Ethernet "C" deve ter um endereço IP atribuído que é usado para comunicação com o armazenamento NFS. Esse tipo de endereço não deve ser mantido no diretório *etc/hosts* .

- A Ethernet "D" deve ser usada exclusivamente para acesso a dispositivos STONITH para pacemaker. Essa interface é necessária quando você configura a replicação do sistema HANA e deseja obter o failover automático do sistema operacional usando um dispositivo baseado em SBD.


### <a name="storage"></a>Armazenamento
O armazenamento é pré-configurado com base na topologia solicitada. Os tamanhos e os pontos de montagem de volume variam dependendo do número de servidores, do número de SKUs e da topologia configurada. Para obter mais informações, examine os cenários necessários (mais adiante neste artigo). Se você precisar de mais armazenamento, poderá comprá-lo em incrementos de 1 TB.

>[!NOTE]
>O ponto de montagem/usr/SAP/ \<SID> é um link simbólico para o ponto de montagem/Hana/Shared.


## <a name="supported-scenarios"></a>Cenários com suporte

Os diagramas de arquitetura nas próximas seções usam as seguintes notações:

[![Tabela de diagramas de arquitetura](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

Aqui estão os cenários com suporte:

* Único nó com um SID
* Único nó MCOS
* Nó único com DR (normal)
* Nó único com DR (multipropósito)
* HSR com STONITH
* HSR com DR (normal/multipropósito) 
* Failover Automático do Host (1 + 1) 
* Expansão com espera
* Expansão sem espera
* Expansão com DR

## <a name="single-node-with-one-sid"></a>Único nó com um SID

Essa topologia dá suporte a um nó em uma configuração de expansão com um SID.

### <a name="architecture-diagram"></a>Diagrama de arquitetura  

![Único nó com um SID](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| Interface lógica NIC | Tipo de SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| Um | TIPO I | eth0.tenant | eno1.tenant | Cliente a HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| Um | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente a HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|/hana/shared/SID | Instalação do HANA | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log | 
|/hana/logbackups/SID | Redo logs |

### <a name="key-considerations"></a>Principais considerações
- /usr/sap/SID is a symbolic link to /hana/shared/SID.

## <a name="single-node-mcos"></a>Único nó MCOS

Essa topologia dá suporte a um nó em uma configuração de expansão com vários SIDs.

### <a name="architecture-diagram"></a>Diagrama de arquitetura  

![Único nó MCOS](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| Interface lógica NIC | Tipo de SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| Um | TIPO I | eth0.tenant | eno1.tenant | Cliente a HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| Um | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente a HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|/hana/shared/SID1 | Instalação do HANA para SID1 | 
|/hana/data/SID1/mnt00001 | Instalação de arquivos de dados para SID1 | 
|/hana/log/SID1/mnt00001 | Instalação de arquivos de log para SID1 | 
|/hana/logbackups/SID1 | Redo logs for SID1 |
|/hana/shared/SID2 | Instalação do HANA para SID2 | 
|/hana/data/SID2/mnt00001 | Instalação de arquivos de dados para SID2 | 
|/hana/log/SID2/mnt00001 | Instalação de arquivos de log para SID2 | 
|/hana/logbackups/SID2 | Redo logs for SID2 |

### <a name="key-considerations"></a>Principais considerações
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- A distribuição do tamanho do volume é baseada no tamanho do banco de dados na memória. Para saber quais tamanhos de banco de dados na memória têm suporte em um ambiente de vários SIDs, consulte [visão geral e arquitetura](./hana-overview-architecture.md).

## <a name="single-node-with-dr-using-storage-replication"></a>Nó único com DR usando replicação de armazenamento
 
Essa topologia dá suporte a um nó em uma configuração de expansão com um ou vários SIDs, com replicação baseada em armazenamento para o local de recuperação de desastre para um SID primário. No diagrama, apenas um sistema de SID único é representado no site primário, mas os sistemas MCOS também têm suporte.

### <a name="architecture-diagram"></a>Diagrama de arquitetura  

![Nó único com DR usando replicação de armazenamento](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| Interface lógica NIC | Tipo de SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| Um | TIPO I | eth0.tenant | eno1.tenant | Cliente a HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| Um | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente a HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|/hana/shared/SID | Instalação do HANA para SID | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID | 
|/hana/logbackups/SID | Redo logs for SID |


### <a name="key-considerations"></a>Principais considerações
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- Para MCOS: a distribuição do tamanho do volume é baseada no tamanho do banco de dados na memória. Para saber quais tamanhos de banco de dados na memória têm suporte em um ambiente de vários SIDs, consulte [visão geral e arquitetura](./hana-overview-architecture.md).
- No local de recuperação de desastre: os volumes e pontos de montagem são configurados (marcados como "necessários para a instalação do HANA") para a instalação da instância do HANA de produção na unidade de recuperação de desastres. 
- No local de recuperação de desastre: os dados, backups de log e volumes compartilhados (marcados como "replicação de armazenamento") são replicados por meio do instantâneo do site de produção. Esses volumes são montados somente durante o failover. Para obter mais informações, consulte [procedimento de failover de recuperação de desastre](./hana-overview-high-availability-disaster-recovery.md).
- O volume de inicialização para a *classe do tipo SKU I* é replicado para o nó de recuperação de desastres.


## <a name="single-node-with-dr-multipurpose-using-storage-replication"></a>Nó único com DR (multipropósito) usando replicação de armazenamento
 
Essa topologia dá suporte a um nó em uma configuração de expansão com um ou vários SIDs, com replicação baseada em armazenamento para o local de recuperação de desastre para um SID primário. No diagrama, apenas um sistema de SID único é representado no site primário, mas também há suporte para sistemas MCOS (vários SIDs). No local de recuperação de desastre, a unidade HLI é usada para a instância de QA enquanto as operações de produção são executadas a partir do site primário. Durante o failover de DR (ou teste de failover), a instância de QA no local de recuperação de desastres é desativada.

### <a name="architecture-diagram"></a>Diagrama de arquitetura  

![Nó único com DR (multipropósito) usando replicação de armazenamento](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| Interface lógica NIC | Tipo de SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| Um | TIPO I | eth0.tenant | eno1.tenant | Cliente a HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| Um | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente a HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**No site principal**|
|/hana/shared/SID | Instalação do HANA para SID de produção | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID de produção | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |
|**No site de DR**|
|/hana/shared/SID | Instalação do HANA para SID de produção | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID de produção | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/shared/QA-SID | Instalação do HANA para SID de QA | 
|/hana/data/QA-SID/mnt00001 | Instalação de arquivos de dados para SID de QA | 
|/hana/log/QA-SID/mnt00001 | Instalação de arquivos de log para SID de QA |
|/hana/logbackups/QA-SID | Redo logs for QA SID |

### <a name="key-considerations"></a>Principais considerações
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- Para MCOS: a distribuição do tamanho do volume é baseada no tamanho do banco de dados na memória. Para saber quais tamanhos de banco de dados na memória têm suporte em um ambiente de vários SIDs, consulte [visão geral e arquitetura](./hana-overview-architecture.md).
- No local de recuperação de desastre: os volumes e pontos de montagem são configurados (marcados como "necessários para a instalação do HANA") para a instalação da instância do HANA de produção na unidade de recuperação de desastres. 
- No local de recuperação de desastre: os dados, backups de log e volumes compartilhados (marcados como "replicação de armazenamento") são replicados por meio do instantâneo do site de produção. Esses volumes são montados somente durante o failover. Para obter mais informações, consulte [procedimento de failover de recuperação de desastre](./hana-overview-high-availability-disaster-recovery.md). 
- No local de recuperação de desastre: os dados, backups de log, log e volumes compartilhados para QA (marcados como "instalação da instância de QA") são configurados para a instalação da instância de QA.
- O volume de inicialização para a *classe do tipo SKU I* é replicado para o nó de recuperação de desastres.

## <a name="hsr-with-stonith-for-high-availability"></a>HSR com STONITH para alta disponibilidade
 
Essa topologia dá suporte a dois nós para a configuração de replicação do sistema do HANA. Essa configuração tem suporte apenas para instâncias do HANA individuais em um nó. Isso significa que *não* há suporte para cenários MCOS.

> [!NOTE]
> A partir de dezembro de 2019, essa arquitetura tem suporte apenas para o sistema operacional SUSE.


### <a name="architecture-diagram"></a>Diagrama de arquitetura  

![HSR com STONITH para alta disponibilidade](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| Interface lógica NIC | Tipo de SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| Um | TIPO I | eth0.tenant | eno1.tenant | Cliente a HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Used for STONITH |
| Um | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente a HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Used for STONITH |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**No nó primário**|
|/hana/shared/SID | Instalação do HANA para SID de produção | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID de produção | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |
|**No nó secundário**|
|/hana/shared/SID | Instalação do HANA para SID secundário | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID secundário | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID secundário | 
|/hana/logbackups/SID | Redo logs para o secundário SID |

### <a name="key-considerations"></a>Principais considerações
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- Para MCOS: a distribuição do tamanho do volume é baseada no tamanho do banco de dados na memória. Para saber quais tamanhos de banco de dados na memória têm suporte em um ambiente de vários SIDs, consulte [visão geral e arquitetura](./hana-overview-architecture.md).
- STONITH: Um SBD está configurado para a configuração do STONITH. No entanto, o uso de STONITH é opcional.


## <a name="high-availability-with-hsr-and-dr-with-storage-replication"></a>Alta disponibilidade com HSR e DR com replicação de armazenamento
 
Essa topologia dá suporte a dois nós para a configuração de replicação do sistema do HANA. Há suporte para DRs normal e multipropósito. Essas configurações têm suporte apenas para instâncias do HANA individuais em um nó. Isso significa que *não* há suporte para cenários MCOS com essas configurações.

No diagrama, um cenário de multipropósito é descrito no site de recuperação de desastre, em que a unidade de HLI é usada para a instância de QA enquanto as operações de produção são executadas a partir do site primário. Durante o failover de DR (ou teste de failover), a instância de QA no local de recuperação de desastres é desativada. 

### <a name="architecture-diagram"></a>Diagrama de arquitetura  

![Alta disponibilidade com HSR e DR com replicação de armazenamento](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| Interface lógica NIC | Tipo de SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| Um | TIPO I | eth0.tenant | eno1.tenant | Cliente a HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Used for STONITH |
| Um | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente a HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Used for STONITH |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**No nó primário no site primário**|
|/hana/shared/SID | Instalação do HANA para SID de produção | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID de produção | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |
|**No nó secundário no site principal**|
|/hana/shared/SID | Instalação do HANA para SID secundário | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID secundário | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID secundário | 
|/hana/logbackups/SID | Redo logs para o secundário SID |
|**No site de DR**|
|/hana/shared/SID | Instalação do HANA para SID de produção | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID de produção | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/shared/QA-SID | Instalação do HANA para SID de QA | 
|/hana/data/QA-SID/mnt00001 | Instalação de arquivos de dados para SID de QA | 
|/hana/log/QA-SID/mnt00001 | Instalação de arquivos de log para SID de QA |
|/hana/logbackups/QA-SID | Redo logs for QA SID |

### <a name="key-considerations"></a>Principais considerações
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- Para MCOS: a distribuição do tamanho do volume é baseada no tamanho do banco de dados na memória. Para saber quais tamanhos de banco de dados na memória têm suporte em um ambiente de vários SIDs, consulte [visão geral e arquitetura](./hana-overview-architecture.md).
- STONITH: Um SBD está configurado para a configuração do STONITH. No entanto, o uso de STONITH é opcional.
- No local de recuperação de desastre: *dois conjuntos de volumes de armazenamento são necessários* para a replicação de nó primário e secundário.
- No local de recuperação de desastre: os volumes e pontos de montagem são configurados (marcados como "necessários para a instalação do HANA") para a instalação da instância do HANA de produção na unidade de recuperação de desastres. 
- No local de recuperação de desastre: os dados, backups de log e volumes compartilhados (marcados como "replicação de armazenamento") são replicados por meio do instantâneo do site de produção. Esses volumes são montados somente durante o failover. Para obter mais informações, consulte [procedimento de failover de recuperação de desastre](./hana-overview-high-availability-disaster-recovery.md). 
- No local de recuperação de desastre: os dados, backups de log, log e volumes compartilhados para QA (marcados como "instalação da instância de QA") são configurados para a instalação da instância de QA.
- O volume de inicialização para a *classe do tipo SKU I* é replicado para o nó de recuperação de desastres.


## <a name="host-auto-failover-11"></a>Failover Automático do Host (1 + 1)
 
Esta topologia suporta dois nós em uma configuração de failover automático do host. Há um nó com uma função mestre/de trabalho e outro como em espera. *O SAP dá suporte a esse cenário somente para S/4 HANA.* Para obter mais informações, consulte [a observação de OSS 2408419-SAP S/4HANA-suporte a vários nós](https://launchpad.support.sap.com/#/notes/2408419).



### <a name="architecture-diagram"></a>Diagrama de arquitetura  

![Failover Automático do Host (1 + 1)](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| Interface lógica NIC | Tipo de SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| Um | TIPO I | eth0.tenant | eno1.tenant | Cliente a HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Comunicação de nó para nó |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| Um | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente a HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Comunicação de nó para nó |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**Nos nós mestre e de espera**|
|/hana/shared | Instalação do HANA para SID de produção | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID de produção | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |



### <a name="key-considerations"></a>Principais considerações
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- Em espera: os volumes e pontos de montagem são configurados (marcados como "necessários para a instalação do HANA") para a instalação da instância do HANA na unidade em espera.
 

## <a name="scale-out-with-standby"></a>Expansão com espera
 
Essa topologia permite vários nós em uma configuração de expansão. Há um nó com uma função mestre, um ou mais nós com uma função de trabalho e um ou mais nós como em espera. No entanto, pode haver apenas um nó mestre em um único ponto no tempo.


### <a name="architecture-diagram"></a>Diagrama de arquitetura  

![Expansão com espera](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| Interface lógica NIC | Tipo de SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| Um | TIPO I | eth0.tenant | eno1.tenant | Cliente a HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Comunicação de nó para nó |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| Um | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente a HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Comunicação de nó para nó |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**Nos nós mestre, de trabalho e de espera**|
|/hana/shared | Instalação do HANA para SID de produção | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID de produção | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |


## <a name="scale-out-without-standby"></a>Expansão sem espera
 
Essa topologia permite vários nós em uma configuração de expansão. Há um nó com uma função mestre e um ou mais nós com uma função de trabalho. No entanto, pode haver apenas um nó mestre em um único ponto no tempo.


### <a name="architecture-diagram"></a>Diagrama de arquitetura  

![Expansão sem espera](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| Interface lógica NIC | Tipo de SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| Um | TIPO I | eth0.tenant | eno1.tenant | Cliente a HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Comunicação de nó para nó |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| Um | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente a HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Comunicação de nó para nó |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**Nos nós mestre e trabalhador**|
|/hana/shared | Instalação do HANA para SID de produção | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID de produção | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |


### <a name="key-considerations"></a>Principais considerações
- /usr/sap/SID is a symbolic link to /hana/shared/SID.

## <a name="scale-out-with-dr-using-storage-replication"></a>Escalar horizontalmente com recuperação de desastre usando replicação de armazenamento
 
Esta topologia permite vários nós em uma expansão com DR. Há suporte para DRs normal e multipropósito. No diagrama, apenas o DR de finalidade única é representado. Você pode solicitar essa topologia com ou sem o nó de espera.


### <a name="architecture-diagram"></a>Diagrama de arquitetura  

![Escalar horizontalmente com recuperação de desastre usando replicação de armazenamento](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| Interface lógica NIC | Tipo de SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| Um | TIPO I | eth0.tenant | eno1.tenant | Cliente a HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Comunicação de nó para nó |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| Um | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente a HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Comunicação de nó para nó |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**No nó primário**|
|/hana/shared | Instalação do HANA para SID de produção | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID de produção | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |
|**No nó DR**|
|/hana/shared | Instalação do HANA para SID de produção | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID de produção | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 


### <a name="key-considerations"></a>Principais considerações
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
-  No local de recuperação de desastre: os volumes e pontos de montagem são configurados (marcados como "necessários para a instalação do HANA") para a instalação da instância do HANA de produção na unidade de recuperação de desastres. 
- No local de recuperação de desastre: os dados, backups de log e volumes compartilhados (marcados como "replicação de armazenamento") são replicados por meio do instantâneo do site de produção. Esses volumes são montados somente durante o failover. Para obter mais informações, consulte [procedimento de failover de recuperação de desastre](./hana-overview-high-availability-disaster-recovery.md). 
- O volume de inicialização para a *classe do tipo SKU I* é replicado para o nó de recuperação de desastres.


## <a name="single-node-with-dr-using-hsr"></a>Nó único com DR usando HSR
 
Essa topologia dá suporte a um nó em uma configuração de expansão com um SID, com replicação de sistema do HANA para o site de recuperação de desastre para um SID primário. No diagrama, apenas um sistema de SID único é representado no site primário, mas também há suporte para sistemas MCOS (vários SIDs).

### <a name="architecture-diagram"></a>Diagrama de arquitetura  

![Nó único com DR usando HSR](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| Interface lógica NIC | Tipo de SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| Um | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI/HSR |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| Um | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente para HLI/HSR |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados em ambas as unidades de HLI (primária e DR):

| Ponto de montagem | Caso de uso | 
| --- | --- |
|/hana/shared/SID | Instalação do HANA para SID | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID | 
|/hana/logbackups/SID | Redo logs for SID |


### <a name="key-considerations"></a>Principais considerações
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- Para MCOS: a distribuição do tamanho do volume é baseada no tamanho do banco de dados na memória. Para saber quais tamanhos de banco de dados na memória têm suporte em um ambiente de vários SIDs, consulte [visão geral e arquitetura](./hana-overview-architecture.md).
- O nó primário é sincronizado com o nó de recuperação de desastres usando a replicação de sistema do HANA. 
- [Alcance global](../../../expressroute/expressroute-global-reach.md) é usado para vincular os circuitos do ExpressRoute em conjunto para fazer uma rede privada entre suas redes regionais.



## <a name="single-node-hsr-to-dr-cost-optimized"></a>HSR de nó único para DR (com otimização de custo) 
 
 Essa topologia dá suporte a um nó em uma configuração de expansão com um SID, com replicação de sistema do HANA para o site de recuperação de desastre para um SID primário. No diagrama, apenas um sistema de SID único é representado no site primário, mas também há suporte para sistemas MCOS (vários SIDs). No local de recuperação de desastre, uma unidade de HLI é usada para a instância de QA enquanto as operações de produção são executadas a partir do site primário. Durante o failover de DR (ou teste de failover), a instância de QA no local de recuperação de desastres é desativada.

### <a name="architecture-diagram"></a>Diagrama de arquitetura  

![HSR de nó único para DR (com otimização de custo)](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| Interface lógica NIC | Tipo de SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| Um | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI/HSR |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| Um | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente para HLI/HSR |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**No site principal**|
|/hana/shared/SID | Instalação do HANA para SID de produção | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID de produção | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |
|**No site de DR**|
|/hana/shared/SID | Instalação do HANA para SID de produção | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID de produção | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |
|/hana/shared/QA-SID | Instalação do HANA para SID de QA | 
|/hana/data/QA-SID/mnt00001 | Instalação de arquivos de dados para SID de QA | 
|/hana/log/QA-SID/mnt00001 | Instalação de arquivos de log para SID de QA |
|/hana/logbackups/QA-SID | Redo logs for QA SID |

### <a name="key-considerations"></a>Principais considerações
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- Para MCOS: a distribuição do tamanho do volume é baseada no tamanho do banco de dados na memória. Para saber quais tamanhos de banco de dados na memória têm suporte em um ambiente de vários SIDs, consulte [visão geral e arquitetura](./hana-overview-architecture.md).
- No local de recuperação de desastre: os volumes e pontos de montagem são configurados (marcados como "instância de produção no local de recuperação de desastre") para a instalação da instância do HANA de produção na unidade de recuperação de desastres. 
- No local de recuperação de desastre: os dados, backups de log, log e volumes compartilhados para QA (marcados como "instalação da instância de QA") são configurados para a instalação da instância de QA.
- O nó primário é sincronizado com o nó de recuperação de desastres usando a replicação de sistema do HANA. 
- [Alcance global](../../../expressroute/expressroute-global-reach.md) é usado para vincular os circuitos do ExpressRoute em conjunto para fazer uma rede privada entre suas redes regionais.

## <a name="high-availability-and-disaster-recovery-with-hsr"></a>Alta disponibilidade e recuperação de desastre com o HSR 
 
 Essa topologia dá suporte a dois nós para a configuração de replicação do sistema HANA para a alta disponibilidade das regiões locais. Para a recuperação de desastre, o terceiro nó na região de DR é sincronizado com o site primário usando HSR (modo assíncrono). 

### <a name="architecture-diagram"></a>Diagrama de arquitetura  

![Alta disponibilidade e recuperação de desastre com o HSR](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| Interface lógica NIC | Tipo de SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| Um | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI/HSR |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| Um | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente para HLI/HSR |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**No site principal**|
|/hana/shared/SID | Instalação do HANA para SID de produção | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID de produção | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |
|**No site de DR**|
|/hana/shared/SID | Instalação do HANA para SID de produção | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID de produção | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |


### <a name="key-considerations"></a>Principais considerações
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- No local da recuperação de desastre: os volumes e pontos de montagem são configurados (marcados como "instância de DR de produção") para a instalação da instância do HANA de produção na unidade de DR HLI. 
- O nó do site primário é sincronizado com o nó de recuperação de desastres usando a replicação do sistema do HANA. 
- [Alcance global](../../../expressroute/expressroute-global-reach.md) é usado para vincular os circuitos do ExpressRoute em conjunto para fazer uma rede privada entre suas redes regionais.

## <a name="high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>Alta disponibilidade e recuperação de desastre com HSR (com otimização de custo)
 
 Essa topologia dá suporte a dois nós para a configuração de replicação de sistema do HANA para a alta disponibilidade das regiões locais. Para a recuperação de desastre, o terceiro nó na região de DR é sincronizado com o site primário usando HSR (modo assíncrono), enquanto outra instância (por exemplo, QA) já está ficando fora do nó de recuperação de desastres. 

### <a name="architecture-diagram"></a>Diagrama de arquitetura  

![Alta disponibilidade e recuperação de desastre com HSR (com otimização de custo)](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| Interface lógica NIC | Tipo de SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| Um | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI/HSR |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| Um | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente para HLI/HSR |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**No site principal**|
|/hana/shared/SID | Instalação do HANA para SID de produção | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID de produção | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |
|**No site de DR**|
|/hana/shared/SID | Instalação do HANA para SID de produção | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID de produção | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |
|/hana/shared/QA-SID | Instalação do HANA para SID de QA | 
|/hana/data/QA-SID/mnt00001 | Instalação de arquivos de dados para SID de QA | 
|/hana/log/QA-SID/mnt00001 | Instalação de arquivos de log para SID de QA |
|/hana/logbackups/QA-SID | Redo logs for QA SID |

### <a name="key-considerations"></a>Principais considerações
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- No local da recuperação de desastre: os volumes e pontos de montagem são configurados (marcados como "instância de DR de produção") para a instalação da instância do HANA de produção na unidade de DR HLI. 
- No local de recuperação de desastre: os dados, backups de log, log e volumes compartilhados para QA (marcados como "instalação da instância de QA") são configurados para a instalação da instância de QA.
- O nó do site primário é sincronizado com o nó de recuperação de desastres usando a replicação do sistema do HANA. 
- [Alcance global](../../../expressroute/expressroute-global-reach.md) é usado para vincular os circuitos do ExpressRoute em conjunto para fazer uma rede privada entre suas redes regionais.

## <a name="scale-out-with-dr-using-hsr"></a>Escale horizontalmente com DR usando HSR
 
Esta topologia permite vários nós em uma expansão com DR. Você pode solicitar essa topologia com ou sem o nó de espera. O nó do site primário é sincronizado com o nó do site de recuperação de desastre usando a replicação do sistema do HANA (modo assíncrono).


### <a name="architecture-diagram"></a>Diagrama de arquitetura  

[![Escale horizontalmente com Dr usando HSR](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| Interface lógica NIC | Tipo de SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de uso|
| --- | --- | --- | --- | --- |
| Um | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI/HSR |
| B | TIPO I | eth2.tenant | eno3.tenant | Comunicação de nó para nó |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| Um | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente para HLI/HSR |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Comunicação de nó para nó |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Nó para armazenamento |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de uso | 
| --- | --- |
|**No nó primário**|
|/hana/shared | Instalação do HANA para SID de produção | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID de produção | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |
|**No nó DR**|
|/hana/shared | Instalação do HANA para SID de produção | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID de produção | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Redo logs para produção SID |


### <a name="key-considerations"></a>Principais considerações
- /usr/sap/SID is a symbolic link to /hana/shared/SID.
- No local de recuperação de desastre: os volumes e pontos de montagem são configurados para a instalação da instância do HANA de produção na unidade de recuperação de desastre. 
- O nó do site primário é sincronizado com o nó de recuperação de desastres usando a replicação do sistema do HANA. 
- [Alcance global](../../../expressroute/expressroute-global-reach.md) é usado para vincular os circuitos do ExpressRoute em conjunto para fazer uma rede privada entre suas redes regionais.


## <a name="next-steps"></a>Próximas etapas
- [Infraestrutura e conectividade](./hana-overview-infrastructure-connectivity.md) para instâncias grandes do Hana
- [Alta disponibilidade e recuperação de desastre](./hana-overview-high-availability-disaster-recovery.md) para instâncias grandes do Hana
