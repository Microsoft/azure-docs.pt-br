---
title: "Visão geral das VMs do Linux no Azure | Microsoft Docs"
description: "Descreve os serviços de Computação, Armazenamento e Rede do Azure com máquinas virtuais Linux."
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: rickstercdn
manager: timlt
editor: 
ms.assetid: 7965a80f-ea24-4cc2-bc43-60b574101902
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: overview
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/14/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: d0c6523b4d90c29781d256b2882b0935b50e2af9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-and-linux"></a>Azure e Linux
O Microsoft Azure é uma coleção crescente de serviços de nuvem pública integrados, incluindo análise, Máquinas Virtuais, bancos de dados, mobilidade, rede, armazenamento, e Web&mdash;ideal para hospedar suas soluções.  O Microsoft Azure fornece uma plataforma de computação escalonável que lhe permite pagar apenas pelo que você usa, quando quiser, sem precisar investir em hardware local.  O Azure está pronto quando você está, para escalar suas soluções horizontal e verticalmente, em qualquer escala necessária para atender às necessidades de seus clientes.

Se você estiver familiarizado com os diversos recursos do AWS da Amazon, examine o [documento de mapeamento de definição do Azure versus AWS](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/).

## <a name="regions"></a>Regiões
Os recursos do Microsoft Azure são distribuídos em várias regiões geográficas em todo o mundo.  Uma "região" representa vários datacenters em uma única área geográfica.  Temos 34 regiões disponíveis em todo o mundo com 4 regiões adicionais anunciadas. Como continuamos a expandir nossa cobertura global - mantemos uma lista atualizada de regiões existentes e recentemente anunciadas.

* [Regiões do Azure](https://azure.microsoft.com/regions/)

## <a name="availability"></a>Disponibilidade
Anunciamos um Contrato de Nível de Serviço de máquina virtual de única instância 99,9%, o melhor que há no mercado, desde que você implante a VM com armazenamento premium para todos os discos.  Para sua implantação se qualificar para nosso Contrato de Nível de Serviço de 99,95% padrão de VM, você ainda precisará implantar duas ou mais VMs que executem sua carga de trabalho dentro de um conjunto de disponibilidade. Isso garantirá que suas VMs sejam distribuídas entre vários domínios de falha em nossos datacenters, além de serem implantadas em hosts com janelas de manutenção diferentes. O [SLA completo do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/) explica a disponibilidade garantida do Azure como um todo.

## <a name="managed-disks"></a>Managed Disks

Os Managed Disks trata da criação da conta de Armazenamento do Azure e do gerenciamento em segundo plano para você, além de garantir que você não tenha que se preocupar com os limites de escalabilidade da conta de armazenamento. Basta especificar o tamanho do disco e o nível de desempenho (Standard ou Premium) e o Azure cria e gerencia o disco para você. Mesmo que você adicione discos ou dimensione a VM para cima e para baixo, não é preciso se preocupar com o armazenamento que está sendo usado. Se estiver criando novas VMs, [use a CLI 2.0 do Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou o portal do Azure para criar VMs com discos de dados e SO gerenciados. Caso tenha VMs com discos não gerenciados, você poderá [convertê-las para que tenham suporte do Managed Disks](convert-unmanaged-to-managed-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Você também pode gerenciar suas imagens personalizadas em uma conta de armazenamento por região do Azure e usá-las para criar centenas de VMs na mesma assinatura. Para saber mais sobre Managed Disks, veja a [Managed Disks Overview](../windows/managed-disks-overview.md) (Visão geral do Managed Disks).

## <a name="azure-virtual-machines--instances"></a>Máquinas Virtuais e instâncias do Azure
O Microsoft Azure dá suporte à execução de várias distribuições populares do Linux fornecidas e mantidas por diversos parceiros.  Você encontrará distribuições como Red Hat Enterprise, CentOS, Debian, Ubuntu, CoreOS, RancherOS, FreeBSD e muito mais no Azure Marketplace. Trabalhamos ativamente com várias comunidades do Linux para adicionar ainda mais opções à lista de [Distribuições do Linux endossadas pelo Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Se sua distribuição preferencial do Linux não estiver presente na galeria no momento, você poderá "trazer sua própria VM do Linux" [criando e carregando um VHD do Linux no Azure](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

As máquinas virtuais do Azure permitem que você implante uma ampla gama de soluções de computação de forma ágil. Você pode implantar praticamente qualquer carga de trabalho e qualquer linguagem em praticamente qualquer sistema operacional, Windows, Linux, ou um personalizado criado de nossa lista cada vez maior de parceiros. Ainda não consegue encontrar o que você está procurando?  Não se preocupe: você também pode usar suas próprias imagens locais.

## <a name="vm-sizes"></a>Tamanhos de VM
Ao implantar uma VM no Azure, você selecionará um tamanho de VM de nossa série de tamanhos que seja adequado à sua carga de trabalho. O tamanho também afeta a capacidade de processamento, a memória e o armazenamento da máquina virtual. Você será cobrado com base na quantidade de tempo pelo qual a máquina virtual estiver em execução e consumindo seus recursos alocados. Uma lista completa de [tamanhos de Máquinas Virtuais](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Aqui estão algumas diretrizes básicas para selecionar um tamanho de VM de uma de nossas séries (A, D, DS, G e GS).
* VMs da série A são nossas VMs básicas econômicas para cargas de trabalho leves e cenários de desenvolvimento e teste. Elas estão amplamente disponíveis em todas as regiões e podem se conectar a e usar todos os recursos padrão disponíveis para máquinas virtuais.
* Os tamanhos da série A (A8 - A11) são configurações especiais com uso intensivo de computação adequadas para aplicativos de cluster com computação de alto desempenho.
* As VMs da série D são projetadas para executar aplicativos que exigem maior capacidade de computação e de desempenho de disco temporário. As VMs da série D fornecem processadores mais rápidos, uma maior taxa de memória por núcleo e uma unidade de estado sólido (SSD) para o disco temporário.
* A série Dv2 é a última versão de nossa série D, com uma CPU mais poderosa. A CPU da série Dv2 é aproximadamente 35% mais rápida do que a CPU da série D. Ela se baseia na última geração do processador Intel Xeon® E5-2673 v3 (Haskell) de 2,4 GHz e, com a Intel Turbo Boost Technology 2.0, pode chegar a até 3,2 GHz. A série Dv2 tem as mesmas configurações de memória e disco que a série D.
* As VMs da série G oferecem a maior memória e são executadas em hosts com processadores da família Intel Xeon E5 V3.

Observação: as VMs da série DS e da série GS têm acesso ao Armazenamento Premium: nosso armazenamento de alto desempenho e baixa latência com suporte de SSD para cargas de trabalho com uso intensivo de E/S. O Armazenamento Premium está disponível em determinadas regiões. Para obter mais informações, consulte:

* [Armazenamento Premium: armazenamento de alto desempenho para as cargas de trabalho da máquina virtual do Azure](../../storage/common/storage-premium-storage.md)

## <a name="automation"></a>Automação
Para obter uma cultura apropriada do DevOps, toda a infraestrutura deve ser codificada.  Quando todos a infraestrutura residir no código, será fácil recriar (Servidores Phoenix).  O Azure funciona com as principais ferramentas de automação, como a Ansible, Chef, SaltStack e Puppet.  O Azure também tem suas próprias ferramentas de automação:

* [Modelos do Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [VMAccess do Azure](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

O Azure está implementando o suporte para [cloud-init](http://cloud-init.io/) na maioria das Distribuições Linux que oferecem suporte a ele.  Atualmente, as VMs do Ubuntu Canonical são implantadas com cloud-init habilitado por padrão.  Red Hats RHEL, CentOS e Fedora dão suporte a cloud-init, no entanto, as imagens do Azure mantidas pelo RedHat não têm o cloud-init instalado.  Para usar o cloud-init em uma sistema operacional da família RedHat, você deve criar uma imagem personalizada com cloud-init instalado.

* [Como usar o cloud-init em VMs Linux do Azure](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quotas"></a>Cotas
Cada assinatura do Azure tem limites de cota padrão que podem afetar a implantação de um grande número de VMs para seu projeto. O limite atual por assinatura é de 20 VMs por região.  Os limites de cota podem ser aumentados de forma rápida e fácil com a emissão de um tíquete de suporte para solicitar um aumento de limite.  Para obter mais detalhes sobre os limites de cota:

* [Limites de Serviço da assinatura do Azure](../../azure-subscription-service-limits.md)

## <a name="partners"></a>Parceiros
A Microsoft trabalha em conjunto com nossos parceiros para garantir que as imagens disponíveis sejam atualizadas e otimizadas para um tempo de execução do Azure.  Para saber mais sobre nossos parceiros, verifique a seguir suas páginas no Marketplace.

* Linux no Azure – [Distribuições endossadas](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* SUSE – [Azure Marketplace – SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?search=%27SUSE%27)
* Redhat - [Azure Marketplace - RedHat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)
* Canonical - [Azure Marketplace - Ubuntu Server 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)
* Debian - [Azure Marketplace - Debian 8 "Jessie"](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)
* FreeBSD - [Azure Marketplace - FreeBSD 10.3](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)
* CoreOS - [Azure Marketplace - CoreOS (Stable)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)
* RancherOS - [Azure Marketplace - RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)
* Bitnami - [Bitnami Library para Azure](https://azure.bitnami.com/)
* Mesosphere - [Azure Marketplace - Mesosphere DC/OS no Azure](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)
* Docker - [Azure Marketplace – Serviço de Contêiner do Azure com Docker Swarm](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)
* Jenkins - [Azure Marketplace - CloudBees Jenkins Platform](https://azure.microsoft.com/marketplace/partners/cloudbees/jenkins-platformjenkins-platform/)

## <a name="getting-started-with-linux-on-azure"></a>Introdução ao Linux no Azure
Para começar a usar o Azure, você precisa de uma conta do Azure, da CLI do Azure instalada e de um par de chaves públicas e privadas de SSH.

### <a name="sign-up-for-an-account"></a>Inscrever-se em uma conta
Inscrever-se em uma conta do Azure é a primeira etapa para usar a Nuvem do Azure.  Acesse a página de [Inscrição em conta do Azure](https://azure.microsoft.com/pricing/free-trial/) para começar.

### <a name="install-the-cli"></a>Instalar a CLI
Com sua nova conta do Azure, você pode começar a usar imediatamente o Portal do Azure, que é um painel de administração baseado na Web.  Para gerenciar a Nuvem do Azure por meio da linha de comando, instale a `azure-cli`.  Instale a [CLI do Azure 2.0](/cli/azure/install-azure-cli) em sua estação de trabalho Mac ou Linux.

### <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH
Agora você tem uma conta do Azure, o Portal da Web do Azure e a CLI do Azure.  A próxima etapa é criar um par de chaves SSH que será usado para SSH no Linux sem usar uma senha.  [Crie chaves SSH no Linux e Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para habilitar logons sem senha e obter mais segurança.

### <a name="create-a-vm-using-the-cli"></a>Criar uma VM usando a CLI
Criar uma VM do Linux usando a CLI é uma maneira rápida de implantar uma VM sem sair do terminal no qual você está trabalhando.  Tudo o que você pode especificar no portal da Web está disponível por meio de um sinalizador ou opção de linha de comando.  

* [Criar uma VM Linux usando a CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="create-a-vm-in-the-portal"></a>Criar uma VM no portal
Criar uma VM do Linux no Portal da Web do Azure é uma maneira fácil de apontar e clicar nas diversas opções a fim de chegar a uma implantação.  Em vez de usar sinalizadores ou opções de linha de comando, é possível exibir um layout de Web interessante com várias opções e configurações.  Todos os itens disponíveis por meio da interface de linha de comando também estão disponíveis no portal.

* [Criar uma VM Linux usando o Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="login-using-ssh-without-a-password"></a>Fazer logon usando SSH sem uma senha
Agora, a VM está em execução no Azure e você está pronto para fazer logon.  Usar senhas para fazer logon via SSH é inseguro e demorado.  Usar chaves SSH é a maneira mais segura, e também a mais rápida, de fazer logon.  Quando você cria sua VM do Linux por meio do portal ou da CLI, você tem duas opções de autenticação.  Se você escolher uma senha para o SSH, o Azure configurará a VM para permitir logons por meio de senhas.  Se você optar por usar uma chave pública SSH, o Azure configurará a VM para permitir somente os logons por meio de chaves SSH, e desabilita logons com senha. Para proteger sua VM do Linux, permitindo apenas logons com chave SSH, use a opção de chave pública SSH durante a criação da VM no portal ou na CLI.

## <a name="related-azure-components"></a>Componentes relacionados do Azure
## <a name="storage"></a>Armazenamento
* [Introdução ao Armazenamento do Microsoft Azure](../../storage/common/storage-introduction.md)
* [Adicionar um disco a uma VM do Linux usando a CLI do Azure](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Como anexar um disco de dados a uma VM Linux no Portal do Azure](attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="networking"></a>Rede
* [Visão geral da Rede Virtual](../../virtual-network/virtual-networks-overview.md)
* [Endereços IP no Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Abertura de portas para uma VM Linux no Azure](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar um nome de domínio totalmente qualificado no portal do Azure](portal-create-fqdn.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="containers"></a>Contêineres
* [Máquinas virtuais e contêineres no Azure](containers.md)
* [Introdução ao Serviço de Contêiner do Azure](../../container-service/container-service-intro.md)
* [Implantar um cluster do Serviço de Contêiner do Azure](../../container-service/dcos-swarm/container-service-deployment.md)

## <a name="next-steps"></a>Próximas etapas
Agora você tem uma visão geral do Linux no Azure.  A próxima etapa é mergulhar de cabeça e criar algumas VMs!

* [Explore nossa lista crescente de Scripts de Exemplo para tarefas comuns via AzureCLI](cli-samples.md)
