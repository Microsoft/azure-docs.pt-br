---
title: "Início rápido: a instalação manual de instância única SAP HANA em máquinas virtuais do Azure | Microsoft Docs"
description: "Guia de início rápido para a instalação manual do SAP HANA de instância única em máquinas virtuais do Azure"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
ms.openlocfilehash: 05fb31007e1e4c2243f93169129ec5b2c93099e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>Início rápido: instalação manual do SAP HANA de instância única em VMs do Azure
## <a name="introduction"></a>Introdução
Esta guia ajuda você a configurar o SAP HANA de instância única em máquinas virtuais (VMs) do Azure, ao instalar o SAP NetWeaver 7.5 e SAP HANA 1.0 SP12 manualmente. O objetivo desta guia é tratar da implantação do SAP HANA no Azure. Ele não substitui a documentação do SAP. 

>[!Note]
>Esta guia descreve as implantações do SAP HANA em VMs do Azure. Para obter informações sobre a implantação do SAP HANA em instâncias grandes do HANA, confira [Como usar SAP no Azure em máquinas virtuais (VMs)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started).
 
## <a name="prerequisites"></a>Pré-requisitos
Esta guia pressupõe que você está familiarizado com tais infraestruturas como, por exemplo, serviços básicos (Iaas):
 * Como implantar máquinas ou redes virtuais usando o portal do Azure ou o PowerShell.
 * A interface de linha de comando (CLI) de plataforma cruzada do Azure, incluindo a opção de usar modelos JavaScript Object Notation (JSON).

Esta guia também pressupõe que você está familiarizado com:
* SAP HANA e SAP NetWeaver e como instalá-los no local.
* Instalação e operação do SAP HANA e instâncias de aplicativos SAP no Azure.
* Os conceitos e procedimentos a seguir:
   * Como planejar a implantação de SAP no Azure, incluindo o planejamento de rede virtual do Azure e o uso do armazenamento do Azure. Confira [SAP NetWeaver em máquinas virtuais (VMs) do Azure — Guia de planejamento e implementação](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide).
   * Princípios de implantação e maneiras de implantar VMs no Azure. Confira [Implantação de máquinas virtuais do Azure para SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide).
   * Alta disponibilidade para o SAP NetWeaver ASCS (Central de Serviços SAP ABAP), o SCS (Central de Serviços SAP) e ERS (liquidação de recebimento avaliado) no Azure. Confira [Alta disponibilidade do SAP NetWeaver em VMs do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide).
   * Obter detalhes sobre como melhorar a eficiência de utilização de uma instalação de vários SID do ASCS/SCS no Azure. Confira [Como criar uma configuração vários SID do SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid). 
   * Os princípios da execução do SAP NetWeaver com base em VMs controladas por Linux no Azure. Confira [Como executar o SAP NetWeaver em VMs do Linux SUSE no Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart). Esta guia fornece configurações específicas para Linux em VMs do Azure e os detalhes sobre como anexar corretamente os discos de armazenamento do Azure para VMs do Linux.

No momento, as VMs do Azure são certificadas pelo SAP somente para configurações de escalação vertical do SAP HANA. Ainda não há suporte para configurações de escalas horizontais de cargas de trabalho do SAP HANA. Para alta disponibilidade do SAP HANA em caso de configurações de escalação vertical, confira [Alta disponibilidade do SAP HANA em máquinas virtuais (VMs) do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability).

Se você quiser uma instância do SAP HANA ou S/4HANA ou sistema BW/4HANA implantado em um tempo muito rápido, considere o uso da [Biblioteca de dispositivo de nuvem do SAP](http://cal.sap.com). Você pode encontrar documentação sobre a implantação, por exemplo, um sistema S/4HANA por meio de SAP CAL no Azure [nesta guia](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). Tudo o que você precisa é uma assinatura do Azure e um usuário SAP que possa ser registrado com a biblioteca de dispositivo de nuvem do SAP.

## <a name="additional-resources"></a>Recursos adicionais
### <a name="sap-hana-backup"></a>Backup do SAP HANA
Para obter informações sobre como fazer backup de bancos de dados do SAP HANA em VMs do Azure, confira:
* [Guia de backup para SAP HANA nas Máquinas Virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [Backup do Azure do SAP HANA no nível do arquivo](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
* [Backup do SAP HANA com base em instantâneos de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

### <a name="sap-cloud-appliance-library"></a>Biblioteca de dispositivo de nuvem do SAP
Para obter informações sobre o uso da biblioteca de dispositivo de nuvem SAP para implantar S/4HANA ou BW/4HANA, confira [Como implantar SAP S/4HANA ou BW/4HANA no Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="sap-hana-supported-operating-systems"></a>Sistemas operacionais SAP HANA com suporte
Para obter informações sobre os sistemas operacionais com suporte SAP HANA, confira [Observação de suporte SAP nº 2235581 - SAP HANA: sistemas operacionais com suporte](https://launchpad.support.sap.com/#/notes/2235581/E). As VMs do Azure oferecem suporte a apenas um subconjunto desses sistemas operacionais. Os seguintes sistemas operacionais têm suporte para implantar o SAP HANA no Azure: 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

Para obter documentação SAP adicional sobre o SAP HANA e sistemas operacionais Linux diferentes, confira:

* [Observação de suporte SAP nº 171356 — software SAP no Linux: informações gerais](https://launchpad.support.sap.com/#/notes/1984787)
* [Observação de suporte SAP nº 1944799 — diretrizes SAP HANA para instalação do sistema operacional SLES](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
* [Observação de suporte SAP nº 2205917 — configurações do sistema operacional recomendadas para o SAP HANA DB para SLES 12 para aplicativos SAP](https://launchpad.support.sap.com/#/notes/2205917/E)
* [Observação de suporte SAP nº 1984787 — SUSE Linux Enterprise Server 12: observações de instalação](https://launchpad.support.sap.com/#/notes/1984787)
* [Observação de suporte SAP nº 1391070 — soluções UUID do Linux](https://launchpad.support.sap.com/#/notes/1391070)
* [Observação de suporte SAP 2009879 – diretrizes SAP HANA para o sistema operacional RHEL (Red Hat Enterprise Linux)](https://launchpad.support.sap.com/#/notes/2009879)
* [2292690 - Banco de dados do SAP HANA: configurações do sistema operacional recomendadas para RHEL 7](https://launchpad.support.sap.com/#/notes/2292690/E)

### <a name="sap-monitoring-in-azure"></a>Monitoramento SAP no Azure
Para obter informações sobre o monitoramento SAP no Azure, confira:

* [Observação SAP 2191498](https://launchpad.support.sap.com/#/notes/2191498/E). Esta observação discute o "monitoramento aprimorado" do SAP com VMs do Linux no Azure. 
* [Observação SAP 1102124](https://launchpad.support.sap.com/#/notes/1102124/E). Esta observação descreve informações sobre SAPOSCOL no Linux. 
* [Observação SAP 2178632](https://launchpad.support.sap.com/#/notes/2178632/E). Esta observação aborda as principais métricas de monitoramento para SAP no Microsoft Azure.

### <a name="azure-vm-types"></a>Tipos de VM do Azure
Os tipos de VM do Azure e os cenários de carga de trabalho com suporte do SAP no SAP HANA estão documentados nas [Plataformas de IaaS certificadas pela SAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). 

Os tipos de VM do Azure que são certificados pela SAP para SAP NetWeaver ou para a camada de aplicativo S/4HANA estão documentados pelo SAP em [Observação SAP 1928533 — Aplicativos SAP no Azure: tipos de VM do Azure e produtos com suporte](https://launchpad.support.sap.com/#/notes/1928533/E).

>[!Note]
>A integração SAP-Linux-Azure tem suporte apenas no Azure Resource Manager e não no modelo de implantação clássico. 

## <a name="manual-installation-of-sap-hana"></a>Instalação manual do SAP HANA
Esse guia descreve duas maneiras diferentes de instalar o SAP HANA manualmente em VMs do Azure:

* Usando o Software Provisioning Manager (SWPM) do SAP como parte de uma instalação distribuída do NetWeaver na etapa "instância de banco de dados"
* Usando a ferramenta gerenciadora do ciclo de vida do banco de dados SAP HANA, HDBLCM e, então instalando o NetWeaver

Você também pode usar SWPM para instalar todos os componentes (SAP HANA, o servidor de aplicativos do SAP e a instância do ASCS) em uma única VM, conforme descrito no [Comunicado do blog SAP HANA](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/). Essa opção não está descrita nesta guia de início rápido, mas os problemas que você deve levar em consideração são os mesmos.

Antes de iniciar uma instalação, recomendamos que você leia a seção "Como preparar as VMs do Azure para instalação manual do SAP HANA" nesta guia. Isso pode ajudar a evitar vários erros básicos que podem ocorrer quando você usa apenas uma configuração de VM do Azure padrão.

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>Principais etapas de instalação do SAP HANA ao usar o SAP SWPM
Esta seção lista as etapas principais para uma instalação manual do SAP HANA de instância única com o uso do SWPM da SAP para executar uma instalação distribuída do SAP NetWeaver 7.5. As etapas individuais são explicadas em mais detalhes nas capturas de tela neste documento.

1. Como criar uma rede virtual do Azure que inclui as duas VMs de teste.
2. Implante as duas VMs do Azure com sistemas operacionais (em nosso exemplo, SUSE Linux Enterprise Server (SLES) e SLES para aplicativos SAP 12 SP1), de acordo com o modelo do Azure Resource Manager.
3. Anexe dois discos de armazenamento do Azure standard ou premium à VM do servidor de aplicativos (por exemplo, discos de 75 GB ou 500 GB).
4. Anexe discos de armazenamento premium à VM do servidor de HANA DB. Para obter detalhes, confira a seção "Configuração de disco", mais adiante nesta guia.
5. Dependendo dos requisitos de tamanho ou taxa de transferência, anexe vários discos e crie volumes distribuídos por meio de gerenciamento de volumes lógicos ou pela ferramenta de administração de vários dispositivos (MDADM) no nível do sistema operacional dentro da VM.
6. Como criar sistemas de arquivos XFS nos discos anexados/volumes lógicos.
7. Montar os novos sistemas de arquivos XFS no nível do sistema operacional. Use um sistema de arquivos para todos os softwares SAP. Use o sistema de arquivos para o diretório /sapmnt e backups, por exemplo. No servidor de banco de dados do SAP HANA, montar os sistemas de arquivos XFS nos discos de armazenamento premium como /hana e /usr/sap. Esse processo é necessário para impedir o sistema de arquivos raiz fique cheio, já que ele não é grande em VMs Linux do Azure.
8. Insira os endereços IP locais das VMs de teste no arquivo /etc/hosts.
9. Insira o parâmetro **nofail** no arquivo /etc/fstab.
10. Defina parâmetros de kernel do Linux de acordo com a versão do sistema operacional Linux que você está usando. Para obter mais informações, confira as observações SAP apropriadas que abordam HANA e a seção "Parâmetros do Kernel" desta guia.
11. Adicionar espaço de troca.
12. Como opção, instalar uma área de trabalho gráfica nas VMs de teste. Caso contrário, usar uma instalação remota SAPinst.
13. Baixe o software SAP no marketplace do serviço SAP.
14. Instalar a instância ASCS do SAP na VM do servidor de aplicativo.
15. Compartilhar o diretório /sapmnt entre as VMs de teste usando NFS. A VM do servidor de aplicativo é o servidor NFS.
16. Instalar a instância de banco de dados, incluindo o HANA, usando o SWPM na VM do servidor de banco de dados.
17. Instalar o PAS (servidor de aplicativo principal) na VM do servidor de aplicativo.
18. Inicie o Console de gerenciamento do SAP (SAP MC). Conecte-se com o SAP GUI ou HANA Studio, por exemplo.

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>Principais etapas de instalação do SAP HANA ao usar HDBLCM
Esta seção lista as etapas principais para uma instalação manual do SAP HANA de instância única com o uso do SAP HDBLCM para executar uma instalação distribuída do SAP NetWeaver 7.5. As etapas individuais são explicadas em mais detalhes nas capturas de tela em toda esta guia.

1. Como criar uma rede virtual do Azure que inclui as duas VMs de teste.
2. Implante duas VMs do Azure com os sistemas operacionais (em nosso exemplo, SLES e SLES para aplicativos SAP 12 SP1) de acordo com o modelo do Azure Resource Manager.
3. Anexe dois discos de armazenamento do Azure standard ou premium à VM do servidor de aplicativos (por exemplo, discos de 75 GB ou 500 GB).
4. Anexe discos de armazenamento premium à VM do servidor de HANA DB. Para obter detalhes, confira a seção "Configuração de disco", mais adiante nesta guia.
5. Dependendo dos requisitos de tamanho ou taxa de transferência, anexe vários discos e crie volumes distribuídos por meio de gerenciamento de volumes lógicos ou pela ferramenta de administração de vários dispositivos (MDADM) no nível do sistema operacional dentro da VM.
6. Como criar sistemas de arquivos XFS nos discos anexados/volumes lógicos.
7. Montar os novos sistemas de arquivos XFS no nível do sistema operacional. Use um sistema de arquivos para armazenar todo o software SAP e use o outro, por exemplo, para o diretório /sapmnt e backups. No servidor de banco de dados do SAP HANA, montar os sistemas de arquivos XFS nos discos de armazenamento premium como /hana e /usr/sap. Esse processo é necessário para ajudar a impedir que o sistema de arquivos raiz fique cheio, já que ele não é grande em VMs Linux do Azure.
8. Insira os endereços IP locais das VMs de teste no arquivo /etc/hosts.
9. Insira o parâmetro **nofail** no arquivo /etc/fstab.
10. Defina os parâmetros de kernel de acordo com a versão do sistema operacional Linux que você está usando. Para obter mais informações, confira as observações SAP apropriadas que abordam HANA e a seção "Parâmetros do Kernel" desta guia.
11. Adicionar espaço de troca.
12. Como opção, instalar uma área de trabalho gráfica nas VMs de teste. Caso contrário, usar uma instalação remota SAPinst.
13. Baixe o software SAP no marketplace do serviço SAP.
14. Crie um grupo, "sapsys", com o ID de grupo 1001 na VM do servidor de banco de dados do HANA.
15. Instale o SAP HANA na VM do servidor de banco de dados usando o gerenciador de ciclo de vida do banco de dados do HANA (HDBLCM).
16. Instalar a instância ASCS do SAP na VM do servidor de aplicativo.
17. Compartilhar o diretório /sapmnt entre as VMs de teste usando NFS. A VM do servidor de aplicativo é o servidor NFS.
18. Instalar a instância de banco de dados, incluindo o HANA, usando o SWPM na VM do servidor de banco de dados do HANA.
19. Instalar o PAS (servidor de aplicativo principal) na VM do servidor de aplicativo.
20. Inicie o SAP MC. Conecte-se por meio do SAP GUI ou HANA Studio.

## <a name="preparing-azure-vms-for-a-manual-installation-of-sap-hana"></a>Prepare as VMs do Azure para instalação manual do SAP HANA
Esta seção contém os seguintes tópicos:

* Atualizações do sistema operacional
* Configuração de disco
* Parâmetros de kernel
* Sistemas de arquivos
* O arquivo /etc/hosts
* O arquivo /etc/fstab

### <a name="os-updates"></a>Atualizações do sistema operacional
Verifique se há atualizações e correções do sistema operacional Linux antes de instalar o software adicional. Ao instalar um patch, você poderá evitar uma chamada para o suporte técnico.

Certifique-se de que você esteja usando:
* SUSE Linux Enterprise Server para aplicativos SAP.
* Red Hat Enterprise Linux para aplicativos SAP ou Red Hat Enterprise Linux para o SAP HANA. 

Caso não tenha registrado, registre agora a implantação de sistema operacional na sua assinatura do Linux do fornecedor do Linux. Observe que o SUSE tem imagens do sistema operacional para aplicativos SAP que já incluem serviços e que são registrados automaticamente.

Aqui está um exemplo de busca de patches disponíveis para Linux SUSE usando o comando **zypper**:

 `sudo zypper list-patches`

Dependendo do tipo de defeito, os patches são classificados por categoria e gravidade. Os valores usados para categoria são: **segurança**, **recomendado**, **opcional**, **recurso**, **documento** ou **yast**.
Os valores usados para gravidade são: **crítica**, **importante**, **moderada**, **baixa** ou **não especificada**.

O comando **zypper** procura somente as atualizações que os seus pacotes instalados precisam. Por exemplo, você pode usar este comando:

`sudo zypper patch  --category=security,recommended --severity=critical,important`

Você pode adicionar o parâmetro `--dry-run` para testar a atualização sem realmente atualizar o sistema.


### <a name="disk-setup"></a>Configuração de disco
O sistema de arquivos- raiz em uma VM do Linux no Azure tem limite de tamanho. Portanto, é necessário anexar espaço em disco adicional a uma VM do Azure para executar o SAP. O uso de discos de armazenamento padrão do Azure pode ser suficiente para as VMs do Azure do servidor de aplicativos SAP. No entanto, no caso de VMs do Azure DBMS com SAP HANA, o uso de discos de armazenamento premium do Azure é obrigatório para implantações de produção e não produção.

Com base nos [requisitos de armazenamento SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), a seguinte configuração de armazenamento premium do Azure é sugerida: 

| SKU da VM | RAM |  hana/data e hana/log <br /> distribuídos com LVM ou MDADM | /hana/shared | /root volume | /usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

Na configuração de disco sugerida, o volume do log e o volume de dados do HANA devem ser colocados no mesmo conjunto de discos de armazenamento premium do Azure, que são distribuídos com LVM ou MDADM. Não é necessário definir um nível de redundância RAID, pois o armazenamento premium do Azure mantém três imagens de discos por motivos de redundância. Confira, no entanto, os [Requisitos de armazenamento SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) e o [Guia de atualização e instalação do servidor SAP HANA](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm) para ter certeza de que você configurou espaço de armazenamento suficiente. Considere também os volumes de taxa de transferência de outro disco rígido virtual (VHD) dos discos de armazenamento premium do Azure diferentes, conforme documentado no [Armazenamento premium de alto desempenho e discos gerenciados para VMs](https://docs.microsoft.com/azure/storage/storage-premium-storage). 

Você pode adicionar mais discos de armazenamento premium para as VMs de HANA DBMS para armazenar backups de log de transação ou de banco de dados.

Para saber mais informações sobre as duas principais ferramentas de configurar distribuição, confira os seguintes artigos:

* [Configurar RAID de software no Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Configurar o LVM em uma VM Linux no Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Para obter mais informações sobre como anexar discos para VMs do Azure executando o Linux como um sistema operacional convidado, confira [Como adicionar um disco para uma VM do Linux](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

O armazenamento premium do Azure permite definir modos de cache de disco. Para a conjunto de faixa contendo /hana/data e /hana/log, o cache de disco deve ser desabilitado. Para os outros volumes (discos), o modo de cache deve ser definido como **ReadOnly**.

Para saber mais, confira [Armazenamento Premium: armazenamento de alto desempenho para as cargas de trabalho das máquinas virtuais do Azure](../../../storage/common/storage-premium-storage.md).

Para localizar modelos JSON de exemplo para criar VMs, vá para [Modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates).
O modelo vm-simple-sles é um modelo básico. Ele inclui uma seção de armazenamento com um disco de dados de 100 GB adicionais. Esse modelo pode ser usado como base. Você pode adaptar o modelo em sua configuração específica.

>[!Note]
>Observe que é importante anexar o disco de armazenamento do Azure usando um UUID, conforme documentado em [Como executar o SAP NetWeaver em VMs do Linux SUSE no Microsoft Azure](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

No ambiente de teste, dois discos de armazenamento padrão do Azure foram anexados à VM do servidor de aplicativo SAP, como mostra a captura de tela a seguir. Um disco armazenou todos os softwares SAP (incluindo NetWeaver 7.5, SAP GUI e SAP HANA) para a instalação. O segundo disco garantiu espaço livre suficiente para os requisitos adicionais (por exemplo, dados de backup e teste) e fez com que o diretório /sapmnt (ou seja, perfis SAP) fosse compartilhados entre todas as VMs que pertencem ao mesmo cenário SAP.

![A janela de discos do servidor de aplicativos SAP HANA exibe dois discos de dados e seus tamanhos](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>Parâmetros de kernel
O SAP HANA exige configurações específicas de kernel do Linux que não fazem parte das imagens padrão da galeria do Azure e precisam ser configuradas manualmente. Dependendo se você usar SUSE ou Red Hat, os parâmetros podem ser diferentes. As observações SAP listadas anteriormente fornecem informações sobre esses parâmetros. Nas capturas de tela mostradas, foi usado o SUSE Linux 12 SP1. 

SLES para de aplicativos SAP 12 GA e SLES para aplicativos SAP 12 SP1 tem uma ferramenta nova, **tuned-adm**, que substitui a ferramenta antiga **sapconf**. Um perfil especial do SAP HANA está disponível para o **tuned-adm**. Para ajustar o sistema para o SAP HANA, insira o seguinte como um usuário raiz:

   `tuned-adm profile sap-hana`

Para saber mais informações sobre o **tuned-adm**, confira a [documentação do SUSE sobre o tuned-adm](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

Na captura de tela a seguir, você pode ver como **tuned-adm** alteram os valores `transparent_hugepage` e `numa_balancing`, de acordo com as configurações necessárias do SAP HANA.

![A ferramenta tuned-adm altera valores de acordo com as configurações obrigatórias do SAP HANA](./media/hana-get-started/image005.jpg)

Para tornar as configurações de kernel SAP HANA permanentes, use **grub2** no SLES 12. Para saber mais informações sobre **grub2**, acesse a seção [Estrutura de arquivos de configuração](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) da documentação do SUSE.

A captura de tela a seguir mostra como as configurações de kernel foram alteradas no arquivo de configuração e compiladas com o uso do **grub2-mkconfig**:

![Configurações de kernel alteradas no arquivo de configuração e compiladas usando grub2 mkconfig](./media/hana-get-started/image006.jpg)

Outra opção seria alterar as configurações usando o YaST e as configurações do **Carregador de Inicialização** > **Parâmetros de Kernel**:

![A guia de configurações Parâmetros de Kernel no carregador de inicialização do YaST](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>Sistemas de arquivos
A captura de tela a seguir mostra dois sistemas de arquivos que foram criados na VM do servidor de aplicativo SAP usando os dois discos de armazenamento padrão do Azure anexados. Os dois sistemas de arquivos são do tipo XFS e estão montados em /sapdata e /sapsoftware.

Não é obrigatório estruturar os sistemas de arquivos dessa maneira. Você tem outras opções para estruturar o espaço em disco. O mais importante é evitar que o sistema de arquivo raiz fique sem espaço livre.

![Dois sistemas de arquivos criados na VM do servidor de aplicativos SAP](./media/hana-get-started/image008.jpg)

Sobre a VM do SAP HANA DB, durante uma instalação do banco de dados, quando você usa SAPinst (SWPM) e a opção de instalação **típica**, tudo é instalado em /hana e /usr/sap. O local padrão para o backup de log do SAP HANA é /usr/sap. Novamente, como é importante evitar que o sistema de arquivos raiz fique sem espaço de armazenamento, verifique se há espaço livre suficiente em /hana e /usr/sap antes de instalar o SAP HANA usando SWPM.

Para obter uma descrição do layout do sistema de arquivos padrão do SAP HANA, confira o [Guia de atualização e instalação do servidor SAP HANA](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm).

![Outros sistemas de arquivos criados na VM do servidor de aplicativos SAP](./media/hana-get-started/image009.jpg)

Quando você instala o SAP NetWeaver em um SLES/SLES padrão para imagem da galeria do Azure para aplicativos SAP 12 padrão, uma mensagem é exibida dizendo que não há nenhum espaço de permuta, conforme exibe a captura de tela a seguir. Para ignorar essa mensagem, você pode adicionar um arquivo de permuta manualmente usando **dd**, **mkswap** e **swapon**. Para saber como fazer isso, pesquise "Como adicionar um arquivo de permuta manualmente" na seção [Como usar o particionador YaST](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) da documentação do SUSE.

Outra opção é configurar o espaço de troca por meio do agente de VM do Linux. Para saber mais, confira o [Guia de usuário do agente Linux para o Azure](../../linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

![Mensagem pop-up informando que não há espaço de troca suficiente](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>O arquivo /etc/hosts
Antes de começar a instalar o SAP, não se esqueça de incluir os nomes de host e endereços IP das VMs SAP no arquivo /etc/hosts. Implante todas as VMs SAP em uma rede virtual do Azure e use os endereços IP internos, conforme mostrado aqui:

![Nomes de host e endereços IP das VMs SAP listados no arquivo /etc/hosts](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>O arquivo /etc/fstab

É útil adicionar o parâmetro **nofail** para o arquivo fstab. Isso garante que a VM funcionará e não travará durante o processo de inicialização mesmo se algo der errado com os discos. Mas, lembre-se de que o espaço em disco adicional pode não estar disponível e processos podem preencher o sistema de arquivos raiz. Se /hana estiver ausente, o SAP HANA não será iniciado.

![Adicione o parâmetro nofail ao arquivo fstab](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>Área de trabalho GNOME gráfica no SLES 12/SLES para Aplicativos SAP 12
Esta seção contém os seguintes tópicos:

* Instalação da área de trabalho GNOME e o xrdp no SLES 12/SLES para aplicativos SAP 12
* Execução do SAP MC baseado em Java usando o Firefox no SLES 12/SLES para aplicativos SAP 12

Você também pode usar alternativas, como Xterminal ou VNC (não descritos nesta guia).

### <a name="installing-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>Instalação da área de trabalho GNOME e o xrdp no SLES 12/SLES para aplicativos SAP 12
Se o Windows está operando em segundo plano, é possível usar uma área de trabalho gráfica diretamente com as VMs do SAP Linux para executar o Firefox, o Sapinst, o SAP GUI, o SAP MC ou o HANA Studio e conectar-se à VM via protocolo RDP de área de trabalho remota de um computador Windows. Dependendo das políticas da sua empresa sobre como adicionar interfaces gráficas do usuário para produção e não produção de sistemas baseado em Linux, talvez seja interessante instalar GNOME no seu servidor. Como instalar a área de trabalho GNOME em um SLES 12/SLES do Azure para VM dos aplicativos SAP 12:

1. Instale a área de trabalho GNOME digitando o seguinte comando (por exemplo, em uma janela PuTTY):

   `zypper in -t pattern gnome-basic`

2. Instale o xrdp para permitir uma conexão com a VM por meio de RDP:

   `zypper in xrdp`

3. Edite /etc/sysconfig/windowmanager e defina o gerenciador de janelas padrão como GNOME:

   `DEFAULT_WM="gnome"`

4. Execute o comando **chkconfig** para ter certeza de que o xrdp será iniciado automaticamente após a reinicialização:

   `chkconfig -level 3 xrdp on`

5. Se você tiver um problema com a conexão do RDP, tente reiniciar (talvez de uma janela PuTTY):

   `/etc/xrdp/xrdp.sh restart`

6. Se uma reinicialização de xrdp mencionada na etapa anterior não funcionar, procure um arquivo .pid:

   `check /var/run` 

   Procure `xrdp.pid`. Se você achar, remova-o e tentar reiniciar novamente.

### <a name="starting-sap-mc"></a>Inicialização do SAP MC
Depois de instalar a área de trabalho GNOME, a inicialização do SAP MC gráfica baseado em Java no Firefox que está em execução em um SLES 12/SLES do Azure para VM de aplicativos SAP 12 poderá exibir um erro devido à falta do plug-in de navegador Java.

A URL para iniciar o SAP MC é `<server>:5<instance_number>13`.

Para saber mais, confira [Iniciando o console de gerenciamento SAP baseado na Web](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm).

A captura de tela a seguir mostra a mensagem de erro que é exibida quando o plug-in de navegador Java está ausente:

![Mensagem de erro indicando a falta do plug-in de navegador Java](./media/hana-get-started/image013.jpg)

Uma maneira de resolver o problema é simplesmente instalar o plug-in ausente usando o YaST, como mostra a captura de tela a seguir:

![Usando o YaST para instalar o plug-in ausente](./media/hana-get-started/image014.jpg)

Ao reinserir a URL do console de gerenciamento do SAP, você receberá uma mensagem solicitando a ativação do plug-in:

![Caixa de diálogo solicitando a ativação do plug-in](./media/hana-get-started/image015.jpg)

Você também poderá receber uma mensagem de erro sobre um arquivo ausente, javafx.properties. Isso está relacionado ao requisito de ter o Oracle Java 1.8 para SAP GUI 7.4. (Confira [Observação SAP 2059429](https://launchpad.support.sap.com/#/notes/2059424).) Nem a versão Java da IBM nem o pacote openjdk fornecido com SLES/SLES para Aplicativos SAP 12 incluem o arquivo javafx.properties necessário. A solução é baixar e instalar o Java SE 8 da Oracle.

Para obter informações sobre um problema semelhante com openjdk no openSUSE, confira a discussão [Leap SAPGui 7.4 Java para openSUSE 42.1](https://scn.sap.com/thread/3908306).

## <a name="manual-installation-of-sap-hana-swpm"></a>Instalação manual do SAP HANA: SWPM
A série de capturas de tela desta seção mostra as principais etapas para instalar o SAP NetWeaver 7.5 e o SAP HANA SP12 com o uso do SWPM (SAPinst). Como parte de uma instalação NetWeaver 7.5, o SWPM também pode instalar o banco de dados do HANA como uma única instância.

Em um ambiente de teste de exemplo, instalamos apenas um servidor de aplicativos do Advanced Business Application Programming (ABAP). Como mostra a captura de tela a seguir, usamos a opção **Sistema Distribuído** para instalar as instâncias ASCS e de servidor de aplicativos principal em uma VM do Azure e no SAP HANA como o sistema de banco de dados em outra VM do Azure.

![Instâncias ASCS e de servidor de aplicativos principal instaladas usando a opção Sistema Distribuído](./media/hana-get-started/image012.jpg)

Após a instalação da instância do ASCS na VM do servidor de aplicativos e sua definição como "verde" no Console de gerenciamento do SAP (exibido na captura de tela a seguir), o diretório /sapmnt (incluindo o diretório do perfil SAP) precisa ser compartilhado com a VM do servidor SAP HANA DB. A etapa de instalação do banco de dados precisa acessar essas informações. A melhor maneira de fornecer acesso é usando NFS, que pode ser configurado com YaST.

![SAP Management Console mostrando a instância do ASCS instalada na VM do servidor de aplicativos e definida como "verde"](./media/hana-get-started/image016.jpg)

Na VM do servidor de aplicativo, o diretório sapmnt deve ser compartilhado por meio de NFS usando as opções **rw** e **no_root_squash**. Os padrões são **ro** e **root_squash**, o que pode levar a problemas durante a instalação da instância de banco de dados.

![Compartilhamento do diretório /sapmnt por meio de NFS usando as opções rw e no_root_squash](./media/hana-get-started/image017b.jpg)

Como mostra a próxima captura de tela, o compartilhamento de /sapmnt da VM do servidor de aplicativos deve ser configurado na VM do servidor de SAP HANA DB usando o **Cliente NFS** (e o YaST).

![O compartilhamento de /sapmnt configurado usando o cliente NFS](./media/hana-get-started/image018b.jpg)

Para executar uma instalação distribuída do NetWeaver 7.5, (**instância de banco de dados**), como mostra a captura de tela a seguir, entre na VM do servidor SAP HANA DB e inicie o SWPM.

![Instalação de uma instância de banco de dados entrando na VM do servidor de banco de dados do SAP HANA e iniciando o SWPM](./media/hana-get-started/image019.jpg)

Depois de selecionar uma instalação **típica** e o caminho para a mídia de instalação, insira um DB SID, o nome de host, o número de instância e a senha do administrador do sistema do banco de dados.

![A página de entrada do administrador de sistema de banco de dados do SAP HANA](./media/hana-get-started/image035b.jpg)

Insira a senha para o esquema DBACOCKPIT:

![A caixa de entrada de senha para o esquema DBACOCKPIT](./media/hana-get-started/image036b.jpg)

Insira uma pergunta para a senha do esquema SAPABAP1:

![Insira uma pergunta para a senha do esquema SAPABAP1](./media/hana-get-started/image037b.jpg)

Depois que a tarefa é concluída, uma marca de seleção verde é exibida ao lado de cada fase do processo de instalação do BD. É exibida a mensagem "A execução da... Instância de banco de dados foi concluída".

![Janela de tarefa concluída com mensagem de confirmação](./media/hana-get-started/image023.jpg)

Após a instalação bem-sucedida, o console de gerenciamento do SAP também deve mostrar a instância de BD como "verde" e exibir a lista completa de processos do SAP HANA (hdbindexserver, hdbcompileserver e assim por diante).

![Janela do SAP Management Console com lista de processos do SAP HANA](./media/hana-get-started/image024.jpg)

A captura de tela a seguir mostra as partes da estrutura de arquivos no diretório /hana/shared criado pelo SWPM durante a instalação do HANA. Como não há nenhuma opção para especificar um caminho diferente, é importante montar o espaço em disco adicional no diretório /hana antes da instalação do SAP HANA usando SWPM. Isso evita que o sistema de arquivos raiz fique sem espaço livre.

![A estrutura de arquivos do diretório /hana/shared criada durante a instalação do HANA](./media/hana-get-started/image025.jpg)

Esta captura de tela mostra a estrutura de arquivos do diretório /usr/sap:

![A estrutura de arquivo de diretório /usr/sap](./media/hana-get-started/image026.jpg)

A última etapa da instalação da ABAP distribuída é a instância do servidor de aplicativos principal:

![Instalação da ABAP mostrando a instância do servidor de aplicativos principal como a última etapa](./media/hana-get-started/image027b.jpg)

Depois que a instância do servidor de aplicativos principal e o SAP GUI são instalados, use a transação **DBA Cockpit** para confirmar que a instalação do SAP HANA foi concluída corretamente:

![Janela DBA Cockpit confirmando a instalação bem-sucedida](./media/hana-get-started/image028b.jpg)

Como etapa final, primeiro instale o SAP HANA Studio na VM do servidor de aplicativos SAP e conecte-se à instância do SAP HANA que está em execução na VM do servidor de BD:

![Instalando o SAP HANA Studio na VM do servidor de aplicativos SAP](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>Instalação manual do SAP HANA: HDBLCM
Além de instalar o SAP HANA como parte de uma instalação distribuída usando SWPM, primeiro, você pode instalar o HANA autônomo usando HDBLCM. Em seguida, você pode instalar o SAP NetWeaver 7.5, por exemplo. As capturas de tela nesta seção mostram como esse processo funciona.

Para obter mais informações sobre a ferramenta HANA HDBLCM, confira:

* [Choosing the Correct SAP HANA HDBLCM for Your Task (Escolhendo o HDBLCM do SAP HANA correto para sua tarefa)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)
* [SAP HANA Lifecycle Management Tools (Ferramentas de gerenciamento de ciclo de vida do SAP HANA)](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)
* [SAP HANA Server Installation and Update Guide (Guia de instalação e atualização do servidor SAP HANA)](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)

Para evitar problemas com uma configuração de ID de grupo padrão para `\<HANA SID\>adm user` (criado pela ferramenta HDBLCM), defina um novo grupo chamado `sapsys` usando o ID de grupo `1001` antes de instalar o SAP HANA com HDBLCM:

![Grupo novo "sapsys" definido usando a ID de grupo 1001](./media/hana-get-started/image030.jpg)

Quando você inicializa o HDBLCM pela primeira vez, um menu de início simples é exibido. Selecione item 1, **Instalar novo sistema**, como mostra a captura de tela a seguir:

![A opção "Instalar o novo sistema" na janela de início do HDBLCM](./media/hana-get-started/image031.jpg)

A captura de tela a seguir exibe todas as opções essenciais que você selecionou anteriormente.

> [!IMPORTANT]
> Os diretórios nomeados para log e volumes de dados do HANA, o caminho de instalação (hana/shared neste exemplo) e /usr/sap não devem ser parte do sistema de arquivos raiz. Esses diretórios pertencem aos discos de dados do Azure anexados à VM (conforme descrito na seção "Configuração de discos"). Essa abordagem ajuda a evitar que o sistema de arquivos raiz fique sem espaço. Na captura de tela a seguir, você verá que o administrador do sistema HANA tem o ID de usuário `1005` e faz parte do grupo `sapsys` (ID `1001`) definido antes da instalação.

![Lista de todos os principais componentes do SAP HANA selecionados anteriormente](./media/hana-get-started/image032.jpg)

Você pode verificar o `\<HANA SID\>adm user` (`azdadm` na seguinte captura de tela) detalhes no diretório /etc/passwd:

![HANA \<HANA SID\> detalhes do usuário de administração listados no diretório /etc/passwd](./media/hana-get-started/image033.jpg)

Depois de instalar o SAP HANA usando HDBLCM, é possível observar a estrutura de arquivos no SAP HANA Studio, conforme mostrado na seguinte captura de tela. O esquema SAPABAP1, que inclui, por exemplo, todas as tabelas do SAP NetWeaver, ainda não está disponível.

![Estrutura de arquivos do SAP HANA no SAP HANA Studio](./media/hana-get-started/image034.jpg)

Após a instalação do SAP HANA, você poderá instalar o SAP NetWeaver nele. Conforme mostrado nesta captura de tela, a instalação foi executada como uma instalação distribuída usando o SWPM (descrito na seção anterior). Ao instalar a instância de banco de dados usando o SWPM, você insere os mesmos dados usando o HDBLCM (por exemplo, nome de host, SID HANA e número de instância). O SWPM usa a instalação do HANA existente e adiciona mais esquemas.

![Uma instalação distribuída realizada pelo SWPM](./media/hana-get-started/image035b.jpg)

A captura de tela a seguir mostra a etapa de instalação do SWPM onde você insere dados sobre o esquema DBACOCKPIT:

![A etapa de instalação do SWPM onde são inseridos os dados do esquema DBACOCKPIT](./media/hana-get-started/image036b.jpg)

Insira dados sobre o esquema SAPABAP1:

![Inserir dados sobre o esquema SAPABAP1](./media/hana-get-started/image037b.jpg)

Após a conclusão da instalação da instância do banco de dados SWPM, você poderá ver o esquema SAPABAP1 no SAP HANA Studio:

![O esquema SAPABAP1 no SAP HANA Studio](./media/hana-get-started/image038b.jpg)

Por fim, depois que as instalações do servidor de aplicativos SAP e do SAP GUI forem concluídos, você poderá verificar a instância de BD do HANA usando a transação **DBA Cockpit**:

![A instância de BD do HANA verificada com transação DBA Cockpit](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>Downloads de software SAP
Você pode baixar o software do Marketplace do serviço SAP como mostram as capturas de tela a seguir.

Baixar o NetWeaver 7.5 para Linux/HANA:

 ![Janela Instalação e Atualização do Serviço SAP para baixar o NetWeaver 7.5](./media/hana-get-started/image001.jpg)

Baixar o HANA SP12 Platform Edition:

 ![Janela Instalação e Atualização do Serviço SAP para baixar o HANA SP12 Platform Edition](./media/hana-get-started/image002.jpg)

