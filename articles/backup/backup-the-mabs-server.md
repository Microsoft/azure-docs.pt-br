---
title: Fazer backup do servidor MABS
description: Saiba como fazer backup do servidor de Backup do Microsoft Azure (MABS).
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: 81a6ee005e15b1d7ab7b11a938b8ab14143818f4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92172115"
---
# <a name="back-up-the-mabs-server"></a>Fazer backup do servidor MABS

Para garantir que os dados possam ser recuperados se Backup do Microsoft Azure servidor (MABS) falhar, você precisará de uma estratégia para fazer backup do servidor MABS. Se não for feito backup, você precisará recriá-lo manualmente após uma falha, e os pontos de recuperação baseados em disco não serão recuperáveis. Você pode fazer backup de servidores MABS fazendo backup do banco de dados MABS.

## <a name="back-up-the-mabs-database"></a>Fazer backup do banco de dados MABS

Como parte da sua estratégia de backup do MABS, você precisará fazer backup do banco de dados do MABS. O banco de dados MABS é denominado DPMDB. Esse banco de dados contém a configuração do MABS juntamente com os backups de MABS. Se houver um desastre, você poderá recriar a maior parte da funcionalidade de um servidor MABS usando um backup recente do banco de dados. Supondo que você possa restaurar o banco de dados, os backups baseados em fita estão acessíveis e mantêm todas as configurações de grupo de proteção e agendas de backup. Se os discos do pool de armazenamento do MABS não foram afetados pela interrupção, os backups baseados em disco também são utilizáveis após uma recompilação. Você pode fazer backup do banco de dados usando vários métodos diferentes.

|Método de backup do banco de dados|Vantagens|Desvantagens|
|--------------------------|--------------|-----------------|
|[Fazer backup no Azure](#back-up-to-azure)|<li>Facilmente configurado e monitorado em MABS.<li>Vários locais dos arquivos do banco de dados de backup.<li>O armazenamento em nuvem fornece uma solução robusta para recuperação de desastres.<li>Armazenamento muito seguro do banco de dados.<li>Oferece suporte a 120 pontos de recuperação online.|<li>Requer a conta do Azure e a configuração de MABS adicional. Incorre em alguns custos de armazenamento do Azure.<li> Requer uma versão com suporte do sistema baseado no Windows Server com o agente do Azure para obter acesso aos backups do MABS armazenados no cofre de backup do Azure. Não pode ser outro servidor MABS.<li>Não é uma opção se o banco de dados estiver hospedado localmente e desejar habilitar a proteção secundária. <li>Pode ter algum tempo de recuperação e preparação extra.|
|[Fazer backup do banco de dados fazendo backup do pool de armazenamento MABS](#back-up-the-database-by-backing-up-the-mabs-storage-pool)|<li>Simples de configurar e monitorar.<li>O backup é mantido nos discos do pool de armazenamento do MABS e é fácil de acessar localmente.<li>Backups agendados do MABS dão suporte a backups completos expressos 512. Se você fizer backup por hora, terá 21 dias de proteção completa.|<li>Não é uma boa opção para a recuperação de desastres. Ele está online e a recuperação pode não funcionar conforme o esperado se o servidor MABS ou o disco do pool de armazenamento falhar.<li>Não é uma opção se o banco de dados estiver hospedado localmente e desejar habilitar a proteção secundária. <li>Algumas etapas especiais e de preparação são necessárias para obter acesso aos pontos de recuperação se o serviço ou console do MABS não estiver em execução ou funcionando.|
|[Fazer backup com backup nativo do SQL Server para um disco local](#back-up-with-native-sql-server-backup-to-a-local-disk)|<li>Integrado ao SQL Server.<li>O backup é mantido em um disco local que é facilmente acessível.<li>Ele pode ser agendado para ser executado sempre que desejar.<li>Totalmente independente do MABS.<li>Você pode agendar uma limpeza do arquivo de backup.|<li>Não é uma boa opção para a recuperação de desastres, a menos que os backups sejam copiados para um local remoto.<li>Requer armazenamento local para backups, o que pode limitar a retenção e a frequência.|
|[Fazer backup com o backup do SQL nativo e a proteção do MABS para um compartilhamento protegido pelo MABS](#back-up-to-a-share-protected-by-mabs)|<li>Facilmente monitorado em MABS.<li>Vários locais dos arquivos do banco de dados de backup.<li>Facilmente acessível a partir de qualquer máquina Windows na rede.<li>Método de recuperação potencialmente mais rápido.|<li>Só oferece suporte a 64 pontos de recuperação.<li>Não é uma boa opção para a recuperação de desastres em sites. A falha de disco do MABS Server ou MABS pool de armazenamento pode atrapalhar os esforços de recuperação.<li>Não é uma opção se o MABS DB estiver hospedado localmente e você quiser habilitar a proteção secundária. <li>Alguma preparação extra é necessária para configurá-lo e testá-lo.<li>É necessário um tempo extra de preparação e recuperação, caso o próprio servidor MABS esteja inoperante, mas os discos do pool de armazenamento MABS estejam bem.|

- Se você fizer backup usando um grupo de proteção MABS, recomendamos que use um grupo de proteção exclusivo para o banco de dados.

    > [!NOTE]
    > Para fins de restauração, a instalação do MABS que você deseja restaurar com o banco de dados MABS deve corresponder à versão do próprio banco de dados MABS.  Por exemplo, se o banco de dados que você deseja recuperar for de um MABS v3 com a instalação do pacote cumulativo de atualizações 1, o servidor MABS deverá estar executando a mesma versão com o pacote cumulativo de atualizações 1. Isso significa que talvez seja necessário desinstalar e reinstalar o MABS com uma versão compatível antes de restaurar o banco de dados.  Para verificar a versão de banco de dados, talvez você precise recriá-lo manualmente em um nome de banco de dados temporário e, em seguida, executar uma consulta SQL no banco de dados para verificar o último pacote cumulativo instalado, com base nas versões principais e secundárias.

- Para verificar a versão do banco de dados MABS, siga estas etapas:

    1. Para executar a consulta, abra o SQL Management Studio e, em seguida, conecte-se à instância do SQL que está executando o banco de dados MABS.

    2. Selecione o banco de dados MABS e, em seguida, inicie uma nova consulta.

    3. Cole a seguinte consulta SQL no painel de consulta e execute-a:

        `Select distinct MajorVersionNumber,MinorVersionNumber ,BuildNumber, FileName FROM dbo.tbl\_AM\_AgentPatch order byMajorVersionNumber,MinorVersionNumber,BuildNumber`

    Se nada for retornado nos resultados da consulta, ou se o servidor MABS tiver sido atualizado de versões anteriores, mas nenhum novo pacote cumulativo de atualizações tiver sido instalado desde então, não haverá uma entrada para a principal, secundária para uma instalação básica do MABS. Para verificar as versões do MABS associadas a pacotes cumulativos de atualizações, confira a [lista de números de compilação para mAbs](https://social.technet.microsoft.com/wiki/contents/articles/36381.microsoft-azure-backup-server-list-of-build-numbers.aspx).

### <a name="back-up-to-azure"></a>Fazer backup para o Azure

1. Antes de começar, você precisará executar um script para recuperar o caminho do ponto de montagem do volume da réplica MABS para saber qual ponto de recuperação contém o backup do MABS. Faça isso após a replicação inicial com o Azure Backup. No script, substitua `dplsqlservername%` pelo nome da instância de SQL Server que hospeda o banco de Dados mAbs.

    ```SQL
    Select ag.NetbiosName as ServerName,ds.DataSourceName,vol.MountPointPath
    from tbl_IM_DataSource as ds
    join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
    join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
    join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
    and vol.Usage =1
    and lr.Validity in (1,2)
    where ds.datasourcename like '%dpmdb%'
    and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB
    ```

    Verifique se você tem a senha que foi especificada quando o agente dos serviços de recuperação do Azure foi instalado e se o servidor MABS foi registrado no cofre de backup do Azure. Você precisará dessa senha para restaurar o backup.

2. Crie um cofre de Backup do Azure, baixe o arquivo de instalação do Agente de Backup do Azure e as credenciais do cofre. Execute o arquivo de instalação para instalar o agente no servidor MABS e use as credenciais do cofre para registrar o servidor MABS no cofre. [Saiba mais](backup-azure-microsoft-azure-backup.md).

3. Depois que o cofre for configurado, configure um grupo de proteção MABS que contenha o banco de dados MABS. Selecione para fazer backup em disco e no Azure.

#### <a name="recover-the-mabs-database-from-azure"></a>Recuperar o banco de dados MABS do Azure

Você pode recuperar o banco de dados do Azure usando qualquer servidor MABS registrado no cofre de backup do Azure, da seguinte maneira:

1. No console do mAbs, selecione **recuperação**  >  **Adicionar mAbs externa**.

2. Forneça as credenciais do cofre (Baixe do cofre de backup do Azure). Observe que as credenciais só são válidas por dois dias.

3. Em **selecionar mAbs externo para recuperação**, selecione o servidor mAbs para o qual você deseja recuperar o banco de dados, digite a senha de criptografia e selecione **OK.**

4. Selecione o ponto de recuperação que você deseja usar na lista de pontos disponíveis. Selecione **limpar mAbs externas** para retornar à exibição mAbs local.

## <a name="back-up-the-mabs-database-to-mabs-storage-pool"></a>Fazer backup do banco de dados MABS no pool de armazenamento MABS

> [!NOTE]  
> Essa opção é aplicável para MABS com Armazenamento de Backup Moderno.

1. No console do mAbs, selecione **proteção**  >  **Criar grupo de proteção**.
2. Na página **Selecionar Tipo de Grupo de Proteção** , selecione **Servidores**.
3. Na página **selecionar membros do grupo** , selecione **banco de dados do DPM**. Expanda o servidor MABS e selecione DPMDB.
4. Na página **Selecionar Método de Proteção de Dados**, selecione **Desejo uma proteção de curto prazo usando disco**. Especifique as opções de curto prazo da política de proteção.
5. Após a replicação inicial do banco de dados MABS, execute o seguinte script SQL:

```SQL
select AG.NetbiosName, DS.DatasourceName, V.AccessPath, LR.PhysicalReplicaId from tbl_IM_DataSource DS
join tbl_PRM_LogicalReplica as LR
on DS.DataSourceId = LR.DataSourceId
join tbl_AM_Server as AG
on DS.ServerId=AG.ServerId
join tbl_PRM_ReplicaVolume RV
on RV.ReplicaId = LR.PhysicalReplicaId
join tbl_STM_Volume V
on RV.StorageId = V.StorageId
where datasourcename like N'%dpmdb%' and ds.ProtectedGroupId is not null
and LR.Validity in (1,2)
and AG.ServerName like N'%<dpmsqlservername>%' -- <dpmsqlservername> is a placeholder, put netbios name of server hosting DPMDB
```

### <a name="recover-mabs-database"></a>Recuperar banco de dados MABS

Para reconstruir o MABS com o mesmo banco de dados, primeiro você precisa recuperar o banco de dados MABS e sincronizá-lo com o MABS instalado recentemente.

#### <a name="use-the-following-steps"></a>Use as etapas a seguir

1. Abra um prompt de comando administrativo e execute `psexec.exe -s powershell.exe` para iniciar uma janela do PowerShell no contexto do sistema.
2. Decida a localização da qual você deseja recuperar o banco de dados:

#### <a name="to-copy-the-database-from-the-last-backup"></a>Para copiar o banco de dados do último backup

1. Navegue até o caminho do VHD da réplica `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PhysicalReplicaId\>`
2. Monte o **disk0. vhdx** presente nele usando o `mount-vhd disk0.vhdx` comando.
3. Depois que o VHD de réplica for montado, use `mountvol.exe` para atribuir uma letra de unidade ao volume de réplica usando a ID da réplica física da saída do script SQL. Por exemplo: `mountvol X: \?\Volume{}\`

#### <a name="to-copy-the-database-from-a-previous-recovery-point"></a>Para copiar o banco de dados de um ponto de recuperação anterior

1. Navegue até o diretório do contêiner do DPMDB  `\<MABSServer FQDN\>\<PhysicalReplicaId\>` . Você verá vários diretórios com alguns identificadores GUID exclusivos sob os pontos de recuperação correspondentes obtidos para o MABS DB. Outros diretórios representam um ponto de PIT/recuperação.
2. Navegue até qualquer caminho VHD do PIT, por exemplo, `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PITId\>` e monte o **disk0. vhdx** presente nele usando o `mount-vhd disk0.vhdx` comando.
3. Depois que o VHD de réplica for montado, use `mountvol.exe` para atribuir uma letra de unidade ao volume de réplica, usando a ID da réplica física da saída do script SQL. Por exemplo: `mountvol X: \?\Volume{}\`

   Todos os termos que aparecem com chaves angulares nas etapas acima são os contentores. Substitua-os por valores apropriados da seguinte maneira:
   - **ReFSVolume** -caminho de acesso da saída do script SQL
   - **MABSSERVER FQDN** – nome totalmente qualificado do servidor mAbs
   - **PhysicalReplicaId** -ID da réplica física do script SQL de saída
   - Identificador **PITId** -GUID diferente da ID da réplica física no diretório do contêiner.
4. Abra outro prompt de comando administrativo e execute `psexec.exe -s cmd.exe` para iniciar um prompt de comando no contexto do sistema.
5. Altere o diretório para a unidade X: e navegue até o local dos arquivos de banco de dados MABS.
6. Copie-os para um local do qual seja fácil restaurá-los. Saia da janela do psexec cmd, depois de copiar.
7. Vá para a janela do PowerShell do PsExec aberta na etapa 1, navegue até o caminho VHDX e desmonte o VHDX usando o comando `dismount-vhd disk0.vhdx` .
8. Depois de reinstalar o servidor MABS, você pode usar o DPMDB restaurado para anexar ao servidor MABS executando o `DPMSYNC-RESTOREDB` comando.
9. Executar `DPMSYNC-SYNC` uma vez `DPMSYNC-RESTOREDB` concluído.

### <a name="back-up-the-database-by-backing-up-the-mabs-storage-pool"></a>Fazer backup do banco de dados fazendo backup do pool de armazenamento MABS

> [!NOTE]
> Essa opção é aplicável para MABS com armazenamento herdado.

Antes de começar, você precisará executar um script para recuperar o caminho do ponto de montagem do volume da réplica MABS para saber qual ponto de recuperação contém o backup do MABS. Faça isso após a replicação inicial com o Azure Backup. No script, substitua `dplsqlservername%` pelo nome da instância de SQL Server que hospeda o banco de Dados mAbs.

```SQL
Select ag.NetbiosName as ServerName,ds.DataSourceName,vol.MountPointPath
from tbl_IM_DataSource as ds
join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
and vol.Usage =1
and lr.Validity in (1,2)
where ds.datasourcename like '%dpmdb%'
and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB
```

1. No console do mAbs, selecione **proteção**  >  **Criar grupo de proteção**.

2. Na página **Selecionar Tipo de Grupo de Proteção**, selecione **Servidores**.

3. Na página **selecionar membros do grupo** , selecione o banco de Dados mAbs. Expanda o item do servidor MABS e selecione **DPMDB**.

4. Na página  **selecionar método de proteção de dados** , selecione **desejo proteção de curto prazo usando disco**. Especifique as opções de curto prazo da política de proteção. Recomendamos um período de retenção de duas semanas para bancos de dados MABS.

#### <a name="recover-the-database"></a>Recuperar o banco de dados

Se o servidor MABS ainda estiver operacional e o pool de armazenamento estiver intacto (como problemas com o serviço ou console do MABS), copie o banco de dados do volume de réplica ou uma cópia de sombra da seguinte maneira:

1. Decida quando deseja recuperar o banco de dados.

    - Se você quiser copiar o banco de dados do último backup obtido diretamente do volume de réplica MABS, use **mountvol.exe** para atribuir uma letra de unidade ao volume de réplica usando o GUID da saída do script SQL. Por exemplo: `C:\Mountvol X: \\?\Volume{d7a4fd76\-a0a8\-11e2\-8fd3\-001c23cb7375}\`

    - Se desejar copiar o banco de dados de um ponto de recuperação anterior (cópia de sombra), você precisará listar todas as cópias de sombra da réplica usando o GUID do volume da saída do script SQL. Este comando lista as cópias de sombra para esse volume: `C:\>Vssadmin list shadows /for\=\\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` . Anote a hora de criação e a ID da cópia de sombra da qual você deseja recuperar.

2. Em seguida, use **diskshadow.exe** para montar a cópia de sombra para uma letra de unidade não usada X: usando a ID da cópia de sombra, para que você possa copiar os arquivos do banco de dados.

3. Abra um prompt de comando administrativo e execute `psexec.exe -s cmd.exe` para iniciar um prompt de comando no contexto do sistema, para que você tenha permissão para navegar até o volume da réplica (X:) e copie os arquivos.

4. CD para a unidade X: e navegue até o local dos arquivos de banco de dados MABS. Copie-os para um local do qual seja fácil restaurá-los. Depois que a cópia for concluída, exista a janela do PsExec cmd e execute **diskshadow.exe** e exporte o X: volume.

5. Agora você pode restaurar os arquivos de banco de dados usando o SQL Management Studio ou executando o **DPMSYNC \- RESTOREDB**.

## <a name="back-up-with-native-sql-server-backup-to-a-local-disk"></a>Fazer backup com backup nativo do SQL Server para um disco local

Você pode fazer backup do banco de dados MABS em um disco local com o backup de SQL Server nativo, independente do MABS.

- Obtenha uma [visão geral](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) do backup do SQL Server.

- [Saiba mais](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) sobre como fazer backup do SQL Server para a nuvem.

## <a name="back-up-to-a-share-protected-by-mabs"></a>Fazer backup em um compartilhamento protegido por MABS

Essa opção de backup usa o SQL nativo para fazer backup do banco de dados MABS em um compartilhamento, protege o compartilhamento com MABS e usa versões anteriores do VSS do Windows para facilitar a restauração.

### <a name="before-you-start"></a>Antes de começar

1. Na SQL Server, torne uma pasta em uma unidade com espaço livre suficiente para manter uma única cópia de um backup. Por exemplo: `C:\MABSBACKUP`.

1. Compartilhe a pasta. Por exemplo, compartilhe `C:\MABSBACKUP` a pasta como *DPMBACKUP*.

1. Copie e cole o comando OSQL abaixo no bloco de notas e salve-o em um arquivo chamado `C:\MABSACKUP\bkupdb.cmd` . Verifique se não há nenhuma extensão. txt. Modifique o SQL_Instance_name e DPMDB_NAME para corresponder à instância e ao nome do DPMDB usado pelo servidor MABS.

    ```SQL
    OSQL -E -S localhost\SQL_INSTANCE_NAME -Q "BACKUP DATABASE DPMDB_NAME TO DISK='C:\DPMBACKUP\dpmdb.bak' WITH FORMAT"
    ```

1. Usando o bloco de notas, abra o arquivo **ScriptingConfig.xml** localizado na `C:\Program Files\Microsoft System Center\DPM\DPM\Scripting` pasta no servidor mAbs.

1. Modifique **ScriptingConfig.xml** e altere **DataSourceName =** para ser a letra da unidade que contém a pasta/compartilhamento DPMDBBACKUP. Altere a entrada PreBackupScript para o caminho completo e o nome do **BkUpDB. cmd** salvo na etapa 3.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ScriptConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="https://schemas.microsoft.com/2003/dls/ScriptingConfig.xsd">
    <DatasourceScriptConfig DataSourceName="C:">
    <PreBackupScript>C:\MABSDBBACKUP\bkupdb.cmd</PreBackupScript>
    <TimeOut>120</TimeOut>
    </DatasourceScriptConfig>
    </ScriptConfiguration>
    ```

1. Salve as alterações em **ScriptingConfig.xml**.

1. Proteja a pasta C:\MABSBACKUP ou o `\sqlservername\MABSBACKUP` compartilhamento usando mAbs e aguarde até que a réplica inicial seja criada. Deve haver um **DPMDB. bak** na pasta C:\MABSBACKUP como resultado do script de pré-backup em execução, que, por sua vez, foi copiado para a réplica do mAbs.

1. Se você não habilitar a recuperação de autoatendimento, precisará de algumas etapas adicionais para compartilhar a pasta MABSBACKUP na réplica:

    1. No console do MABS > **proteção**, localize a fonte de dados MABSBACKUP e selecione-a. Na seção detalhes, selecione **clique para exibir detalhes** no link para o caminho da réplica e copie o caminho para o bloco de notas. Remova o caminho de origem e mantenha o caminho de destino. O caminho deve ser semelhante ao seguinte: `C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP` .

    2. Faça um compartilhamento para esse caminho usando o nome de compartilhamento **MABSSERVERNAME-DPMDB**. Você pode usar o comando Net Share abaixo a partir de um prompt de comando administrativo.

        ```cmd
        Net Share MABSSERVERNAME-dpmdb="C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

### <a name="configure-the-backup"></a>Configurar o backup

Você pode fazer backup do banco de dados MABS como faria com qualquer outro banco de dados SQL Server usando SQL Server Backup nativo.

- Obtenha uma [visão geral](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) do backup do SQL Server.

- [Saiba mais](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) sobre como fazer backup do SQL Server para a nuvem.

### <a name="recover-the-mabs-database"></a>Recuperar o banco de dados MABS

1. Conecte-se ao `\\MABSServer\MABSSERVERNAME-dpmdb` compartilhamento usando o Explorer de qualquer computador com Windows.

2. Clique com o botão direito do mouse no arquivo **DPMDB. bak** para exibir as propriedades. Na guia **Versões Anteriores**, você encontrará todos os backups que pode selecionar e copiar. Também há o último backup ainda localizado na pasta C:\MABSBACKUP que também é facilmente acessível.

3. Se você precisar mover um disco de pool de armazenamento MABS conectado à SAN para outro servidor para poder ler o volume da réplica ou para reinstalar o Windows para ler discos anexados localmente, você precisará saber o caminho do ponto de montagem do volume da réplica MABS ou o GUID do volume com antecedência para saber qual volume contém o backup do banco de dados. Você pode usar o script do SQL abaixo para extrair essas informações sempre após a proteção inicial, mas antes da necessidade de restaurar. Substitua pelo `%dpmsqlservername%` nome do SQL Server que hospeda o banco de dados.

    ```sql
    Select ag.NetbiosName as
    ServerName,ds.DataSourceName,vol.MountPointPath,vol.GuidName
    from tbl_IM_DataSource as ds
    join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
    join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
    join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
    and vol.Usage =1
    and lr.Validity in (1,2)
    where ds.datasourcename like '%C:\%' -- volume drive letter for DPMBACKUP
    and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB

    ```

4. Se você precisar recuperar depois de mover os discos do pool de armazenamento MABS ou uma recompilação do servidor MABS:

    1. Você tem o GUID do volume, portanto, se esse volume precisar ser montado em outro servidor Windows ou após uma recompilação do MABS Server, use **mountvol.exe** para atribuir a ele uma letra da unidade usando o GUID do volume da saída do script do SQL: `C:\Mountvol X: \\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` .

    2. Compartilhe novamente a pasta MABSBACKUP no volume da réplica usando a letra da unidade e a parte do caminho da réplica que representa a estrutura da pasta.

        ```cmd
        net share SERVERNAME-DPMDB="X:\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

    3. Conecte-se ao `\\SERVERNAME\MABSSERVERNAME-dpmdb` compartilhamento usando o Explorer de qualquer computador com Windows.

    4. Clique com o botão direito do mouse no arquivo **DPMDB. bak** para exibir as propriedades. Na guia **Versões Anteriores**, você encontrará todos os backups que pode selecionar e copiar.

## <a name="using-dpmsync"></a>Como usar o DPMSync

O **DpmSync** é uma ferramenta de linha de comando que permite sincronizar o banco de Dados mAbs com o estado dos discos no pool de armazenamento e com os agentes de proteção instalados. O DpmSync restaura o banco de dados MABS, sincroniza o banco de dados MABS com as réplicas no pool de armazenamento, restaura o banco de dados de relatório e realoca as réplicas ausentes.

### <a name="parameters"></a>Parâmetros

| Parâmetro      | Descrição    |
|----------------|-----------------------------|
| **-RestoreDb**                       | Restaura um banco de dados MABS de um local especificado.|
| **-Sync**                            | Sincroniza bancos de dados restaurados. Você deve executar o **DpmSync – Sync** depois de restaurar os bancos de dados. Depois de executar o **DpmSync – Sync**, algumas réplicas ainda podem ser marcadas como ausentes. |
| **-DbLoc** *local*                | Identifica o local do backup do banco de dados MABS.|
| **-InstanceName** <br/>*servidor \ instância*     | Instância para a qual o DPMDB deve ser restaurado.|
| **-ReallocateReplica**         | Realoca todos os volumes de réplicas ausentes sem sincronização. |
| **-DataCopied**                      | Indica que você concluiu o carregamento de dados nos volumes de réplica recém-alocados. Isso só é aplicável para computadores cliente. |

**Exemplo 1:** Para restaurar o banco de dados MABS da mídia de backup local no servidor MABS, execute o seguinte comando:

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak
```

Depois de restaurar o banco de dados MABS, execute o seguinte comando para sincronizá-los:

```cmd
DpmSync -Sync
```

Depois de restaurar e sincronizar o banco de dados MABS e antes de restaurar a réplica, execute o seguinte comando para realocar espaço em disco para a réplica:

```cmd
DpmSync -ReallocateReplica
```

**Exemplo 2:** Para restaurar o banco de dados MABS de um banco de dados remoto, execute o seguinte comando no computador remoto:

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak –InstanceName contoso\ms$dpm
```

Depois de restaurar o banco de dados MABS, execute o seguinte comando no servidor MABS:

```cmd
DpmSync -Sync
```

Depois de restaurar e sincronizar o banco de dados MABS e antes de restaurar a réplica, execute o seguinte comando no servidor MABS para realocar espaço em disco para a réplica:

```cmd
DpmSync -ReallocateReplica
```

## <a name="next-steps"></a>Próximas etapas

- [Matriz de suporte do MABS](backup-support-matrix-mabs-dpm.md)
- [PERGUNTAS FREQUENTES DO MABS](backup-azure-dpm-azure-server-faq.md)