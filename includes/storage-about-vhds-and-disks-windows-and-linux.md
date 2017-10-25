
## <a name="about-vhds"></a>Sobre VHDs

Os VHDs usados no Azure são arquivos .vhd armazenados como blobs de página em uma conta de armazenamento padrão ou premium no Azure. Para conhecer mais detalhes sobre os blobs, consulte [Noções Gerais sobre blobs de blocos e blobs de páginas](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs/). Para obter detalhes sobre o armazenamento premium, veja [Armazenamento premium de alto desempenho e máquinas virtuais do Azure](../articles/storage/common/storage-premium-storage.md).

O Azure dá suporte aos discos fixos no formato VHD. O formato fixo dispõe o disco lógico linearmente dentro do arquivo, de forma que o deslocamento X do disco é armazenado no deslocamento X do blob. Um pequeno rodapé ao final do blob descreve as propriedades do VHD. Frequentemente, o formato fixo desperdiça espaço, porque muitos discos têm em si grandes intervalos não usados. No entanto, o Azure armazena arquivos .vhd em um formato livre, assim você obtém os benefícios de discos fixos e dinâmicos ao mesmo tempo. Para obter mais detalhes, consulte [Introdução aos discos rígidos virtuais](https://technet.microsoft.com/library/dd979539.aspx).

Todos os arquivos .vhd no Azure que você deseja usar como fonte para criar discos ou imagens são somente leitura. Quando você cria um disco ou uma imagem, o Azure faz cópias dos arquivos .vhd. Essas cópias podem ser somente leitura ou de leitura e gravação, dependendo de como você usar o VHD.

Quando você cria uma máquina virtual por meio de uma imagem, o Azure cria um disco para a máquina virtual que é uma cópia do arquivo .vhd de origem. Para proteger contra exclusão acidental, o Azure faz uma concessão de qualquer arquivo .vhd de origem que é usado para criar uma imagem, um disco de sistema operacional ou um disco de dados.

Antes de excluir um arquivo .vhd de origem, você precisará remover a concessão excluindo o disco ou a imagem. Para excluir um arquivo .vhd que está sendo usado por uma máquina virtual como disco do sistema operacional, você pode excluir a máquina virtual, o disco do sistema operacional e o arquivo .vhd de origem de uma só vez excluindo a máquina virtual e todos os discos associados. No entanto, a exclusão de um arquivo .vhd que é uma origem de um disco de dados requer várias etapas em uma ordem definida. Primeiro, desanexar o disco da máquina virtual; em seguida, excluir o disco e, por fim, excluir o arquivo .vhd.

> [!WARNING]
> Se você excluir um arquivo .vhd de origem do armazenamento ou excluir sua conta de armazenamento, a Microsoft não poderá recuperar esses dados para você.
> 

## <a name="types-of-disks"></a>Tipos de discos 

Os Discos do Azure foram projetados para oferecer uma disponibilidade de 99,999%. Os Discos do Azure forneceram consistentemente a durabilidade no nível empresarial, com uma Taxa de Falha Anual de ZERO% líder do setor.

Há dois níveis de desempenho de armazenamento que você pode escolher ao criar seus discos – armazenamento Standard e Premium. Além disso, há dois tipos de discos--não gerenciado e gerenciado – e podem residir em qualquer nível de desempenho.


### <a name="standard-storage"></a>Armazenamento Standard 

Armazenamento padrão é apoiado por HDDs e oferece armazenamento econômico e eficaz. Armazenamento padrão pode ser replicado localmente em um data center ou ser redundante geograficamente com centros de dados primários e secundários. Para saber mais sobre a replicação de armazenamento, veja [Replicação do Armazenamento do Azure](../articles/storage/common/storage-redundancy.md). 

Para saber mais sobre como usar o armazenamento Standard com discos de VM, veja [Armazenamento Standard e discos](../articles/storage/common/storage-standard-storage.md).

### <a name="premium-storage"></a>Armazenamento Premium 

O Armazenamento Premium tem o suporte de SSDs e oferece suporte de disco de alto desempenho e baixa latência para VMs executando cargas de trabalho intensivas para entradas e saídas. Você pode usar o Armazenamento Premium com DS, DSv2, GS, Ls ou FS as VMs do Azure da série. Para saber mais, veja [Armazenamento Premium](../articles/storage/common/storage-premium-storage.md).

### <a name="unmanaged-disks"></a>Discos não gerenciados

Discos não gerenciados são do tipo tradicional de discos que foram usados por máquinas virtuais. Com isso, você cria sua própria conta de armazenamento e especifique a conta de armazenamento quando você criar o disco. Você precisa certificar-se de não colocar muitos discos na mesma conta de armazenamento, pois você pode exceder o [metas de escalabilidade](../articles/storage/common/storage-scalability-targets.md) da conta de armazenamento (20.000 IOPS, por exemplo), resultando em máquinas virtuais sendo limitadas. Com discos não gerenciados, você precisa descobrir como maximizar o uso de uma ou mais contas de armazenamento para obter o melhor desempenho do suas VMs.

### <a name="managed-disks"></a>Discos gerenciados 

Gerenciado identificadores de discos de armazenamento de criação/gerenciamento em segundo plano de contas para você e garante que você não precisa se preocupar sobre os limites de escalabilidade da conta de armazenamento. Você simplesmente especifica o tamanho do disco e o nível de desempenho (Standard/Premium) e o Azure cria e gerencia o disco para você. Mesmo que você adicione discos ou dimensionar a VM para cima e para baixo, você não precisa se preocupar sobre o armazenamento está sendo usado. 

Você também pode gerenciar suas imagens personalizadas em uma conta de armazenamento por região do Azure e usá-las para criar centenas de VMs na mesma assinatura. Para saber mais sobre discos gerenciados, veja a [Visão geral de discos gerenciados](../articles/virtual-machines/windows/managed-disks-overview.md).

É recomendável que você use Azure Managed Disks para novas VMs e converta os discos não gerenciados anteriores para discos gerenciados, para tirar proveito dos muitos recursos disponíveis nos Managed Disks.

### <a name="disk-comparison"></a>Comparação de disco

A tabela a seguir fornece uma comparação do vs Premium padrão para discos gerenciados e não para ajudá-lo a decidir o que usar.

|    | Disco Premium do Azure | Disco padrão do Azure |
|--- | ------------------ | ------------------- |
| Tipo de disco | Unidades de Estado Sólido (SSD) | Unidades de Disco Rígido (HDD)  |
| Visão geral  | Suporte a disco com base em SSD de alto desempenho e baixa latência, para máquinas virtuais executando cargas de trabalho com uso intensivo de E/S ou hospedando ambiente de produção de missão crítica | Suporte a disco econômico com base em HDD para cenários de VM de desenvolvimento/teste |
| Cenário  | Cargas de trabalho confidenciais produção e desempenho | Desenvolvimento e teste, não-crítico, <br>Acesso infrequente |
| Tamanho do disco | P4: 32 GB (somente para discos gerenciados)<br>P6: 64 GB (somente para discos gerenciados)<br>P10: 128 GB<br>P20: 512 GB<br>P30: 1024 GB<br>P40: 2048 GB<br>P50: GB 4095 | Discos não Gerenciados: 1 GB a 4 TB (4095 GB) <br><br>Managed Disks:<br> S4: 32 GB <br>S6: 64 GB <br>S10: 128 GB <br>S20: 512 GB <br>S30: 1024 GB <br>S40: 2048 GB<br>S50: GB 4095| 
| Taxa de Transferência Máxima por Disco | 250 MB/s | 60 MB/s | 
| IOPS Máxima por Disco | 7500 IOPS | 500 IOPS | 

