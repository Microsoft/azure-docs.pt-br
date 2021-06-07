---
title: Visão geral das VMs do Linux no Azure
description: Visão geral das máquinas virtuais do Linux no Azure.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: overview
ms.workload: infrastructure
ms.date: 11/14/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: fe1db967dac8a76119733f930e54a38160395932
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102549831"
---
# <a name="linux-virtual-machines-in-azure"></a>Máquinas virtuais do Linux no Azure

VM (Máquinas Virtuais) do Azure é um dos vários tipos de [recursos de computação sob demanda escalonáveis](/azure/architecture/guide/technology-choices/compute-decision-tree) oferecidos pelo Azure. Normalmente, você escolhe uma VM quando precisar de mais controle sobre o ambiente de computação do que as outras opções oferecem. Este artigo fornece informações sobre o que você deve considerar antes de criar uma VM, como criá-la e como gerenciá-la.

Uma VM do Azure oferece a flexibilidade da virtualização sem a necessidade de comprar e manter o hardware físico que a executa. No entanto, você ainda precisa manter a VM executando tarefas, como configurar, corrigir e instalar o software que será executado nela.

Máquinas virtuais do Azure podem ser usadas de várias maneiras. Alguns exemplos são:

* **Desenvolvimento e teste** – as VMs do Azure oferecem uma rápida e maneira fácil de criar um computador com configurações específicas, necessárias para codificar e testar um aplicativo.
* **Aplicativos na nuvem** – como a demanda por seu aplicativo pode flutuar, pode fazer sentido, em termos econômicos, executá-lo em uma VM no Azure. Você paga por VMs extras quando precisa delas e as desliga quando não são necessárias.
* **Datacenter estendido** – máquinas virtuais em uma rede virtual do Azure podem ser facilmente conectadas à rede de sua organização.

O número de VMs que o aplicativo usa pode ser escalado verticalmente e horizontalmente para atender às suas necessidades.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>O que é necessário pensar antes de criar uma VM?
Sempre há uma infinidade de [considerações de design](/azure/architecture/reference-architectures/n-tier/linux-vm) quando você cria uma infraestrutura de aplicativo no Azure. Estes aspectos de uma VM são importantes a considerar antes de começar:

* Os nomes dos recursos do aplicativo
* O local onde os recursos são armazenados
* O tamanho da VM
* O número máximo de VMs que podem ser criadas
* O sistema operacional que a VM executa
* A configuração da VM após ela ser iniciada
* Os recursos relacionados dos quais a VM precisa

### <a name="locations"></a>Locais
Todos os recursos criados no Azure são distribuídos entre várias [regiões geográficas](https://azure.microsoft.com/regions/) em todo o mundo. Normalmente, a região é chamada **local** quando você cria uma VM. Para uma VM, a localização especifica onde os discos rígidos virtuais são armazenados.

Esta tabela mostra algumas das maneiras de obter uma lista dos locais disponíveis.

| Método | Descrição |
| --- | --- |
| Portal do Azure |Selecione um local na lista quando você criar uma VM. |
| Azure PowerShell |Use o comando [Get-AzLocation](/powershell/module/az.resources/get-azlocation). |
| API REST |Use a operação [Listar locais](/rest/api/resources/subscriptions). |
| CLI do Azure |Use a operação [az account list-locations](/cli/azure/account). |

## <a name="availability"></a>Disponibilidade
O Azure anunciou um Contrato de Nível de Serviço de máquina virtual de única instância de 99,9%, o melhor que há no mercado, desde que você implante a VM com armazenamento premium para todos os discos.  Para sua implantação se qualificar para o Contrato de Nível de Serviço de 99,95% padrão de VM, você ainda precisará implantar duas ou mais VMs que executem sua carga de trabalho dentro de um conjunto de disponibilidade. Um conjunto de disponibilidade garante que suas VMs sejam distribuídas entre vários domínios de falha nos datacenters do Azure, além de serem implantadas em hosts com janelas de manutenção diferentes. O [SLA completo do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) explica a disponibilidade garantida do Azure como um todo.

## <a name="vm-size"></a>Tamanho da VM
O [tamanho](../sizes.md) da VM que você usa é determinado pela carga de trabalho que deseja executar. O tamanho que você escolhe, em seguida, determina fatores como capacidade de processamento, memória e armazenamento. O Azure oferece uma grande variedade de tamanhos para oferecer suporte a muitos tipos de usos.

O Azure cobra um [preço por hora](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) com base no tamanho da VM e do sistema operacional. Para horas parciais, o Azure cobrará somente os minutos usados. O armazenamento terá o preço e será cobrado separadamente.

## <a name="vm-limits"></a>Limites de VM
Sua assinatura do Azure tem [limites de cota](../../azure-resource-manager/management/azure-subscription-service-limits.md) padrão que podem afetar a implantação de muitas VMs para seu projeto. O limite atual por assinatura é de 20 VMs por região. Os limites podem ser aumentados [pelo preenchimento de um tíquete de suporte para solicitar um aumento](../../azure-portal/supportability/resource-manager-core-quotas-request.md)

## <a name="managed-disks"></a>Managed Disks

Os Managed Disks trata da criação da conta de Armazenamento do Azure e do gerenciamento em segundo plano para você, além de garantir que você não tenha que se preocupar com os limites de escalabilidade da conta de armazenamento. Especifique o tamanho do disco e o nível de desempenho (Standard ou Premium) e o Azure cria e gerencia o disco. À medida que você adiciona discos ou dimensiona a VM para cima e para baixo, não é preciso se preocupar com o armazenamento que está sendo usado. Se você estiver criando novas VMs, [use o CLI do Azure](quick-create-cli.md) ou o portal do Azure para criar VMs com SO gerenciado e discos de dados. Caso tenha VMs com discos não gerenciados, você poderá [convertê-las para que tenham suporte do Managed Disks](convert-unmanaged-to-managed-disks.md).

Você também pode gerenciar suas imagens personalizadas em uma conta de armazenamento por região do Azure e usá-las para criar centenas de VMs na mesma assinatura. Para saber mais sobre os Managed Disks, confira a [Visão geral dos Managed Disks](../managed-disks-overview.md).

## <a name="distributions"></a>Distribuições 
O Microsoft Azure dá suporte à execução de várias distribuições populares do Linux fornecidas e mantidas por diversos parceiros.  Você pode encontrar distribuições disponíveis no Azure Marketplace. A Microsoft trabalha ativamente com várias comunidades do Linux para adicionar ainda mais opções à lista de [Distribuições do Linux endossadas pelo Azure](endorsed-distros.md).

Se sua distribuição preferencial do Linux não estiver presente na galeria no momento, você poderá "trazer sua própria VM do Linux" [criando e carregando um VHD do Linux no Azure](create-upload-generic.md).

A Microsoft trabalha junto com parceiros para garantir que as imagens disponíveis sejam atualizadas e otimizadas para um runtime do Azure.  Para obter mais informações sobre as ofertas de parceiros do Azure, confira os seguintes links:

* Linux no Azure – [Distribuições endossadas](endorsed-distros.md)
* SUSE – [Azure Marketplace – SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=suse)
* Red Hat – [Azure Marketplace – Red Hat Enterprise Linux](https://azuremarketplace.microsoft.com/marketplace/apps?search=Red%20Hat%20Enterprise%20Linux)
* Canonical – [Azure Marketplace – Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps/Canonical.UbuntuServer)
* Debian – [Azure Marketplace – Debian](https://azuremarketplace.microsoft.com/marketplace/apps?search=Debian&page=1)
* FreeBSD – [Azure Marketplace – FreeBSD](https://azuremarketplace.microsoft.com/marketplace/apps?search=freebsd&page=1)
* Flatcar – [Azure Marketplace – Flatcar do Contêiner do Linux](https://azuremarketplace.microsoft.com/marketplace/apps?search=Flatcar&page=1)
* RancherOS - [Azure Marketplace - RancherOS](https://azuremarketplace.microsoft.com/marketplace/apps/rancher.rancheros)
* Bitnami - [Bitnami Library para Azure](https://azure.bitnami.com/)
* Mesosphere - [Azure Marketplace - Mesosphere DC/OS no Azure](https://azure.microsoft.com/services/kubernetes-service/mesosphere/)
* Docker – [Azure Marketplace – Imagens do Docker](https://azuremarketplace.microsoft.com/marketplace/apps?search=docker&page=1&filters=virtual-machine-images)
* Jenkins - [Azure Marketplace - CloudBees Jenkins Platform](https://azuremarketplace.microsoft.com/marketplace/apps/cloudbees.cloudbees-core-contact)


## <a name="cloud-init"></a>Cloud-init 

Para obter uma cultura apropriada do DevOps, toda a infraestrutura deve ser codificada.  Quando toda a infraestrutura reside no código, ela pode ser recriada com facilidade.  O Azure funciona com as principais ferramentas de automação, como a Ansible, Chef, SaltStack e Puppet.  O Azure também tem suas próprias ferramentas de automação:

* [Modelos do Azure](create-ssh-secured-vm-from-template.md)
* [Azure `VMaccess`](../extensions/vmaccess.md)

O Azure dá suporte a [cloud-init](https://cloud-init.io/) na maioria das distribuições Linux que dão suporte a ele.  Trabalhamos ativamente com nossos parceiros endossados de distribuição de Linux para termos imagens de cloud-init habilitadas disponíveis no marketplace do Azure. Essas imagens farão com que as implantações e as configurações de cloud-init funcionem perfeitamente com VMs e conjuntos de dimensionamento de máquinas virtuais.

* [Como usar o cloud-init em VMs Linux do Azure](using-cloud-init.md)

## <a name="storage"></a>Armazenamento
* [Introdução ao Armazenamento do Microsoft Azure](../../storage/common/storage-introduction.md)
* [Adicionar um disco a uma VM do Linux usando a CLI do Azure](add-disk.md)
* [Como anexar um disco de dados a uma VM Linux no Portal do Azure](attach-disk-portal.md)

## <a name="networking"></a>Rede
* [Visão geral da Rede Virtual](../../virtual-network/virtual-networks-overview.md)
* [Endereços IP no Azure](../../virtual-network/public-ip-addresses.md)
* [Abertura de portas para uma VM Linux no Azure](nsg-quickstart.md)
* [Criar um nome de domínio totalmente qualificado no portal do Azure](../create-fqdn.md)


## <a name="data-residency"></a>Residência de dadosResidência de dados

No Azure, o recurso para habilitar o armazenamento de dados do cliente em apenas uma região está disponível atualmente apenas na região do Sudeste da Ásia (Singapura), na área geográfica do Pacífico Asiático, e na região Sul do Brasil (Estado de São Paulo), na área geográfica do Brasil. Para todas as outras regiões, os dados do cliente são armazenados na Área geográfica. Para obter mais informações, confira [Central de Confiabilidade](https://azure.microsoft.com/global-infrastructure/data-residency/).


## <a name="next-steps"></a>Próximas etapas

Crie sua primeira VM!

- [Portal](quick-create-portal.md)
- [CLI do Azure](quick-create-cli.md)
- [PowerShell](quick-create-powershell.md)
