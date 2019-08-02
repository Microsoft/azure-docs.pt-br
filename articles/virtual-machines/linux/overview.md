---
title: Visão geral das VMs do Linux no Azure | Microsoft Docs
description: Descreve os serviços de Computação, Armazenamento e Rede do Azure com máquinas virtuais Linux.
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: rickstercdn
manager: gwallace
editor: ''
ms.assetid: 7965a80f-ea24-4cc2-bc43-60b574101902
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: overview
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/29/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: 9d33b478cb848724d0b3747761a99a1269d58b6e
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671049"
---
# <a name="azure-and-linux"></a>Azure e Linux
O Microsoft Azure é uma coleção crescente de serviços de nuvem públicos e integrados, incluindo análise, máquinas virtuais, bancos de dados, dispositivo móvel, rede, armazenamento e Web&mdash;, sendo ideal para hospedar suas soluções.  O Microsoft Azure fornece uma plataforma de computação escalonável que lhe permite pagar apenas pelo que você usa, quando quiser, sem precisar investir em hardware local.  O Azure está pronto quando você está, para escalar suas soluções horizontal e verticalmente, em qualquer escala necessária para atender às necessidades de seus clientes.

Se você estiver familiarizado com os diversos recursos do AWS da Amazon, examine o [documento de mapeamento de definição do Azure versus AWS](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/).

## <a name="regions"></a>Regiões
Os recursos do Microsoft Azure são distribuídos em várias regiões geográficas em todo o mundo.  Uma "região" representa vários datacenters em uma única área geográfica. O Azure atualmente (desde agosto de 2018) tem 42 regiões geralmente disponíveis em todo o mundo com 12 regiões adicionais anunciadas, com mais regiões globais do que qualquer outro provedor de nuvem. Uma lista atualizada de regiões existentes e recentemente anunciadas pode ser encontrada na seguinte página:

* [Regiões do Azure](https://azure.microsoft.com/regions/)

## <a name="availability"></a>Disponibilidade
O Azure anunciou um Contrato de Nível de Serviço de máquina virtual de única instância de 99,9%, o melhor que há no mercado, desde que você implante a VM com armazenamento premium para todos os discos.  Para sua implantação se qualificar para o Contrato de Nível de Serviço de 99,95% padrão de VM, você ainda precisará implantar duas ou mais VMs que executem sua carga de trabalho dentro de um conjunto de disponibilidade. Um conjunto de disponibilidade garante que suas VMs sejam distribuídas entre vários domínios de falha nos datacenters do Azure, além de serem implantadas em hosts com janelas de manutenção diferentes. O [SLA completo do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) explica a disponibilidade garantida do Azure como um todo.

## <a name="managed-disks"></a>Managed Disks

Os Managed Disks trata da criação da conta de Armazenamento do Azure e do gerenciamento em segundo plano para você, além de garantir que você não tenha que se preocupar com os limites de escalabilidade da conta de armazenamento. Especifique o tamanho do disco e o nível de desempenho (Standard ou Premium) e o Azure cria e gerencia o disco. À medida que você adiciona discos ou dimensiona a VM para cima e para baixo, não é preciso se preocupar com o armazenamento que está sendo usado. Se você estiver criando novas VMs, [use o CLI do Azure](quick-create-cli.md) ou o portal do Azure para criar VMs com SO gerenciado e discos de dados. Caso tenha VMs com discos não gerenciados, você poderá [convertê-las para que tenham suporte do Managed Disks](convert-unmanaged-to-managed-disks.md).

Você também pode gerenciar suas imagens personalizadas em uma conta de armazenamento por região do Azure e usá-las para criar centenas de VMs na mesma assinatura. Para saber mais sobre os Managed Disks, confira a [Visão geral dos Managed Disks](../linux/managed-disks-overview.md).

## <a name="azure-virtual-machines--instances"></a>Máquinas Virtuais e instâncias do Azure
O Microsoft Azure dá suporte à execução de várias distribuições populares do Linux fornecidas e mantidas por diversos parceiros.  Você pode encontrar distribuições como Red Hat Enterprise, CentOS, SUSE Linux Enterprise, Debian, Ubuntu, CoreOS, RancherOS, FreeBSD e muito mais no Azure Marketplace. A Microsoft trabalha ativamente com várias comunidades do Linux para adicionar ainda mais opções à lista de [Distribuições do Linux endossadas pelo Azure](endorsed-distros.md).

Se sua distribuição preferencial do Linux não estiver presente na galeria no momento, você poderá "trazer sua própria VM do Linux" [criando e carregando um VHD do Linux no Azure](create-upload-generic.md).

As máquinas virtuais do Azure permitem que você implante uma ampla gama de soluções de computação de forma ágil. Você pode implantar praticamente qualquer carga de trabalho e qualquer linguagem em praticamente qualquer sistema operacional, Windows, Linux ou um personalizado criado de nossa lista cada vez maior de parceiros. Ainda não consegue encontrar o que você está procurando?  Não se preocupe: você também pode usar suas próprias imagens locais.

## <a name="vm-sizes"></a>Tamanhos de VM
O [tamanho](sizes.md) da VM que você usa é determinado pela carga de trabalho que deseja executar. O tamanho que você escolhe, em seguida, determina fatores como capacidade de processamento, memória e armazenamento. O Azure oferece uma grande variedade de tamanhos para oferecer suporte a muitos tipos de usos.

O Azure cobra um [preço por hora](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) com base no tamanho da VM e do sistema operacional. Para horas parciais, o Azure cobrará somente os minutos usados. O armazenamento terá o preço e será cobrado separadamente.

## <a name="automation"></a>Automação
Para obter uma cultura apropriada do DevOps, toda a infraestrutura deve ser codificada.  Quando todos a infraestrutura residir no código, será fácil recriar (Servidores Phoenix).  O Azure funciona com as principais ferramentas de automação, como a Ansible, Chef, SaltStack e Puppet.  O Azure também tem suas próprias ferramentas de automação:

* [Modelos do Azure](create-ssh-secured-vm-from-template.md)
* [VMAccess do Azure](using-vmaccess-extension.md)

O Azure está implementando o suporte para [cloud-init](https://cloud-init.io/) na maioria das Distribuições Linux que oferecem suporte a ele.  Atualmente, as VMs do Ubuntu Canonical são implantadas com cloud-init habilitado por padrão.  RHEL do Red Hat, CentOS e Fedora dão suporte ao cloud-init, no entanto, as imagens do Azure mantidas pelo Red Hat não têm atualmente o cloud-init instalado.  Para usar o cloud-init em um SO da família Red Hat, você deve criar uma imagem personalizada com o cloud-init instalado.

* [Como usar o cloud-init em VMs Linux do Azure](using-cloud-init.md)

## <a name="quotas"></a>Cotas
Cada assinatura do Azure tem limites de cota padrão que podem afetar a implantação de um grande número de VMs para seu projeto. O limite atual por assinatura é de 20 VMs por região.  Os limites de cota podem ser aumentados de forma rápida e fácil com a emissão de um tíquete de suporte para solicitar um aumento de limite.  Para obter mais detalhes sobre os limites de cota:

* [Limites de Serviço da assinatura do Azure](../../azure-subscription-service-limits.md)

## <a name="partners"></a>Parceiros
A Microsoft trabalha junto com parceiros para garantir que as imagens disponíveis sejam atualizadas e otimizadas para um tempo de execução do Azure.  Para obter mais informações sobre os parceiros do Azure, confira os links a seguir:

* Linux no Azure – [Distribuições endossadas](endorsed-distros.md)
* SUSE – [Azure Marketplace – SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/marketplace/apps/SUSE.SLES?tab=Overview)
* Red Hat – [Azure Marketplace – Red Hat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)
* Canonical - [Azure Marketplace - Ubuntu Server 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)
* Debian - [Azure Marketplace - Debian 8 "Jessie"](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)
* FreeBSD - [Azure Marketplace - FreeBSD 10.4](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD104)
* CoreOS - [Azure Marketplace - CoreOS (Stable)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)
* RancherOS - [Azure Marketplace - RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)
* Bitnami - [Bitnami Library para Azure](https://azure.bitnami.com/)
* Mesosphere - [Azure Marketplace - Mesosphere DC/OS no Azure](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)
* Docker - [Azure Marketplace – Serviço de Contêiner do Azure com Docker Swarm](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)
* Jenkins - [Azure Marketplace - CloudBees Jenkins Platform](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cloudbees.cloudbees-core-contact)

## <a name="getting-started-with-linux-on-azure"></a>Introdução ao Linux no Azure
Para começar a usar o Azure, você precisa de uma conta do Azure, da CLI do Azure instalada e de um par de chaves públicas e privadas de SSH.

### <a name="sign-up-for-an-account"></a>Inscrever-se em uma conta
Inscrever-se em uma conta do Azure é a primeira etapa para usar a Nuvem do Azure.  Acesse a página de [Inscrição em conta do Azure](https://azure.microsoft.com/pricing/free-trial/) para começar.

### <a name="install-the-cli"></a>Instalar a CLI
Com sua nova conta do Azure, você pode começar a usar imediatamente o Portal do Azure, que é um painel de administração baseado na Web.  Para gerenciar a Nuvem do Azure por meio da linha de comando, instale a `azure-cli`.  Instale o [Azure CLI](/cli/azure/install-azure-cli) em sua estação de trabalho Mac ou Linux.

### <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH
Agora você tem uma conta do Azure, o Portal da Web do Azure e a CLI do Azure.  A próxima etapa é criar um par de chaves SSH que será usado para SSH no Linux sem usar uma senha.  [Crie chaves SSH no Linux e Mac](mac-create-ssh-keys.md) para habilitar logons sem senha e obter mais segurança.

### <a name="create-a-vm-using-the-cli"></a>Criar uma VM usando a CLI
Criar uma VM do Linux usando a CLI é uma maneira rápida de implantar uma VM sem sair do terminal no qual você está trabalhando.  Tudo o que você pode especificar no portal da Web está disponível por meio de um sinalizador ou opção de linha de comando.  

* [Criar uma VM Linux usando a CLI](quick-create-cli.md)

### <a name="create-a-vm-in-the-portal"></a>Criar uma VM no portal
Criar uma VM do Linux no Portal da Web do Azure é uma maneira fácil de apontar e clicar nas diversas opções a fim de chegar a uma implantação.  Em vez de usar sinalizadores ou opções de linha de comando, é possível exibir um layout de Web interessante com várias opções e configurações.  Todos os itens disponíveis por meio da interface de linha de comando também estão disponíveis no portal.

* [Criar uma VM Linux usando o Portal](quick-create-portal.md)

### <a name="log-in-using-ssh-without-a-password"></a>Fazer logon usando SSH sem uma senha
Agora, a VM está em execução no Azure e você está pronto para fazer logon.  Usar senhas para fazer logon via SSH é inseguro e demorado.  Usar chaves SSH é a maneira mais segura, e também a mais rápida, de fazer logon.  Quando você cria sua VM do Linux por meio do portal ou da CLI, você tem duas opções de autenticação.  Se você escolher uma senha para o SSH, o Azure configurará a VM para permitir logons por meio de senhas.  Se você optar por usar uma chave pública SSH, o Azure configurará a VM para permitir somente os logons por meio de chaves SSH, e desabilita logons com senha. Para proteger sua VM do Linux, permitindo apenas logons com chave SSH, use a opção de chave pública SSH durante a criação da VM no portal ou na CLI.

## <a name="related-azure-components"></a>Componentes relacionados do Azure
## <a name="storage"></a>Armazenamento
* [Introdução ao Armazenamento do Microsoft Azure](../../storage/common/storage-introduction.md)
* [Adicionar um disco a uma VM do Linux usando a CLI do Azure](add-disk.md)
* [Como anexar um disco de dados a uma VM Linux no Portal do Azure](attach-disk-portal.md)

## <a name="networking"></a>Rede
* [Visão geral da Rede Virtual](../../virtual-network/virtual-networks-overview.md)
* [Endereços IP no Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Abertura de portas para uma VM Linux no Azure](nsg-quickstart.md)
* [Criar um nome de domínio totalmente qualificado no portal do Azure](portal-create-fqdn.md)

## <a name="containers"></a>Contêineres
* [Máquinas virtuais e contêineres no Azure](containers.md)
* [Introdução ao Serviço de Contêiner do Azure](../../container-service/container-service-intro.md)
* [Implantar um cluster do Serviço de Contêiner do Azure](../../container-service/dcos-swarm/container-service-deployment.md)

## <a name="next-steps"></a>Próximas etapas
Agora você tem uma visão geral do Linux no Azure.  A próxima etapa é mergulhar de cabeça e criar algumas VMs!

* [Explore a lista crescente de exemplos de scripts para tarefas comuns via AzureCLI](cli-samples.md)
