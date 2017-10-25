---
title: "Instalar SAP HANA em SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs"
description: "Como instalar SAP HANA em um SAP HANA no Azure (Instância Grande)."
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 280001f9057825b9dcd98c5180340a54e2e239cf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Como instalar e configurar SAP HANA (instâncias grandes) no Azure

Veja a seguir algumas definições importantes antes de ler este guia. Em [Visão geral e arquitetura do SAP HANA (instâncias grandes) no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture), apresentamos duas classes diferentes de unidades de Instância Grande do HANA com:

- S72, S72m, S144, S144m, S192 e S192m, que chamamos de “classe do Tipo I” de SKUs.
- S384, S384m, S384xm, S576, S768 e S960, que chamamos de “classe do Tipo II” de SKUs.

O especificador de classe será usado em toda a documentação da Instância Grande do HANA para, em última análise, se referir a diferentes funcionalidades e requisitos de acordo com os SKUs da Instância Grande do HANA.

Outras definições que usamos frequentemente são:
- **Grande carimbo da instância:** uma pilha de infraestrutura de hardware é TDI do SAP HANA certificados e dedicado para executar instâncias do SAP HANA no Azure.
- **SAP HANA no Azure (Instâncias Grandes):** nome oficial para a oferta no Azure para executar instâncias do HANA em hardware certificado por TDI do SAP HANA implantado em carimbos de Instância Grande em diferentes regiões do Azure. O termo relacionado **Instância Grande do HANA** é a abreviação de SAP HANA no Azure (Instâncias Grandes) e é amplamente usado neste guia de implantação técnica.


A instalação do SAP HANA é responsabilidade sua e você pode começar a atividade após a entrega de um novo servidor do SAP HANA no Azure (Instâncias Grandes). E depois que a conectividade entre as VNets do Azure e as unidades de Instância Grande do HANA foi estabelecida. 

> [!Note]
> De acordo com a política da SAP, a instalação do SAP HANA deve ser feita por uma pessoa certificada para realizar instalações do SAP HANA. Uma pessoa, que foi aprovada no exame Certified SAP Technology Associate, o exame de certificação de Instalação do SAP HANA ou por um SI (integrador de sistemas) certificado pela SAP.

Verifique novamente, especialmente ao planejar a instalação do HANA 2.0, [Nota de Suporte da SAP #2235581 - SAP HANA: sistemas operacionais com suporte](https://launchpad.support.sap.com/#/notes/2235581/E) para certificar-se de que o sistema operacional é compatível com a versão do SAP HANA que você decidiu instalar. Você perceberá que o sistema operacional com suporte para o HANA 2.0 é mais restrito do que o sistema operacional com suporte para o HANA 1.0. 

## <a name="first-steps-after-receiving-the-hana-large-instance-units"></a>Primeiras etapas depois de receber as Unidades de Instância Grande do HANA

A **Primeira Etapa** depois de receber a Instância Grande do HANA e ter estabelecido o acesso e a conectividade com as instâncias é registrar o sistema operacional da instância no provedor do sistema operacional. Essa etapa inclui o registro do sistema operacional SUSE Linux em uma instância do SUSE SMT que você precisa ter implantada em uma VM do Azure. A unidade de Instância Grande do HANA pode se conectar a essa instância SMT (consulte mais adiante nesta documentação). Ou o sistema operacional RedHat precisa ser registrado no Gerenciador de Assinaturas do Red Hat ao qual você precisa se conectar. Consulte também os comentários neste [documento](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Essa etapa também é necessária para corrigir o sistema operacional. Uma tarefa que é responsabilidade do cliente. Para o SUSE, encontre a documentação para instalar e configurar o SMT [aqui](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

A **Segunda Etapa** é verificar se há novos patches e correções da versão específica do sistema operacional. Verifique se o nível de patch da Instância Grande do HANA está no estado mais recente. Com base no tempo do patch e das versões do sistema operacional e das alterações na imagem, a Microsoft pode realizar a implantação. Pode haver casos em que os últimos patches não são incluídos. Portanto, depois de obter uma unidade de Instância Grande do HANA, é uma etapa obrigatória verificar se, no momento, foram liberados patches relevantes para a segurança, a funcionalidade, a disponibilidade e o desempenho pelo fornecedor específico do Linux e se eles precisam ser aplicados.

A **Terceira Etapa** é verificar as Notas SAP relevantes para a instalação e configuração do SAP HANA na versão específica do sistema operacional. Devido à alteração de recomendações ou às alterações das Notas SAP ou de configurações que dependem de cenários de instalação individuais, a Microsoft nem sempre poderá ter uma unidade de Instância Grande do HANA configurada perfeitamente. Portanto, é obrigatório que você, como cliente, leia as Observações da SAP relacionadas ao SAP HANA sobre a versão exata do Linux. Também verifique as configurações da versão do sistema operacional necessárias e aplique as definições de configuração nos locais em que ainda não foram aplicadas.

Em particular, verifique os seguintes parâmetros e, finalmente, ajuste-os para:

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

A partir do SLES12 SP1 e RHEL 7.2, esses parâmetros devem ser definidos em um arquivo de configuração no diretório /etc/sysctl.d. Por exemplo, deve-se criar um arquivo de configuração com o nome 91-NetApp-HANA.conf. Para versões mais antigas do SLES e RHEL, esses parâmetros devem ser definidos como in/etc/sysctl.conf.

Para todas as versões do RHEL e a partir do SLES12, o 
- sunrpc.tcp_slot_table_entries = 128

parâmetro deve ser definido como in/etc/modprobe.d/sunrpc-local.conf. Se o arquivo não existir, primeiro ele deverá ser criado com a adição da seguinte entrada: 
- options sunrpc tcp_max_slot_table_entries=128

A **Quarta Etapa** é verificar a hora do sistema da Unidade de Instância Grande do HANA. As instâncias são implantadas com um fuso horário do sistema que representa a localização da região do Azure em que o Selo da Instância Grande do HANA está localizado. Você tem a liberdade para alterar a hora do sistema ou o fuso horário das instâncias que possui. Ao fazer isso e solicitar mais instâncias para seu locatário, prepare-se, pois você precisará adaptar o fuso horário das instâncias recém-entregues. As operações da Microsoft não têm nenhuma informação sobre o fuso horário do sistema configurado com as instâncias após a entrega. Portanto, as instâncias recém-implantadas podem não estar definidas com o mesmo fuso horário para o qual você alterou. Como resultado, é sua responsabilidade como cliente verificar e, se necessário, adaptar o fuso horário das instâncias entregues. 

A **Quinta Etapa** é verificar etc/hosts. Conforme as folhas são entregues, elas têm diferentes endereços IP atribuídos para finalidades diferentes (consulte a próxima seção). Verifique o arquivo /etc/hosts. Nos casos em que as unidades são adicionadas a um locatário existente, não espere ter etc/hosts dos sistemas recém-implantados mantidos corretamente com os endereços IP dos sistemas entregues anteriormente. Portanto, como cliente, cabe a você verificar as configurações corretas, de forma que uma instância recém-implantada possa interagir e resolver os nomes das unidades implantadas anteriormente no locatário. 

## <a name="networking"></a>Rede
Supomos que você seguiu as recomendações de design das VNets do Azure e de conexão dessas VNets com as Instâncias Grandes do HANA, conforme descrito nestes documentos:

- [Visão geral e arquitetura do SAP HANA (instâncias grandes) no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Infraestrutura e conectividade do SAP HANA (instâncias grandes) no Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Há alguns detalhes que vale a pena mencionar sobre a rede das unidades individuais. Cada unidade de Instância Grande do HANA é fornecida com dois ou três endereços IP atribuídos a dois ou três portas NIC da unidade. Três endereços IP são usados em configurações de expansão do HANA e no cenário de Replicação de Sistema do HANA. Um dos endereços IP atribuídos à NIC da unidade está fora do pool de IPs do Servidor descrito na [Visão geral e arquitetura do SAP HANA (instâncias grandes) no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

A distribuição de unidades com dois endereços IP atribuídos deve ser parecida com esta:

- eth0.xx deve ter um endereço IP atribuído que está fora do intervalo de endereços do Pool de IPs do Servidor enviado para a Microsoft. Esse endereço IP deve ser usado para ser mantido em /etc/hosts do sistema operacional.
- eth1.xx deve ter um endereço IP atribuído usado para a comunicação com o NFS. Portanto, esses endereços **NÃO** precisam ser mantidos em etc/hosts para permitir o tráfego entre instâncias no locatário.

Para casos de implantação de Replicação de Sistema do HANA ou de expansão do HANA, uma configuração de folha com dois endereços IP atribuídos não é adequada. Caso você tenha apenas dois endereços IP atribuídos e deseje implantar uma configuração como essa, contate o Gerenciamento de Serviços do SAP HANA no Azure para obter um terceiro endereço IP em uma terceira VLAN atribuída. Para as unidades de Instância Grande do HANA que têm três endereços IP atribuídos em três portas NIC, as seguintes regras de uso se aplicam:

- eth0.xx deve ter um endereço IP atribuído que está fora do intervalo de endereços do Pool de IPs do Servidor enviado para a Microsoft. Portanto, esse endereço IP não deve ser usado para ser mantido em /etc/hosts do sistema operacional.
- eth1.xx deve ter um endereço IP atribuído usado para a comunicação com o armazenamento NFS. Portanto, esse tipo de endereço não deve ser mantido em etc/hosts.
- eth2.xx deve ser usado exclusivamente para ser mantido em etc/hosts para a comunicação entre instâncias diferentes. Esses endereços também serão os endereços IP que precisam ser mantidos em configurações em expansão do HANA como os endereços IP usados pelo HANA para a configuração entre nós.



## <a name="storage"></a>Armazenamento

O layout de armazenamento do SAP HANA no Azure (Instâncias Grandes) é configurado pelo Gerenciamento de Serviços do SAP HANA no Azure por meio das diretrizes recomendadas pela SAP, conforme documentado no white paper [Requisitos de armazenamento do SAP HANA](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Os tamanhos aproximados dos volumes diferentes com os diferentes SKUs de Instâncias Grandes do HANA foram documentados na [Visão geral e arquitetura do SAP HANA (Instância grande) no Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

As convenções de nomenclatura dos volumes de armazenamento são listadas na seguinte tabela:

| Uso de armazenamento | Nome da montagem | Nome do volume | 
| --- | --- | ---|
| Dados do HANA | /hana/data/SID/mnt0000<m> | IP de Armazenamento: /hana_data_SID_mnt00001_tenant_vol |
| Log do HANA | /hana/log/SID/mnt0000<m> | IP de Armazenamento: /hana_log_SID_mnt00001_tenant_vol |
| Backup de log do HANA | /hana/log/backups | IP de Armazenamento: /hana_log_backups_SID_mnt00001_tenant_vol |
| HANA compartilhado | /hana/shared/SID | IP de Armazenamento: /hana_shared_SID_mnt00001_tenant_vol/shared |
| usr/sap | /usr/sap/SID | IP de Armazenamento: /hana_shared_SID_mnt00001_tenant_vol/usr_sap |

Em que SID = a ID do Sistema da instância do HANA 

E locatário = uma enumeração interna das operações durante a implantação de um locatário.

Como você pode ver, o HANA compartilhado e usr/sap estão compartilhando o mesmo volume. A nomenclatura dos pontos de montagem inclui a ID do Sistema das instâncias do HANA, bem como o número de montagem. Em implantações escaláveis, há somente uma montagem, como mnt00001. Ao contrário da implantação escalável, em que você verá o mesmo número de montagens que os nós de trabalho e mestre. Para o ambiente escalável, os dados, o log e os volumes de backup de log são compartilhados e anexados a cada nó na configuração escalável. Para configurações que executam várias instâncias do SAP, um conjunto diferente de volumes é criado e anexado à unidade de Instância Grande do HANA.

Conforme você lê o documento e examina uma unidade de Instância Grande do HANA, descobrirá que as unidades são fornecidas com um volume de disco bem generoso para HANA/data e que temos um HANA/log/backup. O motivo pelo qual dimensionamos HANA/data para um tamanho tão grande é que os instantâneos de armazenamento que oferecemos para você, como cliente, estão usando o mesmo volume de disco. Isso significa que quanto mais instantâneos de armazenamento são tirados, mais espaço é consumido pelos instantâneos nos volumes de armazenamento atribuídos. O volume de HANA/log/backup não é considerado como sendo o volume no qual colocar os backups de banco de dados. Ele é dimensionado para ser usado como volume de backup para os backups de log de transações do HANA. Em versões futuras do autoatendimento de instantâneos de armazenamento, direcionaremos esse volume específico para que ele tenha instantâneos mais frequentes. E com isso, replicações mais frequentes no site de recuperação de desastre, caso você deseje aceitar a funcionalidade de recuperação de desastre fornecida pela infraestrutura de Instância Grande do HANA. Consulte os detalhes em [Alta disponibilidade e recuperação de desastre do SAP HANA (instâncias grandes) no Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 

Além do armazenamento fornecido, você pode adquirir uma capacidade de armazenamento adicional em incrementos de 1 TB. Esse armazenamento adicional pode ser incluído como novos volumes para Instâncias Grandes HANA.

Durante a integração com o Gerenciamento de Serviços do SAP HANA no Azure, o cliente especifica um UID (ID de Usuário) e um GID (ID de Grupo) para o grupo de usuários sidadm e sapsys (por exemplo: 1000, 500). É necessário que, durante a instalação do sistema SAP HANA, esses mesmos valores sejam usados. Conforme você deseja implantar várias instâncias do HANA em uma unidade, obtém vários conjuntos de volumes (um conjunto para cada instância). Como resultado, no momento da implantação, é necessário definir:

- O SID das diferentes instâncias do HANA (sidadm é derivado dele).
- Tamanhos de memória das diferentes instâncias do HANA. Como o tamanho de memória por instância define o tamanho dos volumes em cada conjunto de volumes individual.

De acordo com as recomendações do provedor de armazenamento, as seguintes opções de montagem são configuradas para todos os volumes montados (exclui o LUN de inicialização):

- nfs    rw, vers=4, hard, timeo=600, rsize=1048576, wsize=1048576, intr, noatime, lock 0 0

Esses pontos de montagem são configurados em /etc/fstab, conforme mostrado no seguinte gráfico:

![fstab de volumes montados na unidade de Instância Grande do HANA](./media/hana-installation/image1_fstab.PNG)

O resultado do comando df -h em uma unidade S72m de Instância Grande do HANA será parecido com este:

![fstab de volumes montados na unidade de Instância Grande do HANA](./media/hana-installation/image2_df_output.PNG)


O controlador de armazenamento e os nós nos carimbos de Instância Grande são sincronizados com os servidores NTP. Quando você sincroniza SAP HANA em unidades do Azure (Instâncias Grandes) e VMs do Azure em relação a um servidor NTP, não deve haver um descompasso de tempo significativo entre a infraestrutura e as unidades de computação no Azure ou carimbos de Instância Grande.

Para otimizar o SAP HANA para o armazenamento usado de forma subjacente, você deve também definir os seguintes parâmetros de configuração do SAP HANA:

- max_parallel_io_requests 128
- async_read_submit on
- async_write_submit_active on
- async_write_submit_blocks all
 
Para as versões 1.0 do SAP HANA até SPS12, esses parâmetros podem ser definidos durante a instalação do banco de dados SAP HANA, conforme descrito em [Nota SAP nº 2267798 – Configuração do banco de dados SAP HANA](https://launchpad.support.sap.com/#/notes/2267798)

Você também pode configurar os parâmetros após a instalação do banco de dados SAP HANA usando a estrutura hdbparam. 

Com o SAP HANA 2.0, a estrutura hdbparam foi preterida. Como resultado, os parâmetros devem ser definidos com comandos SQL. Para obter detalhes, consulte [Nota SAP nº 2399079: Eliminação de hdbparam no HANA 2](https://launchpad.support.sap.com/#/notes/2399079).


## <a name="operating-system"></a>Sistema operacional

O espaço de troca da imagem do sistema operacional fornecida é definido como 2 GB, de acordo com a [Observação de Suporte da SAP nº 1999997 – Perguntas frequentes: Memória do SAP HANA](https://launchpad.support.sap.com/#/notes/1999997/E). Qualquer configuração diferentes desejada deve ser definida por você como um cliente.

[SUSE Linux Enterprise Server 12 SP1 para aplicativos SAP](https://www.suse.com/products/sles-for-sap/hana) é a distribuição do Linux instalada para SAP HANA no Azure (Instâncias Grandes). Essa distribuição particular fornece recursos específicos do SAP &quot;prontos para uso&quot; (incluindo parâmetros predefinidos para executar o SAP em SLES de forma eficaz).

Confira [Biblioteca de recursos/White Papers](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) no site do SUSE e [SAP no SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) na SCN (Rede da Comunidade SAP) para obter vários recursos úteis relacionados à implantação do SAP HANA em SLES (incluindo configuração de alta disponibilidade, proteção de segurança específica para operações do SAP e muito mais).

Links adicionais e úteis relacionados ao SAP no SUSE:

- [SAP HANA no Site do SUSE Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Práticas recomendadas para SAP: replicação de enfileiramento – SAP NetWeaver no SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113).
- [ClamSAP – proteção antivírus de SLES para SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (incluindo SLES 12 para aplicativos SAP).

Notas de suporte do SAP aplicáveis à implementação do SAP HANA no SLES 12:

- [Observação de suporte SAP 1944799 – diretrizes SAP HANA para instalação do sistema operacional SLES](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html).
- [Observação de suporte SAP 2205917 – configurações do SO recomendadas para o banco de dados do SAP HANA para SLES 12 para aplicativos SAP](https://launchpad.support.sap.com/#/notes/2205917/E).
- [Observação de suporte SAP 1984787 – SUSE Linux Enterprise Server 12: notas de instalação](https://launchpad.support.sap.com/#/notes/1984787).
- [Observação de suporte SAP 171356 – software SAP no Linux: informações gerais](https://launchpad.support.sap.com/#/notes/1984787).
- [Observação de suporte SAP 1391070 – soluções UUID do Linux](https://launchpad.support.sap.com/#/notes/1391070).

[Red Hat Enterprise Linux para SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) é outra oferta para execução do SAP HANA em instâncias grandes de HANA. As versões do RHEL 6.7 e 7.2 estão disponíveis. 

Links adicionais e úteis relacionados a SAP no Red Hat:
- [SAP HANA no site Red Hat Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

Notas de Suporte da SAP aplicáveis à implementação do SAP HANA no Red Hat:

- [Nota de Suporte da SAP #2009879 – Diretrizes SAP HANA para o sistema operacional RHEL (Red Hat Enterprise Linux)](https://launchpad.support.sap.com/#/notes/2009879/E).
- [Nota de Suporte da SAP #2292690 - Banco de dados SAP HANA: configurações de sistema operacional recomendadas para RHEL 7](https://launchpad.support.sap.com/#/notes/2292690).
- [Nota de Suporte da SAP #2247020 - Banco de dados SAP HANA: configurações de sistema operacional recomendadas para RHEL 6.7](https://launchpad.support.sap.com/#/notes/2247020).
- [Observação de suporte SAP 1391070 – soluções UUID do Linux](https://launchpad.support.sap.com/#/notes/1391070).
- [Nota de Suporte da SAP #2228351 - Linux: SAP HANA Database SPS 11 revisão 110 (ou superior) em RHEL 6 ou SLES 11](https://launchpad.support.sap.com/#/notes/2228351).
- [Nota de Suporte da SAP #2397039 - Perguntas frequentes: SAP em RHEL](https://launchpad.support.sap.com/#/notes/2397039).
- [Nota de Suporte da SAP #1496410 - Red Hat Enterprise Linux 6.x: instalação e atualização](https://launchpad.support.sap.com/#/notes/1496410).
- [Nota de Suporte da SAP #2002167 - Red Hat Enterprise Linux 7.x: instalação e atualização](https://launchpad.support.sap.com/#/notes/2002167).

## <a name="time-synchronization"></a>Sincronização de horário

Os aplicativos SAP criados na arquitetura do SAP NetWeaver são sensíveis às diferenças de hora nos vários componentes que compõem o sistema SAP. Os despejos curtos do SAP ABAP com o título de erro ZDATE\_LARGE\_TIME\_DIFF provavelmente são familiares, pois esses despejos curtos são exibidos quando a hora do sistema de servidores ou VMs diferentes se desvia muito.

Para SAP HANA no Azure (instâncias grandes), a sincronização de tempo feita no Azure não se aplica a unidades de computação nos carimbos de data/hora de Instâncias Grandes. Essa sincronização não é aplicável à execução de aplicativos SAP em VMs nativas do Azure, pois o Azure garante que a hora do sistema é sincronizada corretamente. Como resultado, deve ser configurado um servidor de tempo separado que possa ser usado pelos servidores de aplicativos SAP em execução em VMs do Azure e instâncias de banco de dados HANA SAP em execução em instâncias grandes HANA. A infraestrutura de armazenamento em carimbos de Instância Grande tem seu horário sincronizado com os servidores NTP.

## <a name="setting-up-smt-server-for-suse-linux"></a>Configurando o servidor SMT para o SUSE Linux
As Instâncias Grandes do SAP HANA não têm conectividade direta com a Internet. Portanto, não é um processo simples registrar uma unidade como essa no provedor do sistema operacional e baixar e aplicar patches. No caso do SUSE Linux, uma solução pode ser a configuração de um servidor SMT em uma VM do Azure. Ao contrário da VM do Azure, que precisa ser hospedada em uma VNet do Azure, que está conectada à Instância Grande do HANA. Com um servidor SMT desse tipo, a unidade de Instância Grande do HANA pode registrar e baixar patches. 

O SUSE fornece um guia maior em sua [Ferramenta de Gerenciamento de Assinaturas para SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

Como uma pré-condição para a instalação de um servidor SMT que realiza a tarefa para a Instância Grande do HANA, você precisará:

- Uma VNet do Azure que está conectada ao circuito de ER da Instância Grande do HANA.
- Uma conta SUSE associada a uma organização. Ao contrário da organização, que precisa ter uma assinatura válida do SUSE.

### <a name="installation-of-smt-server-on-azure-vm"></a>Instalação do servidor SMT em uma VM do Azure

Nesta etapa, você instala o servidor SMT em uma VM do Azure. A primeira medida é fazer logon na [Central do Cliente do SUSE](https://scc.suse.com/)

Quando estiver conectado, acesse Organização--> Credenciais da Organização. Nesta seção, você deve encontrar as credenciais necessárias para configurar o servidor SMT.

A terceira etapa é instalar uma VM SUSE Linux na VNet do Azure. Para implantar a VM, use uma imagem da galeria do SLES 12 SP2 do Azure. No processo de implantação, não defina um nome DNS nem use endereços IP estáticos, como visto nesta captura de tela

![implantação da VM no servidor SMT](./media/hana-installation/image3_vm_deployment.png)

A VM implantada era uma VM menor e obteve o endereço IP interno 10.34.1.4 na VNet do Azure. O nome da VM era smtserver. Após a instalação, a conectividade com as unidades de Instância grande do HANA foi verificada. Dependendo de como você organizou a resolução de nomes, talvez precise configurar a resolução das unidades de Instância Grande do HANA em etc/hosts da VM do Azure. Adicione outro disco à VM que será usado para manter os patches. O disco de inicialização em si pode ser muito pequeno. No caso demonstrado, o disco foi montado em /srv/www/htdocs, conforme mostrado na captura de tela a seguir. Um disco de 100 GB deve ser suficiente.

![implantação da VM no servidor SMT](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Faça logon nas unidades de Instância Grande do HANA, mantenha /etc/hosts e verifique se você consegue acessar a VM do Azure que deve executar o servidor SMT na rede.

Depois que essa verificação for feita com êxito, você precisará fazer logon na VM do Azure que deve executar o servidor SMT. Se você estiver usando o putty para fazer logon na VM, precisará executar esta sequência de comandos na janela bash:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Depois de executar esses comandos, reinicie o bash para ativar as configurações. Depois, inicie o YAST.

No YAST, acesse Manutenção de Software e pesquise smt. Selecione smt, que alterna automaticamente para yast2-smt, conforme mostrado abaixo

![SMT no yast](./media/hana-installation/image5_smt_in_yast.PNG)


Aceite a seleção para a instalação no smtserver. Depois de instalado, acesse a configuração do servidor SMT e insira as credenciais da organização da Central do Cliente do SUSE recuperadas anteriormente. Insira também seu nome do host da VM do Azure como a URL do Servidor SMT. Nesta demonstração, ela era https://smtserver, conforme exibido no próximo gráfico.

![Configuração do servidor SMT](./media/hana-installation/image6_configuration_of_smtserver1.png)

Como a próxima etapa, você precisa testar se a conexão com a Central do Cliente do SUSE funciona. Como você vê nos gráficos a seguir, no caso de demonstração, isso funcionou.

![Testar a conexão à Central do Cliente do SUSE](./media/hana-installation/image7_test_connect.png)

Após o início da instalação do SMT, você precisará fornecer uma senha de banco de dados. Como essa é uma nova instalação, é necessário definir essa senha, conforme mostrado no próximo gráfico.

![Definir a senha do banco de dados](./media/hana-installation/image8_define_db_passwd.PNG)

A próxima interação que ocorre é quando um certificado é criado. Percorra a caixa de diálogo, conforme mostrado a seguir e a etapa deverá continuar.

![Criar um certificado para o servidor SMT](./media/hana-installation/image9_certificate_creation.PNG)

A etapa “Executar verificação de sincronização” ao final da configuração pode levar alguns minutos. Após a instalação e configuração do servidor SMT, você deverá encontrar o repositório do diretório sob o ponto de montagem /srv/www/htdocs/, além de alguns subdiretórios no repositório. 

Reinicie o servidor SMT e seus serviços relacionados com estes comandos.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

### <a name="download-of-packages-onto-smt-server"></a>Download de pacotes no servidor SMT

Depois que todos os serviços forem reiniciados, selecione os pacotes apropriados no Gerenciamento SMT usando o Yast. A seleção do pacote depende da imagem do sistema operacional do servidor da Instância Grande do HANA e não da liberação ou versão do SLES da VM que executa o servidor SMT. Um exemplo da tela de seleção é mostrado abaixo.

![Selecionar pacotes](./media/hana-installation/image10_select_packages.PNG)

Depois de concluir a seleção do pacote, você precisará iniciar a cópia inicial dos pacotes selecionados para o servidor SMT configurado. Essa cópia é disparada no shell com o comando smt-mirror, conforme mostrado abaixo


![Baixar os pacotes no servidor SMT](./media/hana-installation/image11_download_packages.PNG)

Como você vê acima, os pacotes devem ser copiados para os diretórios criados no ponto de montagem /srv/www/htdocs. Esse processo pode levar algum tempo. Dependendo de quantos pacotes você selecionar, isso pode levar até uma hora ou mais.
Quando esse processo for concluído, você precisará ir para a instalação do cliente SMT. 

### <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Configurar o cliente SMT em unidades de Instância Grande do HANA

Nesse caso, os clientes são as unidades de Instância Grande do HANA. A instalação do servidor SMT copiou o script clientSetup4SMT.sh para a VM do Azure. Copie esse script para a unidade de Instância Grande do HANA que você deseja conectar ao servidor SMT. Inicie o script com a opção -h e dê a ele como parâmetro o nome do servidor SMT. Neste exemplo, smtserver.

![Configurar o cliente SMT](./media/hana-installation/image12_configure_client.PNG)

Pode haver um cenário em que a carga do certificado do servidor pelo cliente foi bem-sucedida, mas o registro falhou, conforme mostrado abaixo.

![Falha no registro do cliente](./media/hana-installation/image13_registration_failed.PNG)

Se o registro falhou, leia este [documento de suporte do SUSE](https://www.suse.com/de-de/support/kb/doc/?id=7006024) e execute as etapas descritas nele.

> [!IMPORTANT] 
> Como o nome do servidor, você precisa fornecer o nome da VM, nesse caso, smtserver, sem o nome de domínio totalmente qualificado. Apenas o nome da VM funciona. 

Depois que essas etapas forem executadas, você precisará executar o seguinte comando na unidade de Instância Grande do HANA

```
SUSEConnect –cleanup
```

> [!Note] 
> Em nossos testes, sempre precisamos aguardar alguns minutos após essa etapa. O clientSetup4SMT.sh de execução imediata, após as medidas corretivas descritas no artigo sobre o SUSE, terminou com mensagens informando que o certificado ainda não era válido. Geralmente, aguardar de 5 a 10 minutos e executar clientSetup4SMT.sh terminou com uma configuração de cliente bem-sucedida.

Se você teve o problema que precisou corrigir de acordo com as etapas descritas no artigo sobre o SUSE, precisará reiniciar clientSetup4SMT.sh na unidade de Instância Grande do HANA novamente. Agora ele deverá ser concluído com êxito, conforme mostrado abaixo.

![Registro do cliente bem-sucedido](./media/hana-installation/image14_finish_client_config.PNG)

Com essa etapa, você configurou o cliente SMT da unidade de Instância Grande do HANA para se conectar ao servidor SMT instalado na VM do Azure. Agora, você pode usar “zypper up” ou “zypper in” para instalar patches dos sistema operacional nas Instâncias Grandes do HANA ou instalar outros pacotes. Fica entendido que você somente pode obter patches baixados antes no servidor SMT.


## <a name="example-of-an-sap-hana-installation-on-hana-large-instances"></a>Exemplo de uma instalação do SAP HANA nas Instâncias Grandes do HANA
Esta seção ilustra como instalar o SAP HANA em uma unidade de Instância Grande do HANA. O estado inicial que temos tem esta aparência:

- Você forneceu à Microsoft todos os dados para implantar uma Instância Grande do SAP HANA.
- Você recebeu a Instância Grande do SAP HANA da Microsoft.
- Você criou uma VNet do Azure que está conectada à rede local.
- Você conectou o circuito do ExpressRoute das Instâncias Grandes do HANA com a VNet do Azure.
- Você instalou uma VM do Azure usada como uma jumpbox para as Instâncias Grandes do HANA.
- Você garantiu que pode se conectar na jumpbox com a unidade de Instância Grande do HANA e vice-versa.
- Você verificou se todos os pacotes e patches necessários estão instalados.
- Você leu as observações e a documentação da SAP sobre a instalação do HANA no sistema operacional que está sendo usado e verificou se há suporte para a versão do HANA de sua escolha na versão do sistema operacional.

O que é mostrado nas próximas sequências é o download dos pacotes de instalação do HANA na VM de jumpbox, nesse caso, executada em um sistema operacional Windows, a cópia dos pacotes para a unidade de Instância Grande do HANA e a sequência da instalação.

### <a name="download-of-the-sap-hana-installation-bits"></a>Baixar os bits de instalação do SAP HANA
Como as unidades de Instância Grande do HANA não têm conectividade direta com a Internet, não é possível baixar diretamente os pacotes de instalação da SAP na VM da Instância Grande do HANA. Para resolver a conectividade direta com a Internet ausente, você precisa da jumpbox. Baixe os pacotes na VM de jumpbox.

Para baixar os pacotes de instalação do HANA, você precisa ser um usuário S da SAP ou outro usuário, o que permite que você acesse o SAP Marketplace. Siga esta sequência de telas após o logon:

Acesse [SAP Service Marketplace](https://support.sap.com/en/index.html) > clique em Baixar Software > Instalações e Upgrade > Por Índice Alfabético > em H – SAP HANA Platform Edition > SAP HANA Platform Edition 2.0 > Instalação > Baixar os seguintes arquivos

![Baixar a instalação do HANA](./media/hana-installation/image16_download_hana.PNG)

No caso de demonstração, baixamos os pacotes de instalação do SAP HANA 2.0. Na VM de jumpbox do Azure, expanda os arquivos mortos de extração automática para o diretório, conforme mostrado abaixo.

![Extrair a instalação do HANA](./media/hana-installation/image17_extract_hana.PNG)

Conforme os arquivos mortos são extraídos, copie o diretório criado pela extração, no caso acima, 51052030, para a unidade de Instância grande do HANA no volume /hana/shared de um diretório criado.

> [!Important]
> Não copie os pacotes de instalação para a raiz ou para o LUN de inicialização, pois o espaço é limitado e precisa ser usado por outros processos também.


### <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Instalar o SAP HANA na unidade de Instância Grande do HANA
Para instalar o SAP HANA, você precisa fazer logon como a raiz do usuário. Somente a raiz tem permissões suficientes para instalar o SAP HANA.
A primeira coisa que você precisa fazer é definir permissões no diretório copiado em hana/shared. As permissões precisam ser definidas como

```
chmod –R 744 <Installation bits folder>
```

Se você deseja instalar o SAP HANA usando a instalação gráfica, o pacote gtk2 precisa ser instalado nas Instâncias Grandes do HANA. Verifique se ele é instalado com o comando

```
rpm –qa | grep gtk2
```

Nas próximas etapas, demonstramos a instalação do SAP HANA com a interface gráfica do usuário. Como a próxima etapa, vá para o diretório de instalação e navegue para o subdiretório HDB_LCM_LINUX_X86_64. Iniciar

```
./hdblcmgui 
```
fora desse diretório. Agora, você está sendo guiado por uma sequência de telas em que precisa fornecer os dados da instalação. No caso demonstrado, estamos instalando o servidor de banco de dados e os componentes de cliente do SAP HANA. Portanto, nossa seleção é “Banco de Dados do SAP HANA”, conforme mostrado abaixo

![Selecionar o HANA na instalação](./media/hana-installation/image18_hana_selection.PNG)

Na próxima tela, você escolhe a opção “Instalar Novo Sistema”

![Selecionar a nova instalação do HANA](./media/hana-installation/image19_select_new.PNG)

Após essa etapa, você precisa selecionar entre vários outros componentes que podem ser instalados adicionalmente ao servidor de banco de dados do SAP HANA.

![Selecionar os componentes adicionais do HANA](./media/hana-installation/image20_select_components.PNG)

Para fins desta documentação, escolhemos o HANA SAP Client e o SAP HANA Studio. Também instalamos uma instância escalável. Portanto, na próxima tela, você precisa escolher “Sistema de Host Único” 

![Selecionar a instalação escalável](./media/hana-installation/image21_single_host.PNG)

Na próxima tela, você precisa fornecer alguns dados

![Fornecer o SID do SAP HANA](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Como o SID (ID do Sistema) do HANA, você precisa fornecer o mesmo SID fornecido à Microsoft quando solicitou a implantação da Instância Grande do HANA. A escolha de um SID diferente causará falha na instalação, devido a problemas de permissão de acesso em diferentes volumes

Como o diretório de instalação, use o diretório /hana/shared. Na próxima etapa, você precisa fornecer os locais dos arquivos de dados e dos arquivos de log do HANA


![Fornecer o local do Log do HANA](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> Você deve definir como arquivos de dados e de log os volumes que já são fornecidos com os pontos de montagem que contêm o SID escolhido na seleção de tela anterior a esta tela. Se o SID for incompatível com aquele que você digitou, na tela anterior, volte e ajuste o SID para o valor existente nos pontos de montagem.

Na próxima etapa, examine o nome do host e, finalmente, corrija-o. 

![Examinar o nome do host](./media/hana-installation/image24_review_host_name.PNG)

Na próxima etapa, você também precisa recuperar os dados fornecidos à Microsoft quando solicitou a implantação da Instância Grande do HANA. 

![Fornecer um UID e GID do usuário do sistema](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Você precisa fornecer a mesma ID de Usuário do Sistema e ID do Grupo de Usuários fornecidas à Microsoft quando solicitou a implantação da unidade. Se você não fornecer as mesmas IDs, a instalação do SAP HANA na unidade de Instância Grande do HANA falhará.

Nas próximas duas telas, que não mostramos nesta documentação, você precisa fornecer a senha para o usuário do SISTEMA do banco de dados do SAP HANA e a senha do usuário sapadm, que é usada para o Agente de Host do SAP instalado como parte da instância de banco de dados do SAP HANA.

Depois de definir a senha, uma tela de confirmação é exibida. Verifique todos os dados listados e continue com a instalação. Você acessa uma tela de progresso que documenta o progresso da instalação, como a mostrada abaixo

![Verificar o progresso da instalação](./media/hana-installation/image27_show_progress.PNG)

Como a instalação for concluída, você deverá ter uma imagem como a seguir

![A instalação é concluída](./media/hana-installation/image28_install_finished.PNG)

Neste ponto, a instância do SAP HANA deve estar em execução e pronta para uso. Você deve conseguir se conectar a ele no SAP HANA Studio. Verifique também os últimos patches do SAP HANA e aplique-os.
























































 







 




