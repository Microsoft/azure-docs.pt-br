---
title: Considerações para Implantação do DBMS de Máquinas de Virtuais do Azure para carga de trabalho do SAP | Microsoft Docs
description: Considerações para Implantação do DBMS de Máquinas de Virtuais do Azure para carga de trabalho do SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 975289f338e638ed0209d4f6cf2a163ced996e42
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202934"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Considerações para Implantação do DBMS de Máquinas de Virtuais do Azure para carga de trabalho do SAP
[1114181]: https://launchpad.support.sap.com/#/notes/1114181
[1409604]: https://launchpad.support.sap.com/#/notes/1409604
[1597355]: https://launchpad.support.sap.com/#/notes/1597355
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2039619]: https://launchpad.support.sap.com/#/notes/2039619
[2069760]: https://launchpad.support.sap.com/#/notes/2069760
[2171857]: https://launchpad.support.sap.com/#/notes/2171857
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2233094]: https://launchpad.support.sap.com/#/notes/2233094
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[deployment-guide]:deployment-guide.md
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[planning-guide]:planning-guide.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Este guia faz parte da documentação sobre como implementar e implantar o software SAP no Microsoft Azure. Antes de ler este guia, leia as [guia de planejamento e implementação][planning-guide]. Este documento aborda os aspectos de implantação genérico dos sistemas DBMS relacionados ao SAP em máquinas de virtuais (VMs) do Microsoft Azure, usando a infraestrutura do Azure como recursos de um serviço (IaaS).

O documento complementa a documentação de instalação do SAP e as notas de SAP, que representam os recursos primários para instalações e implantações de software SAP em determinadas plataformas.

Neste documento, são introduzidas considerações sobre a execução de sistemas DBMS relacionados ao SAP em VMs do Azure. Há algumas referências a sistemas DBMS específicos neste capítulo. Em vez disso, os sistemas DBMS específicos são abordados neste documento, após este documento.

## <a name="definitions"></a>Definições
Em todo o documento, esses termos são usados:

* **IaaS**: Infraestrutura como serviço.
* **PaaS**: Plataforma como serviço.
* **SaaS**: Software como serviço.
* **Componente SAP**: Um aplicativo SAP individual como ERP ECC (componente Central), BW (Business Warehouse), Solution Manager ou EP (Portal Empresarial). Componentes da SAP podem ser baseados em tecnologias ABAP ou Java tradicionais ou em um aplicativo não baseado em NetWeaver, como objetos de negócios.
* **Ambiente SAP**: Um ou mais componentes SAP agrupados logicamente para executar uma função de negócios, como desenvolvimento, garantia de qualidade, treinamento, DR ou produção.
* **Estrutura da SAP**: Este termo refere-se à totalidade dos ativos SAP na estrutura de TI de um cliente. A estrutura da SAP inclui todos os ambientes de produção e de não produção.
* **Sistema SAP**: A combinação de uma camada DBMS e uma camada de aplicativo de, por exemplo, um sistema de desenvolvimento SAP ERP, um SAP Business Warehouse testar um sistema ou um sistema de produção SAP CRM. Em implantações do Azure, a divisão essas duas camadas entre local e o Azure não é suportado. Como resultado, um sistema SAP é implantado no local ou é implantado no Azure. Você pode implantar os diferentes sistemas de um cenário SAP no Azure ou no local. Por exemplo, você pode implantar o desenvolvimento SAP CRM e sistemas de teste no Azure mas implantar o SAP CRM produção sistema local.
* **Entre instalações**: Descreve um cenário em que as VMs são implantadas em uma assinatura do Azure com o site a site, multissite ou centros de conectividade de Azure ExpressRoute entre os dados locais e o Azure. Na documentação comum do Azure, esses tipos de implantações também são descritas como cenários entre instalações. 

    O motivo para a conexão é estender domínios locais, Active Directory local e DNS local para o Azure. A estrutura local é estendida para os ativos do Azure da assinatura. Com essa extensão, as VMs podem fazer parte do domínio local. Os usuários de domínio do domínio local podem acessar os servidores e executar serviços nessas VMs, como serviços DBMS. A comunicação e resolução de nomes entre VMs implantadas de forma local e VMs implantadas no Azure são possíveis. Esse cenário é o cenário mais comum em uso para implantar os ativos SAP no Azure. Para obter mais informações, consulte [Planejamento e design para gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

> [!NOTE]
> Implantações entre instalações de sistemas SAP são em máquinas virtuais do Azure que executam sistemas SAP são membros de um domínio local e têm suporte para sistemas SAP de produção. Configurações entre locais têm suporte para a implantação de partes ou estruturas da SAP completas no Azure. Até mesmo a execução da estrutura da SAP completa no Azure requer essas VMs para ser parte de um domínio local e/LDAP do Active Directory. 
>
> Nas versões anteriores da documentação, cenários de TI híbrida foram mencionados. O termo *híbrida* está enraizado no fato de que há uma conectividade entre locais entre locais e o Azure. Nesse caso, híbrida também significa que as VMs no Azure são parte do Active Directory local.
>
>

Algumas documentações da Microsoft descreve cenários entre instalações de modo um pouco diferente, especialmente para configurações de alta disponibilidade DBMS. No caso de documentos relacionados ao SAP, o cenário entre locais se resume ao site a site ou privada [ExpressRoute](https://azure.microsoft.com/services/expressroute/) conectividade e um cenário do SAP é distribuído entre os locais e do Azure.

## <a name="resources"></a>Recursos
Há outros artigos disponíveis na carga de trabalho do SAP no Azure. Comece com [carga de trabalho do SAP no Azure: Introdução ao](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) e, em seguida, escolha sua área de interesse.

As seguintes notas SAP estão relacionadas ao SAP no Azure em relação à área de abordados neste documento.

| Número da observação | Title |
| --- | --- |
| [1928533] |Aplicativos SAP no Azure: produtos com suporte e tipos de VM do Azure |
| [2015553] |SAP no Microsoft Azure: pré-requisitos de suporte |
| [1999351] |Solução de problemas de monitoramento aprimorado do Azure para SAP |
| [2178632] |Métricas-chave de monitoramento para SAP no Microsoft Azure |
| [1409604] |Virtualização no Windows: monitoramento aprimorado |
| [2191498] |SAP no Linux com o Azure: monitoramento aprimorado |
| [2039619] |Os aplicativos SAP no Microsoft Azure que usam o Oracle Database: produtos e versões com suporte |
| [2233094] |DB6: Aplicativos SAP no Azure usando o IBM DB2 para Linux, UNIX e Windows: Informações adicionais |
| [2243692] |Linux na VM (IaaS) do Microsoft Azure: Problemas de licença do SAP |
| [1984787] |SUSE Linux Enterprise Server 12: Notas de instalação |
| [2002167] |Red Hat Enterprise Linux 7.x: Instalação e atualização |
| [2069760] |Atualização e instalação do SAP do Oracle Linux 7.x |
| [1597355] |Recomendação de troca de espaço para Linux |
| [2171857] |Oracle Database 12c: Suporte de sistema de arquivos em Linux |
| [1114181] |Oracle Database 11g: Suporte de sistema de arquivos em Linux |


Para obter informações sobre todas as anotações do SAP para Linux, consulte o [wiki da comunidade SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes).

É necessário um conhecimento prático de arquitetura do Microsoft Azure e como as máquinas virtuais do Microsoft Azure são implantadas e operadas. Para obter mais informações, consulte [documentação do Azure](https://docs.microsoft.com/azure/).

Em geral, a configuração e instalação do Windows, Linux e DBMS são essencialmente o mesmo que qualquer máquina virtual ou máquina bare-metal instalado localmente. Há algumas decisões de implementação de gerenciamento arquitetura e sistema que são diferentes quando você usa IaaS do Azure. Este documento explica as diferenças de gerenciamento do sistema para estar preparado para quando você usa IaaS do Azure e de arquitetura específicas.


## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Estrutura de armazenamento de uma VM para implantações de RDBMS
Para seguir este capítulo, ler e entender as informações apresentadas [este capítulo] [ deployment-guide-3] dos [guia de implantação][deployment-guide]. Você precisa compreender e saber sobre as diferentes séries de VMs e as diferenças entre o armazenamento standard e premium antes de ler este capítulo. 

Para saber mais sobre o armazenamento do Azure para VMs do Azure, consulte:

- [Introdução aos discos gerenciados para VMs do Windows Azure](../../windows/managed-disks-overview.md).
- [Introdução aos discos gerenciados para VMs Linux do Azure](../../linux/managed-disks-overview.md).

Em uma configuração básica, geralmente, é recomendável uma estrutura de implantação em que o sistema operacional, DBMS e eventuais binários SAP são separados dos arquivos de banco de dados. É recomendável que os sistemas SAP executados em máquinas virtuais do Azure tem o VHD de base, ou disco, instalado com o sistema operacional, executáveis do sistema de gerenciamento de banco de dados e arquivos executáveis do SAP. 

Os arquivos de dados e de log do DBMS são armazenados no armazenamento standard ou premium no armazenamento. Eles são armazenados em discos separados e anexados como discos lógicos à VM da imagem do sistema operacional do Azure original. Para implantações do Linux, recomendações diferentes são documentadas, especialmente para SAP HANA.

Ao planejar seu layout de disco, encontre o melhor equilíbrio entre estes itens:

* O número de arquivos de dados.
* O número de discos que contêm os arquivos.
* As cotas IOPS de um único disco.
* A taxa de transferência de dados por disco.
* O número de discos de dados adicionais possíveis por tamanho da VM.
* A taxa de transferência geral do armazenamento que uma VM pode fornecer.
* A latência que diferentes tipos de Armazenamento do Azure podem fornecer.
* SLAs VM.

O Azure impõe uma cota de IOPS por disco de dados. Essas cotas são diferentes para discos hospedados no armazenamento standard e premium. Latência de e/s também é diferente entre os dois tipos de armazenamento. O armazenamento Premium fornece melhor latência de e/s. 

Cada um dos diferentes tipos de VM tem um número limitado de discos de dados que você pode anexar. Outra restrição é que somente determinados tipos VM podem usar o armazenamento premium. Normalmente, você decidir usar um determinado tipo VM com base nos requisitos de CPU e memória. Você também pode considerar os requisitos de taxa de transferência de disco que normalmente são dimensionados com o número de discos ou o tipo de discos de armazenamento premium, latência e IOPS. O número de IOPS e a taxa de transferência ser atingido por cada disco pode determinam o tamanho do disco, especialmente com o armazenamento premium.

> [!NOTE]
> Para implantações de DBMS, recomendamos o uso do armazenamento premium para todos os dados, log de transações, ou arquivos de restauração. Não importa se você deseja implantar os sistemas de produção ou de não produção.

> [!NOTE]
> Para se beneficiar do Azure exclusivo do [SLA de única VM](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/), todos os discos anexados devem ser o tipo de armazenamento premium, que inclui o VHD de base.

> [!NOTE]
> Que hospedam arquivos de banco de dados principal, como arquivos de log e de dados, de bancos de dados do SAP no hardware de armazenamento que está localizado em centros de dados de terceiros colocalizadas adjacentes aos data centers do Azure não tem suporte. Para cargas de trabalho do SAP, somente o armazenamento que é representado como um serviço nativo do Azure tem suporte para os arquivos de log de transações e dados de bancos de dados do SAP.

O posicionamento dos arquivos de banco de dados e os arquivos de log e refazer e o tipo de armazenamento do Azure usado é definido por requisitos de taxa de transferência, latência e IOPS. Para ter IOPS suficientes, você poderá ser forçado a usar vários discos ou usar um disco de armazenamento premium maior. Se você usar vários discos, crie um distribuição de software entre os discos que contêm os arquivos de dados ou o log e arquivos de restauração. Em tais casos, o IOPS e a taxa de transferência do disco SLAs do armazenamento premium subjacente o IOPS máximo que pode ser obtido de discos de armazenamento standard ou discos são cumulativos para o conjunto resultante de distribuição.

Como já foi mencionado, se o requisito do seu IOPS exceder o que pode fornecer um único VHD, equilibre o número de IOPS que são necessários para os arquivos de banco de dados em um número de VHDs. A maneira mais fácil de distribuir a carga de IOPS em discos é criar um distribuição de software nos diferentes discos. Em seguida, colocar um número de arquivos de dados do SAP DBMS no LUNs retirado da distribuição de software. O número de discos na distribuição é orientado pelo demandas, demandas de taxa de transferência de disco e volume de IOPs demandas.


---
> ![Windows][Logo_Windows] Windows
>
> É recomendável que você usa espaços de armazenamento do Windows para criar conjuntos de distribuição entre vários VHDs do Azure. Use pelo menos Windows Server 2012 R2 ou Windows Server 2016.
>
> ![Linux][Logo_Linux] Linux
>
> Apenas para MDADM e Gerenciador de Volume lógico (LVM) têm suporte para criar um software RAID no Linux. Para obter mais informações, consulte:
>
> - [Configurar o software RAID no Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid) usando MDADM
> - [Configurar o LVM em uma VM Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm) usando LVM
>
>

---

> [!NOTE]
> Como o armazenamento do Azure mantém três imagens dos VHDs, ele não faz sentido para configurar uma redundância, quando você distribuir. Você só precisará configurar a distribuição para que as e/SS são distribuídas nos diferentes VHDs.
>

### <a name="managed-or-nonmanaged-disks"></a>Discos não gerenciados ou não gerenciados
Uma conta de armazenamento do Azure é um constructo administrativo e também uma entidade de limitações. Limitações diferem entre contas de armazenamento padrão e contas de armazenamento premium. Para obter informações sobre recursos e limitações, consulte [metas de desempenho e escalabilidade do armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

Para o armazenamento standard, lembre-se de que há um limite no IOPS por conta de armazenamento. Consulte a linha que contém **taxa de solicitação Total** no artigo [metas de desempenho e escalabilidade do armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets). Também há um limite inicial no número de contas de armazenamento por assinatura do Azure. VHDs de saldo para a estrutura SAP maior entre diferentes contas de armazenamento para evitar atingir os limites dessas contas de armazenamento. Isso é um trabalho entediante quando se trata de algumas centenas de computadores virtuais com mais de mil VHDs.

Porque não é recomendado usar o armazenamento standard para implantações de DBMS em conjunto com uma carga de trabalho do SAP, referências e recomendações para o armazenamento standard estão limitadas a curtos [artigo](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

Para evitar o trabalho administrativo de planejamento e implantação de VHDs em contas de armazenamento do Azure diferentes, a Microsoft introduziu [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) em 2017. Discos gerenciados estão disponíveis para o armazenamento standard e premium. As principais vantagens de discos gerenciados em comparação comparadas discos não gerenciados são:

- Para discos gerenciados, o Azure distribui diferentes VHDs entre diferentes contas de armazenamento automaticamente no momento da implantação. Dessa forma, limites de conta de armazenamento para o volume de dados, taxa de transferência de e/s e IOPS não são de ocorrências.
- Usando discos gerenciados, o armazenamento do Azure respeita os conceitos de conjuntos de disponibilidade do Azure. Se a VM for parte de um conjunto de disponibilidade do Azure, o VHD de base e o disco anexado de uma VM são implantados em diferentes domínios de falha e atualização.


> [!IMPORTANT]
> Considerando as vantagens do Azure Managed Disks, é recomendável que você use o Azure Managed Disks para suas implantações de DBMS e implantações do SAP em geral.
>

Para converter de não gerenciados em managed disks, consulte:

- [Converter uma máquina virtual do Windows de discos não gerenciados em discos gerenciados](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks).
- [Converter uma máquina virtual Linux de discos não gerenciados em discos gerenciados](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks).


### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Cache de VMs e discos de dados
Quando você monta discos para VMs, você pode escolher se o tráfego de e/s entre a VM e os discos no armazenamento do Azure é armazenada em cache. Armazenamento Standard e premium usam duas tecnologias diferentes para esse tipo de cache.

As recomendações a seguir pressupõem a essas características de e/s para o DBMS padrão:

- É principalmente uma carga de trabalho leitura em relação a arquivos de dados de um banco de dados. Essas leituras são desempenho crítico para o sistema DBMS.
- Gravação em relação aos arquivos de dados ocorre em picos com base em pontos de verificação ou um fluxo constante. Uma média ao longo de um dia, há menos gravações que as leituras. Ao contrário do n leituras de arquivos de dados, essas gravações são assíncronas e não mantenha backup de todas as transações de usuário.
- Praticamente, não há nenhuma leitura dos arquivos de refazer ou log de transação. As exceções são grandes e/SS quando você executa backups de log de transações.
- A principal carga em relação a arquivos de log de transação ou refazer é gravações. Dependendo da natureza da carga de trabalho, você pode ter e/SS tão pequenas de 4 KB ou, em outros casos, os tamanhos de e/s de 1 MB ou mais.
- Todas as gravações devem ser persistidas no disco de forma confiável.

Para o armazenamento standard, os tipos de cache possíveis são:

* Nenhum
* Ler
* Leitura/Gravação

Para obter um desempenho consistente e determinístico, defina o armazenamento em cache no armazenamento padrão para todos os discos que contêm arquivos de dados relacionados ao DBMS, faça logon e restauração de arquivos e o espaço de tabela para **NONE**. O caching de base VHD pode permanecer com o padrão.

Para o armazenamento premium, existem as seguintes opções de cache:

* Nenhum
* Ler
* Leitura/gravação
* Nenhum + acelerador de gravação, que é somente para VMs da série M do Azure
* Leitura + acelerador de gravação, que é somente para VMs da série M do Azure

Para o armazenamento premium, recomendamos que você use **cache para arquivos de dados de leitura** do SAP de banco de dados e escolha **sem cache para os discos de arquivos de log**.

Para implantações da série M, é recomendável que você use o Acelerador de gravação do Azure para sua implantação do DBMS. Para obter detalhes, restrições e implantação do acelerador de gravação do Azure, consulte [habilitar o Acelerador de gravação](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


### <a name="azure-nonpersistent-disks"></a>Discos do Azure não persistentes
As VMs do Azure oferecem discos persistentes depois que uma VM é implantada. No caso de uma reinicialização da VM, todo o conteúdo nessas unidades forem apagado. É um dado que arquivos de dados e arquivos de log e a restauração de bancos de dados sob nenhuma circunstância encontra-se nessas unidades não persistentes. Pode haver exceções para alguns bancos de dados, onde essas unidades persistentes poderiam ser adequadas para espaços de tabela temporários e tempdb. Evite usar essas unidades para VMs da série, pois essas unidades persistentes são limitadas na taxa de transferência dessa família VM. 

Para obter mais informações, consulte [entender a unidade temporária em VMs do Windows no Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/).

---
> ![Windows][Logo_Windows] Windows
>
> A unidade D em uma VM do Azure é uma unidade não persistente, que é apoiada por alguns discos locais no nó de computação do Azure. Porque ele é não persistente, todas as alterações feitas ao conteúdo na unidade D são perdidas quando a VM é reinicializada. As alterações incluem os arquivos que foram armazenados, diretórios que foram criados e aplicativos que foram instalados.
>
> ![Linux][Logo_Linux] Linux
>
> VMs do Azure do Linux montam automaticamente uma unidade em /mnt/resource, que é uma unidade não persistente apoiada por discos locais no nó de computação do Azure. Porque ele é não persistente, todas as alterações feitas ao conteúdo de recurso/dev/emcpowera1 são perdidas quando a VM é reinicializada. As alterações incluem os arquivos que foram armazenados, diretórios que foram criados e aplicativos que foram instalados.
>
>

---



### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Resiliência do Armazenamento do Microsoft Azure
Armazenamento do Microsoft Azure armazena o VHD de base, com o sistema operacional e discos anexados ou blobs, em pelo menos três nós de armazenamento separada. Esse tipo de armazenamento é chamado de armazenamento localmente redundante (LRS). O LRS é o padrão para todos os tipos de armazenamento no Azure.

Há outros métodos de redundância. Para obter mais informações, consulte [Replicação do Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>O armazenamento Premium é o tipo recomendado de armazenamento para VMs de DBMS e os discos que armazenam o banco de dados e log e arquivos de restauração. O método de redundância disponível apenas para o armazenamento premium é LRS. Como resultado, você precisa configurar métodos de banco de dados para habilitar a replicação de dados do banco de dados em outra zona de disponibilidade ou a região do Azure. Métodos de banco de dados incluem SQL Server Always On, o Oracle Data Guard e replicação de sistema do HANA.


> [!NOTE]
> Para implantações de DBMS, o uso de armazenamento com redundância geográfica (GRS) não é recomendado para o armazenamento standard. GRS gravemente afeta o desempenho e não respeitam a ordem de gravação entre diferentes VHDs anexados a uma VM. Não respeitar a ordem de gravação entre diferentes VHDs potencialmente leva a bancos de dados inconsistentes no lado do destino de replicação. Essa situação ocorre se os arquivos de banco de dados e log e refazer são distribuídos entre vários VHDs, como é geralmente o caso, no lado da VM de software.



## <a name="vm-node-resiliency"></a>Resiliência de nó de VM
O Azure oferece vários SLAs diferentes para as VMs. Para obter mais informações, consulte a versão mais recente do [SLA para máquinas virtuais](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). Como a camada de DBMS é geralmente importante para disponibilidade em um sistema SAP, você precisa entender os conjuntos de disponibilidade, zonas de disponibilidade e eventos de manutenção. Para obter mais informações sobre esses conceitos, consulte [gerenciar a disponibilidade das máquinas de virtuais do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) e [gerenciar a disponibilidade das máquinas virtuais Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).

A recomendação mínima para cenários de DBMS de produção com uma carga de trabalho do SAP é:

- Implante duas VMs em uma conjunto na mesma região do Azure de disponibilidade separada.
- Execute essas duas VMs na mesma rede virtual do Azure e ter NICs anexadas fora às mesmas sub-redes.
- Use os métodos de banco de dados para manter um modo de espera ativo com a segunda VM. Métodos podem ser SQL Server Always On, o Oracle Data Guard ou HANA System Replication.

Você também pode implantar uma terceira VM em outra região do Azure e use os mesmos métodos de banco de dados para fornecer uma réplica assíncrona em outra região do Azure.

Para obter informações sobre como configurar conjuntos de disponibilidade do Azure, consulte [este tutorial](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).



## <a name="azure-network-considerations"></a>Considerações de rede do Azure
Em implantações do SAP em larga escala, use o plano gráfico de [Datacenter Virtual do Azure](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter). Usá-la para suas atribuições de configuração e as permissões e a função de rede virtual para diferentes partes da sua organização.

Essas práticas recomendadas são o resultado de centenas de implantações de clientes:

- As redes virtuais em que do aplicativo SAP é implantado não tem acesso à internet.
- Execute o banco de dados de VMs na mesma rede virtual como a camada de aplicativo.
- As VMs dentro da rede virtual têm uma alocação estática do endereço IP privado. Para obter mais informações, consulte [endereço IP, tipos e métodos de alocação no Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).
- Restrições de roteamento de e para as VMs de DBMS são *não* definido com firewalls instalados nas VMs DBMS locais. Em vez disso, o roteamento de tráfego é definido com [(NSGs) de grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/security-overview).
- Para separar e isolar o tráfego para a VM de DBMS, atribua diferentes NICs à VM. Cada NIC obtém um endereço IP diferente e cada NIC é atribuída a uma sub-rede de rede virtual diferente. Cada sub-rede tem diferentes regras NSG. O isolamento ou a separação do tráfego de rede é uma medida para roteamento. Ele não é usado para definir cotas de taxa de transferência de rede.

> [!NOTE]
> Atribuição de endereços IP estáticos por meio do Azure significa atribuí-los a NICs virtuais individuais. Não atribuir endereços IP estáticos dentro do sistema operacional convidado a uma NIC virtual. Alguns serviços do Azure, como Backup do Azure dependem do fato de que pelo menos a NIC virtual primária é definida como o DHCP e não para endereços IP estáticos. Para obter mais informações, consulte [backup da máquina virtual do Azure de solucionar problemas de](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Para atribuir vários endereços IP estáticos a uma VM, atribua várias NICs virtuais a uma VM.
>


> [!IMPORTANT]
> Configurando [soluções de virtualização de rede](https://azure.microsoft.com/solutions/network-appliances/) no caminho de comunicação entre o aplicativo de SAP e a camada de DBMS do NetWeaver um SAP - Hybris- ou sistema baseado em S/4HANA SAP não tem suporte. Essa restrição é por motivos de desempenho e funcionalidade. O caminho de comunicação entre a camada de aplicativo do SAP e a camada de DBMS deve ser um direto. A restrição não inclui [grupo de segurança de aplicativo (ASG) e as regras NSG](https://docs.microsoft.com/azure/virtual-network/security-overview) se essas regras ASG e NSG permitem que um caminho de comunicação direta. 
>
> Outros cenários em que não têm suporte a dispositivos de rede virtual estão em:
>
> * Caminhos de comunicação entre VMs do Azure que representam o Linux Pacemaker cluster nós e os dispositivos SBD, conforme descrito em [alta disponibilidade do SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server for SAP Applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse).
> * Conjunto de caminhos de comunicação entre VMs do Azure e o Windows Server-horizontal arquivo SOFS (servidor) até conforme descrito em [agrupar instância SAP ASCS/SCS em um cluster de failover do Windows usando um compartilhamento de arquivos do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). 
>
> Soluções de virtualização de rede nos caminhos de comunicação podem facilmente double a latência de rede entre os parceiros de comunicação de dois. Eles também podem restringir a taxa de transferência em caminhos críticos entre a camada de aplicativo do SAP e a camada de DBMS. Em alguns cenários de cliente, dispositivos de rede virtual podem causar clusters Linux Pacemaker falhe. Esses são casos em que as comunicações entre os nós de cluster do Linux Pacemaker se comunicar com o dispositivo SBD por meio de um dispositivo de rede virtual.
>

> [!IMPORTANT]
> Outro que de design do *não* com suporte é a segregação de camada do aplicativo SAP e a camada de DBMS em diferentes redes virtuais do Azure que não estão [emparelhadas](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) uns com os outros. É recomendável separar a camada de DBMS e camada do aplicativo SAP usando sub-redes em uma rede virtual do Azure, em vez de por meio de diferentes redes virtuais do Azure. 
>
> Se você decidir não a seguir a recomendação e em vez disso, separar as duas camadas em diferentes redes virtuais, as duas redes virtuais devem ser [emparelhadas](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). 
>
> Lembre-se que tráfego de rede entre dois [emparelhadas](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) redes virtuais do Azure está sujeito a custos de transferência. Volume de dados grande que consiste em muitos terabytes é trocado entre a camada de aplicativo do SAP e a camada de DBMS. Se a camada de DBMS e camada do aplicativo SAP são separadas entre duas redes emparelhadas de virtuais do Azure, você pode acumular custos substanciais.

Defina duas VMs de uso para sua implantação de DBMS em um disponibilidade do Azure em produção. Também use roteamento separado para a camada de aplicativo do SAP e o tráfego de gerenciamento e operações para as duas VMs de DBMS. Veja a seguinte imagem:

![Diagrama de duas VMs em duas sub-redes](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Usar o balanceador de carga do Azure para redirecionar o tráfego
O uso de endereços IP privados virtuais usado em funcionalidades, como o SQL Server Always On ou replicação de sistema HANA requer a configuração de um balanceador de carga do Azure. O balanceador de carga usa portas de investigação para determinar o nó ativo do DBMS e rotear o tráfego exclusivamente para esse nó do banco de dados ativo. 

Se houver um failover de nó de banco de dados, não é necessário para o aplicativo SAP reconfigurar. Em vez disso, as arquiteturas de aplicativo SAP mais comuns se reconectar com o endereço IP virtual. Enquanto isso, o balanceador de carga reage ao failover de nó ao redirecionar o tráfego contra o endereço IP virtual para o segundo nó.

O Azure oferece duas diferentes [SKUs do balanceador de carga](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview): um SKU básico e um SKU standard. A menos que você deseja implantar em zonas de disponibilidade do Azure, o SKU do balanceador de carga básico faz bem.

É o tráfego entre as VMs de DBMS e camada do aplicativo SAP sempre roteadas por meio do balanceador de carga o tempo todo? A resposta depende de como você configura o balanceador de carga. 

Neste momento, o tráfego de entrada para a VM de DBMS sempre é roteado por meio do balanceador de carga. A saída tráfego rota da VM de DBMS que VM depende da configuração do balanceador de carga de camada de aplicativo. 

O balanceador de carga oferece uma opção de DirectServerReturn. Se essa opção estiver configurada, o tráfego direcionado de VM de DBMS para a camada de aplicativo SAP é *não* roteadas por meio do balanceador de carga. Em vez disso, ele vai diretamente para a camada de aplicativo. Se DirectServerReturn não estiver configurado, o tráfego de retorno para a camada de aplicativo SAP é roteado por meio do balanceador de carga.

É recomendável que você configure DirectServerReturn em combinação com os balanceadores de carga são posicionados entre a camada de aplicativo SAP e a camada de DBMS. Essa configuração reduz a latência de rede entre as duas camadas.

Para obter um exemplo de como definir essa configuração com o SQL Server Always On, consulte [configurar um ouvinte de ILB para grupos de disponibilidade Always On no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener).

Se você usar modelos de JSON do GitHub publicados como uma referência para suas implantações de infraestrutura do SAP no Azure, estude este [modelo para um sistema de 3 camadas do SAP](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md). Neste modelo, você também pode ver as configurações corretas para o balanceador de carga.

### <a name="azure-accelerated-networking"></a>Rede Acelerada do Azure
Para reduzir ainda mais a latência de rede entre VMs do Azure, recomendamos que você escolha [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Usá-lo quando você implanta as VMs do Azure para uma carga de trabalho do SAP, especialmente para a camada de aplicativo SAP e a camada de DBMS SAP.

> [!NOTE]
> Nem todos os tipos VM dão suporte a rede acelerada. O artigo anterior lista os tipos VM que dão suporte a rede acelerada.
>

---
> ![Windows][Logo_Windows] Windows
>
> Para saber como implantar VMs com Accelerated Networking para Windows, consulte [criar uma máquina virtual do Windows com rede acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).
>
> ![Linux][Logo_Linux] Linux
>
> Para obter mais informações sobre a distribuição do Linux, consulte [criar uma máquina virtual Linux com rede acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).
>
>

---

> [!NOTE]
> No caso do SUSE, Red Hat e Oracle Linux, o serviço de Rede Acelerada de mensagens é compatível com versões recentes. Versões mais antigas, como o SLES 12 SP2 ou RHEL 7.2 não dão suporte a rede acelerada do Azure.
>


## <a name="deployment-of-host-monitoring"></a>Implantação do monitoramento de host
Para uso em produção de aplicativos SAP em máquinas virtuais do Azure, o SAP requer a capacidade de obter dados de monitoramento de hosts físicos que executam máquinas virtuais do Azure do host. É necessário um nível de patch do Agente de Host SAP específico que habilite essa funcionalidade no SAPOSCOL e no Agente de Host SAP. O nível de patch exato está documentado na Nota SAP [1409604].

Para obter mais informações sobre a implantação de componentes que fornecem dados de host ao SAPOSCOL e SAP Host Agent e o gerenciamento de ciclo de vida desses componentes, consulte o [Deployment guide][deployment-guide].


## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre um DBMS específico, consulte:

- [Implantação do DBMS de Máquinas de Virtuais do SQL Server Azure para carga de trabalho do SAP](dbms_guide_sqlserver.md)
- [Implantação do DBMS de Máquinas Virtuais do Oracle Azure para carga de trabalho do SAP](dbms_guide_oracle.md)
- [Implantação do DBMS de Máquinas Virtuais do IBM DB2 Azure para carga de trabalho do SAP](dbms_guide_ibm.md)
- [Implantação do DBMS de Máquinas Virtuais do SAP ASE Azure para carga de trabalho do SAP](dbms_guide_sapase.md)
- [Implantação do SAP maxDB, Live Cache e Content Server no Azure](dbms_guide_maxdb.md)
- [SAP HANA no guia de operações do Azure](hana-vm-operations.md)
- [Alta disponibilidade do SAP HANA para máquinas virtuais do Azure](sap-hana-availability-overview.md)
- [Guia de backup do SAP HANA em máquinas virtuais do Azure](sap-hana-backup-guide.md)

