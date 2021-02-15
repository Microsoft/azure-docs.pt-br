---
title: Excluir discos da replicação com o Azure Site Recovery
description: Como excluir discos da replicação para o Azure com o Azure Site Recovery.
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 15989fbfd65f758eb777c5170c217aba8707e0be
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008252"
---
# <a name="exclude-disks-from-disaster-recovery"></a>Excluir discos a partir da recuperação de desastre

Este artigo descreve como excluir discos da replicação durante a recuperação de desastres no local para o Azure com o [Azure Site Recovery](site-recovery-overview.md). Você pode excluir discos da replicação por vários motivos:

- Para que os dados irrelevantes produzidos no disco excluído não sejam replicados.
- Para otimizar a largura de banda de replicação consumida ou os recursos no lado de destino.
- Para salvar os recursos de armazenamento e rede não replicando os dados desnecessários.
- As VMs do Azure atingiram os limites de replicação do Site Recovery.


## <a name="supported-scenarios"></a>Cenários com suporte

Você pode excluir discos da replicação conforme resumido na tabela.

**Azure para Azure** | **VMware no Azure** | **Hyper-V para Azure** | **Servidor Físico para o Azure**
--- | --- | --- | ---
Sim | Sim | Sim | Sim

## <a name="exclude-limitations"></a>Limitações de exclusão

**Limitação** | **VMs do Azure** | **VMs VMware** | **VMs Hyper-V**
--- | --- | ---
**Tipos de disco** | Você pode excluir somente os discos básicos da replicação.<br/><br/> Não é possível excluir os discos do sistema operacional ou os discos dinâmicos. Os discos temporários são excluídos por padrão. | Você pode excluir somente os discos básicos da replicação.<br/><br/> Não é possível excluir os discos do sistema operacional ou os discos dinâmicos. | Você pode excluir somente os discos básicos da replicação.<br/><br/> Você não pode excluir os discos do sistema operacional. É recomendável que você não exclua discos dinâmicos. O Site Recovery não pode identificar qual VHS é básico ou dinâmico na VM convidada. Se todos os discos de volume dinâmico dependentes não forem excluídos, o disco dinâmico protegido se tornará um disco com falha em uma VM de failover e os dados nesse disco não estarão acessíveis.
**Replicando o disco** | Você não pode excluir um disco que está replicando.<br/><br/> Desabilite e habilite novamente a replicação para a VM. |  Você não pode excluir um disco que está replicando. |  Você não pode excluir um disco que está replicando.
**Serviço de mobilidade (VMware)** | Irrelevante | Você pode excluir somente os discos nas VMs que já têm o Serviço de mobilidade instalado.<br/><br/> Isso significa que você precisa instalar manualmente o Serviço de mobilidade nas VMs das quais deseja excluir os discos. Não é possível usar o mecanismo de instalação por push porque ele instala o Serviço de mobilidade somente após a replicação ser habilitada. | Irrelevante.
**Adicionar/Remover** | Você pode adicionar discos gerenciados em VMs do Azure habilitadas para a replicação com os discos gerenciados. Não é possível remover os discos em VMs do Azure habilitadas para a replicação. | Você não pode adicionar nem remover os discos depois que a replicação está habilitada. Desabilite e reabilite a replicação para adicionar um disco. | Você não pode adicionar nem remover os discos depois que a replicação está habilitada. Desabilite e reabilite a replicação.
**Failover** | Se um aplicativo precisar de um disco excluído, após o failover, você precisará criar o disco manualmente para que o aplicativo replicado possa ser executado.<br/><br/> Como alternativa, você pode criar o disco durante o failover da VM, integrando a automação do Azure em um plano de recuperação. | Se você excluir um disco do qual um aplicativo precisa, crie-o manualmente no Azure após o failover. | Se você excluir um disco do qual um aplicativo precisa, crie-o manualmente no Azure após o failover.
**Discos de failback locais criados manualmente** | Irrelevante | **VMs do Windows**: Os discos criados manualmente no Azure não sofrerão failback. Por exemplo, se você executar o failover em três discos e criar dois discos diretamente em uma VM do Azure, somente os três discos que sofreram failover serão submetidos ao failback.<br/><br/> **VMs Linux**: Os discos criados manualmente no Azure sofrerão failback. Por exemplo, se você executar o failover em três discos e criar dois discos em uma VM do Azure, todos os cinco serão submetidos ao failback. Você não pode excluir do failback discos que foram criados manualmente. | Os discos criados manualmente no Azure não sofrerão failback. Por exemplo, se você executar o failover em três discos e criar dois discos diretamente em uma VM do Azure, somente os três discos que sofreram failover serão submetidos ao failback.
**Discos excluídos por failback no local** | Irrelevante | Se você fizer failback no computador original, a configuração do disco da VM de failback não incluirá os discos excluídos. Os discos que foram excluídos do VMware para a replicação do Azure não estarão disponíveis na VM de failback. | Quando o failback é para o local do Hyper-V original, a configuração de disco da VM de failback permanece a mesma do disco da VM original. Os discos que foram excluídos do site do Hyper-V para a replicação do Azure estão disponíveis na VM de failback.



## <a name="typical-scenarios"></a>Cenários comuns

Exemplos de dados produzidos que são ótimos candidatos para a exclusão incluem gravações em um arquivo de paginação (pagefile.sys) e gravações no arquivo tempdb do Microsoft SQL Server. Dependendo da carga de trabalho e do subsistema de armazenamento, os arquivos de paginação e tempdb podem registrar uma quantidade significativa de rotatividade. A replicação desse tipo de dados para o Azure consome muitos recursos.

- Para otimizar a replicação de uma VM com um único disco virtual que inclui o sistema operacional e o arquivo de paginação, você pode:
    1. Divida o disco virtual em dois discos virtuais. Um disco virtual tem o sistema operacional e o outro tem o arquivo de paginação.
    2. Exclua o disco do arquivo de paginação da replicação.

- Para otimizar a replicação de um disco que inclui o arquivo tempdb do Microsoft SQL Server e o arquivo do banco de dados do sistema, você pode:
    1. Manter o banco de dados do sistema e tempdb em dois discos diferentes.
    2. Excluir o disco de tempdb da replicação.

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>Exemplo 1: Excluir disco tempdb do SQL Server

Vejamos como lidar com a exclusão de disco, failover e failover para uma fonte SQL Server Windows VM- **SalesDB** _, para a qual desejamos excluir tempdb. 

### <a name="exclude-disks-from-replication"></a>Excluir discos da replicação

Temos esses discos na VM do Windows de origem SalesDB.

_ *Nome do disco** | **Disco do SO convidado** | **Letra da unidade** | **Tipo de dados do disco**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Disco do sistema operacional.
DB-Disk1| Disk1 | D:\ | Banco de dados do sistema SQL e Banco de Dados de Usuário 1.
DB-Disco2 (disco excluído da proteção) | Disk2 | E:\ | Arquivos temporários.
DB-Disco3 (disco excluído da proteção) | Disk3 | F:\ | Banco de dados tempdb do SQL.<br/><br/> Caminho da pasta - F:\MSSQL\Data\. Anote o caminho da pasta antes do failover.
DB-Disk4 | Disk4 |G:\ | User Database2

1. Habilitamos a replicação para a VM SalesDB.
2. Excluímos Disk2 e Disk3 da replicação porque a rotatividade de dados nesses discos é temporária. 


### <a name="handle-disks-during-failover"></a>Tratamento dos discos durante o failover

Como os discos não são replicados, quando você faz failover para o Azure, esses discos não estão presentes na VM do Azure criada após o failover. A VM do Azure tem os discos resumidos nesta tabela.

**Disco do SO convidado** | **Letra da unidade** | **Tipo de dados do disco**
--- | --- | ---
Disk0 | C:\ | Disco do sistema operacional.
Disk1 | E:\ | Armazenamento temporário<br/><br/>O Azure adiciona este disco. Como Disk2 e Disk3 foram excluídos da replicação, E: é a primeira letra de unidade na lista disponível. O Azure atribui e volume de armazenamento temporário. As outras letras de unidade para os discos replicados permanecem iguais.
Disk2 | D:\ | Banco de dados do sistema SQL e User Database1
Disk3 | G:\ | User Database2

Em nosso exemplo, como Disk3, o disco tempdb do SQL, foi excluído da replicação e não está disponível na VM do Azure, o serviço SQL está parado e precisa do caminho F:\MSSQL\Data. Você pode criar esse caminho de algumas maneiras: 

- Adicione um novo disco após o failover e atribua o caminho da pasta tempdb.
- Use um disco de armazenamento temporário existente para o caminho da pasta tempdb.

#### <a name="add-a-new-disk-after-failover"></a>Adicionar um novo disco após o failover

1. Anote os caminhos do SQL de tempdb.mdf e tempdb.ldf antes do failover.
2. No portal do Azure, adicione um novo disco à VM do Azure de failover. O disco deve ter o mesmo tamanho (ou ser maior) que o disco tempdb do SQL de origem (Disk3).
3. Entre na VM do Azure.
4. No console de gerenciamento (diskmgmt.msc) do disco, inicialize e formate o disco recém-adicionado.
5. Atribua a mesma letra de unidade usada pelo disco tempdb do SQL (F:)
6. Crie uma pasta tempdb no volume F: (F:\MSSQL\Data).
7. Inicie o serviço SQL por meio do console de serviço.

#### <a name="use-an-existing-temporary-storage-disk"></a>Usar um disco de armazenamento temporário existente 

1. Abra um prompt de comando.
2. Execute o SQL Server no modo de recuperação do prompt de comando.

    ```console
    Net start MSSQLSERVER /f / T3608
    ```

3. Execute o seguinte sqlcmd para alterar o caminho de tempdb para o novo caminho.

    ```sql
    sqlcmd -A -S SalesDB        **Use your SQL DBname**
    USE master;     
    GO      
    ALTER DATABASE tempdb       
    MODIFY FILE (NAME = tempdev, FILENAME = 'E:\MSSQL\tempdata\tempdb.mdf');
    GO      
    ALTER DATABASE tempdb       
    MODIFY FILE (NAME = templog, FILENAME = 'E:\MSSQL\tempdata\templog.ldf');       
    GO
    ```

4. Parar o serviço do Microsoft SQL Server.

    ```console
    Net stop MSSQLSERVER
    ```

5. Inicie o serviço do Microsoft SQL Server.

    ```console
    Net start MSSQLSERVER
    ```

### <a name="vmware-vms-disks-during-failback-to-original-location"></a>VMs do VMware: Discos durante o failback para o local original

Agora, vejamos como lidar com discos nas VMs do VMware ao fazer failback para o local original.

- **Discos criados no Azure**: Como nosso exemplo usa uma VM do Windows, os discos criados manualmente no Azure não são replicados de volta para seu site quando você faz failback ou protege novamente uma VM.
- **Disco de armazenamento temporário no Azure**: O disco de armazenamento temporário não é replicado de volta para os hosts locais.
- **Discos excluídos**: Os discos que foram excluídos do VMware para a replicação do Azure não estão disponíveis na VM local após o failback.

Antes de realizar o failback das VMs do VMware para o local original, as configurações de disco da VM do Azure são as seguintes.

**Disco do SO convidado** | **Letra da unidade** | **Tipo de dados do disco**
--- | --- | ---
Disk0 | C:\ | Disco do sistema operacional.
Disk1 | E:\ | Armazenamento temporário.
Disk2 | D:\ | Banco de dados do sistema SQL e Banco de Dados de Usuário 1.
Disk3 | G:\ | Banco de Dados de Usuário 2.

Após o failback, a VM do VMware no local original tem os discos resumidos na tabela.

**Disco do SO convidado** | **Letra da unidade** | **Tipo de dados do disco**
--- | --- | ---
Disk0 | C:\ | Disco do sistema operacional.
Disk1 | D:\ | Banco de dados do sistema SQL e Banco de Dados de Usuário 1.
Disk2 | G:\ | Banco de Dados de Usuário 2.


### <a name="hyper-v-vms-disks-during-failback-to-original-location"></a>VMs do Hyper-V: Discos durante o failback para o local original

Agora, vejamos como lidar com os discos nas VMs do Hyper-V ao fazer failback para o local original.

- **Discos criados no Azure**: Os discos criados manualmente no Azure não são replicados de volta para seu site quando você faz failback ou protege novamente uma VM.
- **Disco de armazenamento temporário no Azure**: O disco de armazenamento temporário não é replicado de volta para os hosts locais.
- **Discos excluídos**: Após o failback, a configuração do disco da VM é igual à configuração original do disco da VM. Os discos excluídos da replicação do Hyper-V para o Azure estão disponíveis na VM de failback.

Antes de realizar o failback das VMs do Hyper-V para o local original, as configurações de disco da VM do Azure são as seguintes.

**Disco do SO convidado** | **Letra da unidade** | **Tipo de dados do disco**
--- | --- | ---
Disk0 | C:\ | Disco do sistema operacional.
Disk1 | E:\ | Armazenamento temporário.
Disk2 | D:\ | Banco de dados do sistema SQL e Banco de Dados de Usuário 1.
Disk3 | G:\ | Banco de Dados de Usuário 2.

Após o failover planejado (failback) do Azure para o Hyper-V local, a VM do Hyper-V no local original tem os discos resumidos na tabela.

**Nome do Disco** | **Nº do Disco do Sistema Operacional Convidado** | **Letra da unidade** | **Tipo de dados do disco**
 --- | --- | --- | ---
DB-Disk0-OS | Disk0 |   C:\ | Disco do sistema operacional.
DB-Disk1 | Disk1 | D:\ | Banco de dados do sistema SQL e Banco de Dados de Usuário 1.
DB-disco 2 (disco excluídos) | Disk2 | E:\ | Arquivos temporários.
DB-Disk3 (disco excluídos) | Disk3 | F:\ | Banco de dados tempdb do SQL<br/><br/> Caminho da pasta (F:\MSSQL\Data)\).
DB-Disk4 | Disk4 | G:\ | User Database2


## <a name="example-2-exclude-the-paging-file-disk"></a>Exemplo 2: Excluir o disco do arquivo de paginação

Vejamos como lidar com a exclusão de disco e failover para uma VM do Windows de origem, para a qual desejamos excluir o disco de arquivo pagefile. sys na unidade D e em uma unidade alternativa.


### <a name="paging-file-on-the-d-drive"></a>Arquivo de paginação na unidade D

Temos esses discos na VM de origem.

**Nome do disco** | **Disco do SO convidado** | **Letra da unidade** | **Tipo de dados do disco**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Disco do sistema operacional
DB-Disk1 (Excluir da replicação) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | User data 1
DB-Disk3 | Disk3 | F:\ | User data 2

As configurações do arquivo de paginação na VM de origem são as seguintes:

![Captura de tela da caixa de diálogo memória virtual com a linha D: Drive [volume de paginação] realçada mostrando um tamanho de arquivo de paginação (MB) de 3000-7000.](./media/exclude-disks-replication/pagefile-d-drive-source-vm.png)

1. Habilitamos a replicação para a VM.
2. Excluímos DB-Disk1 da replicação.

#### <a name="disks-after-failover"></a>Discos após o failover

Após o failover, a VM do Azure tem os discos resumidos na tabela.

**Nome do disco** | **Sistema operacional convidado - disco nº** | **Letra da unidade** | **Tipo de dados no disco**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Disco do sistema operacional
DB-Disk1 | Disk1 | D:\ | Armazenamento temporário/pagefile.sys <br/><br/> Como o DB-Disk1 (D:) foi excluído, D: é a primeira letra de unidade na lista disponível.<br/><br/> O Azure atribui D: ao volume de armazenamento temporário.<br/><br/> Como D: está disponível, a configuração do arquivo de paginação da VM permanece a mesma.
DB-Disk2 | Disk2 | E:\ | User data 1
DB-Disk3 | Disk3 | F:\ | User data 2

As configurações do arquivo de paginação na VM do Azure são as seguintes:

![Configurações do arquivo de paginação na máquina virtual do Azure](./media/exclude-disks-replication/pagefile-azure-vm-after-failover.png)

### <a name="paging-file-on-another-drive-not-d"></a>Arquivo de paginação em outra unidade (não D:)

Vejamos um exemplo no qual o arquivo de paginação não está na unidade D.  

Temos esses discos na VM de origem.

**Nome do disco** | **Disco do SO convidado** | **Letra da unidade** | **Tipo de dados do disco**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Disco do sistema operacional
DB-Disk1 (Excluir da replicação) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | User data 1
DB-Disk3 | Disk3 | F:\ | User data 2

As configurações do arquivo de paginação na VM local são as seguintes:

![Configurações do arquivo de paginação na máquina virtual local](./media/exclude-disks-replication/pagefile-g-drive-source-vm.png)

1. Habilitamos a replicação para a VM.
2. Excluímos DB-Disk1 da replicação.

#### <a name="disks-after-failover"></a>Discos após o failover

Após o failover, a VM do Azure tem os discos resumidos na tabela.

**Nome do disco** | **Nº do Disco do Sistema Operacional Convidado** | **Letra da unidade** | **Tipo de dados do disco**
--- | --- | --- | ---
DB-Disk0-OS | Disk0  |C:\ | Disco do sistema operacional
DB-Disk1 | Disk1 | D:\ | Armazenamento temporário<br/><br/> Como D: é a primeira letra de unidade de disponível na lista, o Azure atribui D: ao volume de armazenamento temporário.<br/><br/> Para todos os discos replicados, a letra da unidade permanece igual.<br/><br/> Como o disco G: não está disponível, o sistema usará a unidade C: para o arquivo de paginação.
DB-Disk2 | Disk2 | E:\ | User data 1
DB-Disk3 | Disk3 | F:\ | User data 2

As configurações do arquivo de paginação na VM do Azure são as seguintes:

![Captura de tela da caixa de diálogo memória virtual com a linha da unidade C: realçada mostrando uma configuração de tamanho do arquivo de paginação "gerenciado pelo sistema".](./media/exclude-disks-replication/pagefile-azure-vm-after-failover-2.png)


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as diretrizes do disco de armazenamento temporário:
    - [Saiba mais sobre](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) como usar SSDs nas VMs do Azure para armazenar o TempDB do SQL Server e as Extensões do Pool de Buffers
    - [Examine ](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md) as práticas recomendadas de desempenho para o SQL Server nas VMs do Azure.
- Depois que a implantação estiver configurada e em funcionamento, [saiba mais](failover-failback-overview.md) sobre o os diferentes tipos de failover.
