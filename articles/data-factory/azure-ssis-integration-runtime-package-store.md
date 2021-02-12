---
title: Gerenciar pacotes com o repositório de pacotes Azure-SSIS Integration Runtime
description: Saiba como gerenciar pacotes com Azure-SSIS Integration Runtime repositório de pacotes.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 09/29/2020
ms.openlocfilehash: c7fc1a6c6aa29bfbc074bfa797f31ca8ee4e8fec
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98556392"
---
# <a name="manage-packages-with-azure-ssis-integration-runtime-package-store"></a>Gerenciar pacotes com o repositório de pacotes Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Para aumentar & mudar suas cargas de trabalho do SSIS (SQL Server Integration Services local) para a nuvem, você pode provisionar Azure-SSIS Integration Runtime (IR) no Azure Data Factory (ADF). Para obter mais informações, consulte [provisionar um Azure-SSIS ir](./tutorial-deploy-ssis-packages-azure.md). Um Azure-SSIS IR dá suporte a:

- Execução de pacotes implantados no catálogo do SSIS (SSISDB) hospedado por uma Instância Gerenciada/servidor do Banco de Dados SQL do Azure (modelo de implantação de projeto)
- Execução de pacotes implantados no sistema de arquivos, nos Arquivos do Azure ou no banco de dados do SQL Server (MSDB) hospedado pela Instância Gerenciada de SQL do Azure (modelo de implantação de pacote)

Ao usar o modelo de implantação de pacote, você pode escolher se deseja provisionar seu Azure-SSIS IR com armazenamentos de pacotes. Eles fornecem uma camada de gerenciamento de pacotes na parte superior do sistema de arquivos, arquivos do Azure ou MSDB hospedado pelo Azure SQL Instância Gerenciada. Azure-SSIS IR repositório de pacotes permite que você importe/exporte/exclua/execute pacotes e monitore/interrompa a execução de pacotes via SQL Server Management Studio (SSMS) semelhante ao [repositório de pacotes SSIS herdado](/sql/integration-services/service/package-management-ssis-service). 

## <a name="connect-to-azure-ssis-ir"></a>Conectar-se ao Azure-SSIS IR

Quando o Azure-SSIS IR for provisionado, você poderá se conectar a ele para procurar seus armazenamentos de pacotes no SSMS.

![Conectar-se ao Azure-SSIS IR](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect.png)

Na janela pesquisador de **objetos** do SSMS, selecione **Azure-SSIS Integration Runtime** no menu suspenso **conectar** . Em seguida, entre no Azure e selecione a assinatura, o ADF e a Azure-SSIS IR relevantes que você provisionou com os repositórios de pacotes. Seu Azure-SSIS IR será exibido com os nós **executando pacotes** e **pacotes armazenados** abaixo. Expanda o nó **pacotes armazenados** para ver os armazenamentos de pacotes abaixo. Expanda os armazenamentos de pacotes para ver as pastas e os pacotes abaixo. Você pode ser solicitado a inserir as credenciais de acesso para seus repositórios de pacotes, se o SSMS não conseguir se conectar a eles automaticamente. Por exemplo, se você expandir um repositório de pacotes na parte superior do MSDB, você poderá ser solicitado a se conectar ao SQL do Azure Instância Gerenciada primeiro.

![Conectar-se ao Azure SQL Instância Gerenciada](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect2.png)

## <a name="manage-folders-and-packages"></a>Gerenciar pastas e pacotes

Depois de se conectar ao seu Azure-SSIS IR no SSMS, você pode clicar com o botão direito do mouse em qualquer armazenamento de pacote, pastas ou pacotes para exibir um menu e selecionar **nova pasta**, **Importar pacote**, **Exportar pacote**, **excluir** ou **Atualizar**.

   ![Gerenciar pastas e pacotes](media/azure-ssis-integration-runtime-package-store/ssms-package-store-manage.png)

   *  Selecione **nova pasta** para criar uma nova pasta para pacotes importados.

   *  Selecione **Importar pacote** para importar pacotes do **sistema de arquivos**, **SQL Server** (msdb) ou **armazenar pacote SSIS** herdado em seu repositório de pacotes.

      ![Importar pacote](media/azure-ssis-integration-runtime-package-store/ssms-package-store-import.png)

      Dependendo do local do **pacote** do qual importar, selecione o tipo de  / **autenticação** de servidor relevante, insira as credenciais de acesso, se necessário, selecione o **caminho do pacote** e insira o novo **nome do pacote**. Ao importar pacotes, seu nível de proteção não pode ser alterado. Para alterá-lo, use SQL Server Data Tools (SSDT) ou `dtutil` Utilitário de linha de comando.

      > [!NOTE]
      > A importação de pacotes do SSIS para Azure-SSIS IR repositórios de pacotes só pode ser feita uma por um e simplesmente os copiará para os arquivos do Azure/sistema de arquivos/arquivo de armazenamento subjacente, preservando a versão do SQL Server/SSIS. 
      >
      > Como Azure-SSIS IR atualmente é baseado em **SQL Server 2017**, a execução de pacotes de versão inferior nele os atualizará para pacotes do SSIS 2017 em tempo de execução. Não há suporte para a execução de pacotes de versão superior.
      >
      > Além disso, como os armazenamentos de pacotes SSIS herdados são associados a uma versão específica do SQL Server e acessíveis somente no SSMS para essa versão, os pacotes de versão inferior em armazenamentos de pacotes SSIS herdados precisam ser exportados primeiro para o sistema de arquivos usando a versão do SSMS designada antes que possam ser importados para Azure-SSIS IR repositórios de pacotes usando o SSMS 2019 ou versões
      >
      > Como alternativa, para importar vários pacotes do SSIS para Azure-SSIS IR armazenamentos de pacotes enquanto alterna o nível de proteção, você pode usar o utilitário de linha de comando [dtutil](/sql/integration-services/dtutil-utility) , consulte [implantando vários pacotes com o dtutil](#deploying-multiple-packages-with-dtutil).

   *  Selecione **Exportar pacote** para exportar pacotes do seu repositório de pacotes para o **sistema de arquivos**, **SQL Server** (msdb) ou o **repositório de pacotes SSIS** herdados.

      ![Exportar pacote](media/azure-ssis-integration-runtime-package-store/ssms-package-store-export.png)

      Dependendo do local do **pacote** para o qual exportar, selecione o tipo de autenticação de **servidor** relevante / , insira as credenciais de acesso, se necessário, e selecione o **caminho do pacote**. Ao exportar pacotes, se eles estiverem criptografados, insira as senhas para descriptografá-los primeiro e, em seguida, você poderá alterar o nível de proteção deles, por exemplo, para evitar armazenar dados confidenciais ou para criptografá-los ou todos os dados com a chave de usuário ou senha.

      > [!NOTE]
      > A exportação de pacotes SSIS de Azure-SSIS IR repositórios de pacotes só pode ser feita uma por um e fazer isso sem alternar o nível de proteção simplesmente os copiará enquanto preserva a versão do SQL Server/SSIS, caso contrário, ele os atualizará para os pacotes SSIS 2019 ou posteriores.
      >
      > Como Azure-SSIS IR atualmente é baseado em **SQL Server 2017**, a execução de pacotes de versão inferior nele os atualizará para pacotes do SSIS 2017 em tempo de execução. Não há suporte para a execução de pacotes de versão superior.
      >
      > Como alternativa, para exportar vários pacotes SSIS de armazenamentos de pacotes Azure-SSIS IR ao alternar o nível de proteção, você pode usar o utilitário de linha de comando [dtutil](/sql/integration-services/dtutil-utility) , consulte [implantando vários pacotes com o dtutil](#deploying-multiple-packages-with-dtutil).

   *  Selecione **excluir** para excluir pastas/pacotes existentes do seu repositório de pacotes.

   *  Selecione **Atualizar** para mostrar as pastas/pacotes recém-adicionados no seu repositório de pacotes.

## <a name="execute-packages"></a>Executar pacotes

Depois de se conectar ao seu Azure-SSIS IR no SSMS, você pode clicar com o botão direito do mouse em todos os pacotes armazenados para exibir um menu e selecionar **executar pacote**.  Isso abrirá a caixa de diálogo **Utilitário de execução de pacotes** , em que você pode configurar suas execuções de pacote em Azure-SSIS ir como executar atividades de pacote SSIS em pipelines do ADF.

![Utilitário de Execução de Pacotes páginas 1 & 2](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute.png)

![Utilitário de Execução de Pacotes páginas 3 & 4](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute2.png)

As páginas **geral**, **configurações**, **Opções de execução** e **log** da **Utilitário de execução de pacotes** caixa de diálogo correspondem à guia  **configurações** da atividade executar pacote SSIS. Nessas páginas, você pode inserir a senha de criptografia para o pacote e informações de acesso para o arquivo de configuração do pacote. Você também pode inserir suas credenciais e propriedades de execução de pacote, bem como as informações de acesso para sua pasta de log.  A página **definir valores** da caixa de diálogo **Utilitário de execução de pacotes** corresponde à guia **substituições de propriedade** da atividade executar pacote SSIS, na qual você pode inserir suas propriedades de pacote existentes para substituir. Para obter mais informações, consulte [executar pacotes do SSIS como atividades de execução do pacote do SSIS em pipelines do ADF](./how-to-invoke-ssis-package-ssis-activity.md).

Quando você seleciona o botão **executar** , um novo pipeline do ADF com a atividade executar pacote SSIS será gerado e disparado automaticamente. Se um pipeline do ADF com as mesmas configurações já existir, ele será executado novamente e um novo pipeline não será gerado. O pipeline do ADF e a atividade executar pacote do SSIS serão nomeados `Pipeline_SSMS_YourPackageName_HashString` e `Activity_SSMS_YourPackageName` , respectivamente.

![Botão de Utilitário de Execução de Pacotes](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute3.png)

![Executar a atividade do pacote SSIS](media/azure-ssis-integration-runtime-package-store/ssis-activity-package-store.png)

## <a name="monitor-and-stop-running-packages"></a>Monitorar e interromper a execução de pacotes

Depois de se conectar ao seu Azure-SSIS IR no SSMS, você pode expandir o nó **pacotes em execução** para ver seus pacotes em execução no momento.  Clique com o botão direito do mouse em qualquer um deles para exibir um menu e selecionar **parar** ou **Atualizar**.

   ![Monitorar e interromper a execução de pacotes](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor.png)

   *  Selecione **parar** para cancelar o pipeline do ADF em execução no momento que executa o pacote como a atividade executar pacote do SSIS.

   *  Selecione **Atualizar** para mostrar pacotes que executam recentemente de seus armazenamentos de pacotes.

## <a name="monitor-azure-ssis-ir-and-edit-package-stores"></a>Monitorar Azure-SSIS IR e editar armazenamentos de pacotes

Depois de se conectar ao seu Azure-SSIS IR no SSMS, você pode clicar com o botão direito do mouse nele para exibir um menu e selecionar **ir para o portal de Azure data Factory** ou **Atualizar**.

   ![Ir para o portal do ADF](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor2.png)

   *  Selecione **ir para Azure data Factory portal** para abrir a página **tempos de execução de integração** do hub de monitoramento do ADF, onde você pode monitorar seu Azure-SSIS ir. No bloco **repositórios de pacotes** , você pode ver o número de repositórios de pacotes anexados à sua Azure-SSIS ir.  A seleção desse número exibirá uma janela em que você pode editar os serviços vinculados do ADF que armazenam as informações de acesso para os armazenamentos de pacotes.

      ![Editar armazenamentos de pacotes](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor3.png)

   *  Selecione **Atualizar** para mostrar as pastas/pacotes recém-adicionados em suas lojas de pacotes e executar pacotes de seus repositórios de pacotes.

## <a name="deploying-multiple-packages-with-dtutil"></a>Implantando vários pacotes com o dtutil

Para aumentar & mudar suas cargas de trabalho do SSIS locais para o SSIS no ADF enquanto mantém o modelo de implantação de pacote herdado, você precisa implantar seus pacotes do sistema de arquivos, MSDB hospedado por SQL Server ou armazenamentos de pacotes SSIS herdados em arquivos do Azure, MSDB hospedado pelo SQL Instância Gerenciada do Azure ou Azure-SSIS IR armazenamentos de pacotes. Ao mesmo tempo, você também deve alternar o nível de proteção de criptografia por chave de usuário para não criptografado ou criptografia por senha, se ainda não tiver feito isso.

Você pode usar o utilitário de linha de comando [dtutil](/sql/integration-services/dtutil-utility) que vem com a instalação do SQL Server/SSIS para implantar vários pacotes em lotes. Ele está associado a uma versão específica do SSIS, portanto, se você usá-lo para implantar pacotes de versão inferior sem alternar o nível de proteção, ele simplesmente os copiará enquanto preserva a versão do SSIS. Se você usá-lo para implantá-los e mudar seu nível de proteção ao mesmo tempo, ele os atualizará para sua versão do SSIS.

 Como Azure-SSIS IR atualmente é baseado em **SQL Server 2017**, a execução de pacotes de versão inferior nele os atualizará para pacotes do SSIS 2017 em tempo de execução. Não há suporte para a execução de pacotes de versão superior.

Consequentemente, para evitar atualizações em tempo de execução, a implantação de pacotes para execução em Azure-SSIS IR no modelo de implantação de pacote deve usar o dtutil 2017 que vem com a instalação do SQL Server/SSIS 2017. Você pode baixar e instalar a edição gratuita do [SQL Server/SSIS 2017 Developer](https://go.microsoft.com/fwlink/?linkid=853016) para essa finalidade. Uma vez instalado, você pode encontrar o dtutil 2017 nesta pasta: `YourLocalDrive:\Program Files\Microsoft SQL Server\140\DTS\Binn` .

### <a name="deploying-multiple-packages-from-file-system-on-premises-into-azure-files-with-dtutil"></a>Implantando vários pacotes do sistema de arquivos local em arquivos do Azure com o dtutil

 Para implantar vários pacotes do sistema de arquivos em arquivos do Azure e alternar o nível de proteção ao mesmo tempo, você pode executar os comandos a seguir em um prompt de comando. Substitua todas as cadeias de caracteres que são específicas do seu caso.
  
```dos
REM Persist the access credentials for Azure Files on your local machine
cmdkey /ADD:YourStorageAccountName.file.core.windows.net /USER:azure\YourStorageAccountName /PASS:YourStorageAccountKey

REM Connect Azure Files to a drive on your local machine
net use Z: \\YourStorageAccountName.file.core.windows.net\YourFileShare /PERSISTENT:Yes

REM Go to a local folder where you store your packages
cd YourLocalDrive:\...\YourPackageFolder

REM Run dtutil in a loop to deploy your packages from the local folder into Azure Files while switching their protection level
for %f in (*.dtsx) do dtutil.exe /FILE %f /ENCRYPT FILE;Z:\%f;2;YourEncryptionPassword
```

Para executar os comandos acima em um arquivo em lotes, substitua `%f` por `%%f` .

Para implantar vários pacotes de armazenamentos de pacotes SSIS herdados sobre o sistema de arquivos em arquivos do Azure e alternar o nível de proteção ao mesmo tempo, você pode usar os mesmos comandos, mas substituir `YourLocalDrive:\...\YourPackageFolder` por uma pasta local usada por repositórios de pacotes SSIS herdados: `YourLocalDrive:\Program Files\Microsoft SQL Server\YourSQLServerDefaultCompatibilityLevel\DTS\Packages\YourPackageFolder` . Por exemplo, se o repositório de pacotes SSIS herdados estiver associado a SQL Server 2016, vá para `YourLocalDrive:\Program Files\Microsoft SQL Server\130\DTS\Packages\YourPackageFolder` .  Encontre o valor de `YourSQLServerDefaultCompatibilityLevel` em uma [lista de níveis de compatibilidade padrão do SQL Server](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#arguments).

Se você tiver configurado Azure-SSIS IR armazenamentos de pacotes sobre os arquivos do Azure, seus pacotes implantados aparecerão neles quando você se conectar ao seu Azure-SSIS IR no SSMS 2019 ou em versões posteriores.

### <a name="deploying-multiple-packages-from-msdb-on-premises-into-msdb-in-azure-with-dtutil"></a>Implantando vários pacotes do MSDB localmente no MSDB no Azure com o dtutil

 Para implantar vários pacotes do MSDB hospedado por SQL Server ou armazenamentos de pacotes SSIS herdados sobre o MSDB no MSDB hospedado pelo SQL Instância Gerenciada do Azure e mudar seu nível de proteção ao mesmo tempo, você pode se conectar ao seu SQL Server no SSMS, clicar com o botão direito do mouse no `Databases->System Databases->msdb` nó no Pesquisador de **objetos** do SSMS para abrir uma nova janela de **consulta** e executar o seguinte script T-SQL Substitua todas as cadeias de caracteres que são específicas do seu caso:  
  
```sql
BEGIN
  SELECT 'dtutil /SQL '+f.foldername+'\'+NAME+' /ENCRYPT SQL;'+f.foldername+'\'+NAME+';2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourSQLAuthUsername /DestPassword YourSQLAuthPassword'
  FROM msdb.dbo.sysssispackages p
  inner join msdb.dbo.sysssispackagefolders f
  ON p.folderid = f.folderid
END
```

Para usar o ponto de extremidade privado/público de seu instância gerenciada SQL do Azure, substitua `YourSQLManagedInstanceEndpoint` por `YourSQLMIName.YourDNSPrefix.database.windows.net` / `YourSQLMIName.public.YourDNSPrefix.database.windows.net,3342` , respectivamente.

O script gerará linhas de comando dtutil para todos os pacotes no MSDB que você pode usar multiseleção, copiar & colar e executar em um prompt de comando.

![Gerar linhas de comando dtutil](media/azure-ssis-integration-runtime-package-store/sql-server-msdb-to-sql-mi-msdb.png)

```dos
dtutil /SQL YourFolder\YourPackage1 /ENCRYPT SQL;YourFolder\YourPackage1;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
dtutil /SQL YourFolder\YourPackage2 /ENCRYPT SQL;YourFolder\YourPackage2;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
dtutil /SQL YourFolder\YourPackage3 /ENCRYPT SQL;YourFolder\YourPackage3;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
```

Se você tiver configurado Azure-SSIS IR armazenamentos de pacotes na parte superior do MSDB, seus pacotes implantados serão exibidos neles quando você se conectar ao seu Azure-SSIS IR no SSMS 2019 ou em versões posteriores.

### <a name="deploying-multiple-packages-from-msdb-on-premises-into-azure-files-with-dtutil"></a>Implantando vários pacotes do MSDB localmente em arquivos do Azure com o dtutil

 Para implantar vários pacotes do MSDB hospedado por SQL Server ou armazenamentos de pacotes SSIS herdados na parte superior do MSDB em arquivos do Azure e alternar o nível de proteção ao mesmo tempo, você pode se conectar ao seu SQL Server no SSMS, clicar com o botão direito do mouse no `Databases->System Databases->msdb` nó no Pesquisador de **objetos** do SSMS para abrir uma nova janela de **consulta** e executar o script T-SQL a seguir. Substitua todas as cadeias de caracteres que são específicas do seu caso:  
  
```sql
BEGIN
  SELECT 'dtutil /SQL '+f.foldername+'\'+NAME+' /ENCRYPT FILE;Z:\'+f.foldername+'\'+NAME+'.dtsx;2;YourEncryptionPassword' 
  FROM msdb.dbo.sysssispackages p
  inner join msdb.dbo.sysssispackagefolders f
  ON p.folderid = f.folderid
END
```

O script gerará linhas de comando dtutil para todos os pacotes no MSDB que você pode usar multiseleção, copiar & colar e executar em um prompt de comando.

```dos
REM Persist the access credentials for Azure Files on your local machine
cmdkey /ADD:YourStorageAccountName.file.core.windows.net /USER:azure\YourStorageAccountName /PASS:YourStorageAccountKey

REM Connect Azure Files to a drive on your local machine
net use Z: \\YourStorageAccountName.file.core.windows.net\YourFileShare /PERSISTENT:Yes

REM Multiselect, copy & paste, and run the T-SQL-generated dtutil command lines to deploy your packages from MSDB on premises into Azure Files while switching their protection level
dtutil /SQL YourFolder\YourPackage1 /ENCRYPT FILE;Z:\YourFolder\YourPackage1.dtsx;2;YourEncryptionPassword
dtutil /SQL YourFolder\YourPackage2 /ENCRYPT FILE;Z:\YourFolder\YourPackage2.dtsx;2;YourEncryptionPassword
dtutil /SQL YourFolder\YourPackage3 /ENCRYPT FILE;Z:\YourFolder\YourPackage3.dtsx;2;YourEncryptionPassword
```

Se você tiver configurado Azure-SSIS IR armazenamentos de pacotes sobre os arquivos do Azure, seus pacotes implantados aparecerão neles quando você se conectar ao seu Azure-SSIS IR no SSMS 2019 ou em versões posteriores.

## <a name="next-steps"></a>Próximas etapas

Você pode executar novamente/editar os pipelines do ADF gerados automaticamente com atividades executar pacote do SSIS ou criar novos no portal do ADF. Para obter mais informações, consulte [executar pacotes do SSIS como atividades de execução do pacote do SSIS em pipelines do ADF](./how-to-invoke-ssis-package-ssis-activity.md).