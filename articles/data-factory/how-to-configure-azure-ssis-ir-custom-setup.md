---
title: Personalizar instalação para um Azure-SSIS Integration Runtime
description: Este artigo descreve como usar a interface de configuração personalizada para um Azure-SSIS Integration Runtime para instalar componentes adicionais ou alterar as configurações
ms.service: data-factory
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 11/06/2020
ms.openlocfilehash: b1b3a34ac495936ed92e29353a41efb8c462768f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387726"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Personalizar instalação para um Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Você pode personalizar o Integration Runtime do SSIS (Azure-SQL Server Integration Services) (IR) no Azure Data Factory (ADF) por meio de configurações personalizadas. Eles permitem que você adicione suas próprias etapas durante o provisionamento ou reconfiguração do seu Azure-SSIS IR. 

Usando as configurações personalizadas, você pode alterar a configuração operacional padrão ou o ambiente do seu Azure-SSIS IR. Por exemplo, para iniciar serviços adicionais do Windows, persista as credenciais de acesso para compartilhamentos de arquivos ou use criptografia forte/protocolo 1,2 TLS (mais seguro). Ou você pode instalar mais componentes, como assemblies, drivers ou extensões, em cada nó do seu Azure-SSIS IR. Eles podem ser componentes personalizados, de código aberto ou de terceiros. Para obter mais informações sobre os componentes internos/pré-instalados, consulte [Componentes internos/pré-instalados no Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md).

Você pode executar configurações personalizadas em seu Azure-SSIS IR de duas maneiras: 
* **configuração personalizada padrão com um script**: prepare um script e os arquivos associados e carregue todos eles juntos para um contêiner de blobs em sua conta de armazenamento do Azure. Forneça um URI (Uniform Resource Identifier) de SAS (Assinatura de Acesso Compartilhado) para seu contêiner quando você configurar ou reconfigurar o Azure-SSIS IR. Cada nó do Azure-SSIS IR baixa o script e os arquivos associados do seu contêiner e executa sua configuração personalizada com permissões elevadas. Quando a configuração personalizada for concluída, cada nó carregará a saída de execução padrão e outros logs para seu contêiner.
* **Expressar configuração personalizada sem um script**: execute algumas configurações comuns do sistema e comandos do Windows ou instale alguns componentes adicionais populares ou recomendados sem usar nenhum script.

Você pode instalar os componentes gratuitos (sem licença) e pagos (licenciado) com as instalações personalizadas padrão e expressas. Se você for um fornecedor independente de software (ISV), consulte [desenvolver componentes pagos ou licenciados para Azure-SSIS ir](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Para se beneficiar de aprimoramentos futuros, recomendamos usar a série v3 ou posterior de nós para seu Azure-SSIS IR com configuração personalizada.

## <a name="current-limitations"></a>Limitações atuais

As seguintes limitações se aplicam apenas a configurações personalizadas padrão:

- Se desejar usar *gacutil.exe* em seu script para instalar assemblies no GAC (cache de assembly global), você precisará fornecer *gacutil.exe* como parte de sua configuração personalizada. Ou você pode usar a cópia fornecida na pasta de *exemplo* do nosso contêiner de *Visualização pública* , consulte a seção **exemplos de instalação personalizada padrão** abaixo.

- Se desejar fazer referência a uma subpasta no script, *msiexec.exe* não dará suporte à notação `.\` para fazer referência à pasta raiz. Use um comando como `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` em vez de `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`.

- No momento, os compartilhamentos administrativos, ou compartilhamentos de rede ocultos criados automaticamente pelo Windows, não têm suporte em outro Azure-SSIS IR.

- Não há suporte para o driver ODBC iSeries Access da IBM no Azure-SSIS IR. Talvez você encontre erros de instalação durante a configuração personalizada. Se isso acontecer, entre em contato com o suporte da IBM para obter assistência.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para personalizar o Azure-SSIS IR, você precisa dos seguintes itens:

- [Uma assinatura do Azure](https://azure.microsoft.com/)

- [Provisionar o IR do Azure-SSIS](./tutorial-deploy-ssis-packages-azure.md)

- [Uma conta de armazenamento do Azure](https://azure.microsoft.com/services/storage/). Não é necessário para configurações personalizadas expressas. Para configurações personalizadas expressas, você carrega e armazena seu script de configuração personalizada e seus arquivos associados em um contêiner de blob. O processo de instalação personalizado também carrega seus logs de execução no mesmo contêiner de blob.

## <a name="instructions"></a>Instruções

Você pode provisionar ou reconfigurar seu Azure-SSIS IR com configurações personalizadas na interface do usuário do ADF. Se você quiser fazer o mesmo usando o PowerShell, baixe e instale [Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="standard-custom-setup"></a>Configuração personalizada padrão

Para provisionar ou reconfigurar seu Azure-SSIS IR com as configurações personalizadas padrão na interface do usuário do ADF, conclua as etapas a seguir.

1. Prepare o script de instalação personalizado e seus arquivos associados (por exemplo, arquivos .bat, .cmd, .exe, .dll, .msi ou .ps1).

   * Você deve ter um arquivo de script chamado *main.cmd*, que é o ponto de entrada de sua configuração personalizada.  
   * Para garantir que o script possa ser executado silenciosamente, você deve testá-lo no computador local primeiro.  
   * Se quiser que logs adicionais gerados por outras ferramentas (por exemplo, *msiexec.exe*) sejam carregados em seu contêiner, especifique a variável de ambiente predefinida, `CUSTOM_SETUP_SCRIPT_LOG_DIR`, como a pasta de log em seus scripts (por exemplo, *msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log*).

1. Baixe, instale e abra o [Gerenciador de Armazenamento do Azure](https://storageexplorer.com/).

   a. Em **(Local e Anexo)** , clique com o botão direito do mouse em **Contas de Armazenamento** e selecione **Conectar-se ao Armazenamento do Azure**.

      ![Conectar-se a um Armazenamento do Azure](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. Selecione **Usar um nome e uma chave da conta de armazenamento** e selecione **Próximo**.

      ![Usar um nome e uma chave da conta de armazenamento](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. Digite seu nome e a chave da conta de armazenamento do Azure, selecione **Próximo** e, em seguida, selecione **Conectar**.

      ![Fornecer o nome e a chave da conta de armazenamento](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. Em sua conta de armazenamento do Azure conectada, clique com o botão direito do mouse em **Contêineres de Blob**, selecione **Criar Contêiner de Blob** e dê um nome ao novo contêiner.

      ![Criar um contêiner de blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. Selecione o novo contêiner e carregue o script de configuração personalizada e os arquivos associados. Carregue o *main.cmd* no nível superior de seu contêiner, e não em qualquer pasta. O contêiner deve conter apenas os arquivos de instalação personalizados necessários, portanto, baixá-los para o Azure-SSIS IR mais tarde não levará muito tempo. A duração máxima de uma configuração personalizada está atualmente definida em 45 minutos antes de atingir o tempo limite. Isso inclui o tempo para baixar todos os arquivos do seu contêiner e instalá-los no Azure-SSIS IR. Se a instalação exigir mais tempo, gere um tíquete de suporte.

      ![Carregar arquivos no contêiner de blobs](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. Clique com o botão direito do mouse no contêiner e selecione **Obter Assinatura de Acesso Compartilhado**.

      ![Obter Assinatura de Acesso Compartilhado para o contêiner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. Crie o URI de SAS para seu contêiner com um período de expiração suficientemente longo e permissões de leitura/gravação/lista. Você precisa do URI de SAS para baixar e executar o script de instalação personalizada e seus arquivos associados. Isso acontece sempre que qualquer nó de sua Azure-SSIS IR for refeito ou reiniciado. Você também precisa de permissão de gravação para carregar os logs de execução da instalação.

      > [!IMPORTANT]
      > Verifique se o URI de SAS não vai expirar e se os recursos de configuração personalizada estarão sempre disponíveis durante o ciclo de vida inteiro do Azure-SSIS IR, da criação à exclusão, especialmente se você regularmente parar e iniciar o Azure-SSIS IR durante esse período.

      ![Gerar Assinatura de Acesso Compartilhado para o contêiner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Copie e salve o URI de SAS do seu contêiner.

      ![Copie e salve a Assinatura de Acesso Compartilhado](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Marque a caixa de seleção **personalizar seu Azure-SSIS Integration Runtime com configurações de sistema/instalações de componentes adicionais** na página **Configurações avançadas** do painel **configuração do tempo de execução de integração** . Em seguida, insira o URI de SAS do seu contêiner na caixa de texto **URI de SAS do contêiner de instalação personalizada** .

   ![Configurações avançadas com configurações personalizadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

Depois que a instalação personalizada padrão for concluída e o Azure-SSIS IR for iniciado, você poderá encontrar todos os logs de instalação personalizados na pasta *Main. cmd. log* do seu contêiner. Eles incluem a saída padrão de *Main. cmd* e outros logs de execução.

### <a name="express-custom-setup"></a>Instalação personalizada expressa

Para provisionar ou reconfigurar seu Azure-SSIS IR com as configurações personalizadas expressas na interface do usuário do ADF, conclua as etapas a seguir.

1. Marque a caixa de seleção **personalizar seu Azure-SSIS Integration Runtime com configurações de sistema/instalações de componentes adicionais** na página **Configurações avançadas** do painel **configuração do tempo de execução de integração** . 

1. Selecione **novo** para abrir o painel **Adicionar instalação personalizada expressa** e, em seguida, selecione um tipo na lista suspensa **tipo de instalação personalizada expressa** . Atualmente, oferecemos configurações expressas personalizadas para executar o comando cmdkey, adicionando variáveis de ambiente, instalando Azure PowerShell e instalando componentes licenciados.

#### <a name="running-cmdkey-command"></a>Executando o comando cmdkey

Se você selecionar o tipo de **comando ' executar cmdkey** ' para a instalação personalizada expressa, poderá executar o comando cmdkey do Windows em seu Azure-SSIS ir. Para fazer isso, insira o nome do computador ou nome de domínio definido como destino, nome de usuário ou de conta e chave de senha ou conta nas caixas de texto **/Add**, **/User** e **/Pass** , respectivamente. Isso permitirá que você persista as credenciais de acesso para servidores SQL, compartilhamentos de arquivos ou arquivos do Azure em seu Azure-SSIS IR. Por exemplo, para acessar os arquivos do Azure, você pode inserir `YourAzureStorageAccountName.file.core.windows.net` , `azure\YourAzureStorageAccountName` e `YourAzureStorageAccountKey` para **/Add**, **/User** e **/Pass**, respectivamente. Isso é semelhante a executar o comando [cmdkey](/windows-server/administration/windows-commands/cmdkey) do Windows em seu computador local. Apenas uma instalação personalizada expressa para executar o comando cmdkey tem suporte por enquanto. Para executar vários comandos do cmdkey, use uma configuração personalizada padrão em vez disso.

#### <a name="adding-environment-variables"></a>Adicionando variáveis de ambiente

Se você selecionar o tipo de **variável de ambiente adicionar** para a instalação personalizada expressa, poderá adicionar uma variável de ambiente do Windows em seu Azure-SSIS ir. Para fazer isso, insira o nome e o valor da variável de ambiente nas caixas de texto **nome da variável** e **valor da variável** , respectivamente. Isso permitirá que você use a variável de ambiente em seus pacotes que são executados em Azure-SSIS IR, por exemplo, em componentes/tarefas de script. Isso é semelhante a executar o comando [set](/windows-server/administration/windows-commands/set_1) do Windows em seu computador local.

#### <a name="installing-azure-powershell"></a>Instalando o Azure PowerShell

Se você selecionar o tipo de **Azure PowerShell instalar** para a instalação personalizada expressa, poderá instalar o módulo AZ do PowerShell no seu Azure-SSIS ir. Para fazer isso, insira o número de versão do módulo AZ (x. y. z) desejado em uma [lista de itens com suporte](https://www.powershellgallery.com/packages/az). Isso permitirá que você execute Azure PowerShell cmdlets/scripts em seus pacotes para gerenciar os recursos do Azure, por exemplo [Azure Analysis Services (AAS)](../analysis-services/analysis-services-powershell.md).

#### <a name="installing-licensed-components"></a>Instalando componentes licenciados

Se você selecionar o tipo de **componente de instalação licenciado** para a instalação personalizada expressa, poderá selecionar um componente integrado de nossos parceiros ISV na lista suspensa **nome do componente** :

   * Se você selecionar o componente de **fábrica de tarefas do SentryOne** , poderá instalar o pacote de [tarefas da fábrica](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) de componentes do SentryOne em seu Azure-SSIS ir. Para fazer isso, insira a chave de licença do produto que você comprou com antecedência na caixa de texto **chave de licença** . A versão integrada atual é **2020.1.3**.

   * Se você selecionar o **OH22'S HEDDA. Componente de e/s** , você pode instalar o [HEDDA.](https://github.com/oh22is/HEDDA.IO/tree/master/SSIS-IR) Componente de qualidade de dados de es/limpeza do oh22 no seu Azure-SSIS ir. Para fazer isso, você precisa comprar o serviço com antecedência. A versão integrada atual é **1.0.14**.

   * Se você selecionar o componente **oh22's SQLPhonetics.net** , poderá instalar o componente de correspondência/qualidade de dados do [SQLPhonetics.net](https://appsource.microsoft.com/product/web-apps/oh22.sqlphonetics-ssis) do oh22 no seu Azure-SSIS ir. Para fazer isso, insira a chave de licença do produto que você comprou com antecedência na caixa de texto **chave de licença** . A versão integrada atual é **1.0.45**.

   * Se você selecionar o componente **do kit de ferramentas de integração SSIS do KingswaySoft** , poderá instalar o conjunto de conectores do [SSIS Integration Toolkit](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) para aplicativos CRM/ERP/marketing/colaboração, como Microsoft Dynamics/SharePoint/Project Server, Oracle/Salesforce marketing Cloud, etc. do KingswaySoft no seu Azure-SSIS ir. Para fazer isso, insira a chave de licença do produto que você comprou com antecedência na caixa de texto **chave de licença** . A versão integrada atual é **2020,1**.

   * Se você selecionar o componente **do pacote de produtividade do SSIS do KingswaySoft** , poderá instalar o pacote de [produtividade do SSIS](https://www.kingswaysoft.com/products/ssis-productivity-pack) do pacote de componentes do KingswaySoft em seu Azure-SSIS ir. Para fazer isso, insira a chave de licença do produto que você comprou com antecedência na caixa de texto **chave de licença** . A versão integrada atual é **20,1**.

   * Se você selecionar o componente **trair do Theobald software** , poderá instalar o [trair é](https://theobald-software.com/en/xtract-is/) um conjunto de conectores para sistemas SAP (ERP, s/4HANA, BW) de Theobald software no seu Azure-SSIS ir. Para fazer isso, arraste & soltar/carregar o arquivo de licença do produto que você comprou a partir deles com antecedência na caixa de entrada do **arquivo de licença** . A versão integrada atual é **6.1.1.3**.

   * Se você selecionar o componente **do serviço de integração do AecorSoft** , poderá instalar o pacote do [serviço de integração](https://www.aecorsoft.com/en/products/integrationservice) de conectores para sistemas SAP e Salesforce do AecorSoft no seu Azure-SSIS ir. Para fazer isso, insira a chave de licença do produto que você comprou com antecedência na caixa de texto **chave de licença** . A versão integrada atual é **3.0.00**.

   * Se você selecionar o componente de **pacote padrão do SSIS do CDATA** , poderá instalar o pacote de [pacotes padrão do SSIS](https://www.cdata.com/kb/entries/ssis-adf-packages.rst#standard) de componentes mais populares do CDATA, como conectores do Microsoft SharePoint, no seu Azure-SSIS ir. Para fazer isso, insira a chave de licença do produto que você comprou com antecedência na caixa de texto **chave de licença** . A versão integrada atual é **19,7354**.

   * Se você selecionar o componente de **pacote estendido do SSIS do CDATA** , poderá instalar o pacote de [pacotes estendidos do SSIS](https://www.cdata.com/kb/entries/ssis-adf-packages.rst#extended) de todos os componentes do CDATA, como os conectores do Microsoft Dynamics 365 Business central e outros componentes em seu **pacote padrão do SSIS**, no seu Azure-SSIS ir. Para fazer isso, insira a chave de licença do produto que você comprou com antecedência na caixa de texto **chave de licença** . A versão integrada atual é **19,7354**. Devido a seu grande tamanho, para evitar o tempo limite da instalação, verifique se sua Azure-SSIS IR tem pelo menos 4 núcleos de CPU por nó.

Suas configurações personalizadas expressas adicionais serão exibidas na página de **Configuração avançada** . Para removê-los, marque suas caixas de seleção e, em seguida, selecione **Excluir**.

### <a name="azure-powershell"></a>Azure PowerShell

Para provisionar ou reconfigurar seu Azure-SSIS IR com as configurações personalizadas usando Azure PowerShell, conclua as etapas a seguir.

1. Se seu Azure-SSIS IR já estiver iniciado/em execução, interrompa-o primeiro.

1. Você pode adicionar ou remover configurações personalizadas executando o `Set-AzDataFactoryV2IntegrationRuntime` cmdlet antes de iniciar o Azure-SSIS ir.

   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script

   # Add custom setup parameters if you use standard/express custom setups
   if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
   {
       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -SetupScriptContainerSasUri $SetupScriptContainerSasUri
   }
   if(![string]::IsNullOrEmpty($ExpressCustomSetup))
   {
       if($ExpressCustomSetup -eq "RunCmdkey")
       {
           $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
           $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
           $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
       }
       if($ExpressCustomSetup -eq "SetEnvironmentVariable")
       {
           $variableName = "YourVariableName"
           $variableValue = "YourVariableValue"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
       }
       if($ExpressCustomSetup -eq "InstallAzurePowerShell")
       {
           $moduleVersion = "YourAzModuleVersion"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
       }
       if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
       {
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       if($ExpressCustomSetup -eq "Theobald.XtractIS")
       {
           $jsonData = Get-Content -Raw -Path YourLicenseFile.json
           $jsonData = $jsonData -replace '\s',''
           $jsonData = $jsonData.replace('"','\"')
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "CData.Standard")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "CData.Extended")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       # Create an array of one or more express custom setups
       $setups = New-Object System.Collections.ArrayList
       $setups.Add($setup)

       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -ExpressCustomSetup $setups
   }
   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
       -DataFactoryName $DataFactoryName `
       -Name $AzureSSISName `
       -Force
   ```

### <a name="standard-custom-setup-samples"></a>Exemplos de instalação personalizada padrão

Para exibir e reutilizar alguns exemplos de configurações personalizadas padrão, conclua as etapas a seguir.

1. Conecte-se ao nosso contêiner de visualização pública usando Gerenciador de Armazenamento do Azure.

   a. Em **(Local e conectado)** , clique com botão direito em **Contas de Armazenamento**, selecione **Conectar ao armazenamento do Azure**, selecione **Usar uma cadeia de caracteres de conexão ou uma URI de assinatura de acesso compartilhado** e, em seguida, selecione **Próximo**.

      ![Conecte-se ao armazenamento do Azure com a Assinatura de Acesso Compartilhado](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. Selecione **usar um URI de SAS** e, em seguida, na caixa de texto **URI** , insira o seguinte URI de SAS:

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![Fornecer a Assinatura de Acesso Compartilhado para o contêiner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Selecione **Próximo** e selecione **Conectar**.

   d. No painel esquerdo, selecione o contêiner **publicpreview** conectado e clique duas vezes na pasta *CustomSetupScript*. Nesta pasta, você encontrará os seguintes itens:

      * Uma pasta *Sample*, que contém uma configuração personalizada para instalar uma tarefa básica em cada nó do seu Azure-SSIS IR. A tarefa apenas executará a suspensão por alguns segundos. A pasta também contém uma pasta *gacutil*, cujo conteúdo inteiro (*gacutil.exe*, *gacutil.exe.config* e *1033\gacutlrc.dll*) pode ser copiado no estado em que se encontra para seu contêiner.

      * Uma pasta *UserScenarios*, que contém vários exemplos de configurações personalizadas de cenários de usuário real. Se você quiser instalar vários exemplos em seu Azure-SSIS IR, poderá combinar seus arquivos de script de instalação personalizada (*Main. cmd*) em um único e carregá-los com todos os seus arquivos associados em seu contêiner.

        ![Conteúdo do contêiner de visualização pública](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. Clique duas vezes na pasta *UserScenarios* para localizar os seguintes itens:

      * Uma pasta do *.net FRAMEWORK 3,5* , que contém um script de instalação personalizada (*Main. cmd*) para instalar uma versão anterior do .NET Framework em cada nó de sua Azure-SSIS ir. Essa versão pode ser exigida por alguns componentes personalizados.

      * Uma pasta *bcp* , que contém um script de instalação personalizada (*Main. cmd*) para instalar SQL Server utilitários de linha de comando (*MsSqlCmdLnUtils.msi*) em cada nó do seu Azure-SSIS ir. Um desses utilitários é o *bcp*(programa de cópia em massa).

      * Uma pasta de *sufixo DNS* , que contém um script de instalação personalizada (*Main. cmd*) para acrescentar seu próprio sufixo DNS (por exemplo, *Test.com*) a qualquer nome de domínio de rótulo único não qualificado e transformá-lo em um FQDN (nome de domínio totalmente qualificado) antes de usá-lo em consultas DNS de seu Azure-SSIS ir.

      * Uma pasta do *Excel* , que contém um script de instalação personalizada (*Main. cmd*) para instalar alguns assemblies e bibliotecas C# em cada nó do seu Azure-SSIS ir. Você pode usá-los em tarefas de script para ler e gravar arquivos do Excel de forma dinâmica. 
      
        Primeiro, baixe [*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) e [*DocumentFormat.OpenXml.dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/) e carregue-os juntos com *main.cmd* para seu contêiner. Como alternativa, se você quiser apenas usar os conectores padrão do Excel (Gerenciador de conexões, origem e destino), o acesso redistribuível que os contém já está pré-instalado no seu Azure-SSIS IR, portanto, você não precisa de nenhuma instalação personalizada.
      
      * Uma pasta *MYSQL ODBC*, que contém um script de configuração personalizada (*main.cmd*) para instalar os drivers ODBC do MySQL em cada nó do seu Azure-SSIS IR. Essa configuração permite que você use os conectores ODBC (Gerenciador de conexões, origem e destino) para se conectar ao servidor MySQL. 
     
        Primeiro, [baixe as versões de 64 bits e 32 bits mais recentes dos instaladores do driver ODBC do MySQL](https://dev.mysql.com/downloads/connector/odbc/) (por exemplo, *mysql-connector-odbc-8.0.13-winx64.msi* e *mysql-connector-odbc-8.0.13-win32.msi*) e carregue todas elas juntas com *main.cmd* para o seu contêiner.

      * Uma pasta *ORACLE ENTERPRISE*, que contém um script de configuração personalizada (*main.cmd*) e o arquivo de configuração de instalação silenciosa (*client.rsp*) para instalar os conectores Oracle e o driver OCI em cada nó do seu Azure-SSIS IR Edição Enterprise. Essa configuração permite que você use o Gerenciador de conexões Oracle, a origem e o destino para se conectar ao servidor Oracle. 
      
        Primeiro, baixe o Microsoft Connectors v 5.0 para Oracle (*AttunitySSISOraAdaptersSetup.msi* e *AttunitySSISOraAdaptersSetup64.msi*) do centro de [download da Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=55179) e o cliente Oracle mais recente (por exemplo, *winx64_12102_client.zip*) da [Oracle](https://www.oracle.com/database/technologies/oracle19c-windows-downloads.html). Em seguida, carregue-os juntos com *Main. cmd* e *Client. rsp* em seu contêiner. Se você usar TNS para se conectar ao Oracle, também precisará baixar *arquivo tnsnames. Ora*, editá-lo e carregá-lo em seu contêiner. Dessa forma, ele pode ser copiado para a pasta de instalação do Oracle durante a instalação.

      * Uma pasta *ORACLE STANDARD ADO.NET*, que contém um script de configuração personalizada (*main.cmd*) para instalar o driver do Oracle ODP.NET em cada nó do seu Azure-SSIS IR. Essa configuração permite que você use o Gerenciador de conexões do ADO.NET, a origem e o destino para se conectar ao servidor Oracle. 
      
        Primeiro, [baixe o driver do Oracle ODP.NET](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (por exemplo, *ODP.NET_Managed_ODAC122cR1.zip*) e carregue-o juntamente com *main.cmd* para o seu contêiner.
       
      * Uma pasta *ODBC padrão do Oracle* , que contém um script de instalação personalizada (*Main. cmd*) para instalar o driver ODBC Oracle em cada nó do seu Azure-SSIS ir. O script também configura o DSN (nome da fonte de dados). Essa configuração permite que você use o Gerenciador de conexões ODBC, a origem e o destino ou o Gerenciador de conexões Power Query e a origem com o tipo de fonte de dados ODBC para se conectar ao servidor Oracle. 
      
        Primeiro, baixe o Cliente Instantâneo Oracle (Pacote Basic ou Basic Lite) e o Pacote ODBC e carregue todos eles juntamente com o *main.cmd* para o seu contêiner:
        * [Baixe os pacotes de 64 bits](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (Pacote Basic: *instantclient-basic-windows.x64-18.3.0.0.0dbru.zip*; Pacote Basic Lite: *instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip*; Pacote ODBC: *instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip*) 
        * [Baixe os pacotes de 32 bits](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (Pacote Basic: *instantclient-basic-nt-18.3.0.0.0dbru.zip*; Pacote Basic Lite: *instantclient-basiclite-nt-18.3.0.0.0dbru.zip*; Pacote ODBC: *instantclient-odbc-nt-18.3.0.0.0dbru.zip*)

      * Uma pasta *ORACLE STANDARD OLEDB*, que contém um script de configuração personalizada (*main.cmd*) para instalar o driver do Oracle OLEDB em cada nó do seu Azure-SSIS IR. Essa configuração permite que você use o Gerenciador de conexões OLEDB, a origem e o destino para se conectar ao servidor Oracle. 
     
        Primeiro, [baixe o driver do Oracle OLEDB](https://www.oracle.com/partners/campaign/index-090165.html) (por exemplo, *ODAC122010Xcopy_x64.zip*) e carregue-o juntamente com *main.cmd* para o seu contêiner.

      * Uma pasta *POSTGRESQL ODB*, que contém um script de configuração personalizada (*main.cmd*) para instalar os drivers ODBC do PostgreSQL em cada nó do seu Azure-SSIS IR. Essa configuração permite que você use o Gerenciador de conexões ODBC, a origem e o destino para se conectar ao servidor PostgreSQL. 
     
        Primeiro, [baixe as versões de 64 bits e 32 bits mais recentes dos instaladores do driver ODBC do PostgreSQL](https://www.postgresql.org/ftp/odbc/versions/msi/) (por exemplo, *psqlodbc_x64.msi* e *psqlodbc_x86.msi*) e carregue todas elas juntas com *main.cmd* para o seu contêiner.

      * Uma pasta *SAP BW*, que contém um script de configuração personalizada (*main.cmd*) para instalar o assembly de conector .NET do SAP (*librfc32.dll*) em cada nó do seu Azure-SSIS IR Edição Enterprise. Essa configuração permite que você use o Gerenciador de conexões SAP BW, a origem e o destino para se conectar ao servidor de SAP BW. 
      
        Primeiro, carregue a versão 64 bits ou 32 bits de *librfc32.dll* da pasta de instalação do SAP juntamente com *main.cmd* para seu contêiner. O script copia o assembly do SAP para a pasta *%windir%\SysWow64* ou *%windir%\System32* durante a instalação.

      * Uma pasta *STORAGE*, que contém um script de configuração personalizada (*main.cmd*) para instalar o Azure PowerShell em cada nó do Azure-SSIS IR. Essa configuração permite implantar e executar pacotes do SSIS que executam [Azure PowerShell cmdlets/scripts para gerenciar o armazenamento do Azure](../storage/blobs/storage-quickstart-blobs-powershell.md). 
      
        Copie *main.cmd*, um *AzurePowerShell.msi* de exemplo (ou use a versão mais recente) e *storage.ps1* para seu contêiner. Use *PowerShell.dtsx* como um modelo para os seus pacotes. O modelo de pacote combina uma [tarefa de download de blob do Azure](/sql/integration-services/control-flow/azure-blob-download-task), que baixa um script do PowerShell modificável (*storage.ps1*) e uma [tarefa Executar processo](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/), que executa o script em cada nó.

      * Uma pasta *TERADATA*, que contém um script de configuração personalizada (*main.cmd*), o arquivo associado (*install.cmd*) e os pacotes do instalador ( *.msi*). Esses arquivos instalam os conectores Teradata, a API TPT (Teradata Parallel Transporter) e o driver ODBC em cada nó do Azure-SSIS IR Edição Enterprise. Essa configuração permite que você use o Gerenciador de conexões Teradata, a origem e o destino para se conectar ao servidor Teradata. 
      
        Primeiro, [baixe o arquivo zip Ferramentas e Utilitários do Teradata 15.x](http://partnerintelligence.teradata.com) (por exemplo, *TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip*) e carregue-o junto com os arquivos *.cmd* e *.msi* mencionados anteriormente para seu contêiner.

      * Uma pasta *TLS 1,2* , que contém um script de instalação personalizado (*Main. cmd*) para usar criptografia forte e protocolo TLS 1,2 (mais seguro) em cada nó do seu Azure-SSIS ir. O script também desabilita as versões mais antigas de SSL/TLS.

      * Uma pasta *ZULU OPENJDK*, que contém um script de configuração personalizada (*main.cmd*) e um arquivo do PowerShell (*install_openjdk.ps1*) para instalar o Zulu OpenJDK em cada nó do seu Azure-SSIS IR. Essa instalação permite que você use o Azure Data Lake Storage e conectores de Arquivo Flexíveis para processar arquivos ORC e Parquet. Para obter mais informações, confira [Feature Pack do Azure para o Integration Services](/sql/integration-services/azure-feature-pack-for-integration-services-ssis#dependency-on-java). 
      
        Primeiro, [baixe o Zulu OpenJDK mais recente](https://www.azul.com/downloads/zulu/zulu-windows/) (por exemplo, *zulu8.33.0.1-jdk8.0.192-win_x64.zip*) e carregue-o junto com *main.cmd* e *install_openjdk.ps1* para o seu contêiner.

        ![Pastas na pasta de cenários de usuário](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. Para reutilizar esses exemplos de instalação personalizada padrão, copie o conteúdo da pasta selecionada para o contêiner.

1. Ao provisionar ou reconfigurar seu Azure-SSIS IR na interface do usuário do ADF, marque a caixa de seleção **personalizar seu Azure-SSIS Integration Runtime com configurações de sistema/instalações de componentes adicionais** na página **Configurações avançadas** do painel de **instalação do Integration Runtime** . Em seguida, insira o URI de SAS do seu contêiner na caixa de texto **URI de SAS do contêiner de instalação personalizada** .
   
1. Ao provisionar ou reconfigurar o Azure-SSIS IR usando o Azure PowerShell, interrompa-o se ele já estiver iniciado/em execução, execute o `Set-AzDataFactoryV2IntegrationRuntime` cmdlet com o URI de SAS do seu contêiner como o valor para o `SetupScriptContainerSasUri` parâmetro e, em seguida, inicie o Azure-SSIS ir.

1. Depois que a instalação personalizada padrão for concluída e o Azure-SSIS IR for iniciado, você poderá encontrar todos os logs de instalação personalizados na pasta *Main. cmd. log* do seu contêiner. Eles incluem a saída padrão de *Main. cmd* e outros logs de execução.

## <a name="next-steps"></a>Próximas etapas

- [Configurar a Enterprise Edition do Azure-SSIS IR](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Desenvolva componentes pagos ou licenciados para Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md)
