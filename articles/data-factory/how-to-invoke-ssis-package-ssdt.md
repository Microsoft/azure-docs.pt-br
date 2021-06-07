---
title: Executar pacotes do SSIS do SSDT
description: Saiba como executar pacotes do SSIS no Azure do SSDT.
ms.service: data-factory
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.custom: seo-lt-2019
ms.date: 09/06/2020
ms.openlocfilehash: f11a3845e8644f3f60425538b2ef32cff668d88d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100384921"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>Executar pacotes do SSIS no Azure do SSDT

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve o recurso de projetos SQL Server Integration Services (SSIS) habilitados para o Azure em SQL Server Data Tools (SSDT). Ele permite que você avalie a compatibilidade de nuvem dos seus pacotes do SSIS e execute-os em Azure-SSIS Integration Runtime (IR) no Azure Data Factory (ADF). Você pode usar esse recurso para testar seus pacotes existentes antes de levantar & Shift/migrá-los para o Azure ou para desenvolver novos pacotes para serem executados no Azure.

Com esse recurso, você pode anexar um Azure-SSIS IR recém-criado/existente aos projetos do SSIS e, em seguida, executar seus pacotes nele.  Damos suporte à execução de pacotes a serem implantados no catálogo do SSIS (SSISDB) hospedado pelo seu servidor de banco de dados SQL do Azure ou instância gerenciada no modelo de implantação de projeto. Também há suporte para pacotes em execução a serem implantados no sistema de arquivos/arquivos do Azure/banco de dados SQL Server (MSDB) hospedados pela instância gerenciada do SQL do Azure no modelo de implantação de pacote. 

## <a name="prerequisites"></a>Pré-requisitos

Para usar esse recurso, baixe e instale o SSDT mais recente com a extensão de projetos do SSIS para Visual Studio (VS) [aqui](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects). Como alternativa, você também pode baixar e instalar o SSDT mais recente como um instalador autônomo [aqui](/sql/ssdt/download-sql-server-data-tools-ssdt#ssdt-for-vs-2017-standalone-installer).

## <a name="azure-enable-ssis-projects"></a>Azure – habilitar projetos do SSIS

### <a name="creating-new-azure-enabled-ssis-projects"></a>Criando novos projetos do SSIS habilitados para Azure

No SSDT, você pode criar novos projetos do SSIS habilitados para o Azure usando o modelo de **projeto Integration Services (habilitado para o Azure)** .

   ![Novo projeto SSIS habilitado para Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

Depois que o projeto habilitado para Azure for criado, você será solicitado a se conectar ao SSIS no Azure Data Factory.

   ![Conectar Azure-SSIS IR prompt](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-integration-runtime-connect-prompt.png)

Se você quiser se conectar à sua Azure-SSIS IR imediatamente, consulte [conectando-se ao Azure-SSIS ir](#connectssisir) para obter mais detalhes. Você também pode se conectar mais tarde clicando com o botão direito do mouse no nó do projeto na janela Gerenciador de Soluções do SSDT para exibir um menu. Em seguida, selecione o item **conectar ao SSIS no Azure data Factory** no **SSIS no** submenu Azure data Factory.

### <a name="azure-enabling-existing-ssis-projects"></a><a name="azureenableproject"></a> Azure-habilitando projetos SSIS existentes

Para projetos SSIS existentes, você pode habilitá-los no Azure seguindo estas etapas:

1. Clique com o botão direito do mouse no nó do projeto na janela Gerenciador de Soluções do SSDT para exibir um menu. Em seguida, selecione o item de **projeto habilitado para Azure** no **SSIS no** submenu Azure data Factory para iniciar o **Assistente de projeto habilitado para Azure**.

   ![Azure-habilitar projeto SSIS existente](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-for-existing-project.png)

2. Na página **selecionar configuração do Visual Studio** , selecione a configuração do vs existente para aplicar as configurações de execução do pacote no Azure. Você também pode criar um novo se ainda não tiver feito isso, consulte [criando uma nova configuração do vs](/visualstudio/ide/how-to-create-and-edit-configurations). É recomendável que você tenha pelo menos duas configurações do VS diferentes para execuções de pacote nos ambientes locais e na nuvem, para que você possa habilitar o Azure para o seu projeto em relação à configuração de nuvem. Dessa forma, se você tiver parametrizado seu projeto ou pacotes, poderá atribuir valores diferentes ao projeto ou aos parâmetros do pacote em tempo de execução com base nos diferentes ambientes de execução (em seu computador local ou no Azure). Por exemplo, consulte [alternando ambientes de execução de pacote](#switchenvironment).

   ![Selecione a configuração do Visual Studio](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-select-visual-studio-configurations.png)

3. A habilitação do Azure para seus projetos SSIS existentes exige que você defina a versão do servidor de destino como a mais recente com suporte pelo Azure-SSIS IR. No momento, o Azure-SSIS IR é baseado em **SQL Server 2017**. Verifique se seus pacotes não contêm componentes adicionais que não têm suporte no SQL Server 2017. Verifique também se todos os componentes adicionais compatíveis também foram instalados no seu Azure-SSIS IR por meio de configurações personalizadas, consulte [personalizando sua Azure-SSIS ir](./how-to-configure-azure-ssis-ir-custom-setup.md). Selecione o botão **Avançar** para continuar.

   ![Alternar versão do servidor de destino](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-switch-target-server-version-step.png)

4. Consulte [conectando-se ao Azure-SSIS ir](#connectssisir) para concluir a conexão do seu projeto com o Azure-SSIS ir.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a><a name="connectssisir"></a> Conectar projetos habilitados para o Azure ao SSIS no Azure Data Factory

Conectando seus projetos habilitados para o Azure ao SSIS no ADF, você pode carregar seus pacotes em arquivos do Azure e executá-los em Azure-SSIS IR. Você pode fazer isso seguindo estas etapas:

1. Na página de **introdução do SSIS no ADF** , examine a introdução e selecione o botão **Avançar** para continuar.

   ![Introdução ao SSIS no ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

2. Na página **selecionar ir do SSIS no ADF** , selecione o ADF existente e Azure-SSIS ir para executar pacotes. Você também pode criar novos se não tiver nenhum.
   - Para selecionar o Azure-SSIS IR existente, selecione primeiro a assinatura relevante do Azure e o ADF.
   - Se você selecionar o ADF existente que não tem nenhum Azure-SSIS IR, selecione o botão **criar ir do SSIS** para criar um novo no portal do ADF. Depois de criado, você pode retornar a esta página para selecionar o novo Azure-SSIS IR.
   - Se você selecionar sua assinatura do Azure existente que não tem nenhum ADF, selecione o botão **criar ir do SSIS** para iniciar o **Assistente de criação de Integration Runtime**. No assistente, você pode inserir o local designado e o prefixo para que possamos criar automaticamente um novo grupo de recursos do Azure, Data Factory e o SSIS IR em seu nome, chamado no seguinte padrão: **YourPrefix-RG/DF/ir-YourCreationTime**. Depois de criado, você pode retornar a esta página para selecionar o novo ADF e Azure-SSIS IR.

   ![Selecione o IR do SSIS no ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

3. Na página **selecionar armazenamento do Azure** , selecione sua conta de armazenamento do Azure existente para carregar pacotes em arquivos do Azure. Você também pode criar um novo se não tiver nenhum.
   - Para selecionar sua conta de armazenamento do Azure existente, selecione a assinatura relevante do Azure primeiro.
   - Se você selecionar a mesma assinatura do Azure que o Azure-SSIS IR que não tem nenhuma conta de armazenamento do Azure, selecione o botão **criar armazenamento do Azure** . Criaremos automaticamente um novo em seu nome no mesmo local que seu Azure-SSIS IR, nomeado combinando um prefixo de seu nome de Azure-SSIS IR e sua data de criação. Depois de criado, você pode retornar a esta página para selecionar sua nova conta de armazenamento do Azure.
   - Se você selecionar uma assinatura diferente do Azure que não tenha nenhuma conta de armazenamento do Azure, selecione o botão **criar armazenamento do Azure** para criar uma nova em portal do Azure. Depois de criado, você pode retornar a esta página para selecionar sua nova conta de armazenamento do Azure.

   ![Selecione Armazenamento do Azure](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

4. Selecione o botão **conectar** para concluir a conexão do seu projeto para Azure-SSIS ir. Vamos exibir o Azure-SSIS IR selecionado e a conta de armazenamento do Azure no nó **recursos vinculados do Azure** na janela Gerenciador de soluções do SSDT. Também atualizaremos e exiberemos o status de seu Azure-SSIS IR. Você pode gerenciar seu Azure-SSIS IR clicando com o botão direito do mouse em seu nó para exibir um menu e selecionar o item **Start\Stop\Manage** que leva você ao portal do ADF para fazer isso.

## <a name="assess-ssis-projectpackages-for-executions-in-azure"></a>Avaliar project\packages do SSIS para execuções no Azure

### <a name="assessing-single-or-multiple-packages"></a>Avaliando um ou vários pacotes

Antes de executar seus pacotes no Azure, você pode avaliá-los para superfície de quaisquer problemas potenciais de compatibilidade de nuvem. Isso inclui bloqueadores de migração e informações adicionais das quais você deve estar atento. 
-  Você tem as opções para avaliar pacotes únicos um por um ou todos os pacotes ao mesmo tempo em seu projeto.

   ![Avaliar pacote](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-package.png)

   ![Avaliar projeto](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project.png)

-  Na janela **relatório de avaliação** do SSDT, você pode encontrar todos os problemas potenciais de compatibilidade de nuvem que são exibidos, cada um com sua própria descrição e recomendação detalhadas. Você também pode exportar o relatório de avaliação para um arquivo CSV que pode ser compartilhado com qualquer pessoa que deva mitigar esses problemas. 

   ![Relatório de avaliação](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project-result.png)

### <a name="suppressing-assessment-rules"></a>Suprimindo regras de avaliação

Quando tiver certeza de que alguns problemas potenciais de compatibilidade de nuvem não são aplicáveis ou foram atenuados corretamente em seus pacotes, você poderá suprimir as regras de avaliação relevantes que as Surface. Isso reduzirá o ruído em seus relatórios de avaliação subsequentes.
-  Selecione o link de **supressão configurar regra de avaliação** na janela **relatório de avaliação** do SSDT para exibir a janela configurações de supressão da **regra de avaliação** , na qual você pode selecionar as regras de avaliação a serem suprimidas.

   ![Configurações de supressão da regra de avaliação](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings.png)

-  Como alternativa, clique com o botão direito do mouse no nó do projeto na janela Gerenciador de Soluções do SSDT para exibir um menu. Selecione o item **configurações habilitadas para o Azure** no **SSIS no** submenu Azure data Factory para exibir uma janela que contém as páginas de propriedades do projeto. Selecione a propriedade **IDs da regra de avaliação suprimida** na seção **configurações habilitadas para o Azure** . Por fim, selecione o botão de reticências (**...**) para exibir a janela de **configurações de supressão da regra de avaliação** , em que é possível selecionar as regras de avaliação a serem suprimidas.

   ![Configurações habilitadas para o Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

   ![Configurações de supressão de regra de avaliação por meio de configurações habilitadas para Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings-via-azure-enabled-settings.png)

## <a name="execute-ssis-packages-in-azure"></a>Executar pacotes SSIS no Azure

### <a name="configuring-azure-enabled-settings"></a><a name="azureenabledsettings"></a> Definindo configurações habilitadas para o Azure

Antes de executar seus pacotes no Azure, você pode definir suas configurações habilitadas para o Azure para eles. Por exemplo, você pode habilitar a autenticação do Windows em seu Azure-SSIS IR para acessar armazenamentos de dados locais/na nuvem seguindo estas etapas:

1. Clique com o botão direito do mouse no nó do projeto na janela Gerenciador de Soluções do SSDT para exibir um menu. Em seguida, selecione o item **configurações habilitadas para o Azure** no **SSIS no** submenu Azure data Factory para exibir uma janela que contém as páginas de propriedades do projeto.

   ![Configurações habilitadas para o Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

2. Selecione a seção habilitar a propriedade de **autenticação do Windows** nas **configurações habilitadas para o Azure** e, em seguida, selecione **verdadeiro** no menu suspenso. Em seguida, selecione a propriedade **credenciais de autenticação do Windows** e, em seguida, selecione o botão de reticências (**...**) para exibir a janela de **credenciais de autenticação do Windows** .

   ![Habilitar a autenticação do Windows](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-open.png)

3. Insira suas credenciais de autenticação do Windows. Por exemplo, para acessar os arquivos do Azure, você pode inserir `Azure` , `YourStorageAccountName` e `YourStorageAccountKey` para **domínio**, **nome de usuário** e **senha**, respectivamente.

   ![Credenciais de autenticação do Windows](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-credential.png)

### <a name="starting-package-executions"></a>Iniciando execuções de pacote

Depois de conectar seus projetos habilitados para o Azure ao SSIS no ADF, avaliar sua compatibilidade de nuvem e reduzir possíveis problemas, você pode executar/testar seus pacotes no Azure-SSIS IR.
-  Selecione o botão **Iniciar** na barra de ferramentas SSDT para listar um menu. Em seguida, selecione o item **executar no Azure** .

   ![Executar no Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  Como alternativa, clique com o botão direito do mouse no nó do pacote na janela Gerenciador de Soluções de SSDT para exibir um menu. Em seguida, selecione o **pacote de execução no Azure** item.

   ![Executar pacote no Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> Executar seus pacotes no Azure exige que você tenha um Azure-SSIS IR em execução, portanto, se o Azure-SSIS IR for interrompido, uma janela de caixa de diálogo será exibida para iniciá-lo. Excluindo qualquer hora de instalação personalizada, esse processo deve ser concluído em 5 minutos, mas pode levar aproximadamente 20-30 minutos para Azure-SSIS IR ingressar em uma rede virtual. Depois de executar seus pacotes no Azure, você pode interromper seu Azure-SSIS IR para gerenciar seu custo de execução clicando com o botão direito do mouse em seu nó na janela Gerenciador de Soluções do SSDT para exibir um menu e selecionar o item **Start\Stop\Manage** que leva você ao portal do ADF para fazer isso.

### <a name="using-execute-package-task"></a>Usando a tarefa Executar Pacote

Se seus pacotes contiverem tarefas executar pacote que se referem a pacotes filho armazenados em sistemas de arquivos locais, siga estas etapas adicionais:

1. Carregue os pacotes filho em arquivos do Azure na mesma conta de armazenamento do Azure conectada aos seus projetos e obtenha seu novo caminho de UNC (Convenção de nomenclatura universal), por exemplo, `\\YourStorageAccountName.file.core.windows.net\ssdtexecution\YourChildPackage1.dtsx`

2. Substitua o caminho do arquivo desses pacotes filho no Gerenciador de conexões de arquivos das tarefas executar pacote com seu novo caminho UNC
   - Se o computador local que executa o SSDT não puder acessar o novo caminho UNC, você poderá inseri-lo no painel de propriedades do Gerenciador de conexões de arquivos
   - Como alternativa, você pode usar uma variável para o caminho do arquivo para atribuir o valor correto em tempo de execução

Se seus pacotes contiverem tarefas executar pacote que se referem a pacotes filho no mesmo projeto, nenhuma etapa adicional será necessária.

### <a name="switching-package-protection-level"></a>Alterando o nível de proteção do pacote

A execução de pacotes do SSIS no Azure não dá suporte aos níveis de proteção do **EncryptSensitiveWithUserKey** / **EncryptAllWithUserKey** . Consequentemente, se os pacotes estiverem configurados para usá-los, nós os converteremos temporariamente em usando  / os níveis de proteção do EncryptSensitiveWithPassword **EncryptAllWithPassword** , respectivamente. Também geraremos aleatoriamente senhas de criptografia quando carregarmos seus pacotes em arquivos do Azure para execuções no seu Azure-SSIS IR.

> [!NOTE]
> Se seus pacotes contiverem tarefas executar pacote que se referem a pacotes filho configurados para usar os níveis de proteção do **EncryptSensitiveWithUserKey** / **EncryptAllWithUserKey** , você precisará reconfigurar manualmente esses pacotes filho para usar  / os níveis de proteção do EncryptSensitiveWithPassword **EncryptAllWithPassword** , respectivamente, antes de executar os pacotes.

Se seus pacotes já estiverem configurados para usar os níveis de proteção do **EncryptSensitiveWithPassword** / **EncryptAllWithPassword** , vamos mantê-los inalterados. Ainda geraremos aleatoriamente senhas de criptografia quando carregarmos seus pacotes em arquivos do Azure para execuções no seu Azure-SSIS IR.

### <a name="switching-package-execution-environments"></a><a name="switchenvironment"></a> Alternando ambientes de execução de pacote

Se você parametrizar seu projeto/pacotes no modelo de implantação de projeto, poderá criar várias configurações do VS para alternar os ambientes de execução de pacote. Dessa forma, você pode atribuir valores específicos de ambiente a seus parâmetros de projeto/pacote em tempo de execução. É recomendável que você tenha pelo menos duas configurações do VS diferentes para execuções de pacote nos ambientes locais e de nuvem, para que você possa habilitar seus projetos do Azure em relação à configuração de nuvem. Aqui está um exemplo passo a passo de como alternar os ambientes de execução de pacote entre seu computador local e o Azure:

1. Digamos que seu pacote contenha uma tarefa sistema de arquivos que defina os atributos de um arquivo. Quando você o executa em seu computador local, ele define os atributos de um arquivo armazenado no sistema de arquivos local. Ao executá-lo em seu Azure-SSIS IR, você deseja que ele defina os atributos de um arquivo armazenado nos arquivos do Azure. Primeiro, crie um parâmetro de pacote do tipo cadeia de caracteres e nomeie-o como **FilePath** para conter o valor do caminho do arquivo de destino.

   ![Criar parâmetro de pacote](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-define-parameters.png)

2. Em seguida, na página **geral** da janela do **Editor da tarefa sistema de arquivos** , parametrizamos a propriedade **SourceVariable** na seção **conexão de origem** com o parâmetro de pacote **FilePath** . 

   ![Parametrizar conexão de origem](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-update-task-with-parameters.png)

3. Por padrão, você tem uma configuração VS existente para execuções de pacote no ambiente local chamado **desenvolvimento**. Crie uma nova configuração do VS para execuções de pacote no ambiente de nuvem chamado **Azure**, consulte [criando uma nova configuração do vs](/visualstudio/ide/how-to-create-and-edit-configurations), se você ainda não tiver feito isso.

4. Ao exibir os parâmetros do pacote, selecione o botão **adicionar parâmetros às configurações** para abrir a janela **gerenciar valores de parâmetro** para o pacote. Em seguida, atribua valores diferentes do caminho do arquivo de destino ao parâmetro de pacote **FilePath** nas configurações de **desenvolvimento** e **do Azure** .

   ![Atribuir valores de parâmetro](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-override-parameter.png)

5. Azure – habilite seu projeto em relação à configuração de nuvem, consulte [Azure-habilitando projetos SSIS existentes](#azureenableproject), se você ainda não tiver feito isso. Em seguida, defina as configurações habilitadas para o Azure para habilitar a autenticação do Windows para o Azure-SSIS IR acessar os arquivos do Azure, consulte [definindo as configurações habilitadas](#azureenabledsettings)para o Azure, se você ainda não tiver feito isso.

6. Execute seu pacote no Azure. Você pode alternar o ambiente de execução do pacote de volta para seu computador local selecionando a configuração de **desenvolvimento** .

   ![Alternar Configuração do Visual Studio](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-switch-configurations.png)

### <a name="using-package-configuration-file"></a>Usando o arquivo de configuração do pacote

Se você usar arquivos de configuração de pacote no modelo de implantação de pacote, poderá atribuir valores específicos de ambiente às suas propriedades de pacote em tempo de execução. Nós carregaremos automaticamente esses arquivos com seus pacotes em arquivos do Azure para execuções em seu Azure-SSIS IR.

### <a name="checking-package-execution-logs"></a>Verificando os logs de execução do pacote

Depois de iniciar a execução do pacote, vamos Formatar e exibir seus logs na janela de **progresso** do SSDT. Para um pacote de execução longa, atualizaremos periodicamente seus logs em minutos. Você pode cancelar imediatamente a execução do pacote selecionando o botão **parar** na barra de ferramentas SSDT. Você também pode localizar temporariamente os dados brutos de seus logs no seguinte caminho UNC: `\\<YourStorageAccountName>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs` , mas nós o limparemos após um dia.

## <a name="current-limitations"></a>Limitações atuais

-  O SSDT habilitado para Azure dá suporte apenas a regiões de nuvem comerciais/globais e não dá suporte a regiões de nuvem governamentais/nacionais por enquanto.

## <a name="next-steps"></a>Próximas etapas

Quando estiver satisfeito com a execução de seus pacotes no Azure do SSDT, você poderá implantá-los e executá-los como executar atividades de pacote SSIS em pipelines do ADF, ver [executando pacotes do SSIS como executar atividades de pacote do SSIS em pipelines do ADF](./how-to-invoke-ssis-package-ssis-activity.md).