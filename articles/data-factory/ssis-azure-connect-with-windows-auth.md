---
title: Acessar armazenamentos de dados e compartilhamentos de arquivos com a autenticação do Windows
description: Saiba como configurar o Catálogo do SSIS no Banco de Dados SQL e o Azure-SSIS Integration Runtime no Azure Data Factory para executar pacotes que se acessam armazenamento de dados e compartilhamentos de arquivos com a Autenticação do Windows.
ms.date: 10/27/2020
ms.topic: conceptual
ms.prod: sql
ms.prod_service: integration-services
ms.custom: ''
ms.technology: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: jburchel
ms.openlocfilehash: 8cb92407954ab5376850e57b0ee91d934480b025
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786882"
---
# <a name="access-data-stores-and-file-shares-with-windows-authentication-from-ssis-packages-in-azure"></a>Acessar armazenamentos de dados e compartilhamentos de arquivos com a Autenticação do Windows de pacotes SSIS no Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Você pode usar a Autenticação do Windows para acessar armazenamentos de dados, como SQL Servers, compartilhamentos de arquivos, Arquivos do Azure etc. em pacotes SSIS em execução no seu Azure-SSIS IR (Integration Runtime) no ADF (Azure Data Factory). Seus armazenamentos de dados podem estar no local, hospedados em VMs (máquinas virtuais) do Azure ou em execução no Azure como serviços gerenciados. Se eles estiverem no local, você precisa ingressar seu Azure-SSIS IR em uma rede virtual (Rede Virtual do Microsoft Azure) conectada à sua rede local, consulte [unir Azure-SSIS ir a um rede virtual do Microsoft Azure](./join-azure-ssis-integration-runtime-virtual-network.md). Há quatro métodos para acessar armazenamentos de dados com a Autenticação do Windows em pacotes SSIS em execução no Azure-SSIS IR:

| Método de conexão | Escopo efetivo | Etapa de configuração | Método de acesso em pacotes | Número de conjuntos de credenciais e recursos conectados | Tipo de recursos conectados | 
|---|---|---|---|---|---|
| Como configurar um contexto de execução no nível da atividade | Por atividade Executar pacote do SSIS | Configure a propriedade **Autenticação do Windows** para definir um contexto de "Execução/Executar como" ao executar o pacotes SSIS como atividades Executar pacote SSIS em pipelines do ADF.<br/><br/> Confira mais informações em [Configurar a atividade Executar pacote SSIS](./how-to-invoke-ssis-package-ssis-activity.md). | Acesse recursos diretamente em pacotes, por exemplo, use o caminho UNC para acessar compartilhamentos de arquivos ou arquivos do Azure: `\\YourFileShareServerName\YourFolderName` ou `\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Suporte a apenas um conjunto de credenciais para todos os recursos conectados | - Compartilhamentos de arquivos no local/VMs do Azure<br/><br/> - Arquivos do Azure; confira [Usar um compartilhamento de arquivos do Azure](../storage/files/storage-how-to-use-files-windows.md)<br/><br/> – SQL Servers no local/VMs Azure com a autenticação do Windows<br/><br/> – Outros recursos com Autenticação do Windows |
| Como configurar um contexto de execução de nível de catálogo | Por Azure-SSIS IR, mas é substituído ao configurar um contexto de execução de nível de atividade (veja acima) | Execute o procedimento armazenado `catalog.set_execution_credential` do SSISDB para configurar um contexto de "Execução/Executar como".<br/><br/> Para saber mais, confira o restante deste artigo abaixo. | Acesse recursos diretamente em pacotes, por exemplo, use o caminho UNC para acessar compartilhamentos de arquivos ou arquivos do Azure: `\\YourFileShareServerName\YourFolderName` ou `\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Suporte a apenas um conjunto de credenciais para todos os recursos conectados | - Compartilhamentos de arquivos no local/VMs do Azure<br/><br/> - Arquivos do Azure; confira [Usar um compartilhamento de arquivos do Azure](../storage/files/storage-how-to-use-files-windows.md)<br/><br/> – SQL Servers no local/VMs Azure com a autenticação do Windows<br/><br/> – Outros recursos com Autenticação do Windows |
| Credenciais persistentes por meio do comando `cmdkey` | Por Azure-SSIS IR, mas é substituído durante a configuração de um contexto de execução de nível de catálogo/atividade (veja acima) | Execute `cmdkey` o comando em um script de instalação personalizada ( `main.cmd` ) ao provisionar seu Azure-SSIS ir, por exemplo, se você usar compartilhamentos de arquivos, arquivos do Azure ou SQL Server:<br/><br/> `cmdkey /add:YourFileShareServerName /user:YourDomainName\YourUsername /pass:YourPassword`,<br/><br/> `cmdkey /add:YourAzureStorageAccountName.file.core.windows.net /user:azure\YourAzureStorageAccountName /pass:YourAccessKey`, ou<br/><br/> `cmdkey /add:YourSQLServerFullyQualifiedDomainNameOrIPAddress:YorSQLServerPort /user:YourDomainName\YourUsername /pass:YourPassword`.<br/><br/> Para saber mais, confira [Personalizar a instalação para o Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md). | Acesse recursos diretamente em pacotes, por exemplo, use o caminho UNC para acessar compartilhamentos de arquivos ou arquivos do Azure: `\\YourFileShareServerName\YourFolderName` ou `\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Suporte a vários conjuntos de credenciais para diferentes recursos conectados | - Compartilhamentos de arquivos no local/VMs do Azure<br/><br/> - Arquivos do Azure; confira [Usar um compartilhamento de arquivos do Azure](../storage/files/storage-how-to-use-files-windows.md)<br/><br/> – SQL Servers no local/VMs Azure com a autenticação do Windows<br/><br/> – Outros recursos com Autenticação do Windows |
| Montagem de unidades no tempo de execução do pacote (não persistente) | Por pacote | Execute o comando `net use` em Executar Processo de Tarefa, que será adicionado ao início do fluxo de controle em seus pacotes, por exemplo, `net use D: \\YourFileShareServerName\YourFolderName` | Acessar compartilhamentos de arquivos por meio de unidades mapeadas | Suporte a várias unidades para compartilhamentos de arquivos diferentes | - Compartilhamentos de arquivos no local/VMs do Azure<br/><br/> - Arquivos do Azure; confira [Usar um compartilhamento de arquivos do Azure](../storage/files/storage-how-to-use-files-windows.md) |
|||||||

> [!WARNING]
> Se você não usar nenhum dos métodos acima para acessar os armazenamentos de dados com a autenticação do Windows, os pacotes que dependem da autenticação do Windows não poderão acessá-los e apresentar falha em tempo de execução. 

O restante deste artigo descreve como configurar o catálogo do SSIS (SSISDB) hospedado no banco de dados SQL/SQL Instância Gerenciada para executar pacotes no Azure-SSIS IR que usam a autenticação do Windows para acessar armazenamentos. 

## <a name="you-can-only-use-one-set-of-credentials"></a>Você só pode usar um conjunto de credenciais

Ao usar a autenticação do Windows em um pacote SSIS, você pode usar apenas um conjunto de credenciais. As credenciais de domínio que você fornece ao seguir as etapas neste artigo se aplicam a todas as execuções de pacote, interativas ou agendadas, no Azure-SSIS IR até você alterá-las ou removê-las. Se o pacote precisar se conectar a vários armazenamentos de dados com diferentes conjuntos de credenciais, considere os métodos alternativos acima.

## <a name="provide-domain-credentials-for-windows-authentication"></a>Forneça credenciais de domínio para Autenticação do Windows

Para fornecer credenciais de domínio que permitem que os pacotes usam a autenticação do Windows para acessar armazenamentos de dados locais, faça o seguinte:

1. Com SQL Server Management Studio (SSMS) ou outra ferramenta, conecte-se ao banco de dados SQL/SQL Instância Gerenciada que hospeda o SSISDB. Confira mais informações em [Conectar ao SSISDB no Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Com o SSISDB como o banco de dados atual, abra uma janela de consulta.

3. Execute o procedimento armazenado a seguir e forneça credenciais de domínio apropriadas:

   ```sql
   catalog.set_execution_credential @user='<your user name>', @domain='<your domain name>', @password='<your password>'
   ```

4. Executar seus pacotes SSIS. Os pacotes usam as credenciais que você forneceu para acessar armazenamentos de dados localmente com a autenticação do Windows.

### <a name="view-domain-credentials"></a>Exibir credenciais de domínio

Para exibir as credenciais de domínio ativas, faça o seguinte:

1. Com o SSMS ou outra ferramenta, conecte-se ao banco de dados SQL/SQL Instância Gerenciada que hospeda o SSISDB. Confira mais informações em [Conectar ao SSISDB no Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Com o SSISDB como o banco de dados atual, abra uma janela de consulta.

3. Execute o seguinte procedimento armazenado e verifique a saída:

   ```sql
   SELECT * 
   FROM catalog.master_properties
   WHERE property_name = 'EXECUTION_DOMAIN' OR property_name = 'EXECUTION_USER'
   ```

### <a name="clear-domain-credentials"></a>Limpar credenciais de domínio
Para limpar e remover as credenciais que você forneceu conforme descrito neste artigo, faça o seguinte:

1. Com o SSMS ou outra ferramenta, conecte-se ao banco de dados SQL/SQL Instância Gerenciada que hospeda o SSISDB. Confira mais informações em [Conectar ao SSISDB no Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Com o SSISDB como o banco de dados atual, abra uma janela de consulta.

3. Execute o seguinte procedimento armazenado:

   ```sql
   catalog.set_execution_credential @user='', @domain='', @password=''
   ```

## <a name="connect-to-a-sql-server-on-premises"></a>Conectar a um SQL Server local

Para verificar se você pode se conectar a um SQL Server local, faça o seguinte:

1. Para executar este teste, localize um computador não ingressado em domínio.

2. No computador não ingressado no domínio, execute o seguinte comando para iniciar o SSMS com as credenciais de domínio que você deseja usar:

   ```cmd
   runas.exe /netonly /user:<domain>\<username> SSMS.exe
   ```

3. No SSMS, verifique se você pode se conectar ao SQL Server local.

### <a name="prerequisites"></a>Pré-requisitos

Para acessar um SQL Server local nos pacotes em execução no Azure, faça o seguinte:

1.  No SQL Server Configuration Manager, habilite o protocolo TCP/IP.

2. Permita o acesso pelo firewall do Windows. Confira mais informações em [Configurar o firewall do Windows para acessar o SQL Server](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access).

3. Junte seu Azure-SSIS IR a uma Rede Virtual do Microsoft Azure que esteja conectada ao SQL Server local.  Para obter mais informações, consulte [unir Azure-SSIS ir a um rede virtual do Microsoft Azure](./join-azure-ssis-integration-runtime-virtual-network.md).

4. Use o procedimento armazenado `catalog.set_execution_credential` do SSISDB para fornecer credenciais, conforme descrito neste artigo.

## <a name="connect-to-a-file-share-on-premises"></a>Conectar-se a um compartilhamento de arquivos no local

Para verificar se você pode se conectar a um compartilhamento de arquivos local, faça o seguinte:

1. Para executar este teste, localize um computador não ingressado em domínio.

2. No computador não ingressado no domínio, execute o comando a seguir. Esses comandos abrem uma janela de prompt de comando com as credenciais de domínio que você deseja usar e, em seguida, testam a conectividade ao compartilhamento de arquivos local obtendo uma listagem de diretórios.

   ```cmd
   runas.exe /netonly /user:<domain>\<username> cmd.exe
   dir \\fileshare
   ```

3. Verifique se a listagem de diretórios é retornada para o compartilhamento de arquivos local.

### <a name="prerequisites"></a>Pré-requisitos

Para acessar um compartilhamento de arquivo local nos pacotes em execução no Azure, faça o seguinte:

1. Permita o acesso pelo firewall do Windows.

2. Junte seu Azure-SSIS IR a uma Rede Virtual do Microsoft Azure que esteja conectada ao compartilhamento de arquivos local.  Para obter mais informações, consulte [unir Azure-SSIS ir a um rede virtual do Microsoft Azure](./join-azure-ssis-integration-runtime-virtual-network.md).

3. Use o procedimento armazenado `catalog.set_execution_credential` do SSISDB para fornecer credenciais, conforme descrito neste artigo.

## <a name="connect-to-a-file-share-on-azure-vm"></a>Conectar-se a um compartilhamento de arquivos em uma VM do Azure

Para acessar um compartilhamento de arquivo na VM do Azure nos pacotes em execução no Azure, faça o seguinte:

1. Com o SSMS ou outra ferramenta, conecte-se ao banco de dados SQL/SQL Instância Gerenciada que hospeda o SSISDB. Confira mais informações em [Conectar ao SSISDB no Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Com o SSISDB como o banco de dados atual, abra uma janela de consulta.

3. Execute o procedimento armazenado a seguir e forneça credenciais de domínio apropriadas:

   ```sql
   catalog.set_execution_credential @domain = N'.', @user = N'username of local account on Azure virtual machine', @password = N'password'
   ```

## <a name="connect-to-a-file-share-in-azure-files"></a>Conectar-se a um compartilhamento de arquivos nos Arquivos do Azure

Para obter mais informações sobre o Arquivos do Azure, consulte [Arquivos do Azure](https://azure.microsoft.com/services/storage/files/).

Para acessar um compartilhamento de arquivo nos Arquivos do Azure nos pacotes em execução no Azure, faça o seguinte:

1. Com o SSMS ou outra ferramenta, conecte-se ao banco de dados SQL/SQL Instância Gerenciada que hospeda o SSISDB. Confira mais informações em [Conectar ao SSISDB no Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Com o SSISDB como o banco de dados atual, abra uma janela de consulta.

3. Execute o procedimento armazenado a seguir e forneça credenciais de domínio apropriadas:

   ```sql
   catalog.set_execution_credential @domain = N'Azure', @user = N'<storage-account-name>', @password = N'<storage-account-key>'
   ```

## <a name="next-steps"></a>Próximas etapas

- Implante seus pacotes. Confira mais informações em [Implantar um projeto do SSIS no Azure com o SSMS](/sql/integration-services/ssis-quickstart-deploy-ssms).
- Execute os pacotes. Confira mais informações em [Executar pacotes SSIS no Azure com o SSMS](/sql/integration-services/ssis-quickstart-run-ssms).
- Agende seus pacotes. Para obter mais informações, consulte [agendar pacotes do SSIS no Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).
