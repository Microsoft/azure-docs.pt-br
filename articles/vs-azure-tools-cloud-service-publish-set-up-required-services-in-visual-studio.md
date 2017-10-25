---
title: Preparar para publicar ou implantar um aplicativo do Azure do Visual Studio | Microsoft Docs
description: "Conheça os procedimentos para configurar serviços de conta de armazenamento e de nuvem e configurar seu aplicativo do Azure."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 92ee2f9e-ec49-4c7a-900d-620abe5e9d8a
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: kraigb
ms.openlocfilehash: cc4fb87e559f554634ae062a59bee31f0831da64
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="prepare-to-publish-or-deploy-an-azure-application-from-visual-studio"></a>Preparar para publicar ou implantar um aplicativo do Azure do Visual Studio
## <a name="overview"></a>Visão geral
Antes de publicar um projeto de serviço de nuvem, você deve configurar os seguintes serviços:

* Um **serviço de nuvem** para executar suas funções no ambiente do Azure
* Uma **conta de armazenamento** que forneça acesso aos serviços de blob, fila e tabela.

Use os procedimentos a seguir para configurar esses serviços e configurar seu aplicativo

## <a name="create-a-cloud-service"></a>Criar um Serviço de Nuvem
Para publicar um serviço de nuvem no Azure, crie primeiro um serviço de nuvem que executa suas funções no ambiente do Azure. Você pode criar um serviço de nuvem no [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885), conforme descrito na seção **Para criar um serviço de nuvem usando o Portal Clássico do Azure**mais adiante neste tópico. Você também pode criar um serviço de nuvem no Visual Studio usando o Assistente de publicação.

### <a name="to-create-a-cloud-service-by-using-visual-studio"></a>Criar um serviço de nuvem usando o Visual Studio
1. Abra o menu de atalho do projeto do Azure e escolha **publicar**.

    ![VST_PublishMenu](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/vst-publish-menu.png)
2. Se você ainda não tiver entrado, entre com seu nome de usuário e senha da conta da Microsoft ou conta organizacional associada à sua assinatura do Azure.
3. Escolha o botão **Próximo** para avançar para a página de **Configurações**.

    ![Configurações comuns de assistente de publicação](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/publish-settings-page.png)
4. Na lista **Serviços de nuvem** escolha **Criar novo**. A caixa de diálogo **Criar serviços do Azure** é exibida.
5. Insira o nome de seu serviço de nuvem. O nome faz parte da URL do serviço e, portanto, deve ser globalmente exclusivo. O nome não diferencia maiúsculas de minúsculas.

### <a name="to-create-a-cloud-service-by-using-the-azure-classic-portal"></a>Para criar um serviço de nuvem usando o Portal Clássico do Azure
1. Entre no [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkId=253103) no site da Microsoft.
2. (opcional) Para exibir uma lista de serviços de nuvem que você criou, escolha o link Serviços de Nuvem no lado esquerdo da página.
3. Selecione o **+** ícone no canto inferior esquerdo e, em seguida, selecione **Serviço de nuvem** no menu que é exibido. Outra tela com duas opções, **Criação rápida** e **Criação personalizada**, é exibida. Se escolher **Criação rápida**, você poderá criar um serviço de nuvem apenas especificando a URL e a região onde ele será fisicamente hospedado. Se você escolher **Criação personalizada**, você pode imediatamente publicar um serviço de nuvem, especificando um pacote (arquivo .cspkg), um arquivo de configuração (.cscfg), e um certificado. Criação personalizada não é necessária se você pretende publicar seu serviço de nuvem usando o comando **Publicar** em um projeto do Azure. O comando **Publicar** está disponível no menu de atalho para um projeto do Azure.
4. Escolha **criação rápida** para publicar posteriormente seu serviço de nuvem usando o Visual Studio.
5. Especifique um nome para seu serviço de nuvem. A URL completa é exibida ao lado do nome.
6. Na lista, escolha a região onde se encontra a maioria dos usuários.
7. Na parte inferior da janela, escolha o link **Criar serviço de nuvem** .

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
Uma conta de armazenamento fornece acesso aos serviços de blob, fila e tabela. Você pode criar uma conta de armazenamento usando o Visual Studio ou o [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkId=253103).

### <a name="to-create-a-storage-account-by-using-visual-studio"></a>Para criar uma conta de armazenamento usando o Visual Studio
1. Em **Gerenciador de Soluções**, abra o menu de atalho para o nó de **Armazenamento** e selecione **Criar Conta de Armazenamento**.

    ![Criar uma nova conta de armazenamento do Azure](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/IC744166.png)
2. Selecione ou insira as seguintes informações para a nova conta de armazenamento na caixa de diálogo **Criar Conta de Armazenamento** .

   * A assinatura do Azure à qual você deseja adicionar a conta de armazenamento.
   * O nome que você deseja usar para a nova conta de armazenamento.
   * A região ou o grupo de afinidade (como Oeste dos EUA ou Leste Asiático).
   * O tipo de replicação que você deseja usar para a conta de armazenamento, como redundância geográfica.
3. Quando terminar, selecione **Criar**. A nova conta de armazenamento é exibida na lista **Armazenamento** no **Gerenciador de Servidores**.

### <a name="to-create-a-storage-account-by-using-the-azure-classic-portal"></a>Para criar uma conta de armazenamento usando o Portal Clássico do Azure
1. Entre no [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkId=253103) no site da Microsoft.
2. (Opcional) Para exibir suas contas de armazenamento, selecione o link **Armazenamento** no painel à esquerda da página.
3. No canto inferior esquerdo da página, selecione o ícone **+** .
4. No menu que é exibido, selecione **Armazenamento** e, em seguida, selecione **Criação rápida**.
5. Nomeie a conta de armazenamento que resulta em uma URL exclusiva.
6. Dê um nome ao serviço de nuvem. A URL completa é exibida ao lado do nome.
7. Na lista de regiões, escolha a região onde se encontra a maioria dos usuários.
8. Especifique se deseja habilitar a replicação geográfica. Se você habilitar a replicação geográfica, seus dados serão salvos em vários locais físicos para reduzir a possibilidade de perda. Esse recurso torna o armazenamento mais caro, mas você pode reduzir o custo, permitindo a localização geográfica ao criar a conta de armazenamento em vez de adicionar o recurso mais tarde. Para obter mais informações, consulte [Replicação geográfica](http://go.microsoft.com/fwlink/?LinkId=253108).
9. Na parte inferior da janela, escolha o link **Criar conta de armazenamento** .

Depois de criar sua conta de armazenamento, você verá as URLs que pode usar para acessar recursos em cada um dos serviços de armazenamento do Azure e as chaves de acesso primária e secundária para sua conta. Você pode usar essas chaves para autenticar solicitações feitas nos serviços de armazenamento.

> [!NOTE]
> A chave de acesso secundária fornece o mesmo acesso à sua conta de armazenamento que a chave de acesso primária e é gerada como um backup, caso sua chave de acesso primária seja comprometida. Além disso, é recomendável que você gere novamente e regularmente suas chaves de acesso. Você pode modificar uma configuração de cadeia de conexão para usar a chave secundária enquanto gera novamente a chave primária, e então poderá modificá-la para usar a chave primária gerada enquanto gera novamente a chave secundária.
>
>

## <a name="configure-your-app-to-use-services-provided-by-the-storage-account"></a>Configurar seu aplicativo para usar os serviços fornecidos pela conta de armazenamento
Configure qualquer função que acessa os serviços de armazenamento para usar os serviços de armazenamento do Azure que você criou. Para fazer isso, você pode usar várias configurações de serviço para o seu projeto do Azure. Por padrão, duas são criadas no projeto do Azure. Usando várias configurações de serviço, você pode usar a mesma cadeia de conexão no seu código, mas ter um valor diferente para uma cadeia de conexão em cada configuração de serviço. Por exemplo, você pode usar uma configuração de serviço para executar e depurar seu aplicativo localmente usando o emulador de armazenamento do Azure e uma configuração de serviço diferente para publicar seu aplicativo no Azure. Para obter mais informações sobre configurações de serviço, consulte [Configurando o Projeto do Azure usando várias configurações de serviço](vs-azure-tools-multiple-services-project-configurations.md).

### <a name="to-configure-your-application-to-use-services-that-the-storage-account-provides"></a>Para configurar seu aplicativo para usar os serviços que a conta de armazenamento fornece
1. Abra sua solução Azure no Visual Studio. No Gerenciador de Soluções, abra o menu de atalho para cada função no projeto do Azure que acessa os serviços de armazenamento e escolha **Propriedades**. Uma página com o nome da função é exibida no editor do Visual Studio. A página exibe os campos para a guia **Configuração** .
2. Nas páginas de propriedades para a função, escolha **configurações**.
3. Na lista **Configuração do Serviço** , escolha o nome da configuração do serviço que você deseja editar. Se quiser fazer alterações em todas as configurações de serviço para essa função, você poderá escolher **Todas as Configurações**.  Para obter mais informações sobre como atualizar as configurações de serviço, consulte a seção **Gerenciar cadeias de conexão para contas de armazenamento** no tópico [Configurar as funções para um serviço de nuvem do Azure com o Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).
4. Para modificar as configurações de cadeia de conexão, escolha o botão **...** ao lado da configuração. A caixa de diálogo **Criar Cadeia de Conexão de Armazenamento** é exibida.
5. Em **Conectar usando**, escolha a opção **Sua assinatura**.
6. Na lista **assinatura** , escolha sua assinatura. Se a lista de assinaturas não incluir aquela à qual você deseja, escolha o link **baixar configurações de publicação** .
7. Na lista **nome da conta** , escolha o nome da sua conta de armazenamento. As ferramentas do Azure obtêm credenciais da conta de armazenamento automaticamente usando o arquivo .publishsettings. Para especificar as credenciais de conta de armazenamento manualmente, escolha a opção **credenciais inseridas manualmente** e, em seguida, continue com este procedimento. Você pode obter o nome da conta de armazenamento e a chave primária do [Portal Clássico do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885). Se você não deseja especificar configurações de conta de armazenamento manualmente, escolha o botão **OK** para fechar a caixa de diálogo.
8. Selecione o link **Inserir conta de armazenamento** .
9. No **Nome da conta** digite o nome da sua conta de armazenamento.

   > [!NOTE]
   > Faça logon no [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885)e, em seguida, selecione o botão **Armazenamento** . O portal mostra uma lista de contas de armazenamento. Se você escolher uma conta, uma página será aberta para ela. Você pode copiar o nome da conta de armazenamento nessa página. Se você estiver usando uma versão anterior do portal clássico, o nome da sua conta de armazenamento aparecerá na exibição **Contas de Armazenamento** . Para copiar esse nome, realce-na janela **Propriedades** desta exibição, em seguida, escolha as teclas Ctrl-C. Para colar o nome no Visual Studio, selecione a caixa de texto **Nome da conta** e, em seguida, selecione as teclas Ctrl+V.
   >
   >
10. No caixa **Chave de conta** , digite sua chave primária ou copie e cole-a do [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885).
     Para copiar essa chave:

    1. Na parte inferior da página para a conta de armazenamento apropriado, selecione o botão **Gerenciar chaves** .
    2. Na página **Gerenciar chaves de acesso** , selecione o texto da tecla de acesso primário e, em seguida, selecione as teclas Ctrl+C.
    3. Em Ferramentas do Azure, cole a chave para a caixa **Chave de conta** .
    4. Você deve selecionar uma das opções a seguir para determinar como o serviço acessará a conta de armazenamento:

       * **Usar HTTP**. Essa é a opção padrão. Por exemplo: `http://<account name>.blob.core.windows.net`.
       * **Use HTTPS** para uma conexão segura. Por exemplo: `https://<accountname>.blob.core.windows.net`.
       * **Especifique pontos de extremidade personalizados** para cada um dos três serviços. Em seguida, você pode digitar esses pontos de extremidade no campo para o serviço específico.

         > [!NOTE]
         > Se você criar pontos de extremidade personalizados, poderá criar uma cadeia de conexão mais complexa. Ao usar esse formato de cadeia de caracteres, você pode especificar pontos de extremidade de serviço de armazenamento que incluem um nome de domínio personalizado que você registrou para sua conta de armazenamento com o serviço Blob. Além disso, você pode conceder acesso apenas aos recursos de blob em um único contêiner por meio de uma assinatura de acesso compartilhado. Para obter mais informações sobre como criar pontos de extremidade personalizados, consulte [Configurar cadeias de conexão de armazenamento do Azure](storage/common/storage-configure-connection-string.md).
         >
         >
11. Para salvar essas alterações da cadeia de conexão, selecione o botão **OK** e, em seguida, selecione o botão **Salvar** na barra de ferramentas. Após salvar essas alterações, você pode obter o valor dessa cadeia de conexão no seu código usando [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx). Quando você publica seu aplicativo no Azure, escolhe a configuração do serviço que contém a conta de armazenamento do Azure para a cadeia de conexão. Depois de publicar o aplicativo, verifique se ele funciona conforme esperado nos serviços de armazenamento do Azure

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre publicação de aplicativos para o Azure do Visual Studio, consulte [Publicando um serviço de nuvem usando as ferramentas do Azure](vs-azure-tools-publishing-a-cloud-service.md).
