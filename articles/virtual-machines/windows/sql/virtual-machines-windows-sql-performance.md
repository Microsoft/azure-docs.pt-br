---
title: "Práticas recomendadas de desempenho para o SQL Server no Azure | Microsoft Docs"
description: "Fornece as práticas recomendadas para aprimoramento do desempenho do SQL Server em VMs do Microsoft Azure."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2017
ms.author: jroth
ms.openlocfilehash: 5595ea016ab01d20cee82b75f56623bb0727a1b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="performance-best-practices-for-sql-server-in-azure-virtual-machines"></a>Práticas recomendadas relacionadas ao desempenho para o SQL Server em máquinas virtuais do Azure

## <a name="overview"></a>Visão geral

Este tópico fornece as práticas recomendadas para otimização do desempenho do SQL Server na Máquina Virtual do Microsoft Azure. Durante a execução do SQL Server em máquinas virtuais do Azure, recomendamos que você continue usando as mesmas opções de ajuste de desempenho aplicáveis ao SQL Server no ambiente de servidor local. No entanto, o desempenho de um banco de dados relacional em uma nuvem pública depende de muitos fatores como, por exemplo, o tamanho de uma máquina virtual e a configuração dos discos de dados.

Ao criar imagens do SQL Server, [considere o provisionamento de suas VMs no portal do Azure](virtual-machines-windows-portal-sql-server-provision.md). VMs do SQL Server provisionadas no Portal com o Resource Manager implementam todas essas práticas recomendadas, incluindo a configuração de armazenamento.

Este artigo concentra-se na obtenção do *melhor* desempenho para o SQL Server em máquinas virtuais do Azure. Se sua carga de trabalho for menos exigente, talvez você não precise de todos os aprimoramentos relacionados abaixo. Considere suas necessidades de desempenho e padrões de carga de trabalho ao avaliar essas recomendações.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="quick-check-list"></a>Lista de verificação rápida

Veja a seguir uma lista de verificação rápida para obter o melhor desempenho do SQL Server em máquinas virtuais do Azure:

| Área | Otimizações |
| --- | --- |
| [Tamanho da VM](#vm-size-guidance) |[DS3](../../virtual-machines-windows-sizes-memory.md) ou superior para o SQL Enterprise Edition.<br/><br/>[DS2](../../virtual-machines-windows-sizes-memory.md) ou superior para os SQL Standard e Web Editions. |
| [Armazenamento](#storage-guidance) |Use o [Armazenamento Premium](../../../storage/common/storage-premium-storage.md). O armazenamento padrão é recomendado somente para desenvolvimento/teste.<br/><br/>Mantenha a [conta de armazenamento](../../../storage/common/storage-create-storage-account.md) e a VM do SQL Server na mesma região.<br/><br/>Desabilite o [armazenamento com redundância geográfica](../../../storage/common/storage-redundancy.md) (replicação geográfica) do Azure na conta de armazenamento. |
| [Discos](#disks-guidance) |Usar no mínimo dois [discos P30](../../../storage/common/storage-premium-storage.md#scalability-and-performance-targets) (um para arquivos de log; um para arquivos de dados e o TempDB).<br/><br/>Evite usar o sistema operacional ou discos temporários para armazenamento de banco de dados ou registro em log.<br/><br/>Habilite o caching nos discos que hospedam os arquivos de dados e o TempDB.<br/><br/>Não habilite o caching em discos que hospedam o arquivo de log.<br/><br/>Importante: interrompa o serviço do SQL Server ao alterar as configurações de cache para um disco de VM do Azure.<br/><br/>Particione vários discos de dados do Azure para obter maior taxa de transferência de E/S.<br/><br/>Formate com os tamanhos de alocação documentados. |
| [E/S](#io-guidance) |Habilite a compactação de página do banco de dados.<br/><br/>Habilite a inicialização instantânea de arquivos para arquivos de dados.<br/><br/>Limite ou desabilite o crescimento automático no banco de dados.<br/><br/>Desabilite a redução automática no banco de dados.<br/><br/>Mova todos os bancos de dados para discos de dados, incluindo bancos de dados do sistema.<br/><br/>Mova o log de erros do SQL Server e os diretórios de arquivos de rastreamento para discos de dados<br/><br/>Configure os locais do arquivo de banco de dados e backup padrão.<br/><br/>Habilite as páginas bloqueadas.<br/><br/>Aplique correções de desempenho do SQL Server. |
| [Específico do recurso](#feature-specific-guidance) |Faça backup diretamente no armazenamento de blob. |

Para saber mais sobre *como* e *por que* fazer essas otimizações, examine os detalhes e diretrizes fornecidos nas seções a seguir.

## <a name="vm-size-guidance"></a>Diretrizes de tamanho de VM

Para aplicativos sensíveis ao desempenho, é recomendável o uso dos seguintes [tamanhos de máquinas virtuais](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json):

* **SQL Server Enterprise Edition**: DS3 ou superior
* **SQL Server Standard e Web Editions**: DS2 ou superior

## <a name="storage-guidance"></a>Orientação de armazenamento

As VMs da série DS (com as séries DSv2 e GS) dão suporte ao [Armazenamento Premium](../../../storage/common/storage-premium-storage.md). O Armazenamento Premium é recomendado para todas as cargas de trabalho de produção.

> [!WARNING]
> O Armazenamento Standard tem largura de banda e latências variáveis e só é recomendado para cargas de trabalho de desenvolvimento e teste. As cargas de trabalho de produção devem usar o Armazenamento Premium.

Além disso, recomendamos a criação de sua conta de armazenamento do Azure no mesmo data center que suas máquinas virtuais do SQL Server a fim de reduzir os atrasos de transferência. Ao criar uma conta de armazenamento, desabilite a replicação geográfica, pois não há garantia para uma ordem de gravação consistente em vários discos. Em vez disso, considere a configuração de uma tecnologia de recuperação de desastres do SQL Server entre dois data centers do Azure. Para saber mais, confira [Alta disponibilidade e recuperação de desastre para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Diretrizes de discos

Há três tipos principais de discos em uma VM do Azure:

* **Disco do SO**: quando você cria uma Máquina Virtual do Azure, a plataforma anexa pelo menos um disco (identificado como unidade **C**) à VM para o disco do seu sistema operacional. Cada disco é um VHD armazenado como um blob de páginas no armazenamento.
* **Disco temporário**: Máquinas Virtuais do Azure contêm outro disco denominado disco temporário (identificado como a unidade **D**:). É um disco localizado no nó que pode ser usado como espaço de rascunho.
* **Discos de dados**: você também pode anexar outros discos à sua máquina virtual como discos de dados, e eles serão armazenados no armazenamento como blobs de páginas.

As seções a seguir descrevem as recomendações para usar esses diferentes discos.

### <a name="operating-system-disk"></a>Disco do sistema operacional

Um disco do sistema operacional é um VHD que pode ser inicializado e montado como uma versão em execução de um sistema operacional e é rotulado como a unidade **C** .

A política padrão de caching no disco do sistema operacional é de **Leitura/Gravação**. Para aplicativos sensíveis ao desempenho, recomendamos o uso de discos de dados em vez do disco do sistema operacional. Consulte a seção sobre Discos de Dados abaixo.

### <a name="temporary-disk"></a>Disco temporário

A unidade de armazenamento temporário, rotulada como a unidade **D**:, não é mantida no armazenamento de blob do Azure. Não armazene seus arquivos de banco de dados do usuário ou arquivos de log de transações do usuário na unidade **D**:.

Para VMs da série D, série Dv2 e série G, a unidade temporária dessas VMs é baseada em SSD. Se sua carga de trabalho faz uso intenso de TempDB (por exemplo, para objetos temporários ou junções complexas), armazenar o TempDB na unidade **D** pode resultar em maior produtividade e menor latência de TempDB.

Para VMs que têm suporte para Armazenamento Premium (séries DS, DSv2 e GS), é recomendável armazenar o TempDB em um disco que dê suporte ao Armazenamento Premium com o cache de leitura habilitado. Há uma exceção a essa recomendação. Se o uso do TempDB apresenta gravação intensa, obtenha um melhor desempenho armazenando o TempDB na unidade local **D**, que é também baseada em SSD nesses tamanhos de computador.

### <a name="data-disks"></a>Discos de dados

* **Usar discos de dados para arquivos de dados e de log**: no mínimo, use dois [discos P30](../../../storage/common/storage-premium-storage.md#scalability-and-performance-targets) do Armazenamento Premium: um deles com os arquivos de log e o outro com os arquivos de dados e do TempDB. Cada disco do Armazenamento Premium fornece um número de IOPs e largura de banda (MB/s), dependendo de seu tamanho, conforme descrito neste artigo: [Usando o Armazenamento Premium para discos](../../../storage/common/storage-premium-storage.md).

* **Distribuição de Discos**: para produtividade mais alta, você pode adicionar outros discos de dados e usar a Distribuição de Discos. Para determinar o número de discos de dados, você precisa analisar o número de IOPS e a largura de banda necessários para os arquivos de log e para os arquivos de dados e do TempDB. Observe que diferentes tamanhos de VM têm diferentes limites no número de IOPS e na largura de banda com suporte; consulte as tabelas sobre IOPS por [tamanho de VM](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Use as seguintes diretrizes:

  * Para Windows 8/Windows Server 2012 ou posterior, use os [Espaços de Armazenamento](https://technet.microsoft.com/library/hh831739.aspx) com as seguintes diretrizes:

      1. Defina a intercalação (tamanho de faixa) como 64 KB (65536 bytes) para cargas de trabalho OLTP e 256 KB (262144 bytes) para cargas de trabalho de data warehouse, a fim de evitar o impacto no desempenho devido ao desalinhamento da partição. Isso deve ser definido com o PowerShell.
      1. Defina a contagem de colunas = número de discos físicos. Use o PowerShell ao configurar mais de 8 discos (não interface do usuário do Gerenciador do Servidor). 

    Por exemplo, o PowerShell a seguir cria um novo pool de armazenamento com o tamanho de intercalação de 64 KB e o número de colunas de 2:

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Para o Windows 2008 R2 ou anterior, é possível usar discos dinâmicos (volumes de SO distribuídos) e o tamanho da faixa será sempre 64 KB. Observe que essa opção tornou-se obsoleta no Windows 8/Windows Server 2012. Para obter informações, veja a declaração de suporte em [O Serviço de Disco Virtual está em transição para a API de Gerenciamento de Armazenamento do Windows](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

  * Se sua carga de trabalho não utilizar muito o log e não precisar de um IPS dedicado, você poderá configurar apenas um pool de armazenamento. Caso contrário, crie dois pools de armazenamento, um para os arquivos de log e outro para os arquivos de dados e o TempDB. Determine o número de discos associados a cada pool de armazenamento com base em suas expectativas de carga. Tenha em mente que tamanhos de VM diferentes permitem quantidades diferentes de discos de dados anexados. Para obter mais informações, confira [Tamanhos das Máquinas Virtuais](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Se você não estiver usando o Armazenamento Premium (cenários de desenvolvimento/teste), a recomendação será adicionar o número máximo de discos de dados com suporte pelo [Tamanho da VM](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e usar a Distribuição de Discos.

* **Política de cache**: para discos de dados de Armazenamento Premium, habilite o cache de leitura nos discos de dados que hospedam os arquivos de dados e o TempDB. Se você não estiver usando o Armazenamento Premium, não habilite o caching em discos de dados. Para obter instruções sobre como configurar o caching de disco, confira os seguintes tópicos: [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) e [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx).

  > [!WARNING]
  > Interrompa o serviço SQL Server ao alterar a configuração do cache dos discos da VM do Azure a fim de evitar a possibilidade de corrupção do banco de dados.

* **Tamanho de unidade de alocação de NTFS**: ao formatar o disco de dados, recomendamos o uso de um tamanho de unidade de alocação de 64 KB para arquivos de dados e de log e também para o TempDB.

* **Práticas recomendadas de gerenciamento de disco**: ao remover um disco de dados ou alterar seu tipo de cache, interrompa o serviço SQL Server durante a alteração. Quando as configurações do cache são alteradas no disco do SO, o Azure interrompe a VM, altera o tipo de cache e reinicia a VM. Quando as configurações do cache de um disco de dados são alteradas, a VM não é interrompida, mas o disco de dados é desanexado da VM durante a mudança e depois reanexado.

  > [!WARNING]
  > A não interrupção do serviço SQL Server durante essas operações pode causar corrupção no banco de dados.

## <a name="io-guidance"></a>Diretrizes de E/S

* Os melhores resultados com o Armazenamento Premium são obtidos ao paralelizar seu aplicativo e as solicitações. O Armazenamento Premium foi projetado para cenários nos quais a profundidade da fila de E/S é maior do que um. Portanto, você verá pouco ou nenhum ganho de desempenho para solicitações em série de segmento único (mesmo se usarem muito armazenamento). Por exemplo, isso pode afetar os resultados do teste de segmento único de ferramentas de análise de desempenho, por exemplo, o SQLIO.

* Considere o uso da [compactação de página do banco de dados](https://msdn.microsoft.com/library/cc280449.aspx) , pois isso pode ajudar a melhorar o desempenho de cargas de trabalho com uso intenso de E/S. No entanto, a compactação de dados pode aumentar o consumo de CPU no servidor de banco de dados.

* Considere a habilitação da inicialização instantânea de arquivo a fim de reduzir o tempo necessário para alocação inicial do arquivo. Para aproveitar a inicialização instantânea de arquivo, conceda a SE_MANAGE_VOLUME_NAME à conta de serviço do SQL Server (MSSQLSERVER) e adicione-a à política de segurança **Executar Tarefas de Manutenção de Volume**. Se você estiver usando uma imagem da plataforma do SQL Server para o Azure, a conta de serviço padrão (NT Service\MSSQLSERVER) não será adicionada à política de segurança **Executar Tarefas de Manutenção de volume**. Em outras palavras, a inicialização instantânea de arquivo não está habilitada em uma imagem de plataforma do SQL Server do Azure. Depois de adicionar a conta de serviço do SQL Server à política de segurança **Executar Tarefas de Manutenção de Volume** , reinicie o serviço do SQL Server. Talvez existam considerações de segurança sobre a utilização desse recurso. Para obter mais informações, consulte [Inicialização de Arquivo de Banco de Dados](https://msdn.microsoft.com/library/ms175935.aspx).

* **aumento automático** é considerado meramente uma contingência para o aumento inesperado. Não gerencie diariamente o crescimento de seus dados e do log com o crescimento automático. Se o crescimento automático for usado, aumente previamente o arquivo usando a opção Tamanho.

* Verifique se a **redução automática** está desabilitada a fim de evitar uma sobrecarga desnecessária que pode afetar negativamente o desempenho.

* Mova todos os bancos de dados para discos de dados, incluindo bancos de dados do sistema. Para saber mais, confira [Mover bancos de dados do sistema](https://msdn.microsoft.com/library/ms345408.aspx).

* Mova o log de erros do SQL Server e os diretórios de arquivos de rastreamento para discos de dados Isso pode ser feito no SQL Server Configuration Manager clicando duas vezes na Instância do SQL Server e selecionando Propriedades. As configurações do arquivo de rastreamento e do log de erros podem ser alteradas na guia **Parâmetros de Inicialização**. O Diretório de Despejo é especificado na guia **Avançado**. A captura de tela a seguir mostra onde procurar o parâmetro de inicialização do log de erro.

    ![Captura de Tela de Log de Erros do SQL](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* Configure os locais do arquivo de banco de dados e backup padrão. Use as recomendações neste tópico e faça as alterações na janela de propriedades do servidor. Para obter instruções, confira [Exibir ou alterar os locais padrão de arquivos de log e de dados (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). A captura de tela a seguir demonstra onde fazer essas alterações.

    ![Log de Dados de SQL e Arquivos de Backup](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* Estabeleça páginas bloqueadas a fim de reduzir a ES e quaisquer atividades de paginação. Para saber mais, confira [Habilitar a opção Bloquear Páginas na Memória (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

* Se você estiver executando o SQL Server 2012, instale a Atualização Cumulativa 10 do Service Pack 1. Essa atualização contém a correção para o desempenho ruim de E/S ao executar a instrução seleção na tabela temporária no SQL Server 2012. Para obter informações, consulte este [artigo da base de dados de conhecimento](http://support.microsoft.com/kb/2958012).

* Considere a compactação de qualquer arquivo de dados durante a transferência de entrada/saída do Azure.

## <a name="feature-specific-guidance"></a>Diretrizes específicas do recurso

Algumas implantações podem obter outros benefícios de desempenho usando técnicas mais avançadas de configuração. A lista a seguir destaca alguns recursos do SQL Server que podem ajudá-lo a obter um desempenho maior:

* **Backup no armazenamento do Azure**: ao realizar backups do SQL Server em execução em máquinas virtuais do Azure, você poderá usar [Backup do SQL Server para URL](https://msdn.microsoft.com/library/dn435916.aspx). Esse recurso foi disponibilizado a partir do SQL Server 2012 SP1 CU2 e é recomendado para fazer o backup em discos de dados anexados. Quando fizer backup ou restaurar de/para o armazenamento do Azure, siga as recomendações fornecidas em [Práticas Recomendadas de Backup do SQL Server para URL e Solução de Problemas e Restauração de Backups Armazenados no Armazenamento do Azure](https://msdn.microsoft.com/library/jj919149.aspx). Você também pode automatizar esses backups usando o [Backup Automatizado para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-automated-backup.md).

    Antes do SQL Server 2012, use a [Ferramenta de backup do SQL Server para o Azure](https://www.microsoft.com/download/details.aspx?id=40740). Essa ferramenta pode ajudar a aumentar a taxa de transferência do backup utilizando vários alvos de distribuição de backup.

* **Arquivos de dados do SQL Server no Azure**: este recurso novo, [Arquivos de dados do SQL Server no Azure](https://msdn.microsoft.com/library/dn385720.aspx), está disponível desde o SQL Server 2014. A execução do SQL Server com os arquivos de dados no Azure demonstra características de desempenho comparáveis as dos discos de dados do Azure.

## <a name="next-steps"></a>Próximas etapas

Para conferir as práticas recomendadas de segurança, consulte [Considerações sobre segurança para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-security.md).

Examine outros tópicos sobre Máquinas Virtuais do SQL Server em [Visão geral do SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).
