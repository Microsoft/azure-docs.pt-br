---
title: "Implantar a extensão do Painel de Acesso do Azure do IE usando um GPO | Microsoft Docs"
description: "Como usar a política de grupo para implantar o complemento do Internet Explorer para o portal de meus aplicativos."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 7c2d49c8-5be0-4e7e-abac-332f9dfda736
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/02/2017
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b402ae326ab34ec71ad9de966e22be00045fee3e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>Como implantar a Extensão do Painel de Acesso no Internet Explorer usando a Política de Grupo
Este tutorial mostra como usar a política de grupo para instalar remotamente a extensão do Painel de Acesso para o Internet Explorer nos computadores dos usuários. Essa extensão é necessária para os usuários do Internet Explorer que precisam entrar em aplicativos configurados usando o [logon único baseado em senha](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

É recomendável que administradores automatizem a implantação dessa extensão. Caso contrário, os usuários terão de baixar e instalar a extensão por conta própria, o que poderá causar erros do usuário e que exigirá permissões de administrador. Este tutorial apresenta um método de automatização de implantações de software usando a política de grupo. [Saiba mais sobre a política de grupo.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

A extensão do Painel de Acesso também está disponível para o [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) e o [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998) e nenhum deles exige permissões de administrador para instalar.

## <a name="prerequisites"></a>Pré-requisitos
* Você configurou os [Serviços de Domínio do Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)e os computadores dos usuários ingressaram no domínio.
* Você deve ter a permissão "Editar configurações" para editar o GPO (Objeto de Política de Grupo). Por padrão, os membros dos grupos de segurança a seguir têm esta permissão: Administradores de Domínio, Administradores de Empresa e Proprietários Criadores de Política de Grupo. [Saiba mais.](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

## <a name="step-1-create-the-distribution-point"></a>Etapa 1: Criar o ponto de distribuição
Primeiro, você deve colocar o pacote do instalador em um local de rede que possa ser acessado pelos computadores nos quais você deseja instalar remotamente a extensão. Para fazer isso, siga estas etapas:

1. Faça logon no servidor como um administrador
2. Na janela **Gerenciador do Servidor**, vá para **Arquivos e Serviços de Armazenamento**.
   
    ![Abrir Serviços de Arquivo e Armazenamento](./media/active-directory-saas-ie-group-policy/files-services.png)
3. Vá para a guia **Compartilhamentos** . Em seguida, clique em **Tarefas** > **Novo Compartilhamento...**
   
    ![Abrir Serviços de Arquivo e Armazenamento](./media/active-directory-saas-ie-group-policy/shares.png)
4. Conclua o **Assistente de Novo Compartilhamento** e defina permissões para garantir que ele possa ser acessado dos computadores dos usuários. [Saiba mais sobre compartilhamentos.](https://technet.microsoft.com/library/cc753175.aspx)
5. Baixe o seguinte pacote do Microsoft Windows Installer (arquivo .msi): [Access Panel Extension.msi](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access Panel Extension.msi)
6. Copie o pacote do instalador para um local desejado no compartilhamento.
   
    ![Copie o arquivo .msi para o compartilhamento.](./media/active-directory-saas-ie-group-policy/copy-package.png)
7. Verifique se os computadores do cliente podem acessar o pacote do instalador desde o compartilhamento. 

## <a name="step-2-create-the-group-policy-object"></a>Etapa 2: Criar o Objeto de Política de Grupo
1. Faça logon no servidor que hospeda sua instalação dos Serviços de Domínio do Active Directory (AD DS).
2. No Gerenciador do Servidor, vá para **Ferramentas** > **Gerenciamento de Política de Grupo**.
   
    ![Vá para Ferramentas > Gerenciamento de Política de Grupo](./media/active-directory-saas-ie-group-policy/tools-gpm.png)
3. No painel esquerdo da janela **Gerenciamento de Política de Grupo** , exiba sua hierarquia de UO (unidade organizacional) e determine em qual escopo você gostaria de aplicar a política de grupo. Por exemplo, você poderá optar por escolher uma UO pequena a ser implantada para alguns usuários para teste ou poderá escolher uma UO de nível superior a ser implantada em toda a sua organização.
   
   > [!NOTE]
   > Se você quiser criar ou editar suas Unidades Organizacionais (UOs), volte para o Gerenciador do Servidor e vá para **Ferramentas** > **Usuários e Computadores do Active Directory**.
   > 
   > 
4. Depois de selecionar uma UO, clique com o botão direito do mouse nela e selecione **Criar um GPO neste domínio e vinculá-lo aqui...**
   
    ![Criar um novo GPO](./media/active-directory-saas-ie-group-policy/create-gpo.png)
5. No prompt **Novo GPO** , digite um nome para o novo Objeto de Política de Grupo.
   
    ![Dar um nome ao novo GPO](./media/active-directory-saas-ie-group-policy/name-gpo.png)
6. Clique com o botão direito do mouse no Objeto de Política de Grupo criado e selecione **Editar**.
   
    ![Editar o novo GPO](./media/active-directory-saas-ie-group-policy/edit-gpo.png)

## <a name="step-3-assign-the-installation-package"></a>Etapa 3: Atribuir o pacote de instalação
1. Determine se você deseja implantar a extensão com base na **Configuração do Computador** ou na **Configuração do Usuário**. Ao usar a [configuração do computador](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx), a extensão será instalada no computador, independentemente dos usuários que fizerem logon. Com a [configuração do usuário](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx), os usuários têm a extensão instalada para eles, independentemente dos computadores em que eles fizerem logon.
2. No painel esquerdo da janela **Editor de Gerenciamento de Política de Grupo** , vá para qualquer um dos seguintes caminhos de pasta, dependendo do tipo de configuração escolhida:
   
   * `Computer Configuration/Policies/Software Settings/`
   * `User Configuration/Policies/Software Settings/`
3. Clique com o botão direito do mouse em **Instalação do software** e selecione **Novo** > **Pacote...**
   
    ![Criar um novo pacote de instalação de software](./media/active-directory-saas-ie-group-policy/new-package.png)
4. Vá para a pasta compartilhada que contém o pacote do instalador da [Etapa 1: Criar o ponto de distribuição](#step-1-create-the-distribution-point), selecione o arquivo .msi e clique em **Abrir**.
   
   > [!IMPORTANT]
   > Se o compartilhamento estiver localizado no mesmo servidor, verifique se você está acessando o .msi por meio do caminho do arquivo de rede, em vez do caminho do arquivo local.
   > 
   > 
   
    ![Selecione o pacote de instalação na pasta compartilhada.](./media/active-directory-saas-ie-group-policy/select-package.png)
5. No prompt **Implantar Software**, selecione **Atribuído** para o método de implantação. Em seguida, clique em **OK**.
   
    ![Selecione Atribuído e clique em OK.](./media/active-directory-saas-ie-group-policy/deployment-method.png)

Agora a extensão está implantada na UO que você selecionou. [Saiba mais sobre a instalação do Software de Política de Grupo.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

## <a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>Etapa 4: Habilitar automaticamente a extensão do Internet Explorer
Além da execução do instalador, todas as extensões do Internet Explorer deverão ser habilitadas explicitamente antes de serem usadas. Siga as etapas abaixo para habilitar a Extensão do Painel de Acesso usando a política de grupo:

1. Na janela **Editor de Gerenciamento de Política de Grupo** , vá para um dos caminhos a seguir, dependendo do tipo de configuração escolhida na [Etapa 3: Atribuir o pacote de instalação](#step-3-assign-the-installation-package):
   
   * `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
2. Clique com o botão direito do mouse em **Lista de Complementos** e selecione **Editar**.
    ![Edite a Lista de Complementos.](./media/active-directory-saas-ie-group-policy/edit-add-on-list.png)
3. Na janela **Lista de Complementos**, selecione **Habilitado**. Em seguida, na seção **Opções**, clique em **Mostrar...**.
   
    ![Clique em Habilitar e clique em Mostrar...](./media/active-directory-saas-ie-group-policy/edit-add-on-list-window.png)
4. Na janela **Mostrar Conteúdo** , execute as seguintes etapas:
   
   1. Na primeira coluna (o campo **Nome do Valor**), copie e cole a seguinte ID de Classe: `{030E9A3F-7B18-4122-9A60-B87235E4F59E}`
   2. Na segunda coluna (o campo **Valor**), digite o seguinte valor: `1`
   3. Clique em **OK** para fechar a janela **Mostrar Conteúdo**.
      
      ![Preencha os valores como especificado acima.](./media/active-directory-saas-ie-group-policy/show-contents.png)
5. Clique em **OK** para aplicar as alterações e fechar a janela **Lista de Complementos**.

Agora a extensão deverá estar habilitada para os computadores na UO selecionada. [Saiba mais sobre como usar a política de grupo para habilitar ou desabilitar complementos do Internet Explorer.](https://technet.microsoft.com/library/dn454941.aspx)

## <a name="step-5-optional-disable-remember-password-prompt"></a>Etapa 5 (opcional): Desabilitar o prompt "Lembrar senha"
Quando os usuários entram em sites que usam a extensão do painel de acesso, o Internet Explorer mostra o seguinte prompt perguntando "Deseja armazenar sua senha?"

![](./media/active-directory-saas-ie-group-policy/remember-password-prompt.png)

Se você quiser impedir que os usuários vejam esse prompt, siga as etapas abaixo para impedir o preenchimento automático de lembrar senhas:

1. Na janela **Editor de gerenciamento de política de grupo** , vá para o caminho listado abaixo. Essa configuração só está disponível em **Configuração do usuário**.
   
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`
2. Localize a configuração denominada **Ativar o recurso de preenchimento automático para nomes de usuário e senhas em formulários**.
   
   > [!NOTE]
   > Versões anteriores do Active Directory podem listar essa configuração com o nome **Não permitir o preenchimento automático para salvar senhas**. Essa configuração é diferente da configuração descrita neste tutorial.
   > 
   > 
   
    ![Lembre-se de examinar isso em Configurações do usuário.](./media/active-directory-saas-ie-group-policy/disable-auto-complete.png)
3. Clique com o botão direito na configuração acima e selecione **Editar**.
4. Na janela **Ativar o recurso de preenchimento automático para nomes de usuário e senhas em formulários**, selecione **Desabilitado**.
   
    ![Selecione Desabilitar](./media/active-directory-saas-ie-group-policy/disable-passwords.png)
5. Clique em **OK** para aplicar essas alterações e fechar a janela.

Os usuários não poderão mais armazenar suas credenciais ou usar o preenchimento automático para acessar as credenciais armazenadas anteriormente. No entanto, essa política permite que os usuários continuem a usar o preenchimento automático para outros tipos de campos de formulário, como campos de pesquisa.

> [!WARNING]
> Se essa política for ativada depois que os usuários tiverem escolhido armazenar algumas credenciais, essa política *não* limpará as credenciais que já foram armazenadas.
> 
> 

## <a name="step-6-testing-the-deployment"></a>Etapa 6: Testar a implantação
Siga as etapas abaixo para verificar se a implantação da extensão obteve êxito:

1. Se você implantou usando **Configuração do computador**, faça logon em um computador cliente que pertence à UO que você selecionou na [Etapa 2: Criar o objeto de política de grupo](#step-2-create-the-group-policy-object). Se você implantou usando **Configuração do usuário**, certifique-se de conectar-se como um usuário que pertence a essa UO.
2. As alterações da política de grupo podem demorar algumas entradas para serem totalmente atualizadas no computador. Para forçar a atualização, abra um **Prompt de comando** e execute o seguinte comando: `gpupdate /force`
3. Reinicie o computador para que a instalação ocorra. A inicialização poderá demorar consideravelmente mais do que o normal durante a instalação da extensão.
4. Depois de reiniciar, abra o **Internet Explorer**. No canto superior direito da janela, clique em **Ferramentas** (ícone de engrenagem) e, em seguida, selecione **Gerenciar complementos**.
   
    ![Vá para Ferramentas > Gerenciar Complementos](./media/active-directory-saas-ie-group-policy/manage-add-ons.png)
5. Na janela **Gerenciar Complementos**, verifique se a **Extensão do Painel de Acesso** foi instalada e se seu **Status** foi definido como **Habilitado**.
   
    ![Verifique se a Extensão do Painel de Acesso está instalada e habilitada.](./media/active-directory-saas-ie-group-policy/verify-install.png)

## <a name="related-articles"></a>Artigos relacionados
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)
* [Acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md)
* [Solucionando problemas da extensão do painel de acesso para o Internet Explorer](active-directory-saas-ie-troubleshooting.md)

