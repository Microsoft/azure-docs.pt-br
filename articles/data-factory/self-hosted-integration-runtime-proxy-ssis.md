---
title: Configurar um tempo de execução de integração auto-hospedado como um proxy para SSIS
description: Saiba como configurar um tempo de execução de integração auto-hospedado como um proxy para um Azure-SSIS Integration Runtime.
ms.service: data-factory
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 11/19/2020
ms.openlocfilehash: 5f393e95a7d468ea2c6130077e42b25b038e839d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376217"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>Configurar um IR auto-hospedado como um proxy para um Azure-SSIS IR no Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve como executar pacotes do SQL Server Integration Services (SSIS) em um Azure-SSIS Integration Runtime (Azure-SSIS IR) no Azure Data Factory com um tempo de execução de integração auto-hospedado (IR auto-hospedado) configurado como um proxy. 

Com esse recurso, você pode acessar dados no local sem precisar [ingressar seu Azure-SSIS ir em uma rede virtual](./join-azure-ssis-integration-runtime-virtual-network.md). O recurso é útil quando sua rede corporativa tem uma configuração muito complexa ou uma política muito restritiva para você injetar seu Azure-SSIS IR.

Esse recurso divide sua tarefa de fluxo de dados do SSIS em duas tarefas de preparo sempre que aplicável: 
* **Tarefa de preparo local**: essa tarefa executa o componente de fluxo de dados que se conecta a um armazenamento de dados local em seu ir de hospedagem interna. Ele move dados do armazenamento de dados local para uma área de preparo no armazenamento de BLOBs do Azure ou vice-versa.
* **Tarefa de preparo na nuvem**: essa tarefa executa o componente de fluxo de dados que não se conecta a um armazenamento de dados local em seu Azure-SSIS ir. Ele move os dados da área de preparo no armazenamento de BLOBs do Azure para um armazenamento de dados de nuvem ou vice-versa.

Se a tarefa de fluxo de dados mover dados do local para a nuvem, a primeira e a segunda tarefas de preparo serão locais e tarefas de preparo na nuvem, respectivamente. Se a tarefa de fluxo de dados mover dados da nuvem para o local, a primeira e a segunda tarefas de preparo serão tarefas de preparo locais e na nuvem, respectivamente. Se a tarefa de fluxo de dados mover dados do local para o local, a primeira e a segunda tarefas de preparo serão tarefas de preparo locais. Se a tarefa de fluxo de dados mover dados da nuvem para a nuvem, esse recurso não será aplicável.

Outros benefícios e funcionalidades desse recurso permitem, por exemplo, configurar o IR auto-hospedado em regiões que ainda não têm suporte de um Azure-SSIS IR e permitir o endereço IP estático público do seu IR hospedado no firewall de suas fontes de dados.

## <a name="prepare-the-self-hosted-ir"></a>Preparar o IR auto-hospedado

Para usar esse recurso, primeiro você cria um data factory e configura um Azure-SSIS IR nele. Se você ainda não tiver feito isso, siga as instruções em [configurar um Azure-SSIS ir](./tutorial-deploy-ssis-packages-azure.md).

Em seguida, você configura o IR auto-hospedado na mesma data factory em que o Azure-SSIS IR está configurado. Para fazer isso, consulte [criar um ir auto-hospedado](./create-self-hosted-integration-runtime.md).

Por fim, você baixa e instala a versão mais recente do IR auto-hospedado, bem como os drivers adicionais e o tempo de execução, em seu computador local ou VM (máquina virtual) do Azure, da seguinte maneira:
- Baixe e instale a versão mais recente do [ir auto-hospedado](https://www.microsoft.com/download/details.aspx?id=39717).
- Se você usar a vinculação de objetos e o banco de dados de incorporação (OLEDB), conectividade aberta de banco de dados (ODBC) ou conectores ADO.NET em seus pacotes, baixe e instale os drivers relevantes no mesmo computador em que o IR hospedado automaticamente está instalado, se ainda não tiver feito isso.  

  Se você usar a versão anterior do driver OLEDB para SQL Server (SQL Server Native Client [SQLNCLI]), [Baixe a versão de 64 bits](https://www.microsoft.com/download/details.aspx?id=50402).  

  Se você usar a versão mais recente do driver OLEDB para SQL Server (MSOLEDBSQL), [Baixe a versão de 64 bits](https://www.microsoft.com/download/details.aspx?id=56730).  
  
  Se você usar os drivers OLEDB/ODBC/ADO. NET para outros sistemas de banco de dados, como PostgreSQL, MySQL, Oracle e assim por diante, poderá baixar as versões de 64 bits de seus sites.
- Se você ainda não fez isso, [Baixe e instale a versão de 64 bits do tempo de execução do Visual C++ (VC)](https://www.microsoft.com/download/details.aspx?id=40784) no mesmo computador em que o ir hospedado automaticamente está instalado.

### <a name="enable-windows-authentication-for-on-premises-staging-tasks"></a>Habilitar a autenticação do Windows para tarefas de preparo locais

Se as tarefas de preparo local no seu IR de hospedagem interna exigirem autenticação do Windows, [Configure seus pacotes SSIS para usar a mesma autenticação do Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). 

Suas tarefas locais de preparo serão invocadas com a conta de serviço IR auto-hospedado (*NT SERVICE\DIAHostService*, por padrão) e seus armazenamentos de dados serão acessados com a conta de autenticação do Windows. Ambas as contas exigem que determinadas políticas de segurança sejam atribuídas a elas. Na máquina ir auto-hospedado, vá para **política de segurança local**  >  **políticas locais**  >  **atribuição de direitos de usuário** e, em seguida, faça o seguinte:

1. Atribua as *cotas de ajuste de memória para um processo* e substitua as políticas de *token de nível de processo* para a conta de serviço ir de hospedagem interna. Isso deve ocorrer automaticamente quando você instala o IR auto-hospedado com a conta de serviço padrão. Caso contrário, atribua essas políticas manualmente. Se você usar uma conta de serviço diferente, atribua as mesmas políticas a ela.

1. Atribua o *logon como uma* política de serviço à conta de autenticação do Windows.

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>Preparar o serviço vinculado do armazenamento de BLOBs do Azure para preparo

Se você ainda não tiver feito isso, crie um serviço vinculado do armazenamento de BLOBs do Azure na mesma data factory em que o Azure-SSIS IR está configurado. Para fazer isso, consulte [criar um serviço vinculado ao Azure data Factory](./quickstart-create-data-factory-portal.md#create-a-linked-service). Certifique-se de fazer o seguinte:
- Para **armazenamento de dados**, selecione **armazenamento de BLOBs do Azure**.  
- Para **conectar por meio do Integration Runtime**, selecione **AutoResolveIntegrationRuntime** (não o Azure-SSIS ir nem o ir auto-hospedado), pois usamos o Azure ir padrão para buscar credenciais de acesso para o armazenamento de BLOBs do Azure.
- Para **o método de autenticação**, selecione chave de **conta**, **URI de SAS**, entidade de **serviço** ou **identidade gerenciada**.  

>[!TIP]
>Se você selecionar o método de **entidade de serviço** , conceda à sua entidade de serviço pelo menos uma função de colaborador de dados de *blob de armazenamento* . Para obter mais informações, consulte [conector do armazenamento de BLOBs do Azure](connector-azure-blob-storage.md#linked-service-properties). Se você selecionar o método de **identidade gerenciada** , conceda ao ADF as funções adequadas de identidade gerenciada para acessar o armazenamento de BLOBs do Azure. Para obter mais informações, consulte [acessar o armazenamento de BLOBs do Azure usando a autenticação do Azure Active Directory com a identidade gerenciada do ADF](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication).

![Preparar o serviço vinculado do armazenamento de BLOBs do Azure para preparo](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>Configurar um Azure-SSIS IR com o IR auto-hospedado como um proxy

Tendo preparado seu serviço vinculado do IR e do armazenamento de BLOBs do Azure auto-hospedado para preparo, agora você pode configurar o Azure-SSIS IR novo ou existente com o IR hospedado internamente como um proxy no seu portal data factory ou aplicativo. Antes de fazer isso, no entanto, se o Azure-SSIS IR existente já estiver em execução, interrompa-o e reinicie-o.

1. No painel **instalação do Integration Runtime** , pule as seções **configurações gerais** e **configurações do SQL** selecionando **Avançar**. 

1. Na seção **Configurações avançadas** , faça o seguinte:

   1. Marque a caixa de seleção **configurar Self-Hosted Integration Runtime como um proxy para seu Azure-SSIS Integration Runtime** . 

   1. Na lista suspensa **Integration Runtime auto-hospedado** , selecione o ir auto-hospedado existente como um proxy para o Azure-SSIS ir.

   1. Na lista suspensa **serviço vinculado de armazenamento de preparo** , selecione o serviço vinculado do armazenamento de BLOBs do Azure existente ou crie um novo para preparo.

   1. Na caixa **caminho de preparo** , especifique um contêiner de BLOB em sua conta de armazenamento de BLOBs do Azure selecionada ou deixe em branco para usar um padrão para preparo.

   1. Selecione **Continuar**.

   ![Configurações avançadas com um IR auto-hospedado](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

Você também pode configurar o Azure-SSIS IR novo ou existente com o IR auto-hospedado como um proxy usando o PowerShell.

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>Habilitar pacotes SSIS para se conectar por proxy

Usando o SSDT mais recente como a extensão de projetos do SSIS para Visual Studio ou um instalador autônomo, você pode encontrar uma nova `ConnectByProxy` propriedade que foi adicionada nos gerenciadores de conexões para componentes de fluxo de dados com suporte.
* [Baixar a extensão de projetos do SSIS para o Visual Studio](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [Baixar o instalador autônomo](/sql/ssdt/download-sql-server-data-tools-ssdt#ssdt-for-vs-2017-standalone-installer)   

Quando você cria novos pacotes que contêm tarefas de fluxo de dados com componentes que acessam dados no local, você pode habilitar essa propriedade definindo-a como *true* no painel de **Propriedades** dos gerenciadores de conexões relevantes.

![Habilitar Propriedade ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Você também pode habilitar essa propriedade ao executar pacotes existentes, sem precisar alterá-los manualmente um por um.  Há duas opções:
- **Opção A**: Abra, recompile e reimplante o projeto que contém os pacotes com os SSDT mais recentes para executar em seu Azure-SSIS ir. Em seguida, você pode habilitar a propriedade definindo-a como *true* para os gerenciadores de conexões relevantes. Quando você estiver executando pacotes do SSMS, esses gerenciadores de conexões aparecerão na guia **gerenciadores de conexões** da janela pop-up **executar pacote** .

  ![Habilitar ConnectByProxy Property2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  Você também pode habilitar a propriedade definindo-a como *true* para os gerenciadores de conexões relevantes que aparecem na guia **gerenciadores de conexões** da [atividade executar pacote SSIS](./how-to-invoke-ssis-package-ssis-activity.md) quando você estiver executando pacotes em pipelines de data Factory.
  
  ![Habilitar ConnectByProxy property3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **Opção B:** Reimplante o projeto que contém esses pacotes para executar em seu IR do SSIS. Em seguida, você pode habilitar a propriedade fornecendo seu caminho de propriedade, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]` e definindo-a como *true* como uma substituição de propriedade na guia **avançado** da janela pop-up **executar pacote** quando estiver executando pacotes do SSMS.

  ![Habilitar ConnectByProxy property4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  Você também pode habilitar a propriedade fornecendo seu caminho de propriedade, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]` e definindo-a como *true* como uma substituição de propriedade na guia **substituições de propriedade** da [atividade executar pacote SSIS](./how-to-invoke-ssis-package-ssis-activity.md) quando estiver executando pacotes em pipelines de data Factory.
  
  ![Habilitar ConnectByProxy property5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-on-premises-and-cloud-staging-tasks"></a>Depurar as tarefas locais e de preparo na nuvem

Em seu IR auto-hospedado, você pode encontrar os logs de tempo de execução na pasta *C:\ProgramData\SSISTelemetry* e os logs de execução de tarefas de preparo locais na pasta *C:\ProgramData\SSISTelemetry\ExecutionLog* .  Você pode encontrar os logs de execução das tarefas de preparo de nuvem em seus arquivos de log do SSISDB, especificados ou Azure Monitor dependendo se você armazena seus pacotes no SSISDB, habilita a [integração de Azure monitor](./monitor-using-azure-monitor.md#monitor-ssis-operations-with-azure-monitor), etc. Você também pode encontrar as IDs exclusivas das tarefas de preparo locais nos logs de execução das tarefas de preparo de nuvem. 

![ID exclusiva da primeira tarefa de preparo](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

Se você tiver gerado tíquetes de atendimento ao cliente, poderá selecionar o botão **enviar logs** na guia **diagnóstico** de **Microsoft Integration Runtime Configuration Manager** que está instalado em seu ir hospedado para enviar logs de operação/execução recentes para investigar.

## <a name="billing-for-the-on-premises-and-cloud-staging-tasks"></a>Cobrança para as tarefas locais e de preparo na nuvem

As tarefas de preparo locais que são executadas em seu IR auto-hospedado são cobradas separadamente, assim como as atividades de movimentação de dados executadas em um IR de hospedagem interna são cobradas. Isso é especificado no artigo de [preços Azure data Factory pipeline de dados](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) .

As tarefas de preparo de nuvem que são executadas no Azure-SSIS IR não são cobradas separadamente, mas o Azure-SSIS IR em execução é cobrado conforme especificado no artigo de [preços de Azure-SSIS ir](https://azure.microsoft.com/pricing/details/data-factory/ssis/) .

## <a name="enable-custom3rd-party-components"></a>Habilitar componentes personalizados/de terceiros 

Para habilitar seus componentes personalizados/de terceiros para acessar dados locais usando o IR auto-hospedado como um proxy para Azure-SSIS IR, siga estas instruções:

1. Instale os componentes personalizados/de terceiros direcionados SQL Server 2017 em Azure-SSIS IR por meio de [instalações personalizadas padrão/Express](./how-to-configure-azure-ssis-ir-custom-setup.md).

1. Crie as seguintes chaves do registro DTSPath no IR via hospedagem interna, se elas ainda não existirem:
   1. `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\140\SSIS\Setup\DTSPath` definido como `C:\Program Files\Microsoft SQL Server\140\DTS\`
   1. `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\Microsoft SQL Server\140\SSIS\Setup\DTSPath` definido como `C:\Program Files (x86)\Microsoft SQL Server\140\DTS\`
   
1. Instale os componentes personalizados/de terceiros direcionados SQL Server 2017 no IR hospedado internamente sob o DTSPath acima e verifique se o processo de instalação:

   1. Cria `<DTSPath>` pastas,, e, `<DTSPath>/Connections` `<DTSPath>/PipelineComponents` `<DTSPath>/UpgradeMappings` se elas ainda não existirem.
   
   1. Cria seu próprio arquivo XML para mapeamentos de extensão na `<DTSPath>/UpgradeMappings` pasta.
   
   1. Instala todos os assemblies referenciados por seus assemblies de componente personalizado/de terceiros no GAC (cache de assembly global).

Aqui estão exemplos de nossos parceiros, [Theobald software](https://kb.theobald-software.com/xtract-is/XIS-for-Azure-SHIR) e [Aecorsoft](https://www.aecorsoft.com/blog/2020/11/8/using-azure-data-factory-to-bring-sap-data-to-azure-via-self-hosted-ir-and-ssis-ir), que adaptaram seus componentes para usar nossa configuração personalizada expressa e o ir auto-hospedado como um proxy para Azure-SSIS ir.

## <a name="enforce-tls-12"></a>Impor TLS 1.2

Se você precisar usar criptografia forte/protocolo de rede seguro (TLS 1,2) e desabilitar versões de SSL/TLS mais antigas em seu IR de hospedagem interna, poderá baixar e executar o script *Main. cmd* que pode ser encontrado na pasta *CustomSetupScript/userscenarios/TLS 1,2* de nosso contêiner de visualização pública.  Usando [Gerenciador de armazenamento do Azure](https://storageexplorer.com/), você pode se conectar ao nosso contêiner de visualização pública digitando o seguinte URI de SAS:

`https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

## <a name="current-limitations"></a>Limitações atuais

- Somente os componentes de fluxo de dados internos/pré-instalados no Azure-SSIS IR Standard Edition, exceto os componentes Hadoop/HDFS/DQS, têm suporte no momento, confira [todos os componentes internos/pré-instalados em Azure-SSIS ir](./built-in-preinstalled-components-ssis-integration-runtime.md).
- Somente componentes de fluxo de dados personalizados/de terceiros gravados em código gerenciado (.NET Framework) atualmente são suportados – aqueles escritos no código nativo (C++) atualmente não têm suporte.
- No momento, não há suporte para a alteração de valores de variáveis em tarefas locais e de preparo na nuvem.
- A alteração de valores de variáveis do tipo Object em tarefas de preparo locais não será refletida em outras tarefas.
- No momento, não há suporte para *ParameterMapping* na fonte OLEDB. Como alternativa, use o *comando SQL da variável* como *AccessMode* e use *expression* para inserir variáveis/parâmetros em um comando SQL. Como ilustração, consulte o pacote *ParameterMappingSample. dtsx* que pode ser encontrado na pasta *SelfHostedIRProxy/limitações* do nosso contêiner de visualização pública. Usando Gerenciador de Armazenamento do Azure, você pode se conectar ao nosso contêiner de visualização pública inserindo o URI de SAS acima.

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o IR auto-hospedado como um proxy para seu Azure-SSIS IR, você pode implantar e executar seus pacotes para acessar dados no local como executar atividades de pacote SSIS em pipelines de Data Factory. Para saber como, consulte [executar pacotes SSIS como atividades de execução de pacote SSIS em pipelines de data Factory](./how-to-invoke-ssis-package-ssis-activity.md).