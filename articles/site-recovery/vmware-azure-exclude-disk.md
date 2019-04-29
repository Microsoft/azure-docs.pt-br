---
title: Excluir discos da replicação para recuperação de desastre do VMware para o Azure usando o Azure Site Recovery | Microsoft Docs
description: Descreve por que e como excluir discos de VM da replicação para recuperação de desastre do VMware para o Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.date: 3/3/2019
ms.author: mayg
ms.topic: conceptual
ms.openlocfilehash: 105074892cc6dfa4da1e7c8ddd0a0aad9f1b60a1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60921825"
---
# <a name="exclude-disks-from-replication-of-vmware-vms-to-azure"></a>Excluir discos da replicação de VMs VMware para o Azure

Este artigo descreve como excluir discos ao replicar as VMware VMs ao Azure. Essa exclusão pode otimizar a largura de banda de replicação consumida ou otimizar os recursos de destino que esses discos utilizam. Se você precisar de informações sobre a exclusão de discos do Hyper-V, leia [neste artigo](hyper-v-exclude-disk.md)


## <a name="prerequisites"></a>Pré-requisitos

Por padrão, todos os discos em um computador são replicados. Para excluir um disco da replicação, você deve instalar manualmente o serviço de Mobilidade na máquina antes de habilitar a replicação se estiver replicando do VMware para o Azure.


## <a name="why-exclude-disks-from-replication"></a>Por que excluir discos da replicação?
Excluir discos da replicação geralmente é necessário porque:

- Os dados formados no disco excluído não são importantes ou não precisam ser replicados.

- Você deseja salvar os recursos de armazenamento e rede não replicando essa variação.

## <a name="what-are-the-typical-scenarios"></a>Quais são os cenários típicos?
Você pode identificar exemplos específicos de variação de dados que são bons candidatos para exclusão. Os exemplos podem incluir gravações em um arquivo de paginação (pagefile.sys) e gravações no arquivo tempdb do Microsoft SQL Server. Dependendo da carga de trabalho e o subsistema de armazenamento, o arquivo de paginação pode registrar uma variação considerável. No entanto, replicar esses dados do site primário para o Azure seria muitos recursos. Portanto, você pode usar as etapas a seguir para otimizar a replicação de uma máquina virtual com um único disco virtual com o sistema operacional e o arquivo de paginação:

1. Divida o disco virtual em dois discos virtuais. Um disco virtual tem o sistema operacional e o outro tem o arquivo de paginação.
2. Exclua o disco do arquivo de paginação da replicação.

Da mesma forma, você pode usar as etapas a seguir para otimizar um disco que tenha o arquivo tempdb do Microsoft SQL Server e o arquivo de banco de dados do sistema:

1. Manter o banco de dados do sistema e tempdb em dois discos diferentes.
2. Excluir o disco de tempdb da replicação.

## <a name="how-to-exclude-disks-from-replication"></a>Como excluir discos da replicação?

Execute o fluxo de trabalho [Habilitar replicação](vmware-azure-enable-replication.md) para proteger uma máquina virtual do portal do Azure Site Recovery. Na quarta etapa do fluxo de trabalho, use a coluna **DISCO PARA REPLICAR** para excluir discos da replicação. Por padrão, todos os discos são selecionados para replicação. Desmarque a caixa de seleção dos discos que você deseja excluir da replicação e conclua as etapas para habilitar a replicação.

![Excluir discos da replicação e habilitar a replicação para failback do VMware para o Azure](./media/vmware-azure-exclude-disk/enable-replication-exclude-disk1.png)


>[!NOTE]
>
> * Você pode excluir somente os discos nas VMs que já têm o serviço de Mobilidade instalado. Você precisa instalar o serviço de Mobilidade manualmente porque ele só é instalado usando o mecanismo de push depois que a replicação é habilitada.
> * Apenas discos básicos podem ser excluídos da replicação. Você não pode excluir o sistema operacional ou discos dinâmicos.
> * Depois de habilitar a replicação, você não pode adicionar ou remover discos para replicação. Se desejar adicionar ou excluir um disco, você precisará desabilitar a proteção do computador e habilitá-la novamente.
> * Se excluir um disco necessário para um aplicativo operar, após o failover no Azure você precisará criá-lo manualmente no Azure para que possa executar o aplicativo replicado. Como alternativa, você pode integrar a automação do Azure em um plano de recuperação para criar o disco durante o failover do computador.
> * Máquina virtual do Windows: Os discos que você criar manualmente no Azure não sofrerão failback. Por exemplo, se você executar failover de três discos e criar dois diretamente em VMs do Azure, apenas os três discos com failover serão enviados por failback. Não é possível incluir discos criados manualmente em failback ou em nova proteção do local para o Azure.
> * Máquina Virtual do Linux: Os discos que você criar manualmente no Azure sofrerão failback. Por exemplo, se você executar o failover em três discos e criar dois discos diretamente em máquinas virtuais do Azure, todos os cinco serão submetidos a failback. Você não pode excluir do failback discos que foram criados manualmente.
>


## <a name="end-to-end-scenarios-of-exclude-disks"></a>Cenários de ponta a ponta para exclusão de discos
Vamos considerar dois cenários para entender o recurso de disco de exclusão:

- Disco tempdb do SQL Server
- Disco (pagefile.sys) de arquivo de paginação

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>Exemplo 1: Excluir disco tempdb do SQL Server
Vamos considerar uma máquina virtual do SQL Server que tenha um tempdb que possa ser excluído.

O nome do disco virtual é SalesDB.

Os discos na máquina virtual de origem são os seguintes:


**Nome do disco** | **Sistema operacional convidado - disco nº** | **Letra da unidade** | **Tipo de dados no disco**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Disco do sistema operacional
DB-Disk1| Disk1 | D:\ | Banco de dados do sistema SQL e User Database1
DB-Disco2 (disco excluído da proteção) | Disk2 | E:\ | Arquivos temporários
DB-Disco3 (disco excluído da proteção) | Disk3 | F:\ | Banco de dados do tempdb do SQL (caminho da pasta (F:\MSSQL\Data\) <br /> <br />Anote o caminho da pasta antes do failover.
DB-Disk4 | Disk4 |G:\ |User Database2

Como a variação de dados em dois discos da máquina virtual é temporária, enquanto proteger a máquina virtual SalesDB, exclua Disco2 e Disco3 da replicação. O Azure Site Recovery não replicará esses discos. Durante o failover, os discos não estarão presentes na máquina virtual de failover no Azure.

Os discos na máquina virtual do Azure após o failover são os seguintes:

**Sistema operacional convidado - disco nº** | **Letra da unidade** | **Tipo de dados no disco**
--- | --- | ---
DISK0 | C:\ | Disco do sistema operacional
Disk1 | E:\ | Armazenamento temporário<br /> <br />O Azure adiciona este disco e atribui a primeira letra de unidade disponível.
Disk2 | D:\ | Banco de dados do sistema SQL e User Database1
Disk3 | G:\ | User Database2

Como Disco2 e Disco3 foram excluídos da máquina virtual SalesDB, E: é a primeira letra de unidade disponível na lista. O Azure atribui e volume de armazenamento temporário. Para todos os discos replicados, as letras de unidade permanecem as mesmas.

O Disco3, que era o disco tempdb do SQL (caminho da pasta tempdb F:\MSSQL\Data\), foi excluído da replicação. O disco não está disponível na máquina virtual de failover. Como resultado, o serviço do SQL está em um estado parado e é necessário o caminho F:\MSSQL\Data.

Há duas maneiras de criar esse caminho:

- Adicione um novo disco e atribua o caminho da pasta tempdb.
- Use um disco de armazenamento temporário existente para o caminho da pasta tempdb.

### <a name="add-a-new-disk"></a>Adicione um novo disco:

1. Anote os caminhos do SQL de tempdb.mdf e tempdb.ldf antes do failover.
2. No portal do Azure, adicione um novo disco para o failover de máquina virtual com o mesmo ou mais o tamanho do disco de tempdb SQL de origem (Disco3).
3. Entrar na máquina virtual do Azure. No console de gerenciamento (diskmgmt.msc) do disco, inicialize e formate o disco recém-adicionado.
4. Atribua a mesma letra de unidade que foi usada pelo disco de tempdb do SQL (F:).
5. Crie uma pasta tempdb no volume F: (F:\MSSQL\Data).
6. Inicie o serviço SQL por meio do console de serviço.

### <a name="use-an-existing-temporary-storage-disk-for-the-sql-tempdb-folder-path"></a>Use um disco de armazenamento temporário existente para o caminho de pasta tempdb do SQL:

1. Abra um prompt de comando.
2. Execute o SQL Server no modo de recuperação do prompt de comando.

        Net start MSSQLSERVER /f / T3608

3. Execute o seguinte sqlcmd para alterar o caminho de tempdb para o novo caminho.

        sqlcmd -A -S SalesDB        **Use your SQL DBname**
        USE master;     
        GO      
        ALTER DATABASE tempdb       
        MODIFY FILE (NAME = tempdev, FILENAME = 'E:\MSSQL\tempdata\tempdb.mdf');
        GO      
        ALTER DATABASE tempdb       
        MODIFY FILE (NAME = templog, FILENAME = 'E:\MSSQL\tempdata\templog.ldf');       
        GO


4. Parar o serviço do Microsoft SQL Server.

        Net stop MSSQLSERVER
5. Inicie o serviço do Microsoft SQL Server.

        Net start MSSQLSERVER

Confira as seguintes diretrizes do Azure para o disco de armazenamento temporário:

* [Uso de SSDs em VMs do Azure para armazenar o TempDB do SQL Server e Extensões do Pool de Buffer](https://blogs.technet.microsoft.com/dataplatforminsider/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)
* [Práticas recomendadas para o SQL Server em Máquinas Virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance)

## <a name="failback-from-azure-to-an-on-premises-host"></a>Failback (do Azure para um host local)
Agora vamos entender os discos que são replicados quando você faz o failover do Azure para seu VMware local. Os discos criados manualmente no Azure não serão replicados. Por exemplo, se você executar failover de três discos e criar dois diretamente na VM do Azure, apenas os três discos com failover serão enviados por failback. Não é possível incluir discos criados manualmente em failback ou em nova proteção do local para o Azure. Ele também não replicar disco de armazenamento temporário para hosts locais.

### <a name="failback-to-original-location-recovery"></a>Failback para recuperação no local original

No exemplo anterior, a configuração de disco de máquina virtual do Azure é a seguinte:

**Sistema operacional convidado - disco nº** | **Letra da unidade** | **Tipo de dados no disco**
--- | --- | ---
DISK0 | C:\ | Disco do sistema operacional
Disk1 | E:\ | Armazenamento temporário<br /> <br />O Azure adiciona este disco e atribui a primeira letra de unidade disponível.
Disk2 | D:\ | Banco de dados do sistema SQL e User Database1
Disk3 | G:\ | User Database2

Quando é feito failback para o local original, a configuração de disco de máquina virtual de failback não tem discos excluídos. Os discos que foram excluídos do VMware no Azure não estarão disponíveis na máquina virtual de failback.

Após o failover planejado do Azure para o VMware no local, os discos na máquina virtual VMWare (local original) são:

**Sistema operacional convidado - disco nº** | **Letra da unidade** | **Tipo de dados no disco**
--- | --- | ---
DISK0 | C:\ | Disco do sistema operacional
Disk1 | D:\ | Banco de dados do sistema SQL e User Database1
Disk2 | G:\ | User Database2

## <a name="example-2-exclude-the-paging-file-pagefilesys-disk"></a>Exemplo 2: Excluir o disco (pagefile.sys) do arquivo de paginação

Vamos considerar uma máquina virtual que tem um disco de arquivo de paginação que pode ser excluído.
Existem dois casos.

### <a name="case-1-the-paging-file-is-configured-on-the-d-drive"></a>Caso 1: O arquivo de paginação está configurado na unidade D:
Aqui está a configuração de disco:

**Nome do disco** | **Sistema operacional convidado - disco nº** | **Letra da unidade** | **Tipo de dados no disco**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Disco do sistema operacional
DB-Disk1 (disco excluído da proteção) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | User data 1
DB-Disk3 | Disk3 | F:\ | User data 2

Aqui estão as configurações de arquivo de paginação na máquina virtual de origem:

![Configurações do arquivo de paginação na máquina virtual de origem](./media/vmware-azure-exclude-disk/pagefile-on-d-drive-sourcevm.png)


Após o failover da máquina virtual do VMware para o Azure, os discos na máquina virtual do Azure são:

**Nome do disco** | **Sistema operacional convidado - disco nº** | **Letra da unidade** | **Tipo de dados no disco**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Disco do sistema operacional
DB-Disk1 | Disk1 | D:\ | Armazenamento temporário<br /> <br />pagefile.sys
DB-Disk2 | Disk2 | E:\ | User data 1
DB-Disk3 | Disk3 | F:\ | User data 2

Como Disco1 (d) foi excluído, D: é a primeira letra de unidade na lista disponível. O Azure atribui D: ao volume de armazenamento temporário. Como a unidade D: está disponível na máquina virtual do Azure, a configuração de arquivo de paginação da máquina virtual permanece a mesma.

Aqui estão as configurações de arquivo de paginação na máquina virtual do Azure:

![Configurações do arquivo de paginação na máquina virtual do Azure](./media/vmware-azure-exclude-disk/pagefile-on-azure-vm-after-failover.png)

### <a name="case-2-the-paging-file-is-configured-on-another-drive-other-than-d-drive"></a>Caso 2: O arquivo de paginação está configurado em outra unidade (diferente da unidade D:)

Aqui está a configuração de disco de máquina virtual de origem:

**Nome do disco** | **Sistema operacional convidado - disco nº** | **Letra da unidade** | **Tipo de dados no disco**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Disco do sistema operacional
DB-Disco1 (disco excluído da proteção) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | User data 1
DB-Disk3 | Disk3 | F:\ | User data 2

Aqui estão as configurações de arquivo de paginação na máquina virtual local:

![Configurações do arquivo de paginação na máquina virtual local](./media/vmware-azure-exclude-disk/pagefile-on-g-drive-sourcevm.png)

Após o failover da máquina virtual do VMware para o Azure, os discos na máquina virtual do Azure são:

**Nome do disco** | **Sistema operacional convidado - disco nº** | **Letra da unidade** | **Tipo de dados no disco**
--- | --- | --- | ---
DB-Disk0-OS | DISK0  |C:\ |Disco do sistema operacional
DB-Disk1 | Disk1 | D:\ | Armazenamento temporário<br /> <br />pagefile.sys
DB-Disk2 | Disk2 | E:\ | User data 1
DB-Disk3 | Disk3 | F:\ | User data 2

Como D: é a primeira letra de unidade de disponível na lista, o Azure atribui D: ao volume de armazenamento temporário. Para todos os discos replicados, a letra da unidade permanece igual. Como o disco G: não está disponível, o sistema usará a unidade C: para o arquivo de paginação.

Aqui estão as configurações de arquivo de paginação na máquina virtual do Azure:

![Configurações do arquivo de paginação na máquina virtual do Azure](./media/vmware-azure-exclude-disk/pagefile-on-azure-vm-after-failover-2.png)

## <a name="next-steps"></a>Próximas etapas
Depois que a implantação estiver configurada e em funcionamento, [saiba mais](site-recovery-failover.md) sobre o os diferentes tipos de failover.
