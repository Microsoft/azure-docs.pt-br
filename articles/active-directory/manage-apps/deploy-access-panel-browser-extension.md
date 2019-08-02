---
title: Implantar a extensão do Painel de Acesso do Azure do IE usando um GPO | Microsoft Docs
description: Como usar a política de grupo para implantar o complemento do Internet Explorer para o portal de meus aplicativos.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/08/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71c342ede77349b3f6c22093e5877ad5f5ce6549
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807675"
---
# <a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>Como: Implantar a extensão do painel de acesso para o Internet Explorer usando a diretiva de grupo

Este tutorial mostra como usar a política de grupo para instalar remotamente a extensão do Painel de Acesso para o Internet Explorer nos computadores dos usuários. Essa extensão é necessária para os usuários do Internet Explorer que precisam entrar em aplicativos configurados usando o [logon único baseado em senha](what-is-single-sign-on.md#password-based-sso).

É recomendável que administradores automatizem a implantação dessa extensão. Caso contrário, os usuários terão de baixar e instalar a extensão por conta própria, o que poderá causar erros do usuário e que exigirá permissões de administrador. Este tutorial apresenta um método de automatização de implantações de software usando a política de grupo. [Saiba mais sobre a política de grupo.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

A extensão do Painel de Acesso também está disponível para o [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) e o [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998) e nenhum deles exige permissões de administrador para instalar.

## <a name="prerequisites"></a>Pré-requisitos

* Você configurou os [Serviços de Domínio do Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)e os computadores dos usuários ingressaram no domínio.
* Você deve ter a permissão "Editar configurações" para editar o GPO (Objeto de Política de Grupo). Por padrão, os membros dos grupos de segurança a seguir têm essa permissão: Administradores de Domínio, Administradores da Empresa e Proprietários criadores de políticas de grupo. [Saiba mais.](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

## <a name="step-1-create-the-distribution-point"></a>Etapa 1: Criar o ponto de distribuição

Primeiro, você deve colocar o pacote do instalador em um local de rede que possa ser acessado pelos computadores nos quais você deseja instalar remotamente a extensão. Para fazer isso, siga estas etapas:

1. Faça logon no servidor como administrador.
1. Na janela **Gerenciador do Servidor**, vá para **Arquivos e Serviços de Armazenamento**.

    ![Abrir Serviços de Arquivo e Armazenamento](./media/deploy-access-panel-browser-extension/files-services.png)

1. Vá para a guia **Compartilhamentos** . Em seguida, clique em **Tarefas** > **Novo Compartilhamento...**

    ![Captura de tela mostra onde encontrar o novo compartilhamento de tela de tarefas](./media/deploy-access-panel-browser-extension/shares.png)

1. Conclua o **Assistente de Novo Compartilhamento** e defina permissões para garantir que ele possa ser acessado dos computadores dos usuários. [Saiba mais sobre compartilhamentos.](https://technet.microsoft.com/library/cc753175.aspx)
1. Faça o download do seguinte pacote do Microsoft Windows Installer (arquivo .msi): [Access Panel Extension.msi](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)
1. Copie o pacote do instalador para um local desejado no compartilhamento.

    ![Copie o arquivo. msi para o compartilhamento](./media/deploy-access-panel-browser-extension/copy-package.png)

1. Verifique se os computadores do cliente podem acessar o pacote do instalador desde o compartilhamento.

## <a name="step-2-create-the-group-policy-object"></a>Etapa 2: Criar o objeto de diretiva de grupo

1. Entrar no servidor que hospeda sua instalação de serviços de domínio Active Directory (AD DS).
1. No Gerenciador do Servidor, vá para **Ferramentas** > **Gerenciamento de Política de Grupo**.

    ![Vá para Ferramentas > Gerenciamento de Política de Grupo](./media/deploy-access-panel-browser-extension/tools-gpm.png)

1. No painel esquerdo da janela **Gerenciamento de Política de Grupo** , exiba sua hierarquia de UO (unidade organizacional) e determine em qual escopo você gostaria de aplicar a política de grupo. Por exemplo, você poderá optar por escolher uma UO pequena a ser implantada para alguns usuários para teste ou poderá escolher uma UO de nível superior a ser implantada em toda a sua organização.

   > [!NOTE]
   > Se você quiser criar ou editar suas Unidades Organizacionais (UOs), volte para o Gerenciador do Servidor e vá para **Ferramentas** > **Usuários e Computadores do Active Directory**.

1. Depois de selecionar uma UO, clique com o botão direito do mouse nela e selecione **Criar um GPO neste domínio e vinculá-lo aqui...**

    ![Captura de tela mostra a criar uma nova opção de GPO](./media/deploy-access-panel-browser-extension/create-gpo.png)

1. No prompt **Novo GPO** , digite um nome para o novo Objeto de Política de Grupo.
1. Clique com o botão direito do mouse no Objeto de Política de Grupo criado e selecione **Editar**.

## <a name="step-3-assign-the-installation-package"></a>Etapa 3: Atribuir o pacote de instalação

1. Determine se você deseja implantar a extensão com base na **Configuração do Computador** ou na **Configuração do Usuário**. Ao usar a [configuração do computador](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx), a extensão será instalada no computador, independentemente dos usuários que fizerem logon. Com a [configuração do usuário](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx), os usuários têm a extensão instalada para eles, independentemente dos computadores em que eles fizerem logon.
1. No painel esquerdo da janela **Editor de Gerenciamento de Política de Grupo** , vá para qualquer um dos seguintes caminhos de pasta, dependendo do tipo de configuração escolhida:

   * `Computer Configuration/Policies/Software Settings/`
   * `User Configuration/Policies/Software Settings/`

1. Clique com o botão direito do mouse em **Instalação do software** e selecione **Novo** > **Pacote...**
1. Vá para a pasta compartilhada que contém o pacote do instalador da [Etapa 1: criar o ponto de distribuição](#step-1-create-the-distribution-point), escolha o arquivo .msi e clique em **Abrir**.

   > [!IMPORTANT]
   > Se o compartilhamento estiver localizado no mesmo servidor, verifique se você está acessando o .msi por meio do caminho do arquivo de rede, em vez do caminho do arquivo local.

    ![Selecione o pacote de instalação a partir da pasta compartilhada](./media/deploy-access-panel-browser-extension/select-package.png)

1. No prompt **Implantar Software**, selecione **Atribuído** para o método de implantação. Clique em **OK**.

Agora a extensão está implantada na UO que você selecionou. [Saiba mais sobre a instalação do Software de Política de Grupo.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

## <a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>Etapa 4: Habilitar automaticamente a extensão para o Internet Explorer

Além da execução do instalador, todas as extensões do Internet Explorer deverão ser habilitadas explicitamente antes de serem usadas. Siga as etapas abaixo para habilitar a Extensão do Painel de Acesso usando a política de grupo:

1. Na janela **Editor de Gerenciamento de Política de Grupo**, vá para qualquer um dos seguintes caminhos, dependendo do tipo de configuração escolhida na [Etapa 3: atribuir o pacote de instalação](#step-3-assign-the-installation-package):

   * `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`

1. Clique com o botão direito do mouse em **Lista de Complementos** e selecione **Editar**.

    ![Clique com botão direito "Lista de complementos" e selecione "Editar"](./media/deploy-access-panel-browser-extension/edit-add-on-list.png)

1. Na janela **Lista de Complementos**, selecione **Habilitado**. Em seguida, na seção **Opções**, clique em **Mostrar...** .

    ![Clique em Habilitar e clique em Mostrar...](./media/deploy-access-panel-browser-extension/edit-add-on-list-window.png)

1. Na janela **Mostrar Conteúdo** , execute as seguintes etapas:

   1. Na primeira coluna (o campo **Nome do Valor**), copie e cole a seguinte ID de Classe: `{030E9A3F-7B18-4122-9A60-B87235E4F59E}`
   1. Na segunda coluna (o campo **Valor**), digite o seguinte valor: `1`
   1. Clique em **OK** para fechar a janela **Mostrar Conteúdo**.

      ![Preencha os valores conforme especificado na etapa anterior](./media/deploy-access-panel-browser-extension/show-contents.png)

1. Clique em **OK** para aplicar as alterações e fechar a janela **Lista de Complementos**.

Agora a extensão deverá estar habilitada para os computadores na UO selecionada. [Saiba mais sobre como usar a política de grupo para habilitar ou desabilitar complementos do Internet Explorer.](https://technet.microsoft.com/library/dn454941.aspx)

## <a name="step-5-optional-disable-remember-password-prompt"></a>Etapa 5 (opcional): Desabilitar o prompt "Lembrar senha"

Quando os usuários entram em sites que usam a extensão do painel de acesso, o Internet Explorer mostra o seguinte prompt perguntando "Deseja armazenar sua senha?"

![Mostra a "Você deseja armazenar sua senha..." prompt](./media/deploy-access-panel-browser-extension/remember-password-prompt.png)

Se você quiser impedir que os usuários vejam esse prompt, siga as etapas abaixo para impedir o preenchimento automático de lembrar senhas:

1. Na janela **Editor de gerenciamento de política de grupo** , vá para o caminho listado abaixo. Essa configuração só está disponível em **Configuração do usuário**.

   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`
1. Localize a configuração denominada **Ativar o recurso de preenchimento automático para nomes de usuário e senhas em formulários**.

   > [!NOTE]
   > Versões anteriores do Active Directory podem listar essa configuração com o nome **Não permitir o preenchimento automático para salvar senhas**. Essa configuração é diferente da configuração descrita neste tutorial.

    ![Lembre-se de examinar isso em configurações do usuário](./media/deploy-access-panel-browser-extension/disable-auto-complete.png)

1. Clique com o botão direito na configuração acima e selecione **Editar**.
1. Na janela **Ativar o recurso de preenchimento automático para nomes de usuário e senhas em formulários**, selecione **Desabilitado**.

    ![Selecione a opção "Desabilitado" para a ativar o recurso Preenchimento automático](./media/deploy-access-panel-browser-extension/disable-passwords.png)

1. Clique em **OK** para aplicar essas alterações e fechar a janela.

Os usuários não poderão mais armazenar suas credenciais ou usar o preenchimento automático para acessar as credenciais armazenadas anteriormente. No entanto, essa política permite que os usuários continuem a usar o preenchimento automático para outros tipos de campos de formulário, como campos de pesquisa.

> [!WARNING]
> Se essa política for ativada depois que os usuários tiverem escolhido armazenar algumas credenciais, essa política *não* limpará as credenciais que já foram armazenadas.

## <a name="step-6-testing-the-deployment"></a>Etapa 6: Testar a implantação

Siga as etapas abaixo para verificar se a implantação da extensão obteve êxito:

1. Se a implantação tiver sido feita usando a **Configuração do Computador**, faça logon em um computador cliente que pertence à UO que você escolheu na [Etapa 2: criar o Objeto de Política de Grupo](#step-2-create-the-group-policy-object). Se você implantou usando **Configuração do usuário**, certifique-se de conectar-se como um usuário que pertence a essa UO.
1. Pode levar duas entradas para as alterações de política de grupo para uma atualização completa com esse computador. Para forçar a atualização, abra um **Prompt de comando** e execute o seguinte comando: `gpupdate /force`
1. Reinicie o computador para que a instalação ocorra. A inicialização poderá demorar consideravelmente mais do que o normal durante a instalação da extensão.
1. Depois de reiniciar, abra o **Internet Explorer**. No canto superior direito da janela, clique em **Ferramentas** (ícone de engrenagem) e, em seguida, selecione **Gerenciar complementos**.
1. Na janela **Gerenciar Complementos**, verifique se a **Extensão do Painel de Acesso** foi instalada e se seu **Status** foi definido como **Habilitado**.

   ![Verifique se a extensão do painel de acesso é instalada e habilitada](./media/deploy-access-panel-browser-extension/verify-install.png)

## <a name="learn-more"></a>Saiba mais

* [Acesso a aplicativos e logon único com o Active Directory do Azure](what-is-single-sign-on.md)
* [Solucionando problemas da extensão do painel de acesso para o Internet Explorer](manage-access-panel-browser-extension.md)
