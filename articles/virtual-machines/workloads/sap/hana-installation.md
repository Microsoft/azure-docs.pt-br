---
title: Instalar SAP HANA em SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Como instalar SAP HANA em um SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/16/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 81d44dae0fed45d4a4df76973c7e233fd71baff1
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98198961"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Como instalar e configurar o SAP HANA (Instâncias Grandes) no Azure

Antes de ler este artigo, familiarize-se com os [termos comuns do HANA em Instâncias Grandes](hana-know-terms.md) e os [SKUs do HANA em Instâncias Grandes](hana-available-skus.md).

A instalação do SAP HANA é sua responsabilidade. Você pode começar a instalar um novo SAP HANA no servidor do Azure (Instâncias Grandes) depois de estabelecer a conectividade entre suas redes virtuais do Azure e as unidades de HANA em Instâncias Grandes. 

> [!Note]
> Por política do SAP, a instalação do SAP HANA deve ser executada por uma pessoa que passou pelo exame de associação de tecnologia SAP certificado, pelo exame de certificação de instalação SAP HANA ou por quem é um integrador de sistemas certificado pelo SAP (SI).

Ao planejar a instalação do HANA 2.0, consulte a [Nota de suporte SAP n° 2235581 – SAP HANA: sistemas operacionais com suporte](https://launchpad.support.sap.com/#/notes/2235581/E) para verificar se o sistema operacional é compatível com a versão do SAP HANA que está sendo instalada. O sistema operacional com suporte para o HANA 2.0 é mais restrito do que o sistema operacional com suporte para o HANA 1.0. Você também precisa verificar se a versão do sistema operacional em que está interessado está listada como com suporte para a unidade HLI específica nesta [lista](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)publicada. Clique na unidade para obter os detalhes completos com a lista de sistemas operacionais com suporte dessa unidade. 

Valide o seguinte antes de começar a instalação do HANA:
- [Unidades de HLI](#validate-the-hana-large-instance-units)
- [Configuração do sistema operacional](#operating-system)
- [Configuração de rede](#networking)
- [Configuração de armazenamento](#storage)


## <a name="validate-the-hana-large-instance-units"></a>Validar a(s) unidade(s) do HANA em Instância Grande

Depois de receber a unidade de Instância Grande do HANA da Microsoft, valide as configurações a seguir e ajuste conforme necessário.

A **primeira etapa** depois de receber a instância grande do Hana e estabelecer o acesso e a conectividade com as instâncias é fazer check-in portal do Azure se as instâncias estão aparecendo com as SKUs e o sistema operacional corretos. Leia [controle de instâncias grandes do Azure Hana por meio de portal do Azure](./hana-li-portal.md) para as etapas necessárias para executar as verificações.

A **segunda etapa** depois que você recebe a instância grande do Hana e estabelece acesso e conectividade com as instâncias é registrar o sistema operacional da instância com seu provedor de sistema operacional. Essa etapa inclui o registro do sistema operacional SUSE Linux em uma instância do SUSE SMT que é implantada em uma VM do Azure. 

A unidade de Instância Grande do HANA pode se conectar a essa instância SMT. (Para obter mais informações, consulte [Como configurar o servidor SMT para o SUSE Linux](hana-setup-smt.md)). Como alternativa, seu sistema operacional Red Hat precisa ser registrado com o Gerenciador de Assinaturas Red Hat ao qual você precisa se conectar. Para obter mais informações, consulte os comentários em [O que é o SAP HANA no Azure (Instâncias Grandes)?](./hana-overview-architecture.md?toc=/azure/virtual-machines/linux/toc.json). 

Essa etapa é necessária para aplicar patches no sistema operacional, que é responsabilidade do cliente. Para o SUSE, encontre a documentação para instalar e configurar o SMT nesta página sobre a [instalação do SMT](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

A **terceira etapa** é verificar se há novos patches e correções do lançamento/versão do sistema operacional específico. Verifique se o nível de patch da Instância Grande do HANA está no estado mais recente. Pode haver casos em que os patches mais recentes não estejam incluídos. Depois de assumir uma unidade de Instância Grande do HANA, é obrigatório verificar se é necessário aplicar patches.

A **quarta etapa** é conferir as notas SAP relevantes para instalar e configurar SAP Hana na versão/liberação específica do sistema operacional. Devido à alteração de recomendações ou às alterações das notas SAP ou de configurações que dependem de cenários de instalação individuais, a Microsoft nem sempre poderá configurar uma unidade de Instância Grande do HANA com perfeição. 

Portanto, é obrigatório que você, como cliente, leia as notas SAP relacionadas ao SAP HANA sobre a versão exata do Linux. Também verifique e aplique as configurações necessárias da versão do sistema operacional caso ainda não o tenha feito.

Verifique especificamente os seguintes parâmetros e ajuste-os para:

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

A partir do SLES12 SP1 e RHEL 7.2, esses parâmetros devem ser definidos em um arquivo de configuração no diretório /etc/sysctl.d. Por exemplo, deve-se criar um arquivo de configuração com o nome 91-NetApp-HANA.conf. Para versões mais antigas do SLES e RHEL, esses parâmetros devem ser definidos como in/etc/sysctl.conf.

Para todas as versões de RHEL a partir do RHEL 6,3, tenha em mente: 
- O parâmetro sunrpc.tcp_slot_table_entries = 128 deve ser definido como in/etc/modprobe.d/sunrpc-local.conf. Se o arquivo não existir, você precisará criá-lo primeiro adicionando a entrada: 
    - options sunrpc tcp_max_slot_table_entries=128

A **quinta etapa** é verificar a hora do sistema de sua unidade de instância grande do Hana. As instâncias são implantadas com um fuso horário do sistema. Esse fuso horário representa o local da região do Azure em que o carimbo de Instância Grande do HANA está localizado. Você pode alterar a hora do sistema ou o fuso horário das instâncias que possui. 

Se você solicitar mais instâncias para seu locatário, precisará adaptar o fuso horário das instâncias recém-entregues. A Microsoft não tem nenhum insight sobre o fuso horário do sistema configurado com as instâncias após a entrega. Portanto, as instâncias recém-implantadas podem não estar definidas para o mesmo fuso horário daquelas que você alterou. É sua responsabilidade como cliente adaptar o fuso horário das instâncias entregues, se necessário. 

A **sexta etapa** é verificar etc/hosts. Conforme as folhas são entregues, elas têm diferentes endereços IP atribuídos para finalidades diferentes. Verifique o arquivo /etc/hosts. Quando as unidades são adicionadas a um locatário existente, não espere que etc/hosts dos sistemas recém-implantados sejam mantidos corretamente com os endereços IP dos sistemas que foram entregues anteriormente. É sua responsabilidade como cliente confirmar que uma instância recém-implantada possa interagir e resolver os nomes das unidades implantadas anteriormente no locatário. 


## <a name="operating-system"></a>Sistema operacional

O espaço de troca da imagem do sistema operacional fornecida é definido como 2 GB, de acordo com a [Nota de suporte SAP n° 1999997 – Perguntas frequentes: memória do SAP HANA](https://launchpad.support.sap.com/#/notes/1999997/E). Como cliente, se você desejar uma configuração diferente, deverá defini-la por conta própria.

[SUSE Linux Enterprise Server 12 SP1 para aplicativos SAP](https://www.suse.com/products/sles-for-sap/download/) é a distribuição do Linux instalada para SAP HANA no Azure (Instâncias Grandes). Essa distribuição particular fornece recursos específicos do SAP “prontos para uso” (incluindo parâmetros predefinidos para executar o SAP em SLES de modo eficaz).

Consulte [Biblioteca de recursos/white papers](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) no site do SUSE e [SAP no SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) na SCN (Rede da Comunidade SAP) para obter vários recursos úteis relacionados à implantação do SAP HANA em SLES (incluindo configuração de alta disponibilidade, proteção de segurança específica para operações do SAP e muito mais).

A seguir, há links adicionais e úteis relacionados ao SAP no SUSE:

- [SAP HANA no Site do SUSE Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Práticas recomendadas para SAP: replicação de enfileiramento – SAP NetWeaver no SUSE Linux Enterprise 12](https://www.suse.com/media/guide/SLES4SAP-NetWeaver-ha-guide-EnqRepl-12_color_en.pdf)
- [ClamSAP – proteção antivírus de SLES para SAP](https://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (incluindo SLES 12 para aplicativos SAP)

A seguir, há notas de suporte da SAP aplicáveis à implementação do SAP HANA no SLES 12:

- [Nota de suporte SAP n° 1944799 – diretrizes do SAP HANA para instalação do sistema operacional SLES](http://service.sap.com/sap/support/notes/1944799)
- [Nota de suporte SAP n° 2205917 – configurações de SO recomendadas para o banco de dados do SAP HANA para SLES 12 para aplicativos SAP](https://launchpad.support.sap.com/#/notes/2205917/E)
- [Nota de suporte SAP n° 1984787 – SUSE Linux Enterprise Server 12: notas de instalação](https://launchpad.support.sap.com/#/notes/1984787)
- [Nota de suporte SAP n° 171356 – software SAP no Linux: informações gerais](https://launchpad.support.sap.com/#/notes/1984787)
- [Nota de suporte da SAP #1391070 – soluções de UUID do Linux](https://launchpad.support.sap.com/#/notes/1391070)

[Red Hat Enterprise Linux para SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) é outra oferta para execução do SAP HANA em instâncias grandes de HANA. As versões do RHEL 7,2 e 7,3 estão disponíveis e têm suporte. 

A seguir, há links adicionais e úteis relacionados ao SAP no Red Hat:
- [SAP HANA no site Red Hat Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

A seguir, há notas de suporte SAP aplicáveis à implementação do SAP HANA no Red Hat:

- [Nota de suporte da SAP #2009879 diretrizes de SAP HANA para o sistema operacional Red Hat Enterprise Linux (RHEL)](https://launchpad.support.sap.com/#/notes/2009879/E)
- [Nota de suporte SAP n° 2292690 – Banco de dados SAP HANA: configurações de sistema operacional recomendadas para RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
- [Nota de suporte da SAP #1391070 – soluções de UUID do Linux](https://launchpad.support.sap.com/#/notes/1391070)
- [Nota de suporte SAP n° 2228351 – Linux: banco de dados SAP HANA SPS 11 revisão 110 (ou superior) em RHEL 6 ou SLES 11](https://launchpad.support.sap.com/#/notes/2228351)
- [Nota de suporte SAP n° 2397039 – Perguntas frequentes: SAP em RHEL](https://launchpad.support.sap.com/#/notes/2397039)
- [Nota de suporte SAP n° 2002167 – Red Hat Enterprise Linux 7.x: instalação e atualização](https://launchpad.support.sap.com/#/notes/2002167)

### <a name="time-synchronization"></a>Sincronização da hora

Os aplicativos SAP criados na arquitetura do SAP NetWeaver são sensíveis às diferenças de hora nos vários componentes que compõem o sistema SAP. Provavelmente os despejos de memória curtos do SAP ABAP com o título de erro ZDATE\_LARGE\_TIME\_DIFF são familiares. Isso ocorre porque esses despejos curtos aparecem quando a hora do sistema de servidores ou VMs diferentes fica muito distante.

Para o SAP HANA no Azure (Instâncias Grandes), a sincronização de tempo feita no Azure não se aplica a unidades de computação nos carimbos de data/hora de Instâncias Grandes. Essa sincronização não é aplicável à execução de aplicativos SAP em VMs nativas do Azure, pois o Azure garante que a hora do sistema é sincronizada corretamente. 

Como resultado, você deve configurar um servidor de tempo separado que possa ser usado pelos servidores de aplicativos SAP em execução em VMs do Azure e por instâncias de banco de dados SAP HANA em execução em Instâncias Grandes do HANA. A infraestrutura de armazenamento em carimbos de Instância Grande tem seu horário sincronizado com os servidores NTP.


## <a name="networking"></a>Rede
Supomos que você seguiu as recomendações para projetar as redes virtuais do Azure e conectá-las ao HANA em Instâncias Grandes, conforme descrito nestes documentos:

- [Visão geral e arquitetura do SAP HANA (instância grande) no Azure](./hana-overview-architecture.md)
- [Infraestrutura e conectividade do SAP HANA (instâncias grandes) no Azure](hana-overview-infrastructure-connectivity.md)

Há alguns detalhes que vale a pena mencionar sobre a rede das unidades individuais. Cada unidade de Instância Grande do HANA é fornecida com dois ou três endereços IP atribuídos a dois ou três portas NIC. Três endereços IP são usados em configurações de expansão do HANA e no cenário de replicação de sistema do HANA. Um dos endereços IP atribuídos à NIC da unidade está fora do pool de IPs do Servidor descrito na [Visão geral e arquitetura do SAP HANA (Instâncias Grandes) no Azure](./hana-overview-architecture.md).

Para obter mais informações sobre detalhes de Ethernet para a sua arquitetura, consulte [cenários com suporte para HLI (Instâncias Grandes do HANA)](hana-supported-scenario.md).

## <a name="storage"></a>Armazenamento

O layout de armazenamento para SAP HANA no Azure (instâncias grandes) é configurado por SAP HANA no Azure `service management` por meio das diretrizes recomendadas do SAP. Essas diretrizes estão documentadas no white paper [Requisitos de armazenamento do SAP HANA](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). 

Os tamanhos aproximados dos volumes diferentes com os diferentes SKUs do HANA em Instâncias Grandes estão documentados na [Visão geral e arquitetura do SAP HANA (Instâncias Grandes) no Azure](hana-overview-architecture.md).

As convenções de nomenclatura dos volumes de armazenamento são listadas na seguinte tabela:

| Uso de armazenamento | Nome da montagem | Nome do volume | 
| --- | --- | ---|
| Dados do HANA | /hana/data/SID/mnt0000\<m> | IP de Armazenamento: /hana_data_SID_mnt00001_tenant_vol |
| Log do HANA | /hana/log/SID/mnt0000\<m> | IP de Armazenamento: /hana_log_SID_mnt00001_tenant_vol |
| Backup de log do HANA | /hana/log/backups | IP de Armazenamento: /hana_log_backups_SID_mnt00001_tenant_vol |
| HANA compartilhado | /hana/shared/SID | IP de Armazenamento: /hana_shared_SID_mnt00001_tenant_vol/shared |
| usr/sap | /usr/sap/SID | IP de Armazenamento: /hana_shared_SID_mnt00001_tenant_vol/usr_sap |

*SID* é a ID do Sistema da instância do HANA. 

*Locatário* é uma enumeração interna das operações durante a implantação de um locatário.

Usr/sap do HANA compartilham o mesmo volume. A nomenclatura dos pontos de montagem inclui a ID do sistema das instâncias do HANA, bem como o número de montagem. Em implantações escaláveis, há somente uma montagem, como mnt00001. Em implantações escaláveis, por outro lado, você vê a mesma quantidade de montagens que os nós de trabalho e mestre. 

Para os ambientes escaláveis, os dados, o log e os volumes de backup de log são compartilhados e anexados a cada nó na configuração de expansão. Para configurações que sejam várias instâncias do SAP, um conjunto diferente de volumes é criado e anexado à unidade de Instância Grande do HANA. Para obter detalhes do layout de armazenamento do seu cenário, consulte os [cenários com suporte para HLI](hana-supported-scenario.md).

Ao examinar uma unidade de Instância Grande do HANA, você descobrirá que as unidades são fornecidas com um volume de disco bem generoso para HANA/data e que temos um HANA/log/backup. O motivo pelo qual fizemos o HANA/data tão grande é porque os instantâneos de armazenamento que oferecemos para você, como cliente, estão usando o mesmo volume de disco. Quanto mais instantâneos de armazenamento são tirados, mais espaço é consumido pelos instantâneos nos volumes de armazenamento atribuídos. 

O volume de HANA/log/backup não deve ser o volume dos backups de banco de dados. Ele é dimensionado para ser usado como volume de backup para os backups de log de transações do HANA. Para obter mais informações, consulte [Alta disponibilidade e recuperação de desastre do SAP HANA (Instâncias Grandes) do Azure](hana-overview-high-availability-disaster-recovery.md). 

Além do armazenamento fornecido, você pode adquirir uma capacidade de armazenamento adicional em incrementos de 1 TB. Esse armazenamento adicional pode ser incluído como novos volumes para o HANA em Instâncias Grandes.

Durante a integração com SAP HANA no Azure `service management` , o cliente especifica uma ID de usuário (UID) e ID de grupo (GID) para o usuário SIDADM e o grupo SAPs (por exemplo: 1.000.500). Durante a instalação do sistema SAP HANA, você deverá usar esses mesmos valores. Como você deseja implantar várias instâncias do HANA em uma unidade, você obterá vários conjuntos de volumes (um conjunto para cada instância). Como resultado, no momento da implantação, é necessário definir:

- O SID das diferentes instâncias do HANA (o sidadm é derivado dele).
- Os tamanhos de memória das diferentes instâncias do HANA. O tamanho de memória por instância define o tamanho dos volumes em cada conjunto de volumes individual.

De acordo com as recomendações do provedor de armazenamento, as seguintes opções de montagem são configuradas para todos os volumes montados (exclui o LUN de inicialização):

- nfs    rw, vers=4, hard, timeo=600, rsize=1048576, wsize=1048576, intr, noatime, lock 0 0

Esses pontos de montagem são configurados em /etc/fstab, conforme mostrado nos grafos a seguir:

![fstab de volumes montados na unidade de Instância Grande do HANA](./media/hana-installation/image1_fstab.PNG)

O resultado do comando df -h em uma unidade S72m de Instância Grande do HANA se parece com este:

![Captura de tela mostra a saída do comando para a unidade de instância grande do HANA.](./media/hana-installation/image2_df_output.PNG)


O controlador de armazenamento e os nós nos carimbos de Instância Grande são sincronizados com os servidores NTP. Ao sincronizar o SAP HANA em unidades (Instâncias Grandes) do Azure e VMs do Azure em relação a um servidor NTP, não deve haver uma diferença de tempo significativa entre a infraestrutura e as unidades de computação no Azure ou carimbos de Instância Grande.

Para otimizar o SAP HANA para o armazenamento usado de forma subjacente, defina os seguintes parâmetros de configuração do SAP HANA:

- max_parallel_io_requests 128
- async_read_submit on
- async_write_submit_active on
- async_write_submit_blocks all
 
Para as versões 1.0 do SAP HANA até SPS12, esses parâmetros podem ser definidos durante a instalação do banco de dados SAP HANA, conforme descrito em [Nota SAP nº 2267798 – configuração do banco de dados SAP HANA](https://launchpad.support.sap.com/#/notes/2267798).

Você também pode configurar os parâmetros após a instalação do banco de dados SAP HANA usando a estrutura hdbparam. 

O armazenamento usado no HANA em instâncias grandes tem uma limitação de tamanho de arquivo. A [limitação de tamanho é de 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) por arquivo. Ao contrário das limitações de tamanho de arquivo nos sistemas de arquivos EXT3, o HANA não reconhece implicitamente a limitação de armazenamento imposta pelo armazenamento do SAP HANA em instâncias grandes. Como resultado, o HANA não criará automaticamente um novo arquivo de dados quando o limite de tamanho de arquivo de 16TB for atingido. Como o HANA tenta aumentar o arquivo para além de 16 TB, o HANA relatará erros e o servidor de índice falhará no final.

> [!IMPORTANT]
> Para evitar que o HANA tente aumentar os arquivos de dados além do limite de tamanho de arquivo de 16 TB do armazenamento de instância grande do HANA, você precisa definir os seguintes parâmetros no arquivo de configuração SAP HANA global.ini
> 
> - datavolume_striping = true
> - datavolume_striping_size_gb = 15000
> - Consulte também SAP Note [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - Lembre-se do SAP Note [#2631285](https://launchpad.support.sap.com/#/notes/2631285)


Com o SAP HANA 2.0, a estrutura hdbparam foi preterida. Como resultado, os parâmetros devem ser definidos usando os comandos SQL. Para obter mais informações, consulte [Nota SAP nº 2399079: eliminação de hdbparam no HANA 2](https://launchpad.support.sap.com/#/notes/2399079).

Consulte os [cenários com suporte para HLI](hana-supported-scenario.md) para saber mais sobre o layout de armazenamento para sua arquitetura.


**Próximas etapas**

- Consulte a [Instalação do HANA em HLI](hana-example-installation.md)










































 







 
