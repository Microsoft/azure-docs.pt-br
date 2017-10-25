---
title: "Como gerenciar perfis e configurações de serviço | Microsoft Docs"
description: "Saiba como trabalhar com arquivos de configuração de perfis e configurações de serviço| que armazenam as configurações para os ambientes de implantação e configurações de publicação para os serviços de nuvem."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 7da8c551-fb06-4057-b5c7-c77f4b39d803
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/11/2017
ms.author: kraigb
ms.openlocfilehash: af1205f8c3e477d123d4835c80a68b3afd6ee5ad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-service-configurations-and-profiles"></a>Como gerenciar perfis e configurações de serviço
## <a name="overview"></a>Visão geral
Quando você publica um serviço de nuvem, o Visual Studio armazena informações de configuração em dois tipos de arquivos de configuração: perfis e configurações de serviço. Configurações de serviço (arquivos .cscfg) armazenam configurações para os ambientes de implantação para um serviço de nuvem do Azure. O Azure usa esses arquivos de configuração ao gerenciar seus serviços de nuvem. Por outro lado, perfis (arquivos .azurePubxml) armazenam configurações de publicação para os serviços de nuvem. Essas configurações são um registro do que você escolhe quando utiliza o assistente de publicação e são usadas localmente pelo Visual Studio. Este tópico explica como trabalhar com ambos os tipos de arquivos de configuração.

## <a name="service-configurations"></a>Configurações de Serviço
Você pode criar várias configurações de serviço a usar para cada um dos seus ambientes de implantação. Por exemplo, você pode criar uma configuração de serviço para o ambiente local que você usa para executar e testar um aplicativo do Azure e outra configuração de serviço para o seu ambiente de produção.

Você pode adicionar, excluir, renomear e modificar essas configurações de serviço com base em suas necessidades. Você pode gerenciar essas configurações de serviço do Visual Studio, conforme mostrado na ilustração a seguir.

![Gerenciar Configurações de Serviço](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-service-config.png)

Você também pode abrir a caixa de diálogo **Gerenciar Configurações** das páginas de propriedades da função. Para abrir as propriedades de uma função em seu projeto do Azure, abra o menu de atalho para essa função e, em seguida, escolha **Propriedades**. Na guia **Configurações**, expanda a lista **Configuração de Serviço** e selecione **Gerenciar** para abrir a caixa de diálogo **Gerenciar Configurações**.

### <a name="to-add-a-service-configuration"></a>Para adicionar uma configuração de serviço
1. No Gerenciador de Soluções, abra o menu de atalho do projeto do Azure e selecione **Gerenciar Configurações**.
   
    A caixa de diálogo **Gerenciar Configurações de Serviço** é exibida.
2. Para adicionar uma configuração de serviço, você deve criar uma cópia de uma configuração existente. Para fazer isso, escolha a configuração que você deseja copiar na lista Nome e selecione **Criar cópia**.
3. (Opcional) Para atribuir um nome diferente à configuração de serviço, escolha a nova configuração de serviço na lista Nome e selecione **Renomear**. Na caixa de texto **Nome**, digite o nome que você deseja usar para essa configuração de serviço e selecione **OK**.
   
    Um novo arquivo de configuração de serviço chamado ServiceConfiguration.[Novo nome].cscfg é adicionado ao projeto do Azure no Gerenciador de Soluções.

### <a name="to-delete-a-service-configuration"></a>Para excluir uma configuração de serviço
1. No Gerenciador de Soluções, abra o menu de atalho do projeto do Azure e selecione **Gerenciar Configurações**.
   
    A caixa de diálogo **Gerenciar Configurações de Serviço** é exibida.
2. Para excluir uma configuração de serviço, escolha a configuração que você deseja excluir na lista **Nome** e selecione **Remover**. Aparece uma caixa de diálogo para confirmar que você deseja excluir esta configuração.
3. Selecione **Excluir**.
   
     O arquivo de configuração de serviço é removido do projeto do Azure no Gerenciador de Soluções.

### <a name="to-rename-a-service-configuration"></a>Para renomear uma configuração de serviço
1. No Gerenciador de Soluções, abra o menu de atalho do projeto do Azure e selecione **Gerenciar Configurações**.
   
    A caixa de diálogo **Gerenciar Configurações de Serviço** é exibida.
2. Para renomear uma configuração de serviço, escolha a nova configuração de serviço na lista **Nome** e selecione **Renomear**. Na caixa de texto **Nome**, digite o nome que você deseja usar para essa configuração de serviço e selecione **OK**.
   
    O nome do arquivo de configuração de serviço é modificado no projeto do Azure no Gerenciador de Soluções.

### <a name="to-change-a-service-configuration"></a>Para alterar uma configuração de serviço
* Se você quiser alterar uma configuração de serviço, abra o menu de atalho da função específica que você deseja alterar no projeto do Azure e selecione **Propriedades**. Confira [Como configurar as funções para um Serviço de Nuvem do Azure com o Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service) para saber mais.

## <a name="make-different-setting-combinations-by-using-profiles"></a>Fazer diferentes combinações de configurações usando perfis
Usando um perfil, você pode preencher automaticamente o **Assistente de Publicação** com diferentes combinações de configurações para diferentes finalidades. Por exemplo, você pode ter um perfil para depuração e outro para compilações de versão. Nesse caso, o perfil **Depurar** teria a opção **IntelliTrace** habilitada e a configuração **Depurar** selecionada; seu perfil **Versão** teria a opção **IntelliTrace** desabilitada e a configuração **Versão** selecionada. Você também pode usar perfis diferentes para implantar um serviço usando uma conta de armazenamento diferente.

Quando você executa o assistente pela primeira vez, um perfil padrão é criado. O Visual Studio armazena o perfil em um arquivo que tem uma extensão .azurePubXml, que é adicionada ao seu projeto do Azure na pasta **Perfis** . Se você especificar diferentes opções manualmente quando você executa o assistente mais tarde, o arquivo é atualizado automaticamente. Antes de executar o procedimento a seguir, você já deve ter publicado seu serviço de nuvem pelo menos uma vez.

### <a name="to-add-a-profile"></a>Para adicionar um perfil
1. Abra o menu de atalho do projeto do Azure e selecione **Publicar**.
2. Ao lado da lista **Perfil de destino**, selecione o botão **Salvar Perfil**, como mostra a ilustração a seguir. Isso cria um perfil para você.
   
    ![Criar um novo perfil](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/create-new-profile.png)
3. Depois que o perfil for criado, selecione **<Gerenciar...>** na lista **Perfil de destino**.
   
    A caixa de diálogo **Gerenciar perfis** é exibida, como mostra a ilustração a seguir.
   
    ![Caixa de Diálogo Gerenciar Perfis](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-profiles.png)
4. Na lista **Nome**, escolha um perfil e selecione **Criar Cópia**.
5. Escolha o botão **Fechar** .
   
    O novo perfil aparece na lista Perfil de destino.
6. Na lista **Perfil de destino** , selecione o perfil que você acabou de criar. As configurações do Assistente de Publicação são preenchidas com as opções do perfil selecionado.
7. Selecione os botões **Anterior** e **Próximo** para exibir cada página do Assistente de Publicação e personalize as configurações desse perfil. Confira [Assistente de Publicação de Aplicativo do Azure](http://go.microsoft.com/fwlink/p/?LinkID=623085) .
8. Depois de concluir a personalização das configurações, clique em **Próximo** para voltar para a página Configurações. O perfil é salvo quando você publica o serviço usando essas configurações ou se você seleciona **Salvar** ao lado da lista de perfis.

### <a name="to-rename-or-delete-a-profile"></a>Para renomear ou excluir um perfil
1. Abra o menu de atalho do projeto do Azure e selecione **Publicar**.
2. Na lista **Perfil de destino**, selecione **Gerenciar**.
3. Na caixa de diálogo **Gerenciar Perfis**, selecione o perfil que você deseja excluir e selecione **Remover**.
4. Na caixa de diálogo de confirmação que é exibida, selecione **OK**.
5. Selecione **Fechar**.

### <a name="to-change-a-profile"></a>Para alterar um perfil
1. Abra o menu de atalho do projeto do Azure e selecione **Publicar**.
2. Na lista **Perfil de destino** , selecione o perfil que você deseja alterar.
3. Selecione os botões **Anterior** e **Próximo** para exibir cada página do Assistente de Publicação e altere as configurações desejadas. Confira [Assistente de Publicação de Aplicativo do Azure](http://go.microsoft.com/fwlink/p/?LinkID=623085) .
4. Depois de concluir a alteração das configurações, selecione **Próximo** para voltar para a página **Configurações**.
5. (Opcional) Selecione **Publicar** para publicar o serviço de nuvem usando as novas configurações. Se você não deseja publicar seu serviço de nuvem no momento e você fecha o Assistente de Publicação, o Visual Studio pergunta se você deseja salvar as alterações feitas ao perfil.

## <a name="next-steps"></a>Próximas etapas
Para obter informações sobre como configurar outras partes do seu projeto do Azure do Visual Studio, consulte [Configurando um projeto do Azure](http://go.microsoft.com/fwlink/p/?LinkID=623075)

