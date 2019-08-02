---
title: Implantar uma ferramenta de gerenciamento – Azure
description: Como instalar uma ferramenta da interface do usuário para gerenciar recursos de visualização da Área de Trabalho Virtual do Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: v-chjenk
ms.openlocfilehash: 9579db9836ef41706f2c6be09570fa7c1459e14f
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620447"
---
# <a name="tutorial-deploy-a-management-tool"></a>Tutorial: Implantar uma ferramenta de gerenciamento

A ferramenta de gerenciamento fornece uma IU (interface do usuário) para gerenciar recursos de Visualização da Área de Trabalho Virtual da Microsoft. Neste tutorial, você aprenderá a implantar a ferramenta de gerenciamento e a conectar-se a ela.

>[!NOTE]
>Essas instruções são referentes a uma configuração específica da Visualização da Área de Trabalho Virtual do Windows que pode ser usada com os processos existentes de sua organização.

## <a name="important-considerations"></a>Considerações importantes

Como o aplicativo requer consentimento para interagir com a Área de Trabalho Virtual do Windows, essa ferramenta não dá suporte a cenários B2B (entre empresas). Cada assinatura de locatário do AAD (Azure Active Directory) precisará da própria implantação separada da ferramenta de gerenciamento.

Esta ferramenta de gerenciamento é uma amostra. A Microsoft fornecerá atualizações de qualidade e de segurança importantes. O [código-fonte está disponível no GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Clientes e parceiros são incentivados a personalizar a ferramenta para atender às suas necessidades empresariais.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>O que é necessário para executar o modelo do Azure Resource Manager

Antes de implantar o modelo do Azure Resource Manager, você precisará de um usuário do Azure Active Directory para implantar a interface do usuário de gerenciamento. Esse usuário precisa:

- Ter a (MFA) Autenticação Multifator do Azure desabilitada
- Ter permissão para criar recursos em sua assinatura do Azure
- Ter permissão para criar um aplicativo do Azure AD. Siga estas etapas para verificar se o usuário tem as [permissões necessárias](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

Após implantar o modelo do Azure Resource Manager, você vai querer iniciar a interface do usuário de gerenciamento para validação. Esse usuário precisa:
- Ter uma atribuição de função para exibir ou editar seu locatário da Área de Trabalho Virtual do Windows

## <a name="run-the-azure-resource-manager-template-to-provision-the-management-ui"></a>Executar o modelo do Azure Resource Manager para provisionar a interface do usuário de gerenciamento

Antes de começar, verifique se os aplicativos cliente e servidor têm consentimento visitando a [Página de Consentimento da Área de Trabalho Virtual do Windows](https://rdweb.wvd.microsoft.com) referente ao AAD (Azure Active Directory) representado.

Siga estas instruções para implantar o modelo do Azure Resource Manager:

1. Vá até a [página de Modelos de RDS do Azure no GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy).
2. Implantar o modelo no Azure.
    - Se estiver implantando em uma assinatura Enterprise, role para baixo e selecione **Implantar no Azure**. Confira [Orientações sobre parâmetros de modelo](#guidance-for-template-parameters).
    - Se estiver implantando em uma assinatura de um Provedor de Soluções de Nuvem, siga estas instruções para implantar no Azure:
        1. Role para baixo e clique com o botão direito do mouse em **Implantar no Azure** e, em seguida, selecione **Copiar o link**.
        2. Abra um editor de texto, como o Bloco de Notas, e cole o link nele.
        3. Logo após <https://portal.azure.com/> e antes da hashtag (#), digite um sinal de arroba (@) seguido do nome de domínio de locatário. Veja um exemplo do formato: <https://portal.azure.com/@Contoso.onmicrosoft.com#create/>.
        4. Entre no portal do Azure como um usuário com permissões de Administrador/Colaborador para a assinatura do Provedor de Soluções de Nuvem.
        5. Cole o link copiado para o editor de texto na barra de endereços.

### <a name="guidance-for-template-parameters"></a>Orientações sobre parâmetros de modelo
Veja como inserir parâmetros para configurar a ferramenta:

- Esta é a URL do agente de área de trabalho remota: https:\//rdbroker.wvd.microsoft.com/
- Esta é a URL do recurso: https:\//mrs-prod.ame.gbl/mrs-RDInfra-prod
- Use suas credenciais do AAD com a MFA desabilitada para entrar no Azure. Confira [O que é necessário para executar o modelo do Azure Resource Manager](#what-you-need-to-run-the-azure-resource-manager-template).
- Use um nome exclusivo para o aplicativo que será registrado em seu Azure Active Directory para a ferramenta de gerenciamento; por exemplo, Apr3UX.

## <a name="provide-consent-for-the-management-tool"></a>Fornecer consentimento para a ferramenta de gerenciamento

Após o modelo do Azure Resource Manager do GitHub ser concluído, você encontrará um grupo de recursos contendo dois serviços de aplicativos, bem como um plano do serviço de aplicativo, no portal do Azure.

Antes de entrar e usar a ferramenta de gerenciamento, você precisará fornecer consentimento para o novo aplicativo do Azure Active Directory associado à ferramenta de gerenciamento. Ao fornecer consentimento, você está permitindo que a ferramenta de gerenciamento faça chamadas de gerenciamento da Área de Trabalho Virtual do Windows em nome do usuário conectado à ferramenta.

![Uma captura de tela mostrando as permissões sendo fornecidas quando você fornece consentimento para a ferramenta de gerenciamento da interface do usuário.](media/management-ui-delegated-permissions.png)

Para determinar qual usuário você pode usar para entrar na ferramenta, vá até sua [página de configurações do usuário do Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) e anote o valor correspondente a **Os usuários podem consentir que os aplicativos acessem dados da empresa em seus nomes**.

![Uma captura de tela mostrando se os usuários podem dar consentimento a aplicativos apenas para seu usuário.](media/management-ui-user-consent-allowed.png)

- Se o valor estiver definido como **Sim**, você poderá entrar com qualquer conta de usuário no Azure Active Directory e fornecer consentimento somente para esse usuário. No entanto, se entrar na ferramenta de gerenciamento com um usuário diferente mais tarde, você deverá executar o mesmo consentimento novamente.
- Se o valor estiver definido como **Não**, você precisará entrar como Administrador Global no Azure Active Directory e fornecer consentimento do administrador para todos os usuários no diretório. Nenhum outro usuário receberá uma solicitação de consentimento.


Após você decidir qual usuário usará para fornecer o consentimento, siga estas instruções para fornecer consentimento para a ferramenta:

1. Vá até seus recursos do Azure, selecione o recurso dos Serviços de Aplicativos do Azure com o nome fornecido no modelo (por exemplo, Apr3UX) e navegue até a URL associada a ele; por exemplo, <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Entre usando a conta de usuário do Azure Active Directory apropriada.
3. Se tiver feito a autenticação com um Administrador Global, agora você poderá marcar a caixa de seleção **Consentimento em nome de sua organização**. Selecione **Aceitar** para fornecer consentimento.
   
   ![Uma captura de tela mostrando a página de consentimento completa que o usuário ou administrador verá.](media/management-ui-consent-page.png)

Agora, você será levado à ferramenta de gerenciamento.

## <a name="use-the-management-tool"></a>Usar a ferramenta de gerenciamento

Após ter fornecido consentimento para a organização ou para um usuário especificado, você poderá acessar a ferramenta de gerenciamento a qualquer momento.

Siga estas instruções para iniciar a ferramenta:

1. Selecione o recurso dos Serviços de Aplicativos do Azure com o nome fornecido no modelo (por exemplo, Apr3UX) e navegue até a URL associada a ele; por exemplo, <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Entre usando suas credenciais da Área de Trabalho Virtual do Windows.
3. Quando solicitado a escolher um Grupo de Locatários, selecione **Grupo de Locatários Padrão** na lista suspensa.

> [!NOTE]
> Se tiver um Grupo de Locatários personalizado, digite o nome manualmente em vez de escolher na lista suspensa.

## <a name="next-steps"></a>Próximas etapas

Agora que aprendeu como implantar a ferramenta de gerenciamento e conectar-se a ela, você pode aprender a usar a Integridade do Serviço do Azure para monitorar problemas de serviço e avisos de integridade.

> [!div class="nextstepaction"]
> [Tutorial de configuração de alertas de serviço](./set-up-service-alerts.md)
