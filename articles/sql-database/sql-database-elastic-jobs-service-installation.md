---
title: Instalando trabalhos de banco de dados elástico | Microsoft Docs
description: Percorra a instalação do recurso de trabalho elástico.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 32df3e7ac6dc22e247bd4aecea4f39bf6d3a8017
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61475708"
---
# <a name="installing-elastic-database-jobs-overview"></a>Visão geral de Instalando trabalhos de Banco de Dados Elástico

[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]



Os [**trabalhos de Banco de Dados Elástico**](sql-database-elastic-jobs-overview.md) podem ser instalados por meio do PowerShell ou pelo portal do Azure. Será possível obter acesso para criar e gerenciar trabalhos usando a API do PowerShell apenas se você instalar o pacote do PowerShell. Além disso, as APIs do PowerShell fornecem muito mais funcionalidade do que o portal neste momento.

Se você já tiver instalado **trabalhos de banco de dados Elástico** por meio do Portal de uma já existente **pool Elástico**, a visualização mais recente do PowerShell incluirá scripts para atualizar sua instalação existente. É altamente recomendável atualizar sua instalação para a versão mais recente dos componentes dos **trabalhos de Banco de Dados Elástico** para aproveitar a nova funcionalidade exposta por meio das APIs do PowerShell.

## <a name="prerequisites"></a>Pré-requisitos
* Uma assinatura do Azure. Para obter uma avaliação gratuita, veja [Avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* PowerShell do Azure. Instale a versão mais recente usando o [Web Platform Installer](https://go.microsoft.com/fwlink/p/?linkid=320376). Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](/powershell/azure/overview).
* [Utilitário de Linha de Comando do NuGet](https://nuget.org/nuget.exe) é usado para instalar o pacote de trabalhos de Banco de Dados Elástico. Para obter mais informações, consulte https://docs.nuget.org/docs/start-here/installing-nuget.

## <a name="download-and-import-the-elastic-database-jobs-powershell-package"></a>Baixar e importar o pacote do PowerShell de trabalhos de Banco de Dados Elástico
1. Inicie a janela de comando do Microsoft Azure PowerShell e navegue até o diretório onde você baixou o utilitário de linha de comando do NuGet (nuget.exe).
2. Baixar e importar o pacote **trabalhos de Banco de Dados Elástico** para o diretório atual com o seguinte comando:
   
        PS C:\>.\nuget install Microsoft.Azure.SqlDatabase.Jobs -prerelease
   
    Os arquivos de **trabalhos de Banco de Dados Elástico** são colocados no diretório local em uma pasta chamada **Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x**, em que *x.x.xxxx.x* reflete o número de versão. Os cmdlets do PowerShell (incluindo .dlls de cliente necessárias) estão localizados no subdiretório **tools\ElasticDatabaseJobs** e os scripts do PowerShell a serem instalados, atualizados e desinstalados também residem no subdiretório **tools**.
3. Navegue até o subdiretório Ferramentas, na pasta Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x, digitando cd ferramentas, por exemplo:
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

4. Execute o script .\InstallElasticDatabaseJobsCmdlets.ps1 para copiar o diretório ElasticDatabaseJobs para $home\Documents\WindowsPowerShell\Modules. Isso também importará automaticamente o módulo a ser usado, por exemplo:
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobsCmdlets.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobsCmdlets.ps1

## <a name="install-the-elastic-database-jobs-components-using-powershell"></a>Instalar componentes de trabalhos de Banco de Dados Elástico usando o PowerShell
1. Inicie uma janela de comando do Microsoft Azure PowerShell e navegue até o subdiretório de ferramentas na pasta Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x: Digite cd \tools
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

2. Execute o script do PowerShell .\InstallElasticDatabaseJobs.ps1 e forneça valores para as variáveis solicitadas. Esse script cria os componentes descritos em [Preços e componentes de trabalhos de Banco de Dados Elástico](sql-database-elastic-jobs-overview.md#components-and-pricing) , juntamente com a configuração do Serviço de Nuvem do Azure para usar os componentes dependentes.

        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobs.ps1

Ao executar este comando, uma janela é aberta solicitando um **nome de usuário** e **senha**. Essas não são suas credenciais do Azure, insira o nome de usuário e a senha que serão as credenciais de administrador que você deseja criar para o novo servidor.

Os parâmetros fornecidos nesta chamada de exemplo podem ser modificados para as configurações desejadas. O exemplo a seguir fornece mais informações sobre o comportamento de cada parâmetro:

<table style="width:100%">
  <tr>
    <th>Parâmetro</th>
    <th>DESCRIÇÃO</th>
  </tr>

<tr>
    <td>ResourceGroupName</td>
    <td>Fornece o nome do grupo de recursos do Azure criado para conter os componentes recém-criados do Azure. Esse parâmetro usa "__ElasticDatabaseJob" como padrão. Não é recomendável alterar esse valor.</td>
    </tr>

<tr>
    <td>ResourceGroupLocation</td>
    <td>Fornece o local do Azure a ser usado para os componentes recém-criados do Azure. O padrão desse parâmetro é o local Central dos Estados Unidos.</td>
</tr>

<tr>
    <td>ServiceWorkerCount</td>
    <td>Fornece o número de trabalhadores de serviço para instalar. O padrão desse parâmetro é 1. Um número maior de trabalhadores pode ser usado para escalar horizontalmente o serviço e fornecer alta disponibilidade. É recomendável usar "2" para implantações que exigem alta disponibilidade do serviço.</td>
</tr>

<tr>
    <td>ServiceVmSize</td>
    <td>Fornece o tamanho da VM para uso no Serviço de Nuvem. O padrão desse parâmetro é A0. Valores de parâmetros de... /.. / A3 são aceitos, o que fazer com que a função de trabalho usar um tamanho extra pequeno/pequeno/médio/grande, respectivamente. Para obter mais informações sobre tamanhos de função de trabalho, consulte <a href="https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview#components-and-pricing">preços e componentes de trabalhos de banco de dados Elástico</a>.</td>
</tr>

<tr>
    <td>SqlServerDatabaseSlo</td>
    <td>Fornece o tamanho da computação para uma edição Standard. O padrão desse parâmetro é S0. Valores de parâmetro de... /.. /.. /.. / S9/S12 são aceitos, o que fazer com que o banco de dados do SQL Azure usar o tamanho da respectiva computação. Para obter mais informações sobre os tamanhos da computação do Banco de Dados SQL, consulte <a href="https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview#components-and-pricing">Preço e componentes de trabalhos do Banco de Dados Elástico</a>.</td>
</tr>

<tr>
    <td>SqlServerAdministratorUserName</td>
    <td>Fornece o nome de usuário do administrador para o servidor do Banco de Dados SQL recém-criado. Quando as credenciais não forem especificadas, será exibida uma janela de credenciais do PowerShell solicitando-as.</td>
</tr>

<tr>
    <td>SqlServerAdministratorPassword</td>
    <td>Fornece a senha do administrador para o servidor do Banco de Dados SQL recém-criado. Quando as credenciais não forem fornecidas, será exibida uma janela de credenciais do PowerShell solicitando-as.</td>
</tr>
</table>

Para sistemas com grandes números de trabalhos sendo executados em paralelo em um grande número de bancos de dados de destino, é recomendável especificar parâmetros como: -ServiceWorkerCount 2 -ServiceVmSize A2 -SqlServerDatabaseSlo S2.

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\InstallElasticDatabaseJobs.ps1 -ServiceWorkerCount 2 -ServiceVmSize A2 -SqlServerDatabaseSlo S2

## <a name="update-an-existing-elastic-database-jobs-components-installation-using-powershell"></a>Atualizar uma instalação existente de componentes do trabalhos de Banco de Dados Elástico usando o PowerShell
**trabalhos de Banco de Dados Elástico** podem ser atualizados em uma instalação existente em relação à escala e à alta disponibilidade. Esse processo permite atualizações futuras do código de serviço sem precisar remover e recriar o banco de dados de controle. Esse processo também pode ser usado dentro da mesma versão para modificar o tamanho da VM de serviço ou a contagem de trabalho do servidor.

Para atualizar o tamanho da VM de uma instalação, execute o script a seguir com parâmetros atualizados para os valores de sua escolha.

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\UpdateElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\UpdateElasticDatabaseJobs.ps1 -ServiceVmSize A1 -ServiceWorkerCount 2

<table style="width:100%">
  <tr>
  <th>Parâmetro</th>
  <th>DESCRIÇÃO</th>
</tr>

<tr>
    <td>ResourceGroupName</td>
    <td>Identifica o nome do grupo de recursos do Azure usado quando os componentes de trabalho de Banco de Dados Elástico foram inicialmente instalados. Esse parâmetro usa "__ElasticDatabaseJob" como padrão. Uma vez que não é recomendável alterar esse valor, você não deve ter que especificar este parâmetro.</td>
</tr>

<tr>
    <td>ServiceWorkerCount</td>
    <td>Fornece o número de trabalhadores de serviço para instalar.  O padrão desse parâmetro é 1.  Um número maior de trabalhadores pode ser usado para escalar horizontalmente o serviço e fornecer alta disponibilidade.  É recomendável usar "2" para implantações que exigem alta disponibilidade do serviço.</td>
</tr>

<tr>
    <td>ServiceVmSize</td>
    <td>Fornece o tamanho da VM para uso no Serviço de Nuvem. O padrão desse parâmetro é A0. Valores de parâmetros de... /.. / A3 são aceitos, o que fazer com que a função de trabalho usar um tamanho extra pequeno/pequeno/médio/grande, respectivamente. Para obter mais informações sobre tamanhos de função de trabalho, consulte <a href="https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview#components-and-pricing">preços e componentes de trabalhos de banco de dados Elástico</a>.</td>
</tr>

</table>

## <a name="install-the-elastic-database-jobs-components-using-the-portal"></a>Instalar os componentes de trabalhos de Banco de Dados Elástico usando o Portal
Após a [criação de um pool elástico](sql-database-elastic-pool-manage-portal.md), você pode instalar componentes dos **trabalhos de Banco de Dados Elástico** para habilitar a execução de tarefas administrativas em cada banco de dados no pool elástico. Ao contrário do que ocorre quando as APIs do PowerShell de **trabalhos de Banco de Dados Elástico** são usadas, a interface do portal está atualmente restrita à execução exclusivamente em um pool existente.

**Tempo estimado para conclusão:** 10 minutos.

1. Na exibição de painel do pool elástico, por meio do [portal do Azure](https://portal.azure.com/#), clique em **Criar trabalho**.
2. Se estiver criando um trabalho pela primeira vez, será necessário instalar os **trabalhos de Banco de Dados Elástico** clicando em **VISUALIZAR TERMOS**.
3. Aceite os termos clicando na caixa de seleção.
4. No modo de exibição "Instalar serviços", clique em **CREDENCIAIS DO TRABALHO**.
   
    ![Instalando os serviços][1]
5. Digite um nome de usuário e uma senha para um administrador de banco de dados. Como parte da instalação, um novo servidor do Banco de Dados SQL é criado. Dentro desse novo servidor, um novo banco de dados, conhecido como banco de dados de controle, é criado e usado para conter os metadados para trabalhos de Banco de Dados Elástico. O nome de usuário e senha criados aqui são usados para fins de logon no banco de dados do controle. Uma credencial separada é usada para execução de scripts nos bancos de dados dentro do pool.
   
    ![Criar nome de usuário e senha][2]
6. Clique no botão OK. Os componentes são criados para você em questão de minutos em um novo [Grupo de recursos](../azure-resource-manager/resource-group-overview.md). O novo grupo de recursos é fixado à tela inicial, conforme mostrado abaixo. Depois de criados, os trabalhos de banco de dados elástico (Serviço de Nuvem, Banco de Dados SQL, Barramento de Serviço e Armazenamento) são criados no grupo.
   
    ![grupo de recursos na tela inicial][3]
7. Se você tentar criar ou gerenciar um trabalho durante instalação dos trabalhos de banco de dados elástico, ao fornecer as **Credenciais** , verá a mensagem a seguir.
   
    ![Implantação ainda em andamento][4]

Se a desinstalação for necessária, exclua o grupo de recursos. Veja [Como desinstalar os componentes de trabalho de Banco de Dados Elástico](sql-database-elastic-jobs-uninstall.md).

## <a name="next-steps"></a>Próximas etapas
Certifique-se de que uma credencial com os direitos apropriados para a execução do script é criada em cada banco de dados no grupo; para obter mais informações, veja [Protegendo seu Banco de Dados SQL](sql-database-manage-logins.md).
Veja [Criando e gerenciando trabalhos de Banco de Dados Elástico](sql-database-elastic-jobs-create-and-manage.md) para começar.

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-service-installation/screen-1.png
[2]: ./media/sql-database-elastic-jobs-service-installation/credentials.png
[3]: ./media/sql-database-elastic-jobs-service-installation/start-board.png
[4]: ./media/sql-database-elastic-jobs-service-installation/not-done.png
