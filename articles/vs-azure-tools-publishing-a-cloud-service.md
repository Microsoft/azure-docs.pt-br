---
title: "Publicando um serviço de nuvem usando as ferramentas do Azure | Microsoft Docs"
description: "Saiba mais sobre como publicar projetos de serviço de nuvem do Azure usando o Visual Studio."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 1a07b6e4-3678-4cbf-b37e-4520b402a3d9
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/14/2017
ms.author: kraigb
ms.openlocfilehash: e617d600dbc8287eea737fc4969833e873365288
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="publishing-a-cloud-service-using-the-azure-tools"></a>Publicando um serviço de nuvem usando as ferramentas do Azure
Usando as ferramentas do Azure para Microsoft Visual Studio, você pode publicar seu aplicativo do Azure diretamente do Visual Studio. O Visual Studio dá suporte à publicação integrada para o ambiente de teste ou de produção de um serviço de nuvem.

Para publicar um aplicativo do Azure, você deve ter uma assinatura do Azure. Você também deve configurar uma conta de armazenamento e serviço de nuvem a ser usada pelo aplicativo. Você pode configurá-los no [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

> [!IMPORTANT]
> Ao publicar, você pode selecionar o ambiente de implantação para seu serviço de nuvem. Você também deve selecionar uma conta de armazenamento que é usada para armazenar o pacote de aplicativos para implantação. Após a implantação, o pacote de aplicativos é removido da conta de armazenamento.
> 
> 

Ao desenvolver e testar um aplicativo do Azure, você pode usar a implantação da Web para publicar alterações incrementalmente para funções Web. Depois de publicar seu aplicativo para um ambiente de implantação, a Implantação da Web permite implantar alterações diretamente na máquina virtual que está executando a função Web. Você não precisa empacotar e publicar o aplicativo do Azure inteiro sempre que desejar atualizar a função Web para testar as alterações. Com essa abordagem, você pode ter suas alterações de função da Web disponíveis na nuvem para testes sem aguardar a publicação do aplicativo em um ambiente de implantação.

Use os procedimentos a seguir para publicar seu aplicativo do Azure e atualizar uma função Web usando a implantação da Web:

* Publicar ou empacotar um aplicativo do Azure do Visual Studio
* Atualizar uma função Web como parte do ciclo de desenvolvimento e teste

## <a name="publish-or-package-an-azure-application-from-visual-studio"></a>Publicar ou empacotar um aplicativo do Azure do Visual Studio
Ao publicar seu aplicativo do Azure, você pode executar uma das seguintes tarefas:

* Criar um pacote de serviço: você pode usar esse pacote e o arquivo de configuração de serviço para publicar seu aplicativo para um ambiente de implantação do [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885).
* Publicar seu projeto do Azure do Visual Studio: para publicar seu aplicativo diretamente no Azure, use o Assistente de Publicação. Para obter informações, consulte [Assistente de Publicação de Aplicativo do Azure](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-create-a-service-package-from-visual-studio"></a>Para criar um pacote de serviço do Visual Studio
1. Quando estiver pronto para publicar seu aplicativo, abra o Gerenciador de Soluções, abra o menu de atalho do projeto do Azure que contém suas funções e selecione Publicar.
2. Para criar um pacote de serviço, siga estas etapas:  
   
   1. No menu de atalho do projeto do Azure, escolha **Pacote**.
   2. Na caixa de diálogo **Empacotar Aplicativo do Azure** , escolha a configuração de serviço para a qual você deseja criar um pacote e, em seguida, escolha a configuração de compilação.
   3. (opcional) Para ativar a Área de Trabalho Remota para o serviço de nuvem depois de publicá-lo, marque a caixa de seleção **Habilitar Área de Trabalho Remota para todas as Funções** e selecione **Configurações** para configurar a Área de Trabalho Remota. Se você quiser depurar seu serviço de nuvem após publicá-lo, ative a depuração remota, selecionando **Ativar o Depurador Remoto para todas as Funções**.
      
      Para saber mais, confira [Usando a Área de Trabalho Remota com as funções do Azure](vs-azure-tools-remote-desktop-roles.md).
   4. Para criar o pacote, escolha o link **pacote** .
      
      O Explorador de Arquivos mostra o local do arquivo do pacote recém-criado. Você pode copiar esse local para que possa usá-lo do [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885).
   5. Para publicar esse pacote em um ambiente de implantação, você deve usar esse local como o local do pacote ao criar um serviço de nuvem e implantar esse pacote em um ambiente com o [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885).
3. (Opcional) Para cancelar o processo de implantação, no menu de atalho do item de linha no log de atividades, escolha **Cancelar e remover**. Isso interrompe o processo de implantação e exclui o ambiente de implantação do Azure.
   
   > [!NOTE]
   > Para remover este ambiente de implantação após ele ter sido implantado, você deve usar o [Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885).
   > 
   > 
4. (Opcional) Após ter iniciado as instâncias de função, o Visual Studio mostra automaticamente o ambiente de implantação no nó **Serviços de Nuvem** no Gerenciador de Servidores. Nele, você pode ver o status das instâncias de função individuais. Confira [Gerenciando recursos de Azure com o Cloud Explorer](vs-azure-tools-resources-managing-with-cloud-explorer.md). A ilustração a seguir mostra as instâncias de função enquanto elas ainda estão no estado Inicializando:
   
    ![VST_DeployComputeNode](./media/vs-azure-tools-publishing-a-cloud-service/IC744134.png)

## <a name="update-a-web-role-as-part-of-the-development-and-testing-cycle"></a>Atualizar uma função Web como parte do ciclo de desenvolvimento e teste
Se a infraestrutura de back-end do aplicativo for estável, mas as funções Web precisarem de atualizações mais frequentes, você poderá usar a implantação da Web para atualizar apenas uma função Web em seu projeto. Isso é útil quando você não deseja recriar e reimplantar as funções de trabalho de back-end ou se tem várias funções Web e deseja atualizar somente uma delas.

### <a name="requirements"></a>Requisitos
Aqui estão os requisitos para usar a Implantação da Web para atualizar a função Web:

* **Apenas para fins de desenvolvimento e teste:** as alterações são feitas diretamente na máquina virtual em que a função Web está sendo executada. Se essa máquina virtual precisar ser reciclada, as alterações serão perdidas porque o pacote original que você publicou é usado para recriar a máquina virtual para a função. Você deve republicar seu aplicativo para obter as alterações mais recentes para a função Web.
* **Apenas funções Web podem ser atualizadas:** funções de trabalho não podem ser atualizadas. Além disso, você não pode atualizar o RoleEntryPoint no role.cs da Web.
* **Só há suporte a uma única instância de uma função Web:** não pode haver várias instâncias da função Web em seu ambiente de implantação. No entanto, há suporte para várias funções Web, cada com apenas uma instância.
* **Você deve habilitar conexões de área de trabalho remota:** isso é necessário para que a Implantação da Web possa usar o usuário e a senha para se conectar à máquina virtual e implantar as alterações no servidor que está executando o IIS (Serviços de Informações da Internet). Além disso, talvez seja necessário conectar-se à máquina virtual para adicionar um certificado confiável ao IIS nessa máquina virtual. (Isso garante que a conexão remota ao IIS usada pela implantação da Web é segura).

O procedimento a seguir supõe que você esteja usando o assistente para **Publicar Aplicativo do Azure** .

### <a name="to-enable-web-deploy-when-you-publish-your-application"></a>Para habilitar a Implantação da Web ao publicar seu aplicativo
1. Para habilitar a caixa de seleção de **Habilitar Implantação da Web** para todas as funções Web, primeiro você deve configurar conexões de área de trabalho remota. Escolha **Habilitar Área de Trabalho Remota** para todas as funções e, em seguida, forneça as credenciais que serão usadas para se conectar remotamente na caixa **Configuração da Área de Trabalho Remota** que é mostrada. Consulte [Usando a Área de Trabalho Remota com funções do Azure](vs-azure-tools-remote-desktop-roles.md) para obter mais informações.
2. Para habilitar a Implantação da Web para todas as funções Web em seu aplicativo, selecione **Habilitar Implantação da Web para todas as funções Web**.
   
    Um triângulo amarelo de aviso será exibido. A Implantação da Web usa um certificado não confiável, autoassinado por padrão, o que não é recomendado para carregar dados confidenciais. Se precisar proteger esse processo para dados confidenciais, você poderá adicionar um certificado SSL a ser usado para conexões de Implantação da Web. Esse deve ser um certificado confiável. Para obter informações sobre como fazer isso, consulte a seção **Para tornar a Implantação da Web segura** , mais adiante neste tópico.
3. Escolha **Avançar** para mostrar a tela **Resumo** e, em seguida, escolha **Publicar** para implantar o serviço de nuvem.
   
    O serviço de nuvem é publicado. A máquina virtual que é criada tem conexões remotas habilitadas para o IIS para que a Implantação da Web possa ser usada para atualizar suas funções Web sem as publicar novamente.
   
   > [!NOTE]
   > Se você tiver mais de uma instância configurada para uma função Web, será exibida uma mensagem de aviso informando que cada função Web será limitada a uma instância somente no pacote que é criado para publicar seu aplicativo. Selecione **OK** para continuar. Conforme mencionado na seção Requisitos, você pode ter mais de uma função Web, mas apenas uma instância de cada função.
   > 
   > 

### <a name="to-update-your-web-role-by-using-web-deploy"></a>Para atualizar sua função Web usando a Implantação da Web
1. Para usar a Implantação da Web, faça alterações de código no projeto para qualquer uma das funções Web no Visual Studio que você deseja publicar, clique com o botão direito no nó do projeto na solução e aponte para **Publicar**. A caixa de diálogo **Publicar Web** é exibida.
2. (Opcional) Se adicionou um certificado SSL confiável a ser usado para conexões remotas para o IIS, você pode desmarcar a caixa de seleção **Permitir certificado não confiável** . Para obter informações sobre como adicionar um certificado para proteger a Implantação da Web, consulte a seção **Para tornar a Implantação da Web segura** , mais adiante neste tópico.
3. Para usar a Implantação da Web, o mecanismo de publicação precisa do nome de usuário e da senha que você configurou para a conexão de área de trabalho remota ao publicar o pacote pela primeira vez.
   
   1. Em **Nome de usuário**, digite o nome de usuário.
   2. Em **Senha**, digite a senha.
   3. (Opcional) Se você quiser salvar esta senha nesse perfil, escolha **Salvar senha**.
4. Para publicar as alterações em sua função Web, escolha **Publicar**.
   
    A linha de status exibe **Publicação iniciada**. Quando a publicação for concluída, a mensagem **Publicação bem-sucedida** será exibida. Agora as alterações foram implantadas na função Web em sua máquina virtual. Você pode então iniciar o aplicativo do Azure no ambiente do Azure para testar as alterações.

### <a name="to-make-web-deploy-secure"></a>Para tornar a Implantação da Web segura
1. A Implantação da Web usa um certificado não confiável, autoassinado por padrão, o que não é recomendado para carregar dados confidenciais. Se precisar proteger esse processo para dados confidenciais, você poderá adicionar um certificado SSL a ser usado para conexões de Implantação da Web. Esse deve ser um certificado confiável, que você obtém de uma CA (autoridade de certificação).
   
    Para tornar a Implantação da Web segura para cada máquina virtual de cada uma das funções web, é necessário carregar o certificado de confiança que você deseja usar para a implantação da Web no [portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885). Isso garante que o certificado seja adicionado à máquina virtual que é criada para a função Web quando você publicar seu aplicativo.
2. Para adicionar um certificado SSL confiável ao IIS a ser usado em conexões remotas, siga estas etapas:
   
   1. Para se conectar à máquina virtual que está executando a função web, selecione a instância da função web em **Cloud Explorer** ou **Gerenciador de Servidores** e escolha o comando **Conectar-se usando a Área de Trabalho Remota**. Para obter etapas detalhadas sobre como se conectar à máquina virtual, consulte [Usando a Área de Trabalho Remota com funções do Azure](vs-azure-tools-remote-desktop-roles.md).
      
      O navegador solicitará que você baixe um arquivo .RDP.
   2. Para adicionar um certificado SSL, abra o serviço de gerenciamento no Gerenciador do IIS. No Gerenciador do IIS, habilite o SSL abrindo o link **Ligações** no painel **Ação**. A caixa de diálogo **Adicionar Ligação do Site** é exibida. Escolha **Adicionar** e selecione HTTPS na lista suspensa **Tipo**. Na lista **Certificado SSL** , escolha o certificado SSL que foi assinado por uma AC e que você carregou no [portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885). Para obter mais informações, consulte [Definir configurações de conexão para o serviço de gerenciamento](http://go.microsoft.com/fwlink/?LinkId=215824).
      
      > [!NOTE]
      > Se você adicionar um certificado SSL confiável, o triângulo amarelo de aviso não será mais exibido no **Assistente de Publicação**.
      > 
      > 

## <a name="include-files-in-the-service-package"></a>Incluir arquivos no pacote de serviço
Talvez seja necessário incluir arquivos específicos em seu pacote de serviço para que eles estejam disponíveis na máquina virtual que é criada para uma função. Por exemplo, você talvez queira adicionar um arquivo .exe ou .msi que é usado por um script de inicialização para o pacote de serviço. Ou talvez seja necessário adicionar um assembly que uma função Web ou um projeto de função de trabalho requer. Para incluir arquivos, eles devem ser adicionados à solução para seu aplicativo do Azure.

### <a name="to-include-files-in-the-service-package"></a>Para incluir arquivos no pacote de serviço
1. Para adicionar um assembly a um pacote de serviço, use as seguintes etapas:
   
   1. No **Gerenciador de Soluções** , abra o nó do projeto que está sem o assembly referenciado.
   2. Para adicionar o assembly ao projeto, abra o menu de atalho da pasta **Referências** e escolha **Adicionar Referência**. A caixa de diálogo Adicionar Referência é exibida.
   3. Escolha a referência que você deseja adicionar e, em seguida, escolha o botão **OK** .
      
      A referência é adicionada à lista na pasta **Referências** .
   4. Abra o menu de atalho do assembly que você adicionou e escolha **Propriedades**. A janela **Propriedades** é exibida.
      
      Para incluir esse assembly no pacote de serviço, na lista **Local da Cópia**, escolha **Verdadeiro**.
2. No **Gerenciador de Soluções** , abra o nó do projeto que está sem o assembly referenciado.
3. Para adicionar o assembly ao projeto, abra o menu de atalho da pasta **Referências** e escolha **Adicionar Referência**. A caixa de diálogo **Adicionar Referência** é exibida.
4. Escolha a referência que você deseja adicionar e, em seguida, escolha o botão **OK** .
   
    A referência é adicionada à lista na pasta **Referências** .
5. Abra o menu de atalho do assembly que você adicionou e escolha **Propriedades**. A janela Propriedades é exibida.
6. Para incluir esse assembly no pacote de serviço, na lista **Local da Cópia**, escolha **Verdadeiro**.
7. Para incluir arquivos no pacote de serviço que foram adicionados ao projeto de função Web, abra o menu de atalho do arquivo e escolha **Propriedades**. Na janela **Propriedades**, escolha **Conteúdo** na caixa de listagem **Criar Ação**.
8. Para incluir arquivos no pacote de serviço que foram adicionados ao projeto de função de trabalho, abra o menu de atalho do arquivo e escolha **Propriedades**. Na janela **Propriedades**, escolha **Copiar se for mais recente** na caixa de listagem **Copiar para diretório de saída**.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a publicação do Azure por meio do Visual Studio, consulte o [Assistente de Publicação de Aplicativo do Azure](vs-azure-tools-publish-azure-application-wizard.md).

