---
title: Migrando VMs para o Armazenamento Premium do Azure | Microsoft Docs
description: "Migre VMs existentes para o Armazenamento Premium do Azure. O Armazenamento Premium dá suporte ao disco de alto desempenho e baixa latência para cargas de trabalho que usam muita E/S em execução em máquinas virtuais do Azure."
services: storage
documentationcenter: na
author: yuemlu
manager: tadb
editor: tysonn
ms.assetid: 272250b3-fd4e-41d2-8e34-fd8cc341ec87
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: yuemlu
ms.openlocfilehash: ca893f87b155a92c457e3bf6d9d39aaf86bf5fb3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="migrating-to-azure-premium-storage-unmanaged-disks"></a>Migrando para o Armazenamento Premium do Azure (Discos não gerenciados)

> [!NOTE]
> Este artigo explica como migrar uma VM que usa discos Standard não gerenciados para uma VM que usa discos Premium não gerenciados. É recomendável usar o Azure Managed Disks para novas VMs e converter os discos não gerenciados anteriores em disco gerenciados. O Managed Disks tratam das contas de armazenamento subjacentes para você. Para saber mais, confira [Managed Disks Overview](../../virtual-machines/windows/managed-disks-overview.md) (Visão geral do Managed Disks).
>

O Armazenamento Premium do Azure dá suporte de disco de alto desempenho e baixa latência para máquinas virtuais executando cargas de trabalho intensivas para entradas e saídas. Você pode tirar proveito da velocidade e desempenho desses discos migrando os discos de VM do aplicativo para o Armazenamento do Azure Premium.

O objetivo deste guia é ajudar novos usuários do Armazenamento Premium do Azure a se preparar melhor para fazer uma transição sem problemas de seu sistema atual para o Armazenamento Premium. O guia aborda três principais componentes nesse processo:

* [Planejar a migração para o Armazenamento Premium](#plan-the-migration-to-premium-storage)
* [Preparar e copiar VHDs (discos rígidos virtuais) para o Armazenamento Premium](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
* [Criar uma Máquina Virtual do Azure usando o Armazenamento Premium](#create-azure-virtual-machine-using-premium-storage)

Você pode migrar VMs de outras plataformas para o Armazenamento do Azure Premium ou migrar VMs do Azure existentes do Armazenamento Standard para o Armazenamento Premium. Este guia aborda as etapas para os dois cenários. Siga as etapas especificadas na seção relevante, dependendo do cenário.

> [!NOTE]
> Você pode encontrar uma visão geral de recursos e os preços do Armazenamento Premium em [Armazenamento Premium: Armazenamento de Alto Desempenho para Cargas de Trabalho de Máquina Virtual do Azure](storage-premium-storage.md). É recomendável migrar qualquer disco de máquina virtual que exija IOPS alta para o Armazenamento Premium do Azure para obter o melhor desempenho para o seu aplicativo. Se o disco não requer IOPS alta, você pode limitar os custos mantendo-a no armazenamento padrão, que armazena dados de disco da máquina virtual em HDDs (unidades de disco rígido) em vez de SSDs.
>

A conclusão do processo de migração em sua totalidade pode exigir ações adicionais antes e depois das etapas fornecidas neste guia. Os exemplos incluem a configuração pontos de extremidade ou redes virtuais ou alterações de código no próprio aplicativo, o que pode exigir algum tempo de inatividade no aplicativo. Essas ações são exclusivas para cada aplicativo e devem ser concluídas junto com as etapas fornecidas neste guia para fazer a transição completa para o Armazenamento Premium da maneira mais simples possível.

## <a name="plan-the-migration-to-premium-storage"></a>Planejar a migração para o Armazenamento Premium
Esta seção garante que você se prepare para seguir as etapas de migração neste artigo e o ajuda a tomar a melhor decisão quanto a tipos de VM e disco.

### <a name="prerequisites"></a>Pré-requisitos
* Você também precisará de uma assinatura do Azure. Se você ainda não tiver uma, você poderá criar uma assinatura de [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) de um mês ou visitar [Preços do Azure](https://azure.microsoft.com/pricing/) para obter mais opções.
* Para executar os cmdlets PowerShell, você precisará do módulo Microsoft Azure PowerShell. Consulte [Como instalar e configurar o PowerShell do Azure](/powershell/azure/overview) para obter o ponto e as instruções de instalação.
* Quando planeja usar VMs do Azure em execução no Armazenamento Premium, você precisa usar VMs compatíveis com o Armazenamento Premium. Você pode usar discos de Armazenamento Standard e Premium com VMs compatíveis com o Armazenamento Premium. Os discos de armazenamento Premium estarão disponíveis com mais tipos de VM no futuro. Para obter informações sobre todos os tamanhos e tipos de discos de VM do Azure disponíveis, veja [Tamanhos para máquinas virtuais](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e [Tamanhos para Serviços de Nuvem](../../cloud-services/cloud-services-sizes-specs.md).

### <a name="considerations"></a>Considerações
Uma VM do Azure oferece suporte à anexação de vários discos de Armazenamento Premium, para que seus aplicativos possam ter até 256 TB de armazenamento por VM. Com o Armazenamento Premium, seus aplicativos podem atingir 80.000 IOPS (operações de entrada/saída por segundo) por VM e taxa de transferência de disco de 2.000 MB por segundo por VM com latências extremamente baixas para operações de leitura. Você tem opções de várias VMs e Discos. Esta seção se destina a ajudá-lo a encontrar uma opção mais adequada para sua carga de trabalho.

#### <a name="vm-sizes"></a>Tamanhos de VM
As especificações de tamanho de VM do Azure são listadas em [Tamanhos para máquinas virtuais](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Examine as características de desempenho das máquinas virtuais que funcionam com o Armazenamento Premium e escolha o tamanho de VM mais apropriado que melhor atende à sua carga de trabalho. Certifique-se de que há largura de banda suficiente disponível na sua VM para direcionar o tráfego de disco.

#### <a name="disk-sizes"></a>Tamanhos do disco
Há cinco tipos de discos que podem ser usados com a VM e cada um tem IOPs específicos e limites de taxa de transferência. Leve em consideração esses limites ao escolher o tipo de disco para sua VM com base nas necessidades de seu aplicativo em termos de capacidade, desempenho, escalabilidade e cargas de pico.

| Tipo de discos premium  | P10   | P20   | P30            | P40            | P50            | 
|:-------------------:|:-----:|:-----:|:--------------:|:--------------:|:--------------:|
| Tamanho do disco           | 128 GB| 512 GB| 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 
| IOPS por disco       | 500   | 2.300  | 5.000           | 7500           | 7500           | 
| Taxa de transferência por disco | 100 MB por segundo | 150 MB por segundo | 200 MB por segundo | 250 MB por segundo | 250 MB por segundo |

Dependendo da carga de trabalho, determine se discos de dados adicionais são necessários para sua VM. Você pode anexar diversos discos de dados persistentes à sua VM. Se necessário, pode distribuir entre os discos para aumentar a capacidade e o desempenho do volume. (Veja o que é a distribuição de disco [aqui](storage-premium-storage-performance.md#disk-striping).) Se você distribuir discos de dados do Armazenamento Premium usando [Espaços de Armazenamento][4], deverá configurá-lo com uma coluna para cada disco usado. Caso contrário, o desempenho geral do volume distribuído pode ser menor que o esperado devido a uma distribuição irregular de tráfego entre os discos. Para as VMs do Linux, você pode usar o utilitário *mdadm* para obter o mesmo resultado. Consulte o artigo [Configurar o Software RAID no Linux](../../virtual-machines/linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter detalhes.

#### <a name="storage-account-scalability-targets"></a>Metas de escalabilidade da conta de armazenamento
As contas de Armazenamento Premium têm as seguintes metas de escalabilidade, além das [Metas de Desempenho e Escalabilidade do Armazenamento do Azure](storage-scalability-targets.md). Se as exigências de seu aplicativo exceder as metas de escalabilidade de uma única conta de armazenamento, crie seu aplicativo para usar múltiplas contas de armazenamento e particione seus dados nessas contas de armazenamento.

| Capacidade total da conta | Largura de banda total para uma conta de armazenamento com redundância local |
|:--- |:--- |
| Capacidade de disco: 35 TB<br />Capacidade do instantâneo: 10 TB |Até 50 gigabits para Entrada + Saída |

Para obter mais informações sobre as especificações do Armazenamento Premium, verifique as [Metas de Escalabilidade e Desempenho ao usar o Armazenamento Premium](storage-premium-storage.md#scalability-and-performance-targets).

#### <a name="disk-caching-policy"></a>Política de cache de disco
Por padrão, a política de cache de disco é *Somente leitura* para todos os discos de dados Premium e *Leitura e gravação* para o disco de sistema operacional Premium anexado à VM. Esta definição de configuração é recomendável para atingir o desempenho ideal de E/Ss dos seus aplicativos. Para discos de dados de gravação intensa ou somente gravação (como arquivos de log do SQL Server), desabilite o cache de disco para que possa obter o melhor desempenho do aplicativo. As configurações de cache existentes para os discos de dados podem ser atualizadas usando o [Portal do Azure](https://portal.azure.com) ou o parâmetro *-HostCaching* do cmdlet *Set-AzureDataDisk*.

#### <a name="location"></a>Local
Escolha um local onde o Armazenamento do Azure Premium está disponível. Confira [Serviços do Azure por região](https://azure.microsoft.com/regions/#services) para obter informações atualizadas sobre as localizações disponíveis. As máquinas virtuais na mesma região da conta de armazenamento que armazena os discos da VM fornecerão um desempenho muito melhor em relação a estarem em regiões separadas.

#### <a name="other-azure-vm-configuration-settings"></a>Outras definições de configuração da VM do Azure
Ao criar uma VM do Azure, você será solicitado a definir certas configurações da máquina virtual. Lembre-se de que algumas configurações são fixas durante o tempo de vida da VM, enquanto você pode modificar ou adicionar outras posteriormente. Revise essas definições de configuração da máquina virtual do Azure e verifique se elas estão definidas corretamente para atender aos seus requisitos de carga de trabalho.

### <a name="optimization"></a>Otimização
[Armazenamento Premium do Azure: design de alto desempenho](storage-premium-storage-performance.md) fornece diretrizes para criação de aplicativos de alto desempenho usando o Armazenamento Premium do Azure. Você pode seguir as diretrizes combinadas a práticas recomendadas aplicáveis a tecnologias usadas por seu aplicativo.

## <a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Preparar e copiar VHDs (discos rígidos virtuais) para o Armazenamento Premium
A seção a seguir fornece diretrizes para preparar VHDs de sua VM e copiar VHDs para o Armazenamento do Azure.

* [Cenário 1: "estou migrando VMs do Azure existentes para o Armazenamento do Azure Premium".](#scenario1)
* [Cenário 2: "estou migrando VMs de outras plataformas para o Armazenamento Premium do Azure".](#scenario2)

### <a name="prerequisites"></a>Pré-requisitos
Para preparar os VHDs para a migração, você precisará do seguinte:

* Uma assinatura do Azure, uma conta de armazenamento e um contêiner na conta de armazenamento para o qual o VHD será copiado. Observe que a conta de armazenamento de destino pode ser uma conta de Armazenamento Standard ou Premium, dependendo do requisito.
* Uma ferramenta para generalizar o VHD, caso você planeje criar várias instâncias da VM por meio dele. Por exemplo, o sysprep para Windows ou o virt-sysprep para Ubuntu.
* Uma ferramenta para carregar o arquivo VHD para a conta de armazenamento. Consulte [Transferir dados com o Utilitário de Linha de Comando AzCopy](storage-use-azcopy.md) ou use um [Gerenciador de armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). Este guia descreve a cópia de seu VHD usando a ferramenta AzCopy.

> [!NOTE]
> Se você escolher a opção de cópia síncrona com o AzCopy, para obter o melhor desempenho, copie o VHD executando uma dessas ferramentas em uma VM do Azure que está na mesma região que a conta de armazenamento de destino. Se você estiver copiando um VHD de uma VM do Azure em uma região diferente, o desempenho pode ser mais lento.
>
> Para copiar uma grande quantidade de dados usando uma largura de banda limitada, considere o [uso do serviço de Importação/Exportação do Azure para transferir os dados para o Armazenamento de Blobs](../storage-import-export-service.md); isso permite transferir os dados enviando as unidades de disco rígido para um datacenter do Azure. Você pode usar o serviço de Importação/Exportação para copiar os dados para apenas uma conta de armazenamento padrão. Quando os dados estiverem em sua conta de armazenamento padrão, você pode usar a [cópia da API do blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) ou o AzCopy para transferir os dados para sua conta de armazenamento premium.
>
> Observe que o Microsoft Azure dá suporte apenas a arquivos VHD de tamanho fixo. Os arquivos VHDX ou VHDs dinâmicos não têm suporte. Se você tiver um VHD dinâmico, poderá convertê-lo para ter um tamanho fixo usando o cmdlet [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) .
>
>

### <a name="scenario1"></a>Cenário 1: "estou migrando VMs do Azure existentes para o Armazenamento Premium do Azure".
Se você estiver migrando VMs existentes do Azure, interrompa a VM, prepare VHDs por tipo de VHD desejado e copie o VHD com o AzCopy ou o PowerShell.

A VM precisa estar completamente inoperante para migrar para um estado limpo. Haverá um tempo de inatividade até que a migração seja concluída.

#### <a name="step-1-prepare-vhds-for-migration"></a>Etapa 1. Preparar VHDs para migração
Se você estiver migrando VMs do Azure existentes para o Armazenamento Premium, o VHD poderá ser:

* Uma imagem de sistema operacional generalizado
* Um disco de sistema operacional exclusivo
* Um disco de dados

Abaixo, percorremos estes três cenários para preparar o VHD.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Use um VHD do Sistema Operacional Generalizado para criar várias instâncias de VM
Se você estiver carregando um VHD que será usado para criar várias instâncias de VM do Azure genéricas, você deverá primeiro generalizar o VHD usando um utilitário sysprep. Isso se aplica a um VHD local ou que está localizado na nuvem. O Sysprep remove do VHD quaisquer informações específicas da máquina.

> [!IMPORTANT]
> Tire um instantâneo ou realize backup de sua VM antes de generalizá-lo. Executar sysprep interromperá e desalocará a instância de VM. Siga as etapas abaixo para usar o sysprep em um VHD do sistema operacional Windows. Observe que executar o comando Sysprep exigirá que você desligue a máquina virtual. Para saber mais sobre o Sysprep, consulte [Visão Geral do Sysprep](http://technet.microsoft.com/library/hh825209.aspx) ou [Referência Técnica do Sysprep](http://technet.microsoft.com/library/cc766049.aspx).
>
>

1. Abra uma janela de Prompt de comando como administrador.
2. Digite o comando a seguir para abrir o Sysprep:

    ```
    %windir%\system32\sysprep\sysprep.exe
    ```

3. Na Ferramenta de Preparação do Sistema, selecione Entrar no Sistema OOBE, marque a caixa de seleção Generalizar, selecione **Desligar** e, em seguida, clique em **OK** conforme mostrado na imagem abaixo. Sysprep generalizará o sistema operacional e desligar o sistema.

    ![][1]

Para uma VM Ubuntu, use virt-sysprep para obter o mesmo resultado. Consulte [virt sysprep](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) para obter mais detalhes. Consulte também um [software de Provisionamento do Servidor Linux](http://www.cyberciti.biz/tips/server-provisioning-software.html) de fonte aberta para ver outros sistemas operacionais Linux.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Use um VHD de Sistema Operacional Exclusivo para criar uma única instância de VM
Se você tiver um aplicativo em execução na máquina virtual que exige dados específicos da máquina, não generalize o VHD. Um VHD não generalizado pode ser usado para criar uma instância VM exclusiva do Azure. Por exemplo, se você tiver um Controlador de Domínio em seu VHD, executar o sysprep irá torná-lo inútil como um Controlador de Domínio. Examine os aplicativos em execução em sua VM e o impacto da execução do sysprep nelas antes de generalizar o VHD.

##### <a name="register-data-disk-vhd"></a>Registrar o VHD do disco de dados
Se você tiver discos de dados no Azure a serem migrados, verifique se as VMs que usam esses discos de dados foram desligadas.

Siga as etapas descritas abaixo para copiar o VHD para o Armazenamento Premium do Azure e registre-o como um disco de dados provisionado.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Etapa 2. Criar o destino para seu VHD
Crie uma conta de armazenamento para manter seus VHDs. Considere os seguintes pontos ao planejar onde armazenar seus VHDs:

* O destino da conta de armazenamento Premium.
* O local da conta de armazenamento deve ser igual às VMs do Azure compatíveis com o Armazenamento Premium que você criará no estágio final. Você pode copiar para uma nova conta de armazenamento ou planejar usar a mesma conta de armazenamento com base em suas necessidades.
* Copie e salve a chave da conta de armazenamento da conta de armazenamento de destino para a próxima fase.

Para discos de dados, você pode optar por manter alguns discos de dados em uma conta de armazenamento padrão (por exemplo, discos que têm um armazenamento melhor), mas é altamente recomendável mover todos os dados para a carga de trabalho de produção para usar o armazenamento premium.

#### <a name="copy-vhd-with-azcopy-or-powershell"></a>Etapa 3. Copiar o VHD com o AzCopy ou o PowerShell
Você precisará localizar o caminho do contêiner e o armazenamento de chaves de conta para processar qualquer uma dessas duas opções. A chave de conta de armazenamento e o caminho do contêiner podem ser encontrados em **Portal do Azure** > **Armazenamento**. A URL do contêiner será semelhante a "https://myaccount.blob.core.windows.net/mycontainer/".

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>Opção 1: copiar um VHD com o AzCopy (cópia assíncrona)
Usando o AzCopy, é possível carregar o VHD facilmente pela Internet. Dependendo do tamanho dos VHDs, isso pode levar tempo. Lembre-se de verificar os limites de entrada/saída da conta de armazenamento ao usar essa opção. Consulte [Metas de Desempenho e Escalabilidade do Armazenamento do Azure](storage-scalability-targets.md) para obter detalhes.

1. Baixe e instale o AzCopy aqui: [Versão mais recente do AzCopy](http://aka.ms/downloadazcopy)
2. Abra o PowerShell do Azure e vá para a pasta onde o AzCopy está instalado.
3. Use o comando a seguir para copiar o arquivo VHD de "Origem" para "Destino".

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    Exemplo:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd
    ```

    Aqui estão as descrições dos parâmetros usados no comando AzCopy:

   * **/Source: *&lt;source&gt;:*** local da pasta ou URL do contêiner de armazenamento que contém o VHD.
   * **/SourceKey: *&lt;source-account-key&gt;:*** chave de conta de armazenamento de origem.
   * **/Dest: *&lt;destination&gt;:*** a URL do contêiner de armazenamento para a qual copiar o VHD.
   * **/DestKey: *&lt;dest-account-key&gt;:*** chave de conta de armazenamento de destino.
   * **/Pattern: *&lt;file-name&gt;:*** especifica o nome do arquivo do VHD a ser copiado.

Para obter detalhes sobre como usar a ferramenta AzCopy, consulte [Transferir dados com o Utilitário de Linha de Comando AzCopy](storage-use-azcopy.md).

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>Opção 2: copiar um VHD com o PowerShell (cópia sincronizada)
Você também pode copiar o arquivo VHD usando o cmdlet do PowerShell Start-AzureStorageBlobCopy. Use o comando a seguir no Azure PowerShell para copiar o VHD. Substitua os valores entre <> por valores correspondentes de sua conta de armazenamento de origem e de destino. Para usar esse comando, você deve ter um contêiner chamado vhds na conta de armazenamento de destino. Se o contêiner não existir, crie um antes de executar o comando.

```powershell
$sourceBlobUri = <source-vhd-uri>

$sourceContext = New-AzureStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>

$destinationContext = New-AzureStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>

Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer <dest-container> -DestBlob <dest-disk-name> -DestContext $destinationContext
```

Exemplo:

```powershell
C:\PS> $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"

C:\PS> $sourceContext = New-AzureStorageContext  –StorageAccountName "sourceaccount" -StorageAccountKey "J4zUI9T5b8gvHohkiRg"

C:\PS> $destinationContext = New-AzureStorageContext  –StorageAccountName "destaccount" -StorageAccountKey "XZTmqSGKUYFSh7zB5"

C:\PS> Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext
```

### <a name="scenario2"></a>Cenário 2: "estou migrando VMs de outras plataformas para o Armazenamento Premium do Azure".
Se você estiver migrando o VHD do Armazenamento em Nuvem não do Azure, primeiro deverá exportar o VHD para um diretório local. Tenha à mão o caminho de origem completo do diretório local em que o VHD é armazenado e use AzCopy para carregá-la no Armazenamento do Azure.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>Etapa 1. Exportar o VHD para um diretório local
##### <a name="copy-a-vhd-from-aws"></a>Copiar um VHD do AWS
1. Se você estiver usando o AWS, exporte a instância do EC2 para um VHD em um depósito do Amazon S3. Execute as etapas descritas na documentação do Amazon para Exportar instâncias do Amazon EC2, a fim de instalar a ferramenta de interface de linha de comando (CLI) do Amazon EC2. Em seguida, execute o comando create-instance-export-task para exportar a instância do EC2 para um arquivo VHD. Use **VHD** para a variável DISK&#95;IMAGE&#95;FORMAT ao executar o comando **create-instance-export-task**. O arquivo VHD exportado é salvo no depósito do Amazon S3 designado durante esse processo.

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
      --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2. Baixe o arquivo VHD do depósito S3. Selecione o arquivo VHD, em seguida **Ações** > **Baixar**.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Copiar um VHD de outra nuvem que não é do Azure
Se você estiver migrando o VHD do Armazenamento em Nuvem não do Azure, primeiro deverá exportar o VHD para um diretório local. Copie o caminho de origem completo do diretório local onde o VHD está armazenado.

##### <a name="copy-a-vhd-from-on-premises"></a>Copiar um VHD do local
Se você estiver migrando o VHD de um ambiente local, você precisará do caminho de origem completo em que o VHD está armazenado. O caminho de origem pode ser um compartilhamento de arquivo ou local do servidor.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Etapa 2. Criar o destino para seu VHD
Crie uma conta de armazenamento para manter seus VHDs. Considere os seguintes pontos ao planejar onde armazenar seus VHDs:

* A conta de armazenamento de destino pode ser o armazenamento standard ou premium, dependendo do requisito do aplicativo.
* A região da conta de armazenamento deve ser igual as VMs do Azure compatíveis com o Armazenamento Premium que você criará no estágio final. Você pode copiar para uma nova conta de armazenamento ou planejar usar a mesma conta de armazenamento com base em suas necessidades.
* Copie e salve a chave da conta de armazenamento da conta de armazenamento de destino para a próxima fase.

É altamente recomendável mover todos os dados para a carga de trabalho de produção para usar o armazenamento premium.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>Etapa 3. Carregar o VHD no Armazenamento do Azure
Agora que você tem o VHD no diretório local, pode usar o AzCopy ou o AzurePowerShell para carregar o arquivo .vhd no Armazenamento do Azure. Ambas as opções são fornecidas aqui:

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>Opção 1: usar Add-AzureVhd do Azure PowerShell para carregar o arquivo .vhd

```powershell
Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>
```

Um exemplo <Uri> pode ser ***"https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd"***. Um exemplo <FileInfo> pode ser ***"C:\path\to\upload.vhd"***.

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>Opção 2: usar o AzCopy para carregar o arquivo .vhd
Usando o AzCopy, é possível carregar o VHD facilmente pela Internet. Dependendo do tamanho dos VHDs, isso pode levar tempo. Lembre-se de verificar os limites de entrada/saída da conta de armazenamento ao usar essa opção. Consulte [Metas de Desempenho e Escalabilidade do Armazenamento do Azure](storage-scalability-targets.md) para obter detalhes.

1. Baixe e instale o AzCopy aqui: [Versão mais recente do AzCopy](http://aka.ms/downloadazcopy)
2. Abra o PowerShell do Azure e vá para a pasta onde o AzCopy está instalado.
3. Use o comando a seguir para copiar o arquivo VHD de "Origem" para "Destino".

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    Exemplo:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd
    ```

    Aqui estão as descrições dos parâmetros usados no comando AzCopy:

   * **/Source: *&lt;source&gt;:*** local da pasta ou URL do contêiner de armazenamento que contém o VHD.
   * **/SourceKey: *&lt;source-account-key&gt;:*** chave de conta de armazenamento de origem.
   * **/Dest: *&lt;destination&gt;:*** a URL do contêiner de armazenamento para a qual copiar o VHD.
   * **/DestKey: *&lt;dest-account-key&gt;:*** chave de conta de armazenamento de destino.
   * **/BlobType: page:** Especifica que o destino é um blob de páginas.
   * **/Pattern: *&lt;file-name&gt;:*** especifica o nome do arquivo do VHD a ser copiado.

Para obter detalhes sobre como usar a ferramenta AzCopy, consulte [Transferir dados com o Utilitário de Linha de Comando AzCopy](storage-use-azcopy.md).

##### <a name="other-options-for-uploading-a-vhd"></a>Outras opções para carregar um VHD
Você também pode carregar um VHD para sua conta de armazenamento usando um dos seguintes meios:

* [API do Blob da Cópia de Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd894037.aspx)
* [Carregamento de Blobs do Gerenciador de Armazenamento do Azure](https://azurestorageexplorer.codeplex.com/)
* [Referência de API REST do Serviço de Importação/Exportação do Armazenamento](https://msdn.microsoft.com/library/dn529096.aspx)

> [!NOTE]
> É recomendável usar o Serviço de Importação/Exportação se o tempo de carregamento estimado é de mais de sete dias. Você pode usar [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) para estimar o tempo com base no tamanho dos dados e na unidade de transferência.
>
> A Importação/Exportação pode ser usada para copiar para a conta de armazenamento standard. Você precisará copiar do armazenamento standard para a conta de armazenamento premium usando uma ferramenta como o AzCopy.
>
>

## <a name="create-azure-virtual-machine-using-premium-storage"></a>Criar máquinas virtuais do Azure usando o Armazenamento Premium
Depois de o VHD ser carregado ou copiado para a conta de armazenamento desejada, siga as instruções nesta seção para registrar o VHD como uma imagem do sistema operacional ou disco do sistema operacional de acordo com seu cenário, em seguida, crie uma instância de VM a partir dele. O VHD do disco de dados pode ser anexado à VM assim que criado.
Um exemplo de script de migração é fornecido no fim desta seção. Este script simples não corresponde a todos os cenários. Talvez seja necessário atualizar o script para corresponder a seu cenário específico. Para ver se esse script se aplica a seu cenário, confira abaixo [Um script de migração de exemplo](#a-sample-migration-script).

### <a name="checklist"></a>Lista de verificação
1. Aguarde até que a cópia de todos os discos de VHD seja concluída.
2. Verifique se o Armazenamento Premium está disponível na região para a qual você está migrando.
3. Decida qual nova série de VM você usará. Deve ser compatível com Armazenamento Premium, e o tamanho deve depender da disponibilidade na região e se basear em suas necessidades.
4. Decida o tamanho exato da VM que você usará. O tamanho da VM precisa ser grande o suficiente para dar suporte ao número de discos de dados que você tem. Por exemplo , se você tiver quatro discos de dados, a VM deverá ter dois ou mais núcleos. Considere também as necessidades de capacidade de processamento, memória e largura de banda de rede.
5. Crie uma conta de Armazenamento Premium na região de destino. Essa é a conta que você usará para a nova VM.
6. Tenha os detalhes da VM atual à mão, incluindo a lista de discos e blobs VHD correspondentes.

Prepare seu aplicativo para o tempo de inatividade. Para fazer uma migração limpa, você precisa interromper todo o processamento no sistema atual. Só então você pode colocá-lo em estado consistente, podendo então migrar para a nova plataforma. A duração do tempo de inatividade dependerá da quantidade de dados nos discos para migração.

> [!NOTE]
> Se você estiver criando uma VM do Azure Resource Manager por meio de um Disco de VHD especializado, confira [este modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) para implantação da VM do Resource Manager usando o disco existente.
>
>

### <a name="register-your-vhd"></a>Registrar seu VHD
Para criar uma máquina virtual no VHD do sistema operacional ou anexar um disco de dados a uma nova VM, primeiro você deve registrá-los. Siga as etapas abaixo, dependendo do cenário do VHD.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>VHD do Sistema Operacional Generalizado para criar várias instâncias de VM do Azure
Depois do VHD de imagem do sistema operacional generalizado ser carregado para a conta de armazenamento, registre-o como uma **Imagem da VM do Azure** para que você possa criar uma ou mais instâncias de VM a partir dele. Use os seguintes cmdlets do PowerShell para registrar seu VHD como uma imagem do sistema operacional da VM do Azure. Forneça a URL completa do contêiner para onde o VHD foi copiado.

```powershell
Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows
```

Copie e salve o nome dessa nova Imagem da VM do Azure. No exemplo acima, é *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>VHD do sistema operacional exclusivo para criar uma instância de VM do Azure
Depois do VHD exclusivo do sistema operacional ser carregado para a conta de armazenamento, registre-o como um **Disco do Sistema Operacional do Azure** para que você possa criar uma instância de VM a partir dele. Use estes cmdlets do PowerShell para registrar seu VHD como um Disco do sistema operacional do Azure. Forneça a URL completa do contêiner para onde o VHD foi copiado.

```powershell
Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"
```

Copie e salve o nome desse novo Disco do sistema operacional do Azure. No exemplo acima, é *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>VHD do disco de dados a ser anexado à(s) instância(s) de VM(s) do Azure
Depois do VHD do disco de dados ser carregado para a conta de armazenamento, registre-o como um Disco de Dados do Azure para que ele possa ser anexado à sua nova instância VM do Azure da série DS, DSv2 ou GS.

Use estes cmdlets do PowerShell para registrar seu VHD como um Disco de Dados do Azure. Forneça a URL completa do contêiner para onde o VHD foi copiado.

```powershell
Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"
```

Copie e salve o nome desse novo Disco de Dados do Azure. No exemplo acima, é *DataDisk*.

### <a name="create-a-premium-storage-capable-vm"></a>Criar uma VM com capacidade de Armazenamento Premium
Assim que a imagem do sistema operacional ou o disco do sistema operacional for registrado, crie uma nova VM da série DS, DSv2 ou GS. Você usará a imagem do sistema operacional ou o nome de disco do sistema operacional registrado. Selecione o tipo de VM na camada de Armazenamento Premium. No exemplo abaixo, estamos usando o tamanho de VM *Standard_DS2*.

> [!NOTE]
> Atualize o tamanho do disco para assegurar que ele corresponda à sua capacidade, requisitos de desempenho e aos tamanhos de disco do Azure disponíveis.
>
>

Execute passo a passo os cmdlets do PowerShell abaixo para criar a nova VM. Primeiro, defina os parâmetros comuns:

```powershell
$serviceName = "yourVM"
$location = "location-name" (e.g., West US)
$vmSize ="Standard_DS2"
$adminUser = "youradmin"
$adminPassword = "yourpassword"
$vmName ="yourVM"
$vmSize = "Standard_DS2"
```

Primeiro, crie um serviço de nuvem em que você hospedará suas novas VMs.

```powershell
New-AzureService -ServiceName $serviceName -Location $location
```

Em segundo lugar, dependendo do cenário, crie a instância VM do Azure por meio da imagem do sistema operacional ou do disco do sistema operacional registrado.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>VHD do Sistema Operacional Generalizado para criar várias instâncias de VM do Azure
Crie uma ou mais instâncias de VM do Azure novas da Série DS usando a **Imagem do sistema operacional do Azure** registrada. Especifique o nome da Imagem do sistema operacional na configuração da máquina virtual ao criar uma nova máquina, conforme mostrado abaixo.

```powershell
$OSImage = Get-AzureVMImage –ImageName "OSImageName"

$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

New-AzureVM -ServiceName $serviceName -VM $vm
```

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>VHD do sistema operacional exclusivo para criar uma instância de VM do Azure
Crie uma nova instância de VM do Azure da série DS usando o **Disco do sistema operacional do Azure** registrado. Especifique o nome do Disco do sistema operacional na configuração da máquina virtual ao criar uma nova máquina, conforme mostrado abaixo.

```powershell
$OSDisk = Get-AzureDisk –DiskName "OSDisk"

$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

New-AzureVM -ServiceName $serviceName –VM $vm
```

Especifique outras informações da VM do Azure, como um serviço de nuvem, região, conta de armazenamento, conjunto de disponibilidade e política de cache. Observe que a instância VM deve estar localizada junto com o sistema operacional ou discos de dados associados, portanto, o serviço de nuvem selecionado, a região e a conta de armazenamento devem estar no mesmo local dos VHDs subjacentes desses discos.

### <a name="attach-data-disk"></a>Anexar disco de dados
Por fim, se você registrou os VHDs de disco de dados, anexe-os à nova VM do Azure compatível com Armazenamento Premium.

Use o seguinte cmdlet do PowerShell para anexar um disco de dados à nova VM e especifique a política de cache. No exemplo abaixo, a política de cache é definida como *Somente Leitura*.

```powershell
$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

Update-AzureVM  -VM $vm
```

> [!NOTE]
> Pode haver etapas adicionais necessárias para dar suporte aos aplicativos que não são abrangidas por este guia.
>
>

### <a name="checking-and-plan-backup"></a>Verificação e planejamento de backup
Depois que a nova VM estiver em funcionamento, acesse-a usando a mesma id de logon e senha usadas para a VM original e verifique se tudo está funcionando conforme o esperado. Todas as configurações, incluindo volumes distribuídos, devem estar presentes na nova VM.

A última etapa consiste em planejar o backup e a agenda de manutenção para a nova VM com base nas necessidades do aplicativo.

### <a name="a-sample-migration-script"></a>Um script de migração de exemplo
Se você tiver várias VMs para migrar, a automação por meio de scripts do PowerShell será útil. A seguir está um script de exemplo que automatiza a migração de uma VM. Observe que o script abaixo é apenas um exemplo, e algumas suposições são feitas sobre os discos de VM atuais. Talvez seja necessário atualizar o script para corresponder a seu cenário específico.

As suposições são:

* Você está criando VMs clássicas do Azure.
* Seus Discos de origem do Sistema Operacional e Discos de Dados de origem estão na mesma conta de armazenamento e no mesmo contêiner. Se os Discos de SO e Discos de Dados não estiverem no mesmo lugar, você poderá usar o AzCopy ou o Azure PowerShell para copiar VHDs em contêineres e contas de armazenamento. Confira a etapa anterior: [Copiar VHD com AzCopy ou PowerShell](#copy-vhd-with-azcopy-or-powershell). Editar este script para atender a seu cenário é outra opção, mas recomendamos usar o AzCopy ou o PowerShell, pois é mais fácil e rápido.

O script de automação é fornecido abaixo. Substitua o texto por suas informações e atualize o script para corresponder a seu cenário específico.

> [!NOTE]
> Usar o script existente não preserva a configuração de rede da VM de origem. Você precisará reconfigurar as configurações de rede em suas VMs migradas.
>
>

```
    <#
    .Synopsis
    This script is provided as an EXAMPLE to show how to migrate a VM from a standard storage account to a premium storage account. You can customize it according to your specific requirements.

    .Description
    The script will copy the vhds (page blobs) of the source VM to the new storage account.
    And then it will create a new VM from these copied vhds based on the inputs that you specified for the new VM.
    You can modify the script to satisfy your specific requirement, but please be aware of the items specified
    in the Terms of Use section.

    .Terms of Use
    Copyright © 2015 Microsoft Corporation.  All rights reserved.

    THIS CODE AND ANY ASSOCIATED INFORMATION ARE PROVIDED "AS IS" WITHOUT WARRANTY OF ANY KIND,
    EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE IMPLIED WARRANTIES OF MERCHANTABILITY
    AND/OR FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK OF USE, INABILITY TO USE, OR
    RESULTS FROM THE USE OF THIS CODE REMAINS WITH THE USER.

    .Example (Save this script as Migrate-AzureVM.ps1)

    .\Migrate-AzureVM.ps1 -SourceServiceName CurrentServiceName -SourceVMName CurrentVMName –DestStorageAccount newpremiumstorageaccount -DestServiceName NewServiceName -DestVMName NewDSVMName -DestVMSize "Standard_DS2" –Location "Southeast Asia"

    .Link
    To find more information about how to set up Azure PowerShell, refer to the following links.
    http://azure.microsoft.com/documentation/articles/powershell-install-configure/
    http://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

    #>

    Param(
    # the cloud service name of the VM.
    [Parameter(Mandatory = $true)]
    [string] $SourceServiceName,

    # The VM name to copy.
    [Parameter(Mandatory = $true)]
    [String] $SourceVMName,

    # The destination storage account name.
    [Parameter(Mandatory = $true)]
    [String] $DestStorageAccount,

    # The destination cloud service name
    [Parameter(Mandatory = $true)]
    [String] $DestServiceName,

    # the destination vm name
    [Parameter(Mandatory = $true)]
    [String] $DestVMName,

    # the destination vm size
    [Parameter(Mandatory = $true)]
    [String] $DestVMSize,

    # the location of destination VM.
    [Parameter(Mandatory = $true)]
    [string] $Location,

    # whether or not to copy the os disk, the default is only copy data disks
    [Parameter(Mandatory = $false)]
    [Bool] $DataDiskOnly = $true,

    # how frequently to report the copy status in sceconds
    [Parameter(Mandatory = $false)]
    [Int32] $CopyStatusReportInterval = 15,

    # the name suffix to add to new created disks to avoid conflict with source disk names
    [Parameter(Mandatory = $false)]
    [String]$DiskNameSuffix = "-prem"

    ) #end param

    #######################################################################
    #  Verify Azure PowerShell module and version
    #######################################################################

    #import the Azure PowerShell module
    Write-Host "`n[WORKITEM] - Importing Azure PowerShell module" -ForegroundColor Yellow
    $azureModule = Import-Module Azure -PassThru

    if ($azureModule -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    else
    {
        #show module not found interaction and bail out
        Write-Host "[ERROR] - PowerShell module not found. Exiting." -ForegroundColor Red
        Exit
    }


    #Check the Azure PowerShell module version
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module verion" -ForegroundColor Yellow
    If ($azureModule.Version -ge (New-Object System.Version -ArgumentList "0.8.14"))
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    Else
    {
        Write-Host "[ERROR] - Azure PowerShell module must be version 0.8.14 or higher. Exiting." -ForegroundColor Red
        Exit
    }

    #Check if there is an azure subscription set up in PowerShell
    Write-Host "`n[WORKITEM] - Checking Azure Subscription" -ForegroundColor Yellow
    $currentSubs = Get-AzureSubscription -Current
    if ($currentSubs -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
        Write-Host "`tYour current azure subscription in PowerShell is $($currentSubs.SubscriptionName)." -ForegroundColor Green
    }
    else
    {
        Write-Host "[ERROR] - There is no valid Azure subscription found in PowerShell. Please refer to this article http://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an Azure subscription. Exiting." -ForegroundColor Red
        Exit
    }


    #######################################################################
    #  Check if the VM is shut down
    #  Stopping the VM is a required step so that the file system is consistent when you do the copy operation.
    #  Azure does not support live migration at this time..
    #######################################################################

    if (($sourceVM = Get-AzureVM –ServiceName $SourceServiceName –Name $SourceVMName) -eq $null)
    {
        Write-Host "[ERROR] - The source VM doesn't exist in the current subscription. Exiting." -ForegroundColor Red
        Exit
    }

    # check if VM is shut down
    if ( $sourceVM.Status -notmatch "Stopped" )
    {
        Write-Host "[Warning] - Stopping the VM is a required step so that the file system is consistent when you do the copy operation. Azure does not support live migration at this time. If you'd like to create a VM from a generalized image, sys-prep the Virtual Machine before stopping it." -ForegroundColor Yellow
        $ContinueAnswer = Read-Host "`n`tDo you wish to stop $SourceVMName now? Input 'N' if you want to shut down the VM manually and come back later.(Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $sourceVM | Stop-AzureVM

        # wait until the VM is shut down
        $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        while ($VMStatus -notmatch "Stopped")
        {
            Write-Host "`n[Status] - Waiting VM $vmName to shut down" -ForegroundColor Green
            Sleep -Seconds 5
            $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        }
    }

    # exporting the sourve vm to a configuration file, you can restore the original VM by importing this config file
    # see more information for Import-AzureVM
    $workingDir = (Get-Location).Path
    $vmConfigurationPath = $env:HOMEPATH + "\VM-" + $SourceVMName + ".xml"
    Write-Host "`n[WORKITEM] - Exporting VM configuration to $vmConfigurationPath" -ForegroundColor Yellow
    $exportRe = $sourceVM | Export-AzureVM -Path $vmConfigurationPath


    #######################################################################
    #  Copy the vhds of the source vm
    #  You can choose to copy all disks including os and data disks by specifying the
    #  parameter -DataDiskOnly to be $false. The default is to copy only data disk vhds
    #  and the new VM will boot from the original os disk.
    #######################################################################

    $sourceOSDisk = $sourceVM.VM.OSVirtualHardDisk
    $sourceDataDisks = $sourceVM.VM.DataVirtualHardDisks

    # Get source storage account information, not considering the data disks and os disks are in different accounts
    $sourceStorageAccountName = $sourceOSDisk.MediaLink.Host -split "\." | select -First 1
    $sourceStorageKey = (Get-AzureStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzureStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzureStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzureStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzureStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzureStorageContainer -Context $destContext -Name vhds
    }


    $allDisksToCopy = $sourceDataDisks
    # check if need to copy os disk
    $sourceOSVHD = $sourceOSDisk.MediaLink.Segments[2]
    if ($DataDiskOnly)
    {
        # copy data disks only, this option requires deleting the source VM so that dest VM can boot
        # from the same vhd blob.
        $ContinueAnswer = Read-Host "`n`t[Warning] You chose to copy data disks only. Moving VM requires removing the original VM (the disks and backing vhd files will NOT be deleted) so that the new VM can boot from the same vhd. This is an irreversible action. Do you wish to proceed right now? (Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $destOSVHD = Get-AzureStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
        Write-Host "`n[WORKITEM] - Removing the original VM (the vhd files are NOT deleted)." -ForegroundColor Yellow
        Remove-AzureVM -Name $SourceVMName -ServiceName $SourceServiceName

        Write-Host "`n[WORKITEM] - Waiting utill the OS disk is released by source VM. This may take up to several minutes."
        $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        while ($diskAttachedTo -ne $null)
        {
            Start-Sleep -Seconds 10
            $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        }

    }
    else
    {
        # copy the os disk vhd
        Write-Host "`n[WORKITEM] - Starting copying os disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $allDisksToCopy += @($sourceOSDisk)
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
        # update the media link to point to the target blob link
        $disk.MediaLink = $targetBlob.ICloudBlob.Uri.AbsoluteUri
    }

    # Wait until all vhd files are copied.
    $diskComplete = @()
    do
    {
        Write-Host "`n[WORKITEM] - Waiting for all disk copy to complete. Checking status every $CopyStatusReportInterval seconds." -ForegroundColor Yellow
        # check status every 30 seconds
        Sleep -Seconds $CopyStatusReportInterval
        foreach ( $disk in $allDisksToCopy)
        {
            if ($diskComplete -contains $disk)
            {
                Continue
            }
            $blobName = $disk.MediaLink.Segments[2]
            $copyState = Get-AzureStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
            if ($copyState.Status -eq "Success")
            {
                Write-Host "`n[Status] - Success for disk copy $($disk.DiskName) at $($copyState.CompletionTime)" -ForegroundColor Green
                $diskComplete += $disk
            }
            else
            {
                if ($copyState.TotalBytes -gt 0)
                {
                    $percent = ($copyState.BytesCopied / $copyState.TotalBytes) * 100
                    Write-Host "`n[Status] - $('{0:N2}' -f $percent)% Complete for disk copy $($disk.DiskName)" -ForegroundColor Green
                }
            }
        }
    }
    while($diskComplete.Count -lt $allDisksToCopy.Count)

    #######################################################################
    #  Create a new vm
    #  the new VM can be created from the copied disks or the original os disk.
    #  You can ddd your own logic here to satisfy your specific requirements of the vm.
    #######################################################################

    # Create a VM from the existing os disk
    if ($DataDiskOnly)
    {
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $sourceOSDisk.DiskName
    }
    else
    {
        $newOSDisk = Add-AzureDisk -OS $sourceOSDisk.OS -DiskName ($sourceOSDisk.DiskName + $DiskNameSuffix) -MediaLocation $destOSVHD.ICloudBlob.Uri.AbsoluteUri
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $newOSDisk.DiskName
    }
    # Attached the copied data disks to the new VM
    foreach ($dataDisk in $sourceDataDisks)
    {
        # add -DiskLabel $dataDisk.DiskLabel if there are labels for disks of the source vm
        $diskLabel = "drive" + $dataDisk.Lun
        $vm | Add-AzureDataDisk -ImportFrom -DiskLabel $diskLabel -LUN $dataDisk.Lun -MediaLocation $dataDisk.MediaLink
    }

    # Edit this if you want to add more custimization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location
```

#### <a name="optimization"></a>Otimização
A configuração atual da VM pode ser personalizada especificamente para funcionar bem com discos Padrão. Por exemplo, para aumentar o desempenho usando vários discos em um volume distribuído. Por exemplo, em vez de usar quatro discos separadamente no Armazenamento Premium, você poderá otimizar o custo tendo um único disco. Otimizações como essa precisam ser tratadas caso a caso e exigem etapas personalizadas após a migração. Observe também que esse processo pode não funcionar bem para bancos de dados e aplicativos que dependem do layout de disco definido na configuração.

##### <a name="preparation"></a>Preparação
1. Conclua a Migração Simples, conforme descrito na seção anterior. Serão executadas otimizações na nova VM após a migração.
2. Defina os novos tamanhos de disco necessários para a configuração otimizada.
3. Determine o mapeamento dos volumes/discos atuais para as especificações do novo disco.

##### <a name="execution-steps"></a>Etapas de execução
1. Crie os novos discos com os tamanhos corretos na VM de Armazenamento Premium.
2. Faça logon na VM e copie os dados do volume atual para o novo disco que está mapeado para esse volume. Faça isso para todos os volumes atuais que precisam ser mapeados para um novo disco.
3. Em seguida, altere as configurações de aplicativo para alternar para os novos discos e desanexe os volumes antigos.

Para ajustar o aplicativo para melhorar o desempenho de disco, confira [Otimizar o desempenho do aplicativo](storage-premium-storage-performance.md#optimizing-application-performance).

### <a name="application-migrations"></a>Migrações de aplicativos
Bancos de dados e outros aplicativos complexos podem exigir etapas especiais, conforme definido pelo provedor do aplicativo para a migração. Consulte a documentação do respectivo aplicativo. Por exemplo , normalmente, bancos de dados podem ser migrados por meio de backup e restauração.

## <a name="next-steps"></a>Próximas etapas
Consulte as seguintes fontes para cenários específicos de migração de máquinas virtuais:

* [Migrar Máquinas Virtuais do Azure entre as Contas de Armazenamento](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Crie e carregue um VHD do Windows Server no Azure.](../../virtual-machines/windows/classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Criando e Carregando um Disco Rígido Virtual que Contém o Sistema Operacional Linux](../../virtual-machines/linux/classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Migrando Máquinas Virtuais do Amazon AWS para o Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Confira também as fontes a seguir para saber mais sobre o Armazenamento do Azure e as Máquinas Virtuais do Azure:

* [Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Máquinas Virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Armazenamento Premium: Armazenamento de Alto Desempenho para as Cargas de Trabalho da Máquina Virtual do Azure](storage-premium-storage.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: http://technet.microsoft.com/library/hh831739.aspx
