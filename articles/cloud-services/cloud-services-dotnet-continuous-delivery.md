---
title: "Entrega contínua de serviços de nuvem com TFS no Azure | Microsoft Docs"
description: "Saiba como configurar a entrega contínua para aplicativos de nuvem do Azure. Exemplos de código para instruções de linha de comando do MSBuild e scripts do PowerShell."
services: cloud-services
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 4f3c93c6-5c82-4779-9d19-7404a01e82ca
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/12/2017
ms.author: kraigb
ms.openlocfilehash: 0979722b9ec715e91825c7aba74657451df6e83f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="continuous-delivery-for-cloud-services-in-azure"></a>Fornecimento contínuo de serviços de nuvem no Azure
O processo descrito neste artigo mostra como configurar o fornecimento contínuo de aplicativos na nuvem do Azure. Este processo permite criar pacotes automaticamente e implantar o pacote no Azure após cada verificação de código. O processo de compilação do pacote descrito neste artigo equivale ao comando **Package** do Visual Studio, e as etapas de publicação equivalem ao comando **Publish** do Visual Studio.
O artigo aborda os métodos que você usaria para criar um servidor de compilação com instruções de linha de comando do MSBuild e scripts do Windows PowerShell, além de demonstrar como configurar as definições do Visual Studio Team Foundation Server - Team Build para usar os comandos do MSBuild e os scripts do PowerShell. O processo é personalizável para o ambiente de compilação e os ambientes de destino do Azure.

Você também pode usar o Visual Studio Team Services, uma versão do TFS hospedada no Azure, para fazer isso com mais facilidade. 

Antes de começar, você deve publicar seu aplicativo do Visual Studio.
Isso garantirá que todos os recursos estejam disponíveis e sejam inicializados quando você tentar automatizar o processo de publicação.

## <a name="1-configure-the-build-server"></a>1: Configurar o Servidor de Build
Antes de criar um pacote do Azure usando o MSBuild, você deve instalar o software e as ferramentas necessárias no servidor de compilação.

O Visual Studio não precisa estar instalado no servidor de compilação. Se você quiser usar o Team Foundation Build Service para gerenciar o servidor de compilação, siga as instruções na documentação do [Team Foundation Build Service][Team Foundation Build Service].

1. No servidor de build, instale o [.NET Framework 4.5.2][.NET Framework 4.5.2], que inclui o MSBuild.
2. Instale a versão mais recente das [Ferramentas de criação do Azure para .NET](https://azure.microsoft.com/develop/net/).
3. Instale as [Bibliotecas do Azure para .NET](http://go.microsoft.com/fwlink/?LinkId=623519).
4. Copie o arquivo Microsoft.WebApplication.targets de uma instalação do Visual Studio no servidor de compilação.

   Em um computador com o Visual Studio instalado, esse arquivo está localizado no diretório C:\\Arquivos de Programas(x86)\\MSBuild\\Microsoft\\VisualStudio\\v14.0\\WebApplications. Você deve copiá-lo para o mesmo diretório no servidor de compilação.
5. Instale as [Ferramentas do Azure para Visual Studio](https://www.visualstudio.com/features/azure-tools-vs.aspx).

## <a name="2-build-a-package-using-msbuild-commands"></a>2: Compilar um Pacote usando comandos MSBuild
Esta seção descreve como criar um comando do MSBuild que compila um pacote do Azure. Execute esta etapa no servidor de compilação para verificar setudo está configurado corretamente e se o comando do MSBuild faz o que você deseja fazer. Você pode adicionar essa linha de comando a scripts de construção existentes no servidor de compilação ou usar a linha de comando em uma definição de compilação do TFS, conforme descrito na próxima seção. Para obter mais informações sobre parâmetros de linha de comando e sobre o MSBuild, consulte [Referência de linha de comando do MSBuild](https://msdn.microsoft.com/library/ms164311%28v=vs.140%29.aspx).

1. Se o Visual Studio estiver instalado no servidor de build, localize e escolha **Prompt de Comando do Studio Visual** na pasta **Ferramentas do Visual Studio** do Windows.

   Se o Visual Studio não estiver instalado no servidor de compilação, abra um prompt de comando e verifique se MSBuild.exe está acessível no caminho. O MSBuild é instalado com o .NET Framework no caminho %WINDIR%\\Microsoft.NET\\Framework\\*Version*. Por exemplo, para adicionar MSBuild.exe à variável de ambiente PATH quando você tem o .NET Framework 4 instalado, digite o seguinte comando no prompt de comando:

       set PATH=%PATH%;"C:\Windows\Microsoft.NET\Framework\v4.0.30319"
2. No prompt de comando, navegue até a pasta que contém o arquivo de projeto do Azure que você deseja compilar.
3. Execute MSBuild com a opção /target:Publish, como no seguinte exemplo:

       MSBuild /target:Publish

   Essa opção pode ser abreviada como /t:Publish. A opção /t:Publish no MSBuild não deve ser confundida com os comandos Publicar disponíveis no Visual Studio quando você tem o SDK do Azure instalado. A opção /t:Publish compila apenas os pacotes do Azure. Ela não implanta os pacotes como os comandos Publicar no Visual Studio.

   Você também pode especificar o nome do projeto como um parâmetro do MSBuild. Se não for especificado, o diretório atual é usado. Para saber mais sobre as opções da linha de comando, veja [Referência da linha de comando do MSBuild](https://msdn.microsoft.com/library/ms164311%28v=vs.140%29.aspx).
4. Localize a saída. Por padrão, esse comando cria um diretório relacionado à pasta raiz do projeto, como *ProjectDir*\\bin\\*Configuration*\\app.publish\\. Ao criar um projeto do Azure, você gera dois arquivos, o arquivo do pacote propriamente dito e o arquivo de configuração que o acompanha:

   * Project.cspkg
   * ServiceConfiguration.*TargetProfile*.cscfg

   Por padrão, cada projeto do Azure inclui um arquivo de configuração de serviço (arquivo .cscfg) para compilações locais (depuração) e outro para compilações de nuvem (de preparo ou produção), mas você pode adicionar ou remover arquivos de configuração de serviço conforme necessário. Quando compilar um pacote dentro do Visual Studio, você será questionado quanto ao arquivo de configuração de serviço a ser incluído com o pacote.
5. Especifique o arquivo de configuração do serviço. Quando você compila um pacote usando o MSBuild, o arquivo de configuração de serviço local é incluído por padrão. Para incluir um arquivo de configuração de serviço diferente, defina a propriedade TargetProfile do comando MSBuild, como no seguinte exemplo:

       MSBuild /t:Publish /p:TargetProfile=Cloud
6. Especifique o local para a saída. Defina o caminho usando a opção /p:PublishDir=*Directory*\\, incluindo o separador de barra invertida à direita, como no exemplo a seguir:

       MSBuild /target:Publish /p:PublishDir=\\myserver\drops\

   Depois de criar e testar uma linha de comando do MSBuild apropriada para compilar os projetos e integrá-los em um pacote do Azure, você poderá adicionar essa linha de comando aos scripts de compilação. Se seu servidor de compilação usar scripts personalizados, esse processo dependerá das especificidades de seu processo de compilação personalizado. Se estiver usando o TFS como um ambiente de compilação, você poderá seguir as instruções na próxima etapa para adicionar a compilação do pacote do Azure ao processo de compilação.

## <a name="3-build-a-package-using-tfs-team-build"></a>3: Compilar um Pacote usando o TFS Team Build
Se você tiver o TFS (Team Foundation Server) configurado como um controlador de compilação e o servidor de compilação estiver configurado como um computador de compilação TFS, você poderá configurar uma compilação automatizada para o pacote do Azure. Para obter informações sobre como configurar e usar o Team Foundation Server como um sistema de build, veja [Escalar horizontalmente seu sistema de build][Scale out your build system]. Em particular, o procedimento a seguir presume que você configurou seu servidor de build, conforme descrito em [Implantar e configurar um servidor de build][Deploy and configure a build server] e que criou um projeto de equipe e um projeto de serviço de nuvem no projeto de equipe.

Para configurar o TFS para compilar pacotes do Azure, execute as seguintes etapas:

1. No Visual Studio, no computador de desenvolvimento, no menu Exibir , escolha **Team Explorer** ou Ctrl+\\, Ctrl+M. Na janela do Team Explorer, expanda o nó **Builds** ou escolha a página **Builds** e escolha **Definição de Nova Compilação**.

   ![Opção Nova Definição de Compilação][0]
2. Escolha a guia **Gatilho** e especifique as condições desejadas para quando deseja que o pacote seja compilado. Por exemplo, especifique **Integração Contínua** para compilar o pacote sempre que um check-in de controle do código-fonte ocorrer.
3. Selecione a guia **Configurações de Origem**, e certifique-se de que a pasta do projeto esteja listada na coluna **Pasta do Controle do Código-Fonte** e que o status seja **Ativo**.
4. Escolha a guia **Padrões de Compilação** e, em Controlador de compilação, verifique o nome do servidor de compilação.  Além disso, escolha a opção **Copiar resultado da criação para a seguinte pasta** de recebimento e especifique o local de recebimento desejado.
5. Escolha a guia **Processo** . Na guia Processo, selecione o modelo padrão, em **Build**, selecione o projeto se ele ainda não estiver selecionado e expanda a seção **Avançado** na seção **Build** da grade.
6. Escolha **Argumentos do MSBuild**e defina os argumentos da linha de comando do MSBuild apropriados, conforme descrito na Etapa 2 acima. Por exemplo, insira **/t:Publish /p:PublishDir=\\\\meuservidor\\drops\\** para compilar um pacote e copie os arquivos de pacote para o local \\\\meuservidor\\drops\\:

   ![Argumentos do MSBuild][2]

   > [!NOTE]
   > Copiar os arquivos para um compartilhamento público torna mais fácil implantar manualmente os pacotes do seu computador de desenvolvimento.
7. Teste o êxito da etapa de compilação fazendo check-in de uma alteração para o projeto ou coloque na fila uma nova compilação. Para colocar na fila uma nova compilação, no Team Explorer, clique com o botão direito do mouse em **Todas as Definições de Compilação** e, em seguida, escolha **Colocar Nova Compilação na Fila**.

## <a name="4-publish-a-package-using-a-powershell-script"></a>4: Publicar um Pacote usando um Script do PowerShell
Esta seção descreve como criar um script do Windows PowerShell que publicará a saída do pacote do aplicativo de nuvem para o Azure usando parâmetros opcionais. Esse script pode ser chamado após a etapa de compilação na sua automação de compilação personalizada. Ele também pode ser chamado das atividades do fluxo de trabalho do Modelo de processo no Visual Studio TFS Team Build.

1. Instale os [cmdlets do Azure PowerShell][Azure PowerShell cmdlets] (v0.6.1 ou superior).
   Durante a fase de instalação do cmdlet, opte por instalar como um snap-in. Observeque essa versão suportada oficialmente substitui a versão mais antigaoferecida por meio do CodePlex, embora as versões anteriores foram numeradas 2.x.x.
2. Inicie o PowerShell do Azure usando o menu Iniciar. Se você iniciar dessa forma, os cmdlets do PowerShell do Azure serão carregados.
3. No prompt do PowerShell, verifique se os cmdlets do PowerShell são carregados digitando o comando parcial `Get-Azure` e pressionando a tecla Tab para o preenchimento de declaração.

   Se você pressionar a tecla Tab repetidamente, deverá ver diversos comandos do Azure PowerShell.
4. Verifique se você consegue se conectar à assinatura do Azure importando as informações de assinatura do arquivo .publishsettings.

   `Import-AzurePublishSettingsFile c:\scripts\WindowsAzure\default.publishsettings`

   Em seguida, digite o comando

   `Get-AzureSubscription`

   Isso mostra informações sobre sua assinatura. Verifique se tudo está correto.
5. Salve o modelo de script fornecido ao final deste artigo em sua pasta de scripts como c:\\scripts\\WindowsAzure\\**PublishCloudService.ps1**.
6. Consulte a seção de parâmetros do script. Adicione ou modifique os valores padrão. Esses valores podem ser substituídos sempre passando parâmetros explícitos.
7. Verifique se há contas de serviço de nuvem e de armazenamento válidas criadas na assinatura que possam ser direcionadas pelo script de publicação. A conta de armazenamento (armazenamento de blob) será usada para carregar e armazenar temporariamente o arquivo de configuração e o pacote de implantação, enquanto a implantação está sendo criada.

   * Para criar um novo serviço de nuvem, você pode chamar esse script ou usar o [portal do Azure](https://portal.azure.com). O nome do serviço de nuvem será usado como um prefixo em um nome de domínio totalmente qualificado e, portanto, deve ser exclusivo.

         New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
   * Para criar uma nova conta de armazenamento, você pode chamar esse script ou usar o [portal do Azure](https://portal.azure.com). O nome da conta de armazenamento será usado como um prefixo em um nome de domínio totalmente qualificado e, portanto, deve ser exclusivo. Você pode tentar usar o mesmo nome que o serviço de nuvem.

         New-AzureStorageAccount -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
8. Chame o script diretamente do PowerShell do Azure, ou conecte esse script à automação de compilação do host para ocorrer após a compilação do pacote.

   > [!IMPORTANT]
   > O script sempre vai excluir ou substituir as implantações existentes por padrão, se eles forem detectados. Isso é necessário para habilitar o fornecimento contínuo de automação onde não é possível nenhum aviso ao usuário.
   >
   >

   **Cenário de exemplo 1:** implantação contínua ao ambiente de preparo de um serviço:

       PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Staging -serviceName mycloudservice -storageAccountName mystoragesaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

   Isso é normalmente seguido pela verificação da execução de teste e uma troca de VIP. A troca de VIP pode ser feita por meio do [portal do Azure](https://portal.azure.com) ou usando o cmdlet Move-Deployment.

   **Cenário de exemplo 2:** implantação contínua ao ambiente de produção de um serviço de teste dedicado

       PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Production -enableDeploymentUpgrade 1 -serviceName mycloudservice -storageAccountName mystorageaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

   **Área de trabalho remota:**

   Se a Área de Trabalho Remota estiver habilitada no projeto do Azure, você precisará realizar etapas individuais adicionais para garantir que o certificado de serviço de nuvem seja carregado em todos os serviços de nuvem segmentados por esse script.

   Localize os valores de impressão digital do certificado esperados por suas funções. Os valores da impressão digital são visíveis na seção Certificados no arquivo de configuração de nuvem (ou seja, ServiceConfiguration.Cloud.cscfg). Ela também é visível na caixa de diálogo Configuração de Área de Trabalho Remota no Visual Studio, quando você Mostrar Opções e exibir o certificado selecionado.

       <Certificates>
             <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="C33B6C432C25581601B84C80F86EC2809DC224E8" thumbprintAlgorithm="sha1" />
       </Certificates>

   Carregue certificados de Área de Trabalho Remota como uma etapa única de configuração usando o seguinte script de cmdlet:

       Add-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MY\<THUMBPRINT>)

   Por exemplo:

       Add-AzureCertificate -serviceName 'mytestcloudservice' -certToDeploy (get-item cert:\CurrentUser\MY\C33B6C432C25581601B84C80F86EC2809DC224E8

   Como alternativa, você pode exportar o arquivo de certificado PFX com a chave privada e carregar certificados para cada serviço de nuvem de destino usando o [portal do Azure](https://portal.azure.com).

   <!---
   Fixing broken links for Azure content migration from ACOM to DOCS. I'm unable to find a replacement links, so I'm commenting out this reference for now. The author can investigate in the future. "Read the following article to learn more: http://msdn.microsoft.com/library/windowsazure/gg443832.aspx.
   -->
   **Atualizar implantação vs. Excluir Implantação -\> Nova Implantação**

   O script vai, por padrão, executar uma implantação de atualização($enableDeploymentUpgrade = 1) quando nenhum parâmetro for passado ou o valor 1 for passado explicitamente. Para instâncias únicas isso tem a vantagem de levar menos tempo do que uma implantação completa. Para instâncias que exigem alta disponibilidade, isso também tem a vantagem de deixar algumas instâncias em execução enquanto outras são atualizadas (movimentação de seu domínio de atualização), além de seu VIP não ser excluído.

   A Implantação de Atualização pode ser desabilitada no script ($enableDeploymentUpgrade = 0) ou transmitindo *-enableDeploymentUpgrade 0* como um parâmetro, o que altera o comportamento do script para excluir primeiro qualquer implantação existente e, em seguida, criar uma nova implantação.

   > [!IMPORTANT]
   > O script sempre vai excluir ou substituir as implantações existentes por padrão, se eles forem detectados. Isso é necessário para habilitar o fornecimento contínuo de automação onde é possível sem nenhum aviso ao usuário/operador.
   >
   >

## <a name="5-publish-a-package-using-tfs-team-build"></a>5: Publicar um Pacote usando o TFS Team Build
Esta etapa opcional conecta o TFS Team Build ao script criado na etapa 4, que manipula a publicação da compilação do pacote no Azure. Isso implica modificar o modelo de processo usado pela sua definição de compilação para que seja executada uma atividade de Publicar no final do fluxo de trabalho. A atividade de Publicar executará o comando PowerShell passando parâmetros da compilação. A saída dos destinos do MSBuild e o script de publicação serão redirecionados para a saída de compilação padrão.

1. Edite a definição de compilação responsável pela implantação contínua.
2. Selecione a guia **Processo** .
3. Execute [estas instruções](http://msdn.microsoft.com/library/dd647551.aspx) para adicionar um projeto de atividade ao modelo de processo de compilação, baixe o modelo padrão, adicione-o ao projeto e faça check-in. Atribua um novo nome ao modelo de processo de compilação, como AzureBuildProcessTemplate.
4. Volte à guia **Processo** e use **Mostrar Detalhes** para mostrar uma lista dos modelos de processos de compilação disponíveis. Escolha o botão **Novo...** e navegue para o projeto recém-adicionado e faça o check-in. Localize o modelo que você acabou de criar e escolha **OK**.
5. Abra o modelo de processo selecionado para edição. Você pode abrir diretamente no designer de fluxo de trabalho ou no editor de XML para trabalhar como XAML.
6. Adicione a seguinte lista de novos argumentos como itens de linha separados na guia de argumentos do designer de fluxo de trabalho. Todos os argumentos devem ter direção =In e digite =String. Esses serão usados para parâmetros de fluxo da definição de compilação para o fluxo de trabalho, que, em seguida, é usado para chamar o script de publicação.

       SubscriptionName
       StorageAccountName
       CloudConfigLocation
       PackageLocation
       Environment
       SubscriptionDataFileLocation
       PublishScriptLocation
       ServiceName

   ![Lista de argumentos][3]

   O XAML correspondente tem esta aparência:

       <Activity  _ />
         <x:Members>
           <x:Property Name="BuildSettings" Type="InArgument(mtbwa:BuildSettings)" />
           <x:Property Name="TestSpecs" Type="InArgument(mtbwa:TestSpecList)" />
           <x:Property Name="BuildNumberFormat" Type="InArgument(x:String)" />
           <x:Property Name="CleanWorkspace" Type="InArgument(mtbwa:CleanWorkspaceOption)" />
           <x:Property Name="RunCodeAnalysis" Type="InArgument(mtbwa:CodeAnalysisOption)" />
           <x:Property Name="SourceAndSymbolServerSettings" Type="InArgument(mtbwa:SourceAndSymbolServerSettings)" />
           <x:Property Name="AgentSettings" Type="InArgument(mtbwa:AgentSettings)" />
           <x:Property Name="AssociateChangesetsAndWorkItems" Type="InArgument(x:Boolean)" />
           <x:Property Name="CreateWorkItem" Type="InArgument(x:Boolean)" />
           <x:Property Name="DropBuild" Type="InArgument(x:Boolean)" />
           <x:Property Name="MSBuildArguments" Type="InArgument(x:String)" />
           <x:Property Name="MSBuildPlatform" Type="InArgument(mtbwa:ToolPlatform)" />
           <x:Property Name="PerformTestImpactAnalysis" Type="InArgument(x:Boolean)" />
           <x:Property Name="CreateLabel" Type="InArgument(x:Boolean)" />
           <x:Property Name="DisableTests" Type="InArgument(x:Boolean)" />
           <x:Property Name="GetVersion" Type="InArgument(x:String)" />
           <x:Property Name="PrivateDropLocation" Type="InArgument(x:String)" />
           <x:Property Name="Verbosity" Type="InArgument(mtbw:BuildVerbosity)" />
           <x:Property Name="Metadata" Type="mtbw:ProcessParameterMetadataCollection" />
           <x:Property Name="SupportedReasons" Type="mtbc:BuildReason" />
           <x:Property Name="SubscriptionName" Type="InArgument(x:String)" />
           <x:Property Name="StorageAccountName" Type="InArgument(x:String)" />
           <x:Property Name="CloudConfigLocation" Type="InArgument(x:String)" />
           <x:Property Name="PackageLocation" Type="InArgument(x:String)" />
           <x:Property Name="Environment" Type="InArgument(x:String)" />
           <x:Property Name="SubscriptionDataFileLocation" Type="InArgument(x:String)" />
           <x:Property Name="PublishScriptLocation" Type="InArgument(x:String)" />
           <x:Property Name="ServiceName" Type="InArgument(x:String)" />
         </x:Members>

         <this:Process.MSBuildArguments>
7. Adicione uma nova sequência no final do Agente de Execução:

   1. Comece adicionando uma atividade de declaração If para verificar se há um arquivo de script válido. Defina a condição para este valor:

          Not String.IsNullOrEmpty(PublishScriptLocation)
   2. No caso Then da declaração If, adicione uma nova atividade de sequência. Defina o nome de exibição para 'Iniciar publicação'
   3. Com a sequência de publicação inicial ainda selecionada, adicione a lista a seguir de novas variáveis como itens de linha separadas na guia de variáveis do designer de fluxo de trabalho. Todas as variáveis devem ter um tipo de Variável =String e Escopo=Início da publicação. Esses serão usados para parâmetros de fluxo da definição de compilação para o fluxo de trabalho, que, em seguida, é usado para chamar o script de publicação.

      * SubscriptionDataFilePath, do tipo String
      * PublishScriptFilePath, do tipo String

        ![Novas variáveis][4]
   4. Se estiver usando o TFS 2012 ou anterior, adicione uma atividade ConvertWorkspaceItem ao início da nova Sequência. Se estiver usando o TFS 2013 ou posterior, adicione uma atividade GetLocalPath ao início da nova sequência. Para um ConvertWorkspaceItem, defina as propriedades da seguinte maneira: Direction=ServerToLocal, DisplayName='Convert publish script filename', Input=' PublishScriptLocation', Result='PublishScriptFilePath', Workspace='Workspace'. Para uma atividade GetLocalPath, defina a propriedade IncomingPath como 'PublishScriptLocation’ e o Resultado como 'PublishScriptFilePath'. Esta atividade converte o caminho para o script de publicação dos locais do servidor TFS (se aplicável) para um caminho de disco local padrão.
   5. Se estiver usando o TFS 2012 ou anterior, adicione outra atividade ConvertWorkspaceItem ao final da nova Sequência. Direction=ServerToLocal, DisplayName='Convert subscription filename', Input=' SubscriptionDataFileLocation', Result= 'SubscriptionDataFilePath', Workspace='Workspace'. Se estiver usando o TFS 2013 ou posterior, adicione outra atividade GetLocalPath. IncomingPath='SubscriptionDataFileLocation' e Result='SubscriptionDataFilePath.'
   6. Adicione uma atividade InvokeProcess ao final da nova sequência.
      Essa atividade chama PowerShell.exe com os argumentos passados pela definição de compilação.

      + Arguments = String.Format(" -File ""{0}"" -serviceName {1}  -storageAccountName {2} -packageLocation ""{3}""  -cloudConfigLocation ""{4}"" -subscriptionDataFile ""{5}""  -selectedSubscription {6} -environment ""{7}""",  PublishScriptFilePath, ServiceName, StorageAccountName,  PackageLocation, CloudConfigLocation,  SubscriptionDataFilePath, SubscriptionName, Environment)
      + DisplayName = Execute publish script
      + FileName = "PowerShell" (incluindo as aspas)
      + OutputEncoding=  System.Text.Encoding.GetEncoding(System.Globalization.CultureInfo.InstalledUICulture.TextInfo.OEMCodePage)
   7. Na caixa de texto da seção **Gerenciar Saída Padrão** de InvokeProcess, defina o valor da caixa de texto como “ data”. Essa é uma variável para armazenar os dados de saída padrão.
   8. Adicione uma atividade WriteBuildMessage logo abaixo da seção de **Gerenciar Saída Padrão** . Defina a importância ='Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High'e a mensagem = 'dados'. Isso garante que a saída padrão do script será gravada na saída de compilação.
   9. Na caixa de texto da seção **Gerenciar Erro de Saída** de InvokeProcess, defina o valor da caixa de texto como “data”. Essa é uma variável para armazenar os dados de erro padrão.
   10. Adicione uma atividade WriteBuildError pouco abaixo da seção **Gerenciar Erro de Saída** . Defina a mensagem = 'dados'. Isso garante que os erros padrões do script serão gravados na saída do erro de compilação.
   11. Corrija quaisquer erros, indicados por sinais de exclamação azuis. Passe o mouse sobre os pontos de exclamação para ver informações sobre o erro. Salve o fluxo de trabalho para limpar os erros.

   O resultado final das atividades de fluxo de trabalho de publicação terá a seguinte aparência no designer:

   ![Atividades do fluxo de trabalho][5]

   O resultado final das atividades de fluxo de trabalho de publicação terá a seguinte aparência neste XAML:

       <If Condition="[Not String.IsNullOrEmpty(PublishScriptLocation)]" sap2010:WorkflowViewState.IdRef="If_1">
           <If.Then>
             <Sequence DisplayName="Start Publish" sap2010:WorkflowViewState.IdRef="Sequence_4">
               <Sequence.Variables>
                 <Variable x:TypeArguments="x:String" Name="SubscriptionDataFilePath" />
                 <Variable x:TypeArguments="x:String" Name="PublishScriptFilePath" />
               </Sequence.Variables>
               <mtbwa:ConvertWorkspaceItem DisplayName="Convert publish script filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_1" Input="[PublishScriptLocation]" Result="[PublishScriptFilePath]" Workspace="[Workspace]" />
               <mtbwa:ConvertWorkspaceItem DisplayName="Convert subscription filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_2" Input="[SubscriptionDataFileLocation]" Result="[SubscriptionDataFilePath]" Workspace="[Workspace]" />
               <mtbwa:InvokeProcess Arguments="[String.Format(&quot; -File &quot;&quot;{0}&quot;&quot; -serviceName {1}&#xD;&#xA;            -storageAccountName {2} -packageLocation &quot;&quot;{3}&quot;&quot;&#xD;&#xA;            -cloudConfigLocation &quot;&quot;{4}&quot;&quot; -subscriptionDataFile &quot;&quot;{5}&quot;&quot;&#xD;&#xA;            -selectedSubscription {6} -environment &quot;&quot;{7}&quot;&quot;&quot;,&#xD;&#xA;            PublishScriptFilePath, ServiceName, StorageAccountName,&#xD;&#xA;            PackageLocation, CloudConfigLocation,&#xD;&#xA;            SubscriptionDataFilePath, SubscriptionName, Environment)]" DisplayName="'Execute Publish Script'" FileName="[PowerShell]" sap2010:WorkflowViewState.IdRef="InvokeProcess_1">
                 <mtbwa:InvokeProcess.ErrorDataReceived>
                   <ActivityAction x:TypeArguments="x:String">
                     <ActivityAction.Argument>
                       <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                     </ActivityAction.Argument>
                     <mtbwa:WriteBuildError Message="{x:Null}" sap2010:WorkflowViewState.IdRef="WriteBuildError_1" />
                   </ActivityAction>
                 </mtbwa:InvokeProcess.ErrorDataReceived>
                 <mtbwa:InvokeProcess.OutputDataReceived>
                   <ActivityAction x:TypeArguments="x:String">
                     <ActivityAction.Argument>
                       <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                     </ActivityAction.Argument>
                     <mtbwa:WriteBuildMessage sap2010:WorkflowViewState.IdRef="WriteBuildMessage_2" Importance="[Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High]" Message="[data]" mva:VisualBasic.Settings="Assembly references and imported namespaces serialized as XML namespaces" />
                   </ActivityAction>
                 </mtbwa:InvokeProcess.OutputDataReceived>
               </mtbwa:InvokeProcess>
             </Sequence>
           </If.Then>
         </If>
       </Sequence>
8. Salve o fluxo de trabalho do modelo de processo de compilação e o faça check-in deste arquivo.
9. Edite a definição de compilação (feche-a se já estiver aberta), e selecione o botão **Novo** se não quiser ver o novo modelo na lista de Modelos de Processo.
10. Defina os valores da propriedade do parâmetro na seção Vários como segue:

    1. CloudConfigLocation ='c:\\drops\\app.publish\\ServiceConfiguration.Cloud.cscfg' *Esse valor é derivado de: ($PublishDir)ServiceConfiguration.Cloud.cscfg*
    2. PackageLocation = 'c:\\drops\\app.publish\\ContactManager.Azure.cspkg' *Esse valor é derivado de: ($PublishDir)($ProjectName).cspkg*
    3. PublishScriptLocation = 'c:\\scripts\\WindowsAzure\\PublishCloudService.ps1'
    4. ServiceName = 'mycloudservicename' *Use o nome de serviço de nuvem apropriado aqui*
    5. Ambiente = 'Teste'
    6. StorageAccountName = 'mystorageaccountname' *Use o nome da conta de armazenamento adequado aqui*
    7. SubscriptionDataFileLocation = 'c:\\scripts\\WindowsAzure\\Subscription.xml'
    8. SubscriptionName = 'padrão'

    ![Valores de propriedade do parâmetro][6]
11. Salve as alterações para a Definição de Compilação.
12. Coloque uma compilação em fila para executar a compilação do pacote e publicar. Se você tiver um gatilho definido para Integração Contínua, você vai executar esse comportamento em cada check-in.

### <a name="publishcloudserviceps1-script-template"></a>Modelo de script PublishCloudService.ps1
```
Param(  $serviceName = "",
        $storageAccountName = "",
        $packageLocation = "",
        $cloudConfigLocation = "",
        $environment = "Staging",
        $deploymentLabel = "ContinuousDeploy to $servicename",
        $timeStampFormat = "g",
        $alwaysDeleteExistingDeployments = 1,
        $enableDeploymentUpgrade = 1,
        $selectedsubscription = "default",
        $subscriptionDataFile = ""
     )


function Publish()
{
    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot -ErrorVariable a -ErrorAction silentlycontinue
    if ($a[0] -ne $null)
    {
        Write-Output "$(Get-Date -f $timeStampFormat) - No deployment is detected. Creating a new deployment. "
    }
    #check for existing deployment and then either upgrade, delete + deploy, or cancel according to $alwaysDeleteExistingDeployments and $enableDeploymentUpgrade boolean variables
    if ($deployment.Name -ne $null)
    {
        switch ($alwaysDeleteExistingDeployments)
        {
            1
            {
                switch ($enableDeploymentUpgrade)
                {
                    1  #Update deployment inplace (usually faster, cheaper, won't destroy VIP)
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Upgrading deployment."
                        UpgradeDeployment
                    }
                    0  #Delete then create new deployment
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Deleting deployment."
                        DeleteDeployment
                        CreateNewDeployment

                    }
                } # switch ($enableDeploymentUpgrade)
            }
            0
            {
                Write-Output "$(Get-Date -f $timeStampFormat) - ERROR: Deployment exists in $servicename.  Script execution cancelled."
                exit
            }
        } #switch ($alwaysDeleteExistingDeployments)
    } else {
            CreateNewDeployment
    }
}

function CreateNewDeployment()
{
    write-progress -id 3 -activity "Creating New Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: In progress"

    $opstat = New-AzureDeployment -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName

    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid

    write-progress -id 3 -activity "Creating New Deployment" -completed -Status "Complete"
    Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: Complete, Deployment ID: $completeDeploymentID"

    StartInstances
}

function UpgradeDeployment()
{
    write-progress -id 3 -activity "Upgrading Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: In progress"

    # perform Update-Deployment
    $setdeployment = Set-AzureDeployment -Upgrade -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName -Force

    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid

    write-progress -id 3 -activity "Upgrading Deployment" -completed -Status "Complete"
    Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: Complete, Deployment ID: $completeDeploymentID"
}

function DeleteDeployment()
{

    write-progress -id 2 -activity "Deleting Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: In progress"

    #WARNING - always deletes with force
    $removeDeployment = Remove-AzureDeployment -Slot $slot -ServiceName $serviceName -Force

    write-progress -id 2 -activity "Deleting Deployment: Complete" -completed -Status $removeDeployment
    Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: Complete"

}

function StartInstances()
{
    write-progress -id 4 -activity "Starting Instances" -status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: In progress"

    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $runstatus = $deployment.Status

    if ($runstatus -ne 'Running')
    {
        $run = Set-AzureDeployment -Slot $slot -ServiceName $serviceName -Status Running
    }
    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $oldStatusStr = @("") * $deployment.RoleInstanceList.Count

    while (-not(AllInstancesRunning($deployment.RoleInstanceList)))
    {
        $i = 1
        foreach ($roleInstance in $deployment.RoleInstanceList)
        {
            $instanceName = $roleInstance.InstanceName
            $instanceStatus = $roleInstance.InstanceStatus

            if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
            {
                $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
                Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
            }

            write-progress -id (4 + $i) -activity "Starting Instance '$instanceName'" -status "$instanceStatus"
            $i = $i + 1
        }

        sleep -Seconds 1

        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    }

    $i = 1
    foreach ($roleInstance in $deployment.RoleInstanceList)
    {
        $instanceName = $roleInstance.InstanceName
        $instanceStatus = $roleInstance.InstanceStatus

        if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
        {
            $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
            Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
        }

        $i = $i + 1
    }

    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $opstat = $deployment.Status

    write-progress -id 4 -activity "Starting Instances" -completed -status $opstat
    Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: $opstat"
}

function AllInstancesRunning($roleInstanceList)
{
    foreach ($roleInstance in $roleInstanceList)
    {
        if ($roleInstance.InstanceStatus -ne "ReadyRole")
        {
            return $false
        }
    }

    return $true
}

#configure powershell with Azure 1.7 modules
Import-Module Azure

#configure powershell with publishsettings for your subscription
$pubsettings = $subscriptionDataFile
Import-AzurePublishSettingsFile $pubsettings
Set-AzureSubscription -CurrentStorageAccountName $storageAccountName -SubscriptionName $selectedsubscription
Select-AzureSubscription $selectedsubscription

#set remaining environment variables for Azure cmdlets
$subscription = Get-AzureSubscription $selectedsubscription
$subscriptionname = $subscription.subscriptionname
$subscriptionid = $subscription.subscriptionid
$slot = $environment

#main driver - publish & write progress to activity log
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script started."
Write-Output "$(Get-Date -f $timeStampFormat) - Preparing deployment of $deploymentLabel for $subscriptionname with Subscription ID $subscriptionid."

Publish

$deployment = Get-AzureDeployment -slot $slot -serviceName $servicename
$deploymentUrl = $deployment.Url

Write-Output "$(Get-Date -f $timeStampFormat) - Created Cloud Service with URL $deploymentUrl."
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script finished."
```

## <a name="next-steps"></a>Próximas etapas
Para habilitar a depuração remota ao usar a entrega contínua, consulte [Habilitar a depuração remota ao usar a entrega contínua para publicar no Azure](cloud-services-virtual-machines-dotnet-continuous-delivery-remote-debugging.md).

[Team Foundation Build Service]: https://msdn.microsoft.com/library/ee259687.aspx
[.NET Framework 4]: https://www.microsoft.com/download/details.aspx?id=17851
[.NET Framework 4.5]: https://www.microsoft.com/download/details.aspx?id=30653
[.NET Framework 4.5.2]: https://www.microsoft.com/download/details.aspx?id=42643
[Scale out your build system]: https://msdn.microsoft.com/library/dd793166.aspx
[Deploy and configure a build server]: https://msdn.microsoft.com/library/ms181712.aspx
[Azure PowerShell cmdlets]: /powershell/azureps-cmdlets-docs
[the .publishsettings file]: https://manage.windowsazure.com/download/publishprofile.aspx?wa=wsignin1.0
[0]: ./media/cloud-services-dotnet-continuous-delivery/tfs-01bc.png
[2]: ./media/cloud-services-dotnet-continuous-delivery/tfs-02.png
[3]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-03.png
[4]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-04.png
[5]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-05.png
[6]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-06.png
