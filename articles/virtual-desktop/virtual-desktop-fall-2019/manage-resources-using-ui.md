---
title: Implantar uma ferramenta de gerenciamento com um modelo do Azure Resource Manager – Azure
description: Como instalar uma ferramenta de interface do usuário com um modelo Azure Resource Manager para gerenciar recursos da área de trabalho virtual do Windows (clássico).
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f96365bcdf64d19dc0b894f2f1230233b3137bc7
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842644"
---
# <a name="deploy-a-windows-virtual-desktop-classic-management-tool-with-an-azure-resource-manager-template"></a>Implantar uma ferramenta de gerenciamento de área de trabalho virtual do Windows (clássica) com um modelo de Azure Resource Manager

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows (clássica), que não é compatível com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager.

As instruções deste artigo lhe dirão como implantar a interface do usuário usando um modelo do Azure Resource Manager.

## <a name="important-considerations"></a>Considerações importantes

Como o aplicativo requer consentimento para interagir com a Área de Trabalho Virtual do Windows, essa ferramenta não dá suporte a cenários B2B (entre empresas). Cada assinatura de locatário do AAD (Azure Active Directory) precisará da própria implantação separada da ferramenta de gerenciamento.

Esta ferramenta de gerenciamento é uma amostra. A Microsoft fornecerá atualizações de qualidade e de segurança importantes. O [código-fonte está disponível no GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Clientes e parceiros são incentivados a personalizar a ferramenta para atender às suas necessidades empresariais.

Os seguintes navegadores são compatíveis com a ferramenta de gerenciamento:
- Google Chrome 68 ou posterior
- Microsoft Edge 40.15063 ou posterior
- Mozilla Firefox 52.0 ou posterior
- Safari 10 ou posterior (somente macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>O que você precisa para implantar a ferramenta de gerenciamento

Antes de implantar a ferramenta de gerenciamento, você precisará que um usuário do Azure AD (Azure Active Directory) crie um registro de aplicativo e implante a interface do usuário de gerenciamento. Esse usuário precisa:

- Ter a autenticação multifator (MFA) do Azure AD desabilitada
- Ter permissão para criar recursos em sua assinatura do Azure
- Ter permissão para criar um aplicativo do Azure AD. Siga estas etapas para verificar se o usuário tem as permissões necessárias, seguindo as instruções em [Permissões necessárias](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

Depois de implantar e configurar a ferramenta de gerenciamento, é recomendável solicitar que um usuário inicie a interface do usuário de gerenciamento para verificar se tudo está funcionando. O usuário que inicia a interface do usuário de gerenciamento deve ter uma atribuição de função que permita exibir ou editar o locatário da Área de Trabalho Virtual do Windows.

## <a name="deploy-the-management-tool"></a>Implantar a ferramenta de gerenciamento

Antes de começar, verifique se os aplicativos cliente e servidor têm consentimento visitando a [Página de Consentimento da Área de Trabalho Virtual do Windows](https://rdweb.wvd.microsoft.com) referente ao AAD (Azure Active Directory) representado.

Siga estas instruções para implantar o modelo do Azure Resource Manager:

1. Vá até a [página de Modelos de RDS do Azure no GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy).
2. Implantar o modelo no Azure.
    - Se estiver implantando em uma assinatura Enterprise, role para baixo e selecione **Implantar no Azure**.
    - Se estiver implantando em uma assinatura de um Provedor de Soluções de Nuvem, siga estas instruções para implantar no Azure:
        1. Role para baixo e clique com o botão direito do mouse em **Implantar no Azure** e, em seguida, selecione **Copiar o link**.
        2. Abra um editor de texto, como o Bloco de Notas, e cole o link nele.
        3. Logo após <https://portal.azure.com/> e antes da hashtag (#), digite um sinal de arroba (@) seguido do nome de domínio de locatário. Veja um exemplo do formato: <https://portal.azure.com/@Contoso.onmicrosoft.com#create/>.
        4. Entre no portal do Azure como um usuário com permissões de Administrador/Colaborador para a assinatura do Provedor de Soluções de Nuvem.
        5. Cole o link copiado para o editor de texto na barra de endereços.
3. Ao inserir os parâmetros, faça o seguinte:
    - Para o parâmetro **isServicePrincipal**, selecione **false**.
    - Para as credenciais, insira suas credenciais do Azure AD com a autenticação multifator desabilitada. Essas credenciais serão usadas para criar o aplicativo do Azure AD e os recursos do Azure. Para saber mais, confira o artigo [O que você precisa para implantar a ferramenta de gerenciamento](#what-you-need-to-deploy-the-management-tool).
    - Para o **applicationName**, use um nome exclusivo para o aplicativo que será registrado no Azure Active Directory. Esse nome também será usado para a URL do aplicativo Web. Por exemplo, você pode usar um nome como "Apr3UX".
4. Depois de fornecer os parâmetros, aceite os termos e condições e selecione **Comprar**.

## <a name="provide-consent-for-the-management-tool"></a>Fornecer consentimento para a ferramenta de gerenciamento

Após o modelo do Azure Resource Manager do GitHub ser concluído, você encontrará um grupo de recursos contendo dois serviços de aplicativos, bem como um plano do serviço de aplicativo, no portal do Azure.

Antes de entrar e usar a ferramenta de gerenciamento, você precisará fornecer consentimento ao novo aplicativo do Azure AD associado à ferramenta de gerenciamento. Ao fornecer consentimento, você permite que a ferramenta de gerenciamento faça chamadas de gerenciamento da Área de Trabalho Virtual do Windows em nome do usuário conectado atualmente à ferramenta.

> [!div class="mx-imgBorder"]
> ![Uma captura de tela mostrando as permissões sendo fornecidas quando você fornece consentimento para a ferramenta de gerenciamento da interface do usuário.](../media/management-ui-delegated-permissions.png)

Para determinar qual usuário você pode usar para entrar na ferramenta, vá até sua [página de configurações do usuário do Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) e anote o valor correspondente a **Os usuários podem consentir que os aplicativos acessem dados da empresa em seus nomes**.

> [!div class="mx-imgBorder"]
> ![Uma captura de tela mostrando se os usuários podem dar consentimento a aplicativos apenas para seu usuário.](../media/management-ui-user-consent-allowed.png)

- Se o valor estiver definido como **Sim**, você poderá entrar com qualquer conta de usuário no Azure Active Directory e fornecer consentimento somente para esse usuário. No entanto, se entrar na ferramenta de gerenciamento com um usuário diferente mais tarde, você deverá executar o mesmo consentimento novamente.
- Se o valor estiver definido como **Não**, você precisará entrar como Administrador Global no Azure Active Directory e fornecer consentimento do administrador para todos os usuários no diretório. Nenhum outro usuário receberá uma solicitação de consentimento.


Após você decidir qual usuário vai usar para fornecer o consentimento, siga estas instruções para fornecer consentimento para a ferramenta:

1. Vá para os recursos do Azure, selecione o recurso serviços de Azure App com o nome fornecido no modelo (por exemplo, Apr3UX) e navegue até a URL associada a ele; por exemplo,  `https://rdmimgmtweb-210520190304.azurewebsites.net` .
2. Entre usando a conta de usuário do Azure Active Directory apropriada.
3. Se tiver feito a autenticação com um Administrador Global, agora você poderá marcar a caixa de seleção **Consentimento em nome de sua organização**. Selecione **Aceitar** para fornecer consentimento.

   > [!div class="mx-imgBorder"]
   > ![Uma captura de tela mostrando a página de consentimento completa que o usuário ou administrador verá.](../media/management-ui-consent-page.png)

Agora, você será levado à ferramenta de gerenciamento.

## <a name="use-the-management-tool"></a>Usar a ferramenta de gerenciamento

Após ter fornecido consentimento para a organização ou para um usuário especificado, você poderá acessar a ferramenta de gerenciamento a qualquer momento.

Siga estas instruções para iniciar a ferramenta:

1. Selecione o recurso dos Serviços de Aplicativos do Azure com o nome fornecido no modelo (por exemplo, Apr3UX) e navegue até a URL associada a ele; por exemplo, `https://rdmimgmtweb-210520190304.azurewebsites.net`.
2. Entre usando suas credenciais da Área de Trabalho Virtual do Windows.
3. Quando solicitado a escolher um Grupo de Locatários, selecione **Grupo de Locatários Padrão** na lista suspensa.
4. Quando você seleciona **Grupo de Locatários Padrão**, um menu deve ser exibido no lado esquerdo da janela. Nesse menu, encontre o nome do seu grupo de locatários e selecione-o.

  > [!NOTE]
  > Se tiver um grupo de locatários personalizado, digite o nome manualmente em vez de escolher na lista suspensa.

## <a name="report-issues"></a>Relatar problemas

Caso tenha problemas com a ferramenta de gerenciamento ou outras ferramentas da Área de Trabalho Virtual do Windows, siga as instruções em [Modelos do Azure Resource Manager para Serviços de Área de Trabalho Remota](https://github.com/Azure/RDS-Templates/blob/master/README.md) para relatá-los no GitHub.

## <a name="next-steps"></a>Próximas etapas

Agora que aprendeu como implantar a ferramenta de gerenciamento e conectar-se a ela, você pode aprender a usar a ajuda do Serviço do Azure para monitorar problemas de serviço e avisos de integridade. Para saber mais, confira o tutorial [Configurar alertas de serviço](set-up-service-alerts-2019.md).