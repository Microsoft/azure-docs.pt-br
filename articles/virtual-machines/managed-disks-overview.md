---
title: Visão geral de Armazenamento em Disco do Azure
description: Visão geral dos Azure Managed disks, que lidam com as contas de armazenamento para você ao usar VMs.
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: contperf-fy21q1
ms.openlocfilehash: 6c9b4a9ee1a778ba7a534377f8b2abe9d9a7e18a
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102504723"
---
# <a name="introduction-to-azure-managed-disks"></a>Introdução aos discos gerenciados do Azure

Os Azure Managed Disks são volumes de armazenamento em nível de bloco que são gerenciados pelo Azure e usados com Máquinas Virtuais do Azure. Os discos gerenciados são como um disco físico em um servidor local, mas virtualizados. Com os discos gerenciados, basta especificar o tamanho e o tipo de disco e provisioná-lo. Depois que você provisionar o disco, o Azure cuidará do resto.

Os tipos disponíveis de discos são Discos Ultra, SSD (unidades de estado sólido) Premium, SSDs Standard e HD (unidades de disco rígido) Standard. Para obter informações sobre cada tipo de disco individual, confira [Selecionar um tipo de disco para VMs IaaS](disks-types.md).

## <a name="benefits-of-managed-disks"></a>Benefícios dos discos gerenciados

Vamos falar sobre alguns benefícios que você ganha usando discos gerenciados.

### <a name="highly-durable-and-available"></a>Altamente durável e disponível

Os discos gerenciados foram criados para oferecer uma disponibilidade de 99,999%. Os discos gerenciados atingem isso fornecendo três réplicas dos seus dados, possibilitando alta durabilidade. Se uma ou duas réplicas apresentarem problemas, as réplicas restantes ajudarão a garantir a persistência dos seus dados e a alta tolerância contra falhas. Esta arquitetura ajudou o Azure a proporcionar consistentemente durabilidade de nível empresarial para discos IaaS (Infraestrutura como serviço), com uma taxa de falha anualizada líder do setor de ZERO POR CENTO.

### <a name="simple-and-scalable-vm-deployment"></a>Implantação simples e escalonável de VM

Usando discos gerenciados, é possível criar até 50 mil **discos** de VM de um tipo em uma assinatura por região, permitindo que você crie milhares de **VMs** em uma assinatura única. Esse recurso também aumenta a ainda mais escalabilidade dos [conjuntos de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/overview.md), permitindo que você crie até mil VMs em um conjunto de dimensionamento de máquinas virtuais usando uma imagem do Marketplace.

### <a name="integration-with-availability-sets"></a>Integração com conjuntos de disponibilidade

Os discos gerenciados são integrados a conjuntos de disponibilidade para garantir que os discos de [VMs em um conjunto de disponibilidade](./availability-set-overview.md) estejam suficientemente isolados entre si para evitar pontos únicos de falha. Os discos são automaticamente colocados em unidades de escala de armazenamento diferentes (carimbos). Se um carimbo falhar devido a uma falha de hardware ou de software, somente as instâncias da VM com discos nesses carimbos falharão. Por exemplo, vamos supor que você tenha um aplicativo em execução em cinco VMs, e que as VMs estejam em um Conjunto de Disponibilidade. Os discos dessas VMs não serão armazenados no mesmo stamp, portanto, se um stamp ficar inativo, as outras instâncias do aplicativo continuarão em execução.

### <a name="integration-with-availability-zones"></a>Integração com as Zonas de Disponibilidade

Os discos gerenciados são compatíveis com as [Zonas de Disponibilidade](../availability-zones/az-overview.md), que são uma oferta de alta disponibilidade capaz de proteger seus aplicativos contra falhas no datacenter. As Zonas de Disponibilidade são locais físicos exclusivos em uma região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas. Com Zonas de Disponibilidade, o Azure oferece o melhor SLA de tempo de atividade da VM de 99,99% do setor.

### <a name="azure-backup-support"></a>Suporte de Backup do Azure

Para proteger contra desastres regionais, o [Backup do Azure](../backup/backup-overview.md) pode ser usado para criar um trabalho de backup com backups baseados em tempo e políticas de retenção de backup. Isso permite que você execute restaurações de VM ou de disco gerenciado fáceis sempre que quiser. No momento, o Backup do Azure dá suporte a tamanhos de disco de até 32 TiB (tebibytes). [Saiba mais](../backup/backup-support-matrix-iaas.md) sobre o suporte de backup da VM do Azure.

#### <a name="azure-disk-backup"></a>Backup de Disco do Azure

O backup do Azure oferece o backup em disco do Azure (versão prévia) como uma solução de backup nativa baseada em nuvem que protege seus dados em discos gerenciados. É uma solução simples, segura e econômica que permite configurar a proteção de discos gerenciados em algumas etapas. O backup em disco do Azure oferece uma solução completa que fornece gerenciamento de ciclo de vida de instantâneos para discos gerenciados automatizando a criação periódica de instantâneos e retendo-o para duração configurada usando a política de backup. Para obter detalhes sobre o backup em disco do Azure, consulte [visão geral do backup em disco do Azure (em versão prévia)](../backup/disk-backup-overview.md).

### <a name="granular-access-control"></a>Controle de acesso granular

Use o [RBAC (controle de acesso baseado em função) do Azure](../role-based-access-control/overview.md) para atribuir permissões específicas em um disco gerenciado a um ou mais usuários. Os discos gerenciados expõem uma variedade de operações, incluindo leitura, gravação (criar/atualizar), exclusão e recuperação de um [URI de SAS (assinatura de acesso compartilhado)](../storage/common/storage-sas-overview.md) para o disco. Conceda acesso somente às operações que uma pessoa necessita para executar seu trabalho. Por exemplo, se não quiser que uma pessoa copie um disco gerenciado em uma conta de armazenamento, opte por não conceder acesso à ação de exportação para esse disco gerenciado. Da mesma forma, se não quiser que uma pessoa use um URI de SAS para copiar um disco gerenciado, opte por não conceder essa permissão ao disco gerenciado.

### <a name="upload-your-vhd"></a>Faça upload do seu VHD

O upload direto facilita a transferência do VHD para um disco gerenciado do Azure. Anteriormente, você precisava seguir um processo mais envolvido que incluía preparar seus dados em uma conta de armazenamento. Agora, há menos etapas. É mais fácil fazer upload das VMs locais para o Azure e para grandes discos gerenciados. Além disso, o processo de backup e de restauração foi simplificado. Isso também reduz o custo ao permitir que você faça upload dos dados para discos gerenciados diretamente sem anexá-los às VMs. É possível usar o upload direto para carregar VHDs de até 32 TiB de tamanho.

Para saber como transferir seu VHD para o Azure, confira os artigos da [CLI](linux/disks-upload-vhd-to-managed-disk-cli.md) ou do [PowerShell](windows/disks-upload-vhd-to-managed-disk-powershell.md).

## <a name="security"></a>Segurança

### <a name="private-links"></a>Links Privados

O suporte de link privado para discos gerenciados pode ser usado para importar ou exportar um disco gerenciado interno para sua rede. Os Links Privados permitem que você gere um URI de SAS (Assinatura de Acesso Compartilhado) com limite de tempo para discos gerenciados e instantâneos desanexados que podem ser usados para exportar os dados para outras regiões para expansão regional, recuperação de desastre e para análise forense. Use também o URI de SAS para carregar diretamente o VHD em um disco vazio local. Agora você pode aproveitar os [Links Privados](../private-link/private-link-overview.md) para restringir a exportação e a importação de discos gerenciados para que isso só possa ocorrer dentro de sua rede virtual do Azure. Os Links Privados oferecem a garantia de que seus dados trafeguem apenas na rede de backbone protegida da Microsoft.

Para saber como habilitar Links Privados para importar ou exportar um disco gerenciado, consulte os artigos da [CLI](linux/disks-export-import-private-links-cli.md) ou do [Portal](disks-enable-private-links-for-import-export-portal.md).

### <a name="encryption"></a>Criptografia

Os discos gerenciados oferecem dois tipos diferentes de criptografia. O primeiro é a SSE (Criptografia do Serviço de Armazenamento), executada pelo serviço de armazenamento. O segundo é o ADE (Azure Disk Encryption), que pode ser habilitado nos discos do sistema operacional e de dados das VMs.

#### <a name="server-side-encryption"></a>Criptografia no servidor

A criptografia no servidor fornece criptografia em repouso e protege seus dados para atender aos compromissos de conformidade e segurança da sua organização. A criptografia no servidor está habilitada por padrão para todos os discos gerenciados, instantâneos e imagens em todas as regiões nas quais os discos gerenciados estão disponíveis. (Os discos temporários, por outro lado, não são criptografados pela criptografia do lado do servidor, a menos que você habilite a criptografia no host; consulte [Funções de disco: discos temporários](#temporary-disk)).

Você poderá permitir que o Azure gerencie as chaves para você (essas são chaves gerenciadas pela plataforma) ou você poderá gerenciar as chaves por conta própria (essas são chaves gerenciadas pelo cliente). Veja o artigo [Criptografia no lado do servidor do Armazenamento em Disco do Azure](./disk-encryption.md) para obter detalhes.


#### <a name="azure-disk-encryption"></a>Azure Disk Encryption

O Azure Disk Encryption permite criptografar os discos do sistema operacional e os discos de dados usados por uma Máquina Virtual IaaS. Essa criptografia inclui discos gerenciados. No Windows, as unidades são criptografadas usando a tecnologia de criptografia BitLocker padrão do setor. No Linux, os discos são criptografados usando a tecnologia DM-Crypt. Esse processo de criptografia é integrado ao Azure Key Vault para permitir que você controle e gerencie as chaves de criptografia de disco. Para obter mais informações, confira [Azure Disk Encryption para VMs do Linux](linux/disk-encryption-overview.md) ou [Azure Disk Encryption para VMs do Windows](windows/disk-encryption-overview.md).

## <a name="disk-roles"></a>Funções do disco

Há três funções principais de disco no Azure: o disco de dados, o disco do SO e o disco temporário. Essas funções são mapeadas para discos anexados à sua máquina virtual.

![Funções de disco em ação](media/virtual-machines-managed-disks-overview/disk-types.png)

### <a name="data-disk"></a>Disco de dados

Um disco de dados é um disco gerenciado anexado a uma máquina virtual para armazenar dados de aplicativos ou outros dados que precisam ser mantidos. Discos de dados são registrados como unidades SCSI e rotulados com a letra que você escolher. Cada disco de dados tem uma capacidade máxima de 32.767 GiB (gibibytes). O tamanho da máquina virtual determina quantos discos de dados você pode anexar a ele e o tipo de armazenamento que pode usar para hospedar os discos.

### <a name="os-disk"></a>Disco do sistema operacional

Cada máquina virtual tem um disco de sistema operacional anexado. Esse disco do sistema operacional tem um SO pré-instalado, que foi selecionado quando a VM foi criada. Esse disco contém o volume de inicialização.

Esse disco tem uma capacidade máxima de 4.095 GiB.

### <a name="temporary-disk"></a>Disco temporário

A maioria das VMs contém um disco temporário, que não é um disco gerenciado. O disco temporário fornece armazenamento de curto prazo para aplicativos e processos e destina-se apenas a armazenar dados como arquivos de página ou de permuta. Os dados no disco temporário podem ser perdidos durante um [evento de manutenção](./understand-vm-reboots.md) ou durante a [reimplantação de uma VM](troubleshooting/redeploy-to-new-node-windows.md?toc=/azure/virtual-machines/windows/toc.json). Durante uma reinicialização padrão bem-sucedida da VM, os dados no disco temporário serão mantidos. Para obter mais informações sobre VMs sem discos temporários, consulte [tamanhos de VM do Azure sem disco temporário local](azure-vms-no-temp-disk.md).

Em VMs do Linux do Azure, o disco temporário é normalmente /dev/sdb e em VMs do Windows, o disco temporário é D: por padrão. O disco temporário não é criptografado pela criptografia do servidor, a menos que você habilite a criptografia no host.

## <a name="managed-disk-snapshots"></a>Instantâneos de disco gerenciado

Um instantâneo de disco gerenciado é uma cópia completa consistente com falhas e somente leitura de um disco gerenciado que, por padrão, é armazenada como um disco gerenciado padrão. Com os instantâneos, você pode fazer backup de seus discos gerenciados a qualquer momento. Esses instantâneos existem independentemente do disco de origem e podem ser usados para criar novos discos gerenciados. 

Os instantâneos são cobrados com base no tamanho utilizado. Por exemplo, se você criar um instantâneo de um disco gerenciado com capacidade provisionada de 64 GiB e tamanho real de dados usados de 10 GiB, esse instantâneo será cobrado apenas pelo tamanho de dados usados de 10 GiB. Você poderá ver o tamanho utilizado dos instantâneos examinando o [relatório de uso do Azure](../cost-management-billing/understand/review-individual-bill.md). Por exemplo, se o tamanho dos dados de um snapshot usado for 10 GiB, o relatório de uso **diário** mostrará 10 GiB/(31 dias) = 0,3226 como a quantidade consumida.

Para saber mais sobre como criar instantâneos para discos gerenciados, confira os seguintes recursos:

- [Criar um instantâneo de um disco gerenciado no Windows](windows/snapshot-copy-managed-disk.md)
- [Criar um instantâneo de um disco gerenciado no Linux](linux/snapshot-copy-managed-disk.md)

### <a name="images"></a>Imagens

Os discos gerenciados também dão suporte à criação de uma imagem personalizada gerenciada. É possível criar uma imagem do seu VHD personalizado em uma conta de armazenamento ou diretamente de uma VM generalizada (por meio do sysprep). Esse processo captura uma única imagem. Essa imagem contém todos os discos gerenciados associados a uma VM, incluindo os discos do SO e de dados. Esta imagem personalizada gerenciada permite a criação de centenas de VMs usando sua imagem personalizada, sem a necessidade de copiar ou gerenciar contas de armazenamento.

Para saber mais sobre a criação de imagens, confira os artigos a seguir:

- [Como capturar uma imagem gerenciada de uma VM generalizada no Azure](windows/capture-image-resource.md)
- [Como generalizar e capturar uma máquina virtual Linux usando a CLI do Azure](linux/capture-image.md)

#### <a name="images-versus-snapshots"></a>Imagens versus instantâneos

É importante compreender a diferença entre imagens e instantâneos. Com discos gerenciados é possível capturar uma imagem de uma VM generalizada que foi desalocada. Essa imagem inclui todos os discos anexados à VM. É possível usar essa imagem para criar uma VM e isso inclui todos os discos.

Um instantâneo é uma cópia de um disco no momento exato em que o instantâneo é tirado. Ele só se aplica a um disco. Se você tiver uma VM que tem um disco (o disco do SO), será possível capturar um instantâneo ou uma imagem dele e criar uma VM com base no instantâneo ou na imagem.

Um instantâneo não tem reconhecimento de nenhum disco, exceto o contido por ele. Isso torna problemático usá-lo em cenários que precisam da coordenação de vários discos, como distribuição. Os instantâneos precisariam ser capazes de coordenar uns com os outros e, no momento, não há suporte para isso.

## <a name="disk-allocation-and-performance"></a>Desempenho e alocação de disco

O diagrama a seguir ilustra a alocação em tempo real de largura de banda e IOPS para discos, usando um sistema de provisionamento de três níveis:

![Sistema de provisionamento de três níveis mostrando alocação de largura de banda e de IOPS](media/virtual-machines-managed-disks-overview/real-time-disk-allocation.png)

O provisionamento do primeiro nível define a atribuição de IOPS e de largura de banda por disco.  No segundo nível, o host do servidor de computação implementa o provisionamento de SSD, aplicando-o aos dados armazenados no SSD do servidor, que inclui discos com armazenamento em cache (ReadWrite e ReadOnly), bem como discos locais e temporários. Por fim, o provisionamento da rede de VMs ocorre no terceiro nível para qualquer E/S que o host de computação envia para o back-end do Armazenamento do Azure. Com esse esquema, o desempenho de uma VM depende de uma variedade de fatores, desde a maneira como a VM usa o SSD local até o número de discos anexados, bem como o tipo de desempenho e de cache dos discos que ele anexou.

Como um exemplo dessas limitações, uma VM Standard_DS1v1 é impedida de alcançar o potencial de 5 mil IOPS de um disco P30, esteja em cache ou não, devido aos limites nos níveis do SSD e de rede:

![Alocação de exemplo de Standard_DS1v1](media/virtual-machines-managed-disks-overview/example-vm-allocation.png)

O Azure usa um canal de rede priorizado para tráfego de disco, que prevalece sobre outra baixa prioridade do tráfego de rede. Isso ajuda os discos a manter o desempenho esperado em caso de contenções de rede. Da mesma forma, o Armazenamento do Azure lida com contenções de recursos e com outros problemas em segundo plano com balanceamento de carga automático. O Armazenamento do Azure aloca os recursos necessários quando você cria um disco e aplica o balanceamento proativo e reativo de recursos para lidar com o nível de tráfego. Com isso, os discos podem sustentar seus destinos de IOPS e de taxa de transferência esperados. Você pode usar as métricas em nível de VM e de disco para acompanhar o desempenho e os alertas de configuração, conforme necessário.

Confira nosso artigo de [design para alto desempenho](premium-storage-performance.md) para aprender as melhores práticas para otimizar as configurações de VM + Disco para que você possa atingir o desempenho desejado

## <a name="next-steps"></a>Próximas etapas

Se você quiser ver um vídeo que entre em mais detalhes sobre os discos gerenciados, confira: [Melhor resiliência de VM do Azure com Managed Disks](https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency).

Saiba mais sobre os tipos de disco individual oferecidos pelo Azure, qual tipo é uma boa opção para suas necessidades e sobre os destinos de desempenho em nosso artigo sobre tipos de disco.

> [!div class="nextstepaction"]
> [Selecionar um tipo de disco para VMs de IaaS](disks-types.md)