---
title: "Configure as funções para um serviço de nuvem do Azure com o Visual Studio | Microsoft Docs"
description: "Saiba como instalar e configurar funções para serviços de nuvem do Azure usando o Visual Studio."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: d397ef87-64e5-401a-aad5-7f83f1022e16
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/21/2017
ms.author: kraigb
ms.openlocfilehash: 17da71ac0c5ab9330b9244c0354e4d161d98229e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-azure-cloud-service-roles-with-visual-studio"></a>Configurar funções de serviço de nuvem do Azure com o Visual Studio
Um serviço de nuvem do Azure pode ter uma ou mais funções web ou de trabalho. Para cada função, você precisa definir como essa função é instalada e também configurar como ela é executada. Para saber mais sobre as funções em serviços de nuvem, assista ao vídeo [Introdução aos Serviços de Nuvem do Azure](https://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Introduction-to-Windows-Azure-Cloud-Services). 

As informações sobre seu serviço de nuvem são armazenadas nos seguintes arquivos:

- **ServiceDefinition.csdef** — o arquivo de definição de serviço define as configurações de tempo de execução de seu serviço de nuvem, incluindo as funções que são necessárias, os pontos de extremidade e o tamanho da máquina virtual. Nenhum dos dados armazenados em `ServiceDefinition.csdef` pode ser alterado quando a função está sendo executada.
- **ServiceConfiguration.cscfg** — o arquivo de configuração de serviço define quantas instâncias de uma função são executadas e os valores das configurações definidas para uma função. Os dados armazenados em `ServiceConfiguration.cscfg` podem ser alterados enquanto a função está sendo executada.

Para armazenar valores diferentes para essas configurações que controlam como uma função é executada, você pode definir várias configurações de serviço. Você pode usar uma configuração de serviço diferente para cada ambiente de implantação. Por exemplo, você pode definir sua cadeia de conexão de conta de armazenamento para usar o emulador de armazenamento local do Azure em uma configuração de serviço local e criar outra configuração de serviço para usar o armazenamento do Azure na nuvem.

Quando você cria um serviço de nuvem do Azure no Visual Studio, duas configurações de serviço são criadas e adicionadas automaticamente ao projeto do Azure:

- `ServiceConfiguration.Cloud.cscfg`
- `ServiceConfiguration.Local.cscfg`

## <a name="configure-an-azure-cloud-service"></a>Configurar um serviço de nuvem do Azure
Você pode configurar um serviço de nuvem do Azure por meio do Gerenciador de Soluções no Visual Studio, conforme mostrado nas seguintes etapas:

1. Crie ou abra um projeto de serviço de nuvem do Azure no Visual Studio.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e, no menu de contexto, selecione **Propriedades**.
   
    ![Menu de contexto do projeto do Gerenciador de Soluções](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-project-context-menu.png)

1. Na página de propriedades do projeto, selecione a guia **Desenvolvimento**. 

    ![Página de propriedades do projeto — guia Desenvolvimento](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-development-tab.png)

1. Na lista **Configuração de Serviço**, escolha o nome da configuração do serviço que deseja editar. (Se quiser fazer alterações em todas as configurações de serviço para essa função, selecione **Todas as Configurações**).
   
    > [!IMPORTANT]
    > Se você escolher uma configuração de serviço específica, algumas propriedades ficarão desabilitadas porque elas só podem ser definidas para todas as configurações. Para editar essas propriedades, você deve escolher **Todas as Configurações**.
    > 
    > 
   
    ![Lista Configuração de Serviço para um serviço de nuvem do Azure](./media/vs-azure-tools-configure-roles-for-cloud-service/cloud-service-service-configuration-property.png)

## <a name="change-the-number-of-role-instances"></a>Alterar o número de instâncias de função
Para melhorar o desempenho do serviço de nuvem, você pode alterar o número de instâncias de uma função em execução, com base no número de usuários ou na carga esperada para uma função específica. Uma máquina virtual separada é criada para cada instância de uma função quando o serviço de nuvem é executado no Azure. Isso afeta a cobrança pela implantação desse serviço de nuvem. Para obter mais informações sobre a fatura, veja [Entenda sua fatura do Microsoft Azure](billing/billing-understand-your-bill.md).

1. Crie ou abra um projeto de serviço de nuvem do Azure no Visual Studio.

1. Em **Gerenciador de Soluções**, expanda o nó do projeto. No nó **Funções**, clique com o botão direito do mouse na função que deseja atualizar e, no menu de contexto, selecione **Propriedades**.

    ![Menu de contexto da função do Azure no Gerenciador de Soluções](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Selecione a guia **Configuração**.

    ![Guia Configuração](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page.png)

1. Na lista **Configuração de Serviço**, selecione a configuração de serviço que deseja atualizar.
   
    ![Lista Configuração de Serviço](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page-select-configuration.png)

1. Na caixa de texto **Contagem de instâncias** , digite o número de instâncias que você deseja iniciar para essa função. Cada instância é executada em uma máquina virtual separada quando você publica o serviço de nuvem no Azure.

    ![Atualização da contagem de instâncias](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page-instance-count.png)

1. Na barra de ferramentas do Visual Studio, selecione **Salvar**.

## <a name="manage-connection-strings-for-storage-accounts"></a>Gerenciar cadeias de conexão para contas de armazenamento
Você pode adicionar, remover ou modificar cadeias de conexão para suas configurações de serviço. Por exemplo, você pode querer uma cadeia de conexão local para uma configuração de serviço local que tem um valor de `UseDevelopmentStorage=true`. Você também pode querer definir uma configuração de serviço de nuvem que use uma conta de armazenamento no Azure.

> [!WARNING]
> Quando você insere as informações de chave da conta de armazenamento do Azure para uma cadeia de conexão de conta de armazenamento, essas informações são armazenadas localmente no arquivo de configuração de serviço. No entanto, atualmente essas informações não são armazenadas como texto criptografado.
> 
> 

Ao usar um valor diferente para cada configuração de serviço, você não precisa usar cadeias de conexão diferentes em seu serviço de nuvem ou modificar seu código quando publicar o serviço de nuvem no Azure. É possível usar o mesmo nome para a cadeia de conexão no seu código e o valor será diferente, com base na configuração de serviço que você selecionar quando criar seu serviço de nuvem ou quando publicá-lo.

1. Crie ou abra um projeto de serviço de nuvem do Azure no Visual Studio.

1. Em **Gerenciador de Soluções**, expanda o nó do projeto. No nó **Funções**, clique com o botão direito do mouse na função que deseja atualizar e, no menu de contexto, selecione **Propriedades**.

    ![Menu de contexto da função do Azure no Gerenciador de Soluções](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Selecione a guia **Configurações**.

    ![Guia Configurações](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab.png)

1. Na lista **Configuração de Serviço**, selecione a configuração de serviço que deseja atualizar.

    ![Configuração de Serviço](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-select-configuration.png)

1. Para adicionar uma cadeia de conexão, selecione **Adicionar Configuração**.

    ![Adicionar cadeia de conexão](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting.png)

1. Depois que a nova configuração tiver sido adicionada à lista, atualize a linha na lista com as informações necessárias.

    ![Nova cadeia de conexão](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting-new-setting.png)

    - **Nome** — digite o nome que deseja usar para a cadeia de conexão.
    - **Tipo** — selecione **Cadeia de Conexão** na lista suspensa.
    - **Valor** — você pode inserir a cadeia de conexão diretamente na célula **Valor** ou selecionar as reticências (...) para trabalhar na caixa de diálogo **Criar Cadeia de Conexão de Armazenamento**.  

1. Na caixa de diálogo **Criar Cadeia de Conexão de Armazenamento**, selecione uma opção para **Cadeia de Conexão**. Em seguida, siga as instruções para a opção selecionada:

    - **Emulador de Armazenamento do Microsoft Azure** — se selecionar essa opção, as configurações restantes na caixa de diálogo serão desabilitadas, uma vez que se aplicam apenas ao Azure. Selecione **OK**.
    - **Sua assinatura** — se selecionar essa opção, use a lista suspensa para selecionar e entrar em uma conta da Microsoft ou adicionar uma conta da Microsoft. Selecione uma assinatura do Azure e a conta de armazenamento. Selecione **OK**.
    - **Credenciais inseridas manualmente** — digite o nome da conta de armazenamento e a chave primária ou secundária. Selecione uma opção para **Conexão** (HTTPS é recomendado na maioria dos cenários). Selecione **OK**.

1. Para excluir uma cadeia de conexão, selecione a cadeia de conexão e escolha **Remover Configuração**.

1. Na barra de ferramentas do Visual Studio, selecione **Salvar**.

## <a name="programmatically-access-a-connection-string"></a>Acessar uma cadeia de conexão de modo programático

As etapas a seguir mostram como acessar de modo programático uma cadeia de conexão usando C#.

1. Adicione o seguinte usando diretrizes a um arquivo C#, no qual você vai usar a configuração:

    ```csharp
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. O código a seguir ilustra um exemplo de como acessar uma cadeia de conexão. Substitua o espaço reservado &lt;ConnectionStringName> pelo valor apropriado. 

    ```csharp
    // Setup the connection to Azure Storage
    var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("<ConnectionStringName>"));
    ```

## <a name="add-custom-settings-to-use-in-your-azure-cloud-service"></a>Adicionar configurações personalizadas para serem usadas em seu serviço de nuvem do Azure
Configurações personalizadas no arquivo de configuração de serviço permitem que você adicione um nome e valor para uma cadeia de caracteres para uma configuração de serviço específica. Você pode optar por usar essa configuração para configurar um recurso no seu serviço de nuvem lendo o valor da configuração e usando esse valor para controlar a lógica em seu código. Você pode alterar esses valores de configuração de serviço sem precisar recompilar o pacote de serviço ou quando o serviço de nuvem estiver em execução. Seu código pode verificar se há notificações de quando uma configuração é alterada. Consulte [Evento RoleEnvironment.Changing](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx).

Você pode adicionar, remover ou modificar configurações personalizadas para suas configurações de serviço. Talvez você queira valores diferentes para essas cadeias de caracteres para configurações de serviço diferentes.

Ao usar um valor diferente para cada configuração de serviço, você não precisa usar cadeias de caracteres diferentes em seu serviço de nuvem ou modificar seu código quando publicar o serviço de nuvem no Azure. É possível usar o mesmo nome para a cadeia de caracteres no seu código e o valor será diferente, com base na configuração de serviço que você selecionar quando criar seu serviço de nuvem ou quando publicá-lo.

1. Crie ou abra um projeto de serviço de nuvem do Azure no Visual Studio.

1. Em **Gerenciador de Soluções**, expanda o nó do projeto. No nó **Funções**, clique com o botão direito do mouse na função que deseja atualizar e, no menu de contexto, selecione **Propriedades**.

    ![Menu de contexto da função do Azure no Gerenciador de Soluções](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Selecione a guia **Configurações**.

    ![Guia Configurações](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab.png)

1. Na lista **Configuração de Serviço**, selecione a configuração de serviço que deseja atualizar.

    ![Lista Configuração de Serviço](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-select-configuration.png)

1. Para adicionar uma configuração personalizada, selecione **Adicionar Configuração**.

    ![Adicionar configuração personalizada](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting.png)

1. Depois que a nova configuração tiver sido adicionada à lista, atualize a linha na lista com as informações necessárias.

    ![Nova configuração personalizada](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting-new-setting.png)

    - **Nome** — insira o nome da configuração.
    - **Tipo** — selecione **Cadeia de Caracteres** na lista suspensa.
    - **Valor** — insira o valor da configuração. Você pode inserir o valor diretamente na célula **Valor** ou selecionar as reticências (...) para inserir o valor na caixa de diálogo **Editar Cadeia de Caracteres**.  

1. Para excluir uma configuração personalizada, selecione-a e, em seguida, selecione **Remover Configuração**.

1. Na barra de ferramentas do Visual Studio, selecione **Salvar**.

## <a name="programmatically-access-a-custom-settings-value"></a>Acessar o valor de uma configuração personalizada de modo programático
 
As etapas a seguir mostram como acessar de modo programático uma configuração personalizada usando C#.

1. Adicione o seguinte usando diretrizes a um arquivo C#, no qual você vai usar a configuração:

    ```csharp
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. O código a seguir ilustra um exemplo de como acessar uma configuração personalizada. Substitua o espaço reservado &lt;SettingName> pelo valor apropriado. 
    
    ```csharp
    var settingValue = RoleEnvironment.GetConfigurationSettingValue("<SettingName>");
    ```

## <a name="manage-local-storage-for-each-role-instance"></a>Gerenciar o armazenamento local para cada instância de função
Você pode adicionar o armazenamento do sistema de arquivos local para cada instância de uma função. Os dados armazenados nesse armazenamento não podem ser acessados por outras instâncias da função para as quais os dados são armazenados, nem por outras funções.  

1. Crie ou abra um projeto de serviço de nuvem do Azure no Visual Studio.

1. Em **Gerenciador de Soluções**, expanda o nó do projeto. No nó **Funções**, clique com o botão direito do mouse na função que deseja atualizar e, no menu de contexto, selecione **Propriedades**.

    ![Menu de contexto da função do Azure no Gerenciador de Soluções](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Selecione a guia **Armazenamento Local**.

    ![Guia Armazenamento local](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab.png)

1. Na lista **Configuração de Serviço**, assegure-se de que **Todas as Configurações** esteja selecionada como as configurações de armazenamento local que se aplicam a todas as configurações de serviço. Qualquer outro valor faz com que todos os campos de entrada na página sejam desabilitados. 

    ![Lista Configuração de Serviço](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-service-configuration.png)

1. Para adicionar uma entrada de armazenamento local, selecione **Adicionar Armazenamento Local**.

    ![Adicionar armazenamento local](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-add-local-storage.png)

1. Depois que a nova entrada de armazenamento local tiver sido adicionada à lista, atualize a linha na lista com as informações necessárias.

    ![Nova entrada de armazenamento local](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-new-local-storage.png)

    - **Nome** — digite o nome que deseja usar para o novo armazenamento local.
    - **Tamanho (MB)** — digite o tamanho em MB que você precisa para o novo armazenamento local.
    - **Limpar na reciclagem da função** — selecione essa opção para remover os dados desse novo armazenamento local quando a máquina virtual da função for reciclada.

1. Para excluir uma entrada de armazenamento local, selecione a entrada e **Remover Armazenamento Local**.

1. Na barra de ferramentas do Visual Studio, selecione **Salvar**.

## <a name="programmatically-accessing-local-storage"></a>Acessar o armazenamento local de modo programático

Esta seção ilustra como acessar de modo programático o armazenamento local usando C# ao gravar um arquivo de texto de teste `MyLocalStorageTest.txt`.  

### <a name="write-a-text-file-to-local-storage"></a>Gravar um arquivo de texto no armazenamento local

O código a seguir mostra um exemplo de como gravar um arquivo de texto no armazenamento local. Substitua o espaço reservado &lt;LocalStorageName> pelo valor apropriado. 

    ```csharp
    // Retrieve an object that points to the local storage resource
    LocalResource localResource = RoleEnvironment.GetLocalResource("<LocalStorageName>");
    
    //Define the file name and path
    string[] paths = { localResource.RootPath, "MyLocalStorageTest.txt" };
    String filePath = Path.Combine(paths);
    
    using (FileStream writeStream = File.Create(filePath))
    {
        Byte[] textToWrite = new UTF8Encoding(true).GetBytes("Testing Web role storage");
        writeStream.Write(textToWrite, 0, textToWrite.Length);
    }

    ```

### <a name="find-a-file-written-to-local-storage"></a>Localizar um arquivo gravado no armazenamento local

Para exibir o arquivo criado pelo código na seção anterior, siga estas etapas:
    
1.  Na área de notificação do Windows, clique com o botão direito do mouse no ícone do Azure e, no menu de contexto, selecione **Mostrar Interface de Usuário do Emulador de Computação**. 

    ![Mostrar emulador de computação do Azure](./media/vs-azure-tools-configure-roles-for-cloud-service/show-compute-emulator.png)

1. Selecione a função web.

    ![Emulador de computação do Azure](./media/vs-azure-tools-configure-roles-for-cloud-service/compute-emulator.png)

1. No menu **Emulador de Computação do Microsoft Azure**, selecione **Ferramentas** > **Abrir repositório local**.

    ![Item de menu Abrir repositório local](./media/vs-azure-tools-configure-roles-for-cloud-service/compute-emulator-open-local-store-menu.png)

1. Quando a janela do Windows Explorer for aberta, digite "MyLocalStorageTest.txt" na caixa de texto **Pesquisar** e selecione **Enter** para iniciar a pesquisa. 

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre projetos do Azure no Visual Studio, lendo [Configurando um projeto do Azure](vs-azure-tools-configuring-an-azure-project.md). Saiba mais sobre o esquema do serviço de nuvem lendo [Referência de Esquema](https://msdn.microsoft.com/library/azure/dd179398).

