---
title: "Máquinas virtuais Linux do Azure e Armazenamento do Azure | Microsoft Docs"
description: "Descreve o Armazenamento Standard e Premium do Azure e Managed Disks e Unmanaged Disks com máquinas virtuais Linux."
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: d364c69e-0bd1-4f80-9838-bbc0a95af48c
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 2/7/2017
ms.author: rasquill
ms.openlocfilehash: 5e8a62bb180de7288531139594cb61440cab04c5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-and-linux-vm-storage"></a>Armazenamento de VM do Linux e do Azure
O Armazenamento do Azure é a solução de armazenamento em nuvem para aplicativos modernos que dependem de durabilidade, disponibilidade e escalabilidade para atender às necessidades dos clientes.  Além de permitir que os desenvolvedores criem aplicativos de grande escala para dar suporte a novos cenários, o Armazenamento do Azure também fornece a base do armazenamento das Máquinas Virtuais do Azure.

## <a name="managed-disks"></a>Managed Disks

As VMs do Azure agora estão disponíveis usando [Azure Managed Disks](../windows/managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), que permite que você crie suas VMs sem criar ou gerenciar quaisquer [contas do Armazenamento do Azure](../../storage/common/storage-introduction.md) por conta própria. Você especifica se deseja armazenamento Premium ou Standard e o tamanho máximo do disco, e o Azure cria os discos da VM para você. As máquinas virtuais com Managed Disks têm vários recursos importantes, incluindo:

- Suporte de dimensionamento automático. O Azure cria os discos e gerencia o armazenamento subjacente para dar suporte a até 10.000 discos por assinatura.
- Maior confiabilidade com Conjuntos de Disponibilidade. O Azure garante que os discos de VM sejam isolados automaticamente uns dos outros em Conjuntos de Disponibilidade.
- Maior controle de acesso. Os Managed Disks expõem uma variedade de operações controladas pelo [RBAC (Controle de Acesso Baseado em Função do Azure)](../../active-directory/role-based-access-control-what-is.md).

Os preços dos Managed Disks são diferentes dos discos não gerenciados. Para obter essas informações, veja [Preços e cobrança para Managed Disks](../windows/managed-disks-overview.md#pricing-and-billing).

Você pode converter máquinas virtuais existentes que usam discos não gerenciados para usar discos gerenciados com [az vm convert](/cli/azure/vm#convert). Para saber mais, veja [Como converter uma VM Linux de discos não gerenciados em Azure Managed Disks](convert-unmanaged-to-managed-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Você não pode converter um disco não gerenciado em um disco gerenciado se o disco não gerenciado está em uma conta de armazenamento que está, ou esteve, [criptografada usando criptografia de serviço de armazenamento (SSE) do Azure](../../storage/common/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). As etapas a seguir especificam como converter discos não gerenciados que estão, ou estiveram, em uma conta de armazenamento criptografada:

- Copie o VHD (disco rígido virtual) com [az storage blob copy start](/cli/azure/storage/blob/copy#start) para uma conta de armazenamento que nunca foi habilitada para a Criptografia do Serviço de Armazenamento do Azure.
- Crie uma VM que usa discos gerenciados e especifique este arquivo VHD durante a criação com [az vm create](/cli/azure/vm#create), ou
- Anexe o VHD copiado com [az vm disk attach](/cli/azure/vm/disk#attach) a uma VM em execução com discos gerenciados.


## <a name="azure-storage-standard-and-premium"></a>Armazenamento do Azure: Standard e Premium
As VMs do Azure - usando Managed Disks ou discos não gerenciados — podem ser criadas em discos de armazenamento standard ou em discos de armazenamento premium. Ao usar o portal para escolher sua VM, você deve ativar uma lista suspensa na tela **Noções básicas** para exibir os discos standard e premium. Quando alternado para SSD, apenas as VMs habilitadas para o armazenamento premium serão exibidas, tudo com o apoio de unidades SSD.  Quando alternado para o HDD, serão mostradas VMs habilitadas para o armazenamento standard, com unidades de disco de rotação, juntamente com VMs de armazenamento premium apoiadas por SSD.

Ao criar uma VM da `azure-cli` você pode escolher entre standard e premium ao escolher o tamanho da VM por meio do sinalizador de cli `-z` ou `--vm-size`.

## <a name="creating-a-vm-with-a-managed-disk"></a>Criação de uma máquina virtual com um Managed Disk

O exemplo a seguir exige a CLI 2.0 do Azure, que pode ser [instalada aqui](/cli/azure/install-azure-cli).

Primeiro, crie um grupo de recursos para gerenciar os recursos com [az group create](/cli/azure/group#create):

```azurecli
az group create --location westus --name myResourceGroup
```

Agora, crie a VM com [az vm create](/cli/azure/vm#create). Especifique um argumento `--public-ip-address-dns-name` exclusivo, pois `mypublicdns` provavelmente já está sendo usado.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --public-ip-address-dns-name mypublicdns
```

O exemplo anterior cria uma VM com um disco gerenciado em uma conta de armazenamento Standard. Para usar uma conta de armazenamento Premium, adicione o argumento `--storage-sku Premium_LRS`, como no exemplo a seguir:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --public-ip-address-dns-name mypublicdns \
    --storage-sku Premium_LRS
```

## <a name="standard-storage"></a>Armazenamento Standard
O Armazenamento Standard do Azure é o tipo padrão de armazenamento.  O Armazenamento Standard é econômico e, ainda assim, eficaz.  

## <a name="premium-storage"></a>Armazenamento Premium
O Armazenamento Premium do Azure dá suporte de disco de alto desempenho e baixa latência para máquinas virtuais executando cargas de trabalho intensivas para entradas e saídas. Discos de VM (máquina virtual) que usam o armazenamento Premium armazenam dados em SSDs (unidades de estado sólido). Você pode migrar os discos de VM do seu aplicativo para o Armazenamento Premium do Azure para aproveitar a velocidade e o desempenho desses discos.

Recursos de Armazenamento Premium:

* Discos de Armazenamento Premium: o Armazenamento Premium do Azure dá suporte a discos de VM que podem ser anexados a VMs do Azure das séries DS, DSv2 ou GS.
* Blob de Páginas Premium: o Armazenamento Premium dá suporte a Blobs de Páginas do Azure, que são usados para manter discos persistentes para VMs (Máquinas Virtuais) do Azure.
* Armazenamento com Redundância Local Premium: uma conta de Armazenamento Premium só dá suporte ao LRS (Armazenamento com Redundância Local) como a opção de replicação e mantém três cópias de dados em uma única região.
* [Armazenamento Premium](../../storage/common/storage-premium-storage.md)

## <a name="premium-storage-supported-vms"></a>VMs com suporte do Armazenamento Premium
O Armazenamento Premium oferece suporte às VMs (Máquinas Virtuais) do Azure das séries DS, DSv2, GS e Fs. Você pode usar discos de armazenamento Standard e Premium com VMs que têm suporte do Armazenamento Premium. Mas não é possível usar discos de Armazenamento Premium com séries de VM que não são compatíveis com o Armazenamento Premium.

A seguir estão as distribuições do Linux que são validadas com o Armazenamento Premium.

| Distribuição | Versão | Kernel com suporte |
| --- | --- | --- |
| Ubuntu |12.04 |3.2.0-75.110+ |
| Ubuntu |14.04 |3.13.0-44.73+ |
| Debian |7.x, 8.x |3.16.7-ckt4-1+ |
| SLES |SLES 12 |3.12.36-38.1+ |
| SLES |SLES 11 SP4 |3.0.101-0.63.1+ |
| CoreOS |584.0.0+ |3.18.4+ |
| Centos |6.5, 6.6, 6.7, 7.0, 7.1 |3.10.0-229.1.2.el7+ |
| RHEL |6.8+, 7.2+ | |

## <a name="azure-file-storage"></a>Armazenamento de arquivos do Azure
O armazenamento de arquivos do Azure oferece compartilhamentos de arquivos na nuvem usando o protocolo SMB padrão. Com os Arquivos do Azure, você pode migrar para o Azure os aplicativos empresariais que dependam de servidores de arquivos. Os aplicativos em execução no Azure podem facilmente montar compartilhamentos de arquivos a partir das máquinas virtuais do Azure executando o Linux. E com a versão mais recente do Armazenamento de arquivos, também é possível montar um compartilhamento de arquivos por meio de um aplicativo local que dá suporte ao SMB 3.0.  Como os compartilhamentos de arquivos são compartilhamentos do SMB, você pode acessá-los por meio de APIs standard do sistema de arquivos.

O armazenamento de arquivos baseia-se na mesma tecnologia de armazenamento de Blobs, Tabelas e Filas e, portanto, o Armazenamento de arquivos oferece a disponibilidade, a durabilidade, a escalabilidade e a redundância geográfica existentes e incorporadas à plataforma de armazenamento do Azure. Para obter detalhes os destinos e os limites do desempenho do Armazenamento de arquivos, veja Escalabilidade e metas de desempenho do Armazenamento do Azure.

* [Como utilizar o Armazenamento de Arquivos do Azure com Linux](../../storage/files/storage-how-to-use-files-linux.md)

## <a name="hot-storage"></a>Armazenamento Dinâmico
A camada de armazenamento dinâmica do Azure é otimizada para armazenar dados acessados com frequência.  O armazenamento dinâmico é o tipo de armazenamento padrão para repositórios de blob.

## <a name="cool-storage"></a>Armazenamento Estático
A camada de armazenamento estática do Azure é otimizada para armazenar dados acessados com menos frequência e de longa duração. Alguns casos de uso de exemplo para armazenamento estático incluem backups, conteúdo de mídia, dados científicos, dados de conformidade e dados de arquivamento. Em geral, todos os dados raramente acessados serão candidatos perfeitos para o armazenamento estático.

|  | camada de armazenamento dinâmica | camada de armazenamento estática |
|:--- |:---:|:---:|
| Disponibilidade |99,9% |99% |
| Disponibilidade (leituras de RA-GRS) |99,99% |99,9% |
| Encargos de uso |Custos de armazenamento maiores |Custos de armazenamento menores |
| Menores custos |Maiores custos | |
| de acesso e de transação |de acesso e de transação | |

## <a name="redundancy"></a>Redundância
Os dados em sua conta de armazenamento do Microsoft Azure sempre são replicados para garantir durabilidade e alta disponibilidade, cumprindo o SLA do Armazenamento do Azure mesmo diante de falhas transitórias de hardware.

Quando você cria uma conta de armazenamento, deve selecionar uma das seguintes opções de replicação:

* Armazenamento com redundância local (LRS)
* Armazenamento com redundância de zona (ZRS)
* Armazenamento com redundância geográfica (GRS)
* Armazenamento com redundância geográfica com acesso de leitura (RA-GRS)

### <a name="locally-redundant-storage"></a>Armazenamento com redundância local
O LRS (armazenamento com redundância local) replica seus dados dentro da região em que você criou sua conta de armazenamento. Para maximizar a durabilidade, todas as solicitações feitas nos dados de sua conta de armazenamento são replicadas três vezes. Essas três réplicas residem em domínios de falha e domínios de atualização separados.  Uma solicitação retorna com êxito depois de ter sido gravada para todas as três réplicas.

### <a name="zone-redundant-storage"></a>Armazenamento com redundância de zona
O ZRS (armazenamento com redundância de zona) é replicado três vezes por duas ou três instalações, em uma única região ou em duas regiões, proporcionando maior durabilidade que o LRS. Se sua conta de armazenamento tiver ZRS habilitado, seus dados são duráveis mesmo no caso de falha em uma das instalações.

### <a name="geo-redundant-storage"></a>Armazenamento com redundância geográfica
O GRS (armazenamento com redundância geográfica) replica seus dados para uma região secundária a centenas de quilômetros da região primária. Se sua conta de armazenamento tem GRS habilitado, seus dados serão duráveis mesmo no caso de uma interrupção regional completa ou um desastre no qual a região principal não possa ser recuperada.

### <a name="read-access-geo-redundant-storage"></a>Armazenamento com redundância geográfica com acesso de leitura
O RA-GRS (armazenamento com redundância geográfica de acesso de leitura) maximiza a disponibilidade da sua conta de armazenamento, fornecendo acesso somente leitura aos dados no local secundário, além de replicação em duas regiões fornecido por GRS. No caso de os dados ficarem indisponíveis na região primária, seu aplicativo poderá ler os dados da região secundária.

Para se aprofundar em redundância de armazenamento do Azure, consulte:

* [Replicação de Armazenamento do Azure](../../storage/common/storage-redundancy.md)

## <a name="scalability"></a>Escalabilidade
O Armazenamento do Azure é extremamente escalonável para que você possa armazenar e processar centenas de terabytes de dados para oferecer suporte a cenários de big data, necessários para análise científica e financeira e aplicativos de mídia. Ou você pode armazenar pequenas quantidades de dados necessários para um site de pequena empresa. Quaisquer que sejam suas necessidades, você paga apenas pelos dados que está armazenando. O Armazenamento do Azure atualmente armazena dezenas de trilhões de objetos exclusivos de clientes e manipula milhões de solicitações por segundo em média.

Para contas de armazenamento standard: uma conta de armazenamento standard tem uma taxa de solicitação total máxima de 20.000 IOPS. O total de IOPS em todos os discos da máquina virtual de uma conta de armazenamento padrão não deve exceder esse limite.

Para contas de armazenamento premium: uma conta de armazenamento premium tem uma taxa de transferência total máxima de 50 Gbps. A taxa de transferência total de todos os discos da VM não deve exceder esse limite.

## <a name="availability"></a>Disponibilidade
Asseguramos que, em pelo menos 99,99% (99,9% para a Camada de Acesso Estática) das vezes, processaremos com êxito as solicitações de leitura de dados em contas do RA-GRS (Armazenamento com Redundância Geográfica com Acesso de Leitura), desde que as tentativas com falha de leitura de dados da região primária sejam repetidas na região secundária.

Asseguramos que, em pelo menos 99,9% (99% para a Camada de Acesso Estática) das vezes, processaremos com êxito as solicitações de leitura de dados em contas do LRS (Armazenamento com Redundância Local), do ZRS (Armazenamento com Redundância de Zona) e do GRS (Armazenamento com Redundância Geográfica).

Asseguramos que, em pelo menos 99,9% (99% para a Camada de Acesso Estática) das vezes, processaremos com êxito as solicitações de gravação de dados em contas do LRS (Armazenamento com Redundância Local), do ZRS (Armazenamento com Redundância de Zona), do GRS (Armazenamento com Redundância Geográfica) e do RA-GRS (Armazenamento com Redundância Geográfica com Acesso de Leitura).

* [SLA do Azure para Armazenamento](https://azure.microsoft.com/support/legal/sla/storage/v1_1/)

## <a name="regions"></a>Regiões
O Azure está disponível em 30 regiões do mundo e anunciou planos para outras 4 regiões. A expansão geográfica é uma prioridade para o Azure, porque ela permite que seus clientes alcancem maior desempenho e dá suporte aos seus requisitos e preferências em relação à localização de dados.  A Alemanha é a região mais recente a iniciar o Azure.

A Microsoft Cloud Alemanha fornece uma opção diferenciada para os serviços Microsoft Cloud já disponíveis na Europa, criando maiores oportunidades de inovação e crescimento econômico para os parceiros e clientes altamente regulamentados da Alemanha, da UE (União Europeia) e da EFTA (Associação Europeia de Livre Comércio).

Os dados de cliente nesses novos datacenters, em Magdeburg e Frankfurt, são gerenciados sob o controle de um administrador de dados, o T-Systems International, uma empresa Alemã independente e subsidiária da Deutsche Telekom. Os serviços de nuvem comercial da Microsoft nesses datacenters estão de acordo com as regulamentações de manipulação de dados da Alemanha e fornecem aos clientes opções adicionais de como e onde os dados são processados.

* [Mapa de Regiões do Azure](https://azure.microsoft.com/regions/)

## <a name="security"></a>Segurança
O Armazenamento do Azure fornece um conjunto abrangente de recursos de segurança que, juntos, permitem aos desenvolvedores criar aplicativos seguros. A conta de armazenamento pode ser protegida usando o Controle de Acesso Baseado em Função e o Azure Active Directory. Os dados podem ser protegidos em trânsito, entre um aplicativo e o Azure usando a Criptografia do cliente, HTTPs ou SMB 3.0. Os dados podem ser definidos para serem criptografados automaticamente quando gravados no Armazenamento do Azure usando a SSE (Criptografia do Serviço de Armazenamento). Os discos do SO e de dados usados pelas máquinas virtuais podem ser definidos para serem criptografados usando o Azure Disk Encryption. O acesso delegado aos objetos de dados no Armazenamento do Azure pode ser concedido usando Assinaturas de Acesso Compartilhado.

### <a name="management-plane-security"></a>Segurança do plano de gerenciamento
O plano de gerenciamento consiste em recursos usados para gerenciar a conta de armazenamento. Nesta seção, falaremos sobre o Modelo de implantação do Azure Resource Manager e como usar o RBAC (Controle de Acesso Baseado em Função) para controlar o acesso às contas de armazenamento. Também falaremos sobre como gerenciar as chaves da conta de armazenamento e como regenerá-las.

### <a name="data-plane-security"></a>Segurança do plano de dados
Nesta seção, vamos examinar a permissão de acesso aos objetos de dados reais na sua conta de armazenamento, como blobs, arquivos, filas e tabelas, usando as Assinaturas de Acesso Compartilhado e as Políticas de Acesso Armazenado. Vamos abordar a SAS de nível de serviço e de nível de conta. Também veremos como limitar o acesso a um endereço IP específico (ou a um intervalo de endereços IP), como limitar o protocolo usado para HTTPS e como revogar uma Assinatura de Acesso Compartilhado sem esperar que ela expire.

## <a name="encryption-in-transit"></a>Criptografia em trânsito
Esta seção ensina a proteger os dados quando você os transfere para dentro ou para fora do Armazenamento do Azure. Falaremos sobre o uso recomendado de HTTPS e a criptografia usada pelo SMB 3.0 para compartilhamentos de Arquivos do Azure. Também examinaremos a Criptografia do Cliente, que permite criptografar os dados antes que eles sejam transferidos para o Armazenamento em um aplicativo cliente e a descriptografá-los depois que eles são transferidos para fora do Armazenamento.

## <a name="encryption-at-rest"></a>Criptografia em repouso
Falaremos sobre a SSE (Criptografia do Serviço de Armazenamento) e como é possível habilitá-la em uma conta de armazenamento, resultando na criptografia automática dos blobs de blocos, dos blobs de páginas e dos blobs de acréscimo quando gravados no Armazenamento do Azure. Também veremos como você pode usar o Azure Disk Encryption e explorar as diferenças básicas e os casos do Disk Encryption em relação ao SSE e à Criptografia do cliente. Examinaremos rapidamente a compatibilidade de FIPS com os computadores do governo norte-americano.

* [Guia de segurança do Armazenamento do Azure](../../storage/common/storage-security-guide.md)

## <a name="temporary-disk"></a>Disco temporário
Cada VM contém um disco temporário. O disco temporário fornece armazenamento de curto prazo para aplicativos e processos e destina-se apenas a armazenar dados, como arquivos de paginação ou de permuta. Os dados no disco temporário podem ser perdidos durante um [evento de manutenção](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) ou durante a [reimplantação de uma VM](redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Durante a reinicialização padrão da VM, os dados na unidade temporária deverão permanecer.

Em máquinas virtuais Linux, normalmente, o disco é **/dev/sdb** e é formatado e montado em **/mnt** pelo Agente Linux do Azure. O tamanho do disco temporário varia com base no tamanho da máquina virtual. Para saber mais, confira [Tamanhos de máquinas virtuais do Linux](sizes.md).

Para obter mais informações sobre como o Azure usa o disco temporário, consulte [Noções básicas sobre a unidade temporária nas Máquinas Virtuais do Microsoft Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="cost-savings"></a>Economia de custos
* [Custo de armazenamento](https://azure.microsoft.com/pricing/details/storage/)
* [Calculadora de custos de armazenamento](https://azure.microsoft.com/pricing/calculator/?service=storage)

## <a name="storage-limits"></a>Limites de armazenamento
* [Limites de Serviço de Armazenamento](../../azure-subscription-service-limits.md#storage-limits)
