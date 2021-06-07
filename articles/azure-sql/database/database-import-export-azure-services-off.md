---
title: Importe ou exporte um banco de dados SQL do Azure sem permitir que os serviços do Azure acessem o servidor.
description: Importe ou exporte um banco de dados SQL do Azure sem permitir que os serviços do Azure acessem o servidor.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/08/2020
ms.openlocfilehash: 3a02876234d43df2e98a3a4e60453fc3f1f74ef6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724145"
---
# <a name="import-or-export-an-azure-sql-database-without-allowing-azure-services-to-access-the-server"></a>Importar ou exportar um banco de dados SQL do Azure sem permitir que os serviços do Azure acessem o servidor
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo mostra como importar ou exportar um banco de dados SQL do Azure quando *permitir que os serviços do Azure* sejam definidos como *off* no servidor. O fluxo de trabalho usa uma máquina virtual do Azure para executar o SqlPackage para executar a operação de importação ou exportação.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="create-the-azure-virtual-machine"></a>Criar a máquina virtual do Azure

Crie uma máquina virtual do Azure selecionando o botão **implantar no Azure** .

Este modelo permite que você implante uma máquina virtual simples do Windows usando algumas opções diferentes para a versão do Windows, usando a versão mais recente com patches. Isso implantará uma VM de tamanho a2 no local do grupo de recursos e retornará o nome de domínio totalmente qualificado da VM.
<br><br>

[![Imagem mostrando um botão rotulado como "Implantar no Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-simple-windows%2Fazuredeploy.json)

Para obter mais informações, consulte [implantação muito simples de uma VM do Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows).

## <a name="connect-to-the-virtual-machine"></a>Conectar-se à máquina virtual

As etapas a seguir mostram como se conectar à sua máquina virtual usando uma conexão de área de trabalho remota.

1. Após a conclusão da implantação, vá para o recurso da máquina virtual.

   ![Captura de tela mostra uma página de visão geral da máquina virtual com um botão conectar.](./media/database-import-export-azure-services-off/vm.png)  

2. Selecione **Conectar**.

   Um formulário de Protocolo RDP (arquivo .rdp) é exibido com o IP endereço IP público e o número da porta para a máquina virtual.

   ![Formulário do RDP](./media/database-import-export-azure-services-off/rdp.png)  

3. Selecione **Baixar Arquivo RDP**.

   > [!NOTE]
   > Você também pode usa SSH para se conectar à VM.

4. Feche o formulário **Conectar-se à máquina virtual**.
5. Para se conectar à sua VM, abra o arquivo RDP baixado.
6. Quando solicitado, selecione **Conectar**. Em um Mac, você precisa de um cliente RDP, como este [Cliente de Área de Trabalho Remota](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12) da Mac App Store.

7. Insira o nome de usuário e a senha especificados na criação da máquina virtual e escolha **OK**.

8. Você pode receber um aviso de certificado durante o processo de entrada. Escolha **Sim** ou **Continuar** para prosseguir com a conexão.

## <a name="install-sqlpackage"></a>Instalar SqlPackage

[Baixe e instale a versão mais recente do SqlPackage](/sql/tools/sqlpackage-download).

Para obter informações adicionais, consulte [SqlPackage.exe](/sql/tools/sqlpackage).

## <a name="create-a-firewall-rule-to-allow-the-vm-access-to-the-database"></a>Criar uma regra de firewall para permitir que a VM acesse o banco de dados

Adicione o endereço IP público da máquina virtual ao firewall do servidor.

As etapas a seguir criam uma regra de firewall de IP no nível de servidor para o endereço IP público de sua máquina virtual e habilita a conectividade da máquina virtual.

1. Selecione **bancos** de dados SQL no menu à esquerda e, em seguida, selecione o seu banco de dados na página **SQL databases** . A página Visão geral do seu banco de dados é aberta, mostrando o nome totalmente qualificado do servidor (como **servername.Database.Windows.net**) e fornece opções para configuração adicional.

2. Copie esse nome de servidor totalmente qualificado para usar ao conectar-se ao servidor e a seus bancos de dados.

   ![nome do servidor](./media/database-import-export-azure-services-off/server-name.png)

3. Selecione **Definir firewall do servidor** na barra de ferramentas. A página **Configurações do firewall** do servidor será aberta.

   ![regra de firewall de IP no nível do servidor](./media/database-import-export-azure-services-off/server-firewall-rule.png)

4. Escolha **Adicionar IP do cliente** na barra de ferramentas para adicionar o endereço IP público da máquina virtual a uma nova regra de firewall de IP no nível de servidor. Uma regra de firewall de IP no nível do servidor pode abrir a porta 1433 para um único endereço IP ou um intervalo de endereços IP.

5. Clique em **Salvar**. Uma regra de firewall de IP no nível de servidor é criada para o endereço IP público da sua máquina virtual abrindo a porta 1433 no servidor.

6. Feche a página **Configurações do Firewall**.

## <a name="export-a-database-using-sqlpackage"></a>Exportar um banco de dados usando SqlPackage

Para exportar um banco de dados SQL do Azure usando o utilitário de linha de comando [SqlPackage](/sql/tools/sqlpackage) , consulte [Exportar parâmetros e propriedades](/sql/tools/sqlpackage#export-parameters-and-properties). O utilitário SqlPackage é fornecido com as versões mais recentes do [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) e do [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt), ou você pode baixar a versão mais recente do [SqlPackage](/sql/tools/sqlpackage-download).

Recomendamos o uso do utilitário SqlPackage para escala e desempenho na maioria dos ambientes de produção. Para ler uma postagem de blog da Equipe de Consultoria ao Cliente do SQL Server sobre a migração usando arquivos BACPAC, confira [Migrando do SQL Server para o Banco de Dados SQL do Azure usando arquivos BACPAC](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files).

Este exemplo mostra como exportar um banco de dados usando SqlPackage.exe com Active Directory autenticação universal. Substituir por valores específicos do seu ambiente.

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=<servername>.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-a-database-using-sqlpackage"></a>Importar um banco de dados usando SqlPackage

Para importar um Banco de Dados do SQL Server usando o utilitário de linha de comando [SqlPackage](/sql/tools/sqlpackage), confira [Importar parâmetros e propriedades](/sql/tools/sqlpackage#import-parameters-and-properties). O SqlPackage tem as últimas [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) e [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt). Você também pode baixar a versão mais recente do [SqlPackage](/sql/tools/sqlpackage-download).

Para escala e desempenho, recomendamos usar o SqlPackage na maioria dos ambientes de produção em vez de usar no portal do Azure. Para ler uma postagem de blog da Equipe de Consultoria ao Cliente do SQL Server sobre a migração usando arquivos `BACPAC`, confira [Migrando do SQL Server para o Banco de Dados SQL do Azure usando arquivos BACPAC](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files).

O comando SqlPackage a seguir importa o banco de dados **AdventureWorks2017** do armazenamento local para um banco de dados SQL do Azure. Ele cria um novo banco de dados chamado **myMigratedDatabase** com uma camada de serviço **Premium** e um Objetivo de serviço **P6**. Altere esses valores conforme apropriado para o seu ambiente.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=myMigratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2017.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Para se conectar ao banco de dados SQL do tAzure por trás de um firewall corporativo, o firewall deve ter a porta 1433 aberta.

Este exemplo mostra como importar um banco de dados usando o SqlPackage com a Autenticação Universal do Active Directory.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="performance-considerations"></a>Considerações sobre o desempenho

As velocidades de exportação variam devido a muitos fatores (por exemplo, forma de dados), portanto, é impossível prever qual velocidade deve ser esperada. O SqlPackage pode levar um tempo considerável, especialmente para bancos de dados grandes.

Para obter o melhor desempenho, você pode experimentar as seguintes estratégias:

1. Verifique se nenhuma outra carga de trabalho está em execução no banco de dados. Criar uma cópia antes da exportação pode ser a melhor solução para garantir que nenhuma outra carga de trabalho esteja em execução.
2. Aumente o objetivo de nível de serviço do banco de dados (SLO) para lidar melhor com a carga de trabalho de exportação (principalmente e/s de leitura). Se o banco de dados estiver atualmente GP_Gen5_4, talvez uma camada de Comercialmente Crítico ajudaria com a carga de trabalho de leitura.
3. Verifique se há índices clusterizados particularmente para tabelas grandes.
4. As máquinas virtuais (VMs) devem estar na mesma região que o banco de dados para ajudar a evitar restrições de rede.
5. As VMs devem ter SSD com tamanho adequado para gerar artefatos temporários antes de carregar para o armazenamento de BLOBs.
6. As VMs devem ter a configuração de núcleo e memória adequada para o banco de dados específico.

## <a name="store-the-imported-or-exported-bacpac-file"></a>Armazene o importado ou exportado. Arquivo BACPAC

Dos. O arquivo BACPAC pode ser armazenado em [BLOBs do Azure](../../storage/blobs/storage-blobs-overview.md)ou [arquivos do Azure](../../storage/files/storage-files-introduction.md).

Para obter o melhor desempenho, use os arquivos do Azure. O SqlPackage opera com o sistema de arquivos para que ele possa acessar os arquivos do Azure diretamente.

Para reduzir o custo, use BLOBs do Azure, que custam menos do que um compartilhamento de arquivos premium do Azure. No entanto, será necessário copiar o [. Arquivo BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) entre o blob e o sistema de arquivos local antes da operação de importação ou exportação. Como resultado, o processo levará mais tempo.

Para carregar ou baixar. Arquivos BACPAC, consulte [transferir dados com o armazenamento de BLOBs e AzCopy](../../storage/common/storage-use-azcopy-v10.md#transfer-data)e [transferir dados com o AzCopy e o armazenamento de arquivos](../../storage/common/storage-use-azcopy-files.md).

Dependendo do seu ambiente, talvez seja necessário configurar as [redes virtuais e os firewalls de armazenamento do Azure](../../storage/common/storage-network-security.md).

## <a name="next-steps"></a>Próximas etapas

- Para saber como se conectar a um banco de dados SQL importado e consultá-lo, consulte [início rápido: Azure SQL Server Management Studio SQL](connect-query-ssms.md)
- Para ler uma postagem de blog da Equipe de Consultoria ao Cliente do SQL Server sobre a migração usando arquivos BACPAC, confira [Migrando do SQL Server para o Banco de Dados SQL do Azure usando arquivos BACPAC](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- Para ver uma discussão sobre todo o processo de migração do banco de dados do SQL Server, incluindo as recomendações de desempenho, consulte [Migração de um banco de dados do SQL Server para o Banco de Dados SQL do Azure](migrate-to-database-from-sql-server.md).
- Para aprender como gerenciar e compartilhar chaves de armazenamento e assinaturas de acesso compartilhado com segurança, consulte [Guia de Segurança do Armazenamento do Microsoft Azure](../../storage/blobs/security-recommendations.md).
