---
title: Executar pacotes do SQL Server Integration Services (SSIS) com o utilitário dtexec habilitado para Azure
description: Saiba como executar pacotes do SQL Server Integration Services (SSIS) com o utilitário dtexec habilitado para Azure.
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/12/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 980e8e3c2f2c1ca1dc716df1e77caaa3fe3181aa
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386162"
---
# <a name="run-sql-server-integration-services-packages-with-the-azure-enabled-dtexec-utility"></a>Executar pacotes de SQL Server Integration Services com o utilitário dtexec habilitado para Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve o utilitário de prompt de comando dtexec (AzureDTExec) habilitado para Azure. Ele é usado para executar pacotes SQL Server Integration Services (SSIS) no Azure-SSIS Integration Runtime (IR) no Azure Data Factory.

O utilitário dtexec tradicional vem com SQL Server. Para obter mais informações, consulte [dtexec Utility](/sql/integration-services/packages/dtexec-utility). Geralmente, ele é invocado por orquestradores de terceiros ou agendadores, como ActiveBatch e Control-M, para executar pacotes do SSIS localmente. 

O utilitário AzureDTExec moderno vem com uma ferramenta de SQL Server Management Studio (SSMS). Ele também pode ser invocado por orquestradores de terceiros ou agendadores para executar pacotes do SSIS no Azure. Ele facilita o levantamento e a mudança ou a migração de seus pacotes do SSIS para a nuvem. Após a migração, se você quiser continuar usando os orquestradores de terceiros ou agendadores em suas operações diárias, eles agora podem invocar AzureDTExec em vez de dtexec.

O AzureDTExec executa seus pacotes como atividades de execução de pacote SSIS em pipelines Data Factory. Para obter mais informações, consulte [executar pacotes do SSIS como Azure data Factory atividades](./how-to-invoke-ssis-package-ssis-activity.md). 

O AzureDTExec pode ser configurado por meio do SSMS para usar um aplicativo Azure Active Directory (Azure AD) que gera pipelines no seu data factory. Ele também pode ser configurado para acessar sistemas de arquivos, compartilhamentos de arquivos ou arquivos do Azure em que você armazena seus pacotes. Com base nos valores que você fornece para suas opções de invocação, o AzureDTExec gera e executa um pipeline de Data Factory exclusivo com uma atividade executar pacote SSIS nele. Invocar AzureDTExec com os mesmos valores para suas opções executa novamente o pipeline existente.

## <a name="prerequisites"></a>Pré-requisitos
Para usar o AzureDTExec, baixe e instale a versão mais recente do SSMS, que é a versão 18,3 ou posterior. Baixe-o [neste site](/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="configure-the-azuredtexec-utility"></a>Configurar o utilitário AzureDTExec
Instalar o SSMS em seu computador local também instala o AzureDTExec. Para definir suas configurações, inicie o SSMS com a opção **Executar como administrador** . Em seguida, selecione **ferramentas**  >  **migrar para o Azure**  >  **Configurar dtexec habilitado para Azure**.

![Configurar o menu dtexec habilitado para Azure](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

Essa ação abre uma janela **AzureDTExecConfig** que precisa ser aberta com privilégios administrativos para gravar no arquivo *AzureDTExec. Settings* . Se você não tiver executado o SSMS como administrador, uma janela de controle de conta de usuário (UAC) será aberta. Insira sua senha de administrador para elevar seus privilégios.

![Definir configurações de dtexec habilitadas para o Azure](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

Na janela **AzureDTExecConfig** , insira as definições de configuração da seguinte maneira:

- **ApplicationId**: Insira o identificador exclusivo do aplicativo do Azure AD que você cria com as permissões corretas para gerar pipelines em seu data Factory. Para obter mais informações, consulte [criar um aplicativo do Azure AD e uma entidade de serviço por meio de portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md).
- **AuthenticationKey**: Insira a chave de autenticação para seu aplicativo do Azure AD.
- **Tenantid**: Insira o identificador exclusivo do locatário do Azure AD, no qual seu aplicativo do Azure AD é criado.
- **DataFactory**: Insira o nome do seu data Factory no qual pipelines exclusivos com atividade executar pacote SSIS neles são gerados com base nos valores das opções fornecidas quando você invoca AzureDTExec.
- **IRName**: Insira o nome do Azure-SSIS IR no data Factory, no qual os pacotes especificados em seu caminho UNC (Convenção de nomenclatura universal) serão executados quando você invocar AzureDTExec.
- **PipelineNameHashStrLen**: Insira o comprimento das cadeias de caracteres de hash a serem geradas a partir dos valores das opções que você fornece ao invocar AzureDTExec. As cadeias de caracteres são usadas para formar nomes exclusivos para Data Factory pipelines que executam seus pacotes no Azure-SSIS IR. Geralmente, um comprimento de 32 caracteres é suficiente.
- **Resourcegroup**: Insira o nome do grupo de recursos do Azure no qual seu data Factory foi criado.
- **SubscriptionId**: Insira o identificador exclusivo da assinatura do Azure, sob a qual seu data Factory foi criado.
- **LogAccessDomain**: Insira a credencial do domínio para acessar sua pasta de log em seu caminho UNC ao gravar arquivos de log, o que é necessário quando **logPath** é especificado e **logLevel** não é **nulo**.
- **LogAccessPassword**: Insira a credencial de senha para acessar a pasta de log em seu caminho UNC ao gravar arquivos de log, o que é necessário quando **logPath** é especificado e **logLevel** não é **nulo**.
- **LogAccessUserName**: Insira a credencial de nome de usuário para acessar sua pasta de log em seu caminho UNC ao gravar arquivos de log, o que é necessário quando **logPath** é especificado e **logLevel** não é **nulo**.
- **LogLevel**: Insira o escopo selecionado de log das opções predefinidas de **NULL**, **Basic**, **Verbose** ou de **desempenho** para as execuções de pacote no Azure-SSIS ir.
- **LogPath**: Insira o caminho UNC da pasta de log, em que arquivos de log de suas execuções de pacote no Azure-SSIS ir são gravados.
- **PackageAccessDomain**: Insira a credencial do domínio para acessar seus pacotes em seu caminho UNC que é especificado quando você invoca o AzureDTExec.
- **PackageAccessPassword**: Insira a credencial de senha para acessar seus pacotes em seu caminho UNC que é especificado quando você invoca o AzureDTExec.
- **PackageAccessUserName**: Insira a credencial de nome de usuário para acessar seus pacotes em seu caminho UNC que é especificado quando você invoca o AzureDTExec.

Para armazenar seus pacotes e arquivos de log em sistemas de arquivos ou compartilhamentos de arquivos locais, ingresse seu Azure-SSIS IR em uma rede virtual conectada à sua rede local para que ele possa buscar seus pacotes e gravar seus arquivos de log. Para obter mais informações, consulte [unir um Azure-SSIS ir a uma rede virtual](./join-azure-ssis-integration-runtime-virtual-network.md).

Para evitar a exibição de valores confidenciais gravados no arquivo *AzureDTExec. Settings* em texto sem formatação, nós os codificamos em cadeias de caracteres de codificação Base64. Quando você invoca AzureDTExec, todas as cadeias de caracteres codificadas em base64 são decodificadas de volta para seus valores originais. Você pode proteger ainda mais o arquivo *AzureDTExec. Settings* limitando as contas que podem acessá-lo.

## <a name="invoke-the-azuredtexec-utility"></a>Invocar o utilitário AzureDTExec
Você pode invocar AzureDTExec no prompt de linha de comando e fornecer os valores relevantes para as opções específicas em seu cenário de caso de uso.

O utilitário é instalado em `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn` . Você pode adicionar seu caminho à variável de ambiente ' PATH ' para que ele seja invocado de qualquer lugar.

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

Invocar AzureDTExec oferece opções semelhantes como invocar dtexec. Para saber mais, veja [dtexec Utility](/sql/integration-services/packages/dtexec-utility). Aqui estão as opções com suporte no momento:

- **/F [ile]**: carrega um pacote armazenado no sistema de arquivos, compartilhamento de arquivos ou arquivos do Azure. Como o valor dessa opção, você pode especificar o caminho UNC para o arquivo de pacote no sistema de arquivos, compartilhamento de arquivos ou arquivos do Azure com sua extensão. dtsx. Se o caminho UNC especificado contiver qualquer espaço, coloque o caminho inteiro entre aspas.
- **/Conf [igFile]**: especifica um arquivo de configuração do qual extrair valores. Usando essa opção, você pode definir uma configuração de tempo de execução para o pacote que difere da especificada em tempo de design. Você pode armazenar configurações diferentes em um arquivo de configuração XML e, em seguida, carregá-las antes da execução do pacote. Para obter mais informações, consulte [configurações do pacote SSIS](/sql/integration-services/packages/package-configurations). Para especificar o valor dessa opção, use o caminho UNC para o arquivo de configuração no sistema de arquivos, compartilhamento de arquivos ou arquivos do Azure com sua extensão dtsConfig. Se o caminho UNC especificado contiver qualquer espaço, coloque o caminho inteiro entre aspas.
- **/Conn [ection]**: especifica as cadeias de conexão para os gerenciadores de conexões existentes em seu pacote. Usando essa opção, você pode definir cadeias de conexão de tempo de execução para os gerenciadores de conexões existentes em seu pacote que diferem daqueles especificados em tempo de design. Especifique o valor para essa opção da seguinte maneira: `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]` .
- **/Set**: substitui a configuração de um parâmetro, uma variável, uma propriedade, um contêiner, um provedor de log, um enumerador foreach ou uma conexão em seu pacote. Essa opção pode ser especificada várias vezes. Especifique o valor para essa opção da seguinte maneira: `property_path;value` . Por exemplo, `\package.variables[counter].Value;1` substitui o valor da `counter` variável como 1. Você pode usar o assistente de **configuração de pacotes** para localizar, copiar e colar o valor de `property_path` para itens em seu pacote cujo valor você deseja substituir. Para obter mais informações, consulte [Assistente de configuração de pacote](/sql/integration-services/packages/legacy-package-deployment-ssis).
- **/De [criptu]**: define a senha de descriptografia para o pacote que é configurado com o / nível de proteção **EncryptSensitiveWithPassword** do EncryptAllWithPassword.

> [!NOTE]
> Invocar AzureDTExec com novos valores para suas opções gera um novo pipeline, exceto para a opção **/de [cript]**.

## <a name="next-steps"></a>Próximas etapas

Depois que pipelines exclusivos com a atividade executar pacote SSIS neles são gerados e executados quando você invoca o AzureDTExec, eles podem ser monitorados no portal de Data Factory. Você também pode atribuir Data Factory gatilhos a eles se desejar orquestrar/agendá-los usando Data Factory. Para obter mais informações, consulte [executar pacotes do SSIS como data Factory atividades](./how-to-invoke-ssis-package-ssis-activity.md).

> [!WARNING]
> Espera-se que o pipeline gerado seja usado somente por AzureDTExec. Suas propriedades ou parâmetros podem mudar no futuro, portanto, não modifique nem reutilize-os para outras finalidades. As modificações podem interromper o AzureDTExec. Se isso acontecer, exclua o pipeline. AzureDTExec gera um novo pipeline na próxima vez que for invocado.