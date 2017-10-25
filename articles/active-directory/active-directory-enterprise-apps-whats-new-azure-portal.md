---
title: "O que há de novo no gerenciamento de aplicativos empresariais no Azure Active Directory | Microsoft Docs"
description: "Saiba o que há de novo no gerenciamento de aplicativos empresariais no Azure Active Directory."
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
editor: 
ms.assetid: 34ac4028-a5aa-40d9-a93b-0db4e0abd793
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: asteen
ms.reviewer: asteen
ms.openlocfilehash: 0c32a6719292aa903aa32dfdc4a31114e7a28346
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="whats-new-in-enterprise-application-management-in-azure-active-directory"></a>O que há de novo no gerenciamento de aplicativos empresariais no Azure Active Directory 

O Azure AD (Azure Active Directory) aprimorou as ferramentas de gerenciamento de aplicativos empresariais, com novos recursos e funcionalidades para tornar os aplicativos de gerenciamento mais simples e eficientes.

A seguir estão alguns dos aprimoramentos do Azure AD no [Portal do Azure](https://portal.azure.com).

- Uma experiência aprimorada da galeria de aplicativos, com um modelo de criação de aplicativos simplificado e suporte para todos os tipos de aplicativo aos quais você está acostumado. 
- Uma experiência de início rápido totalmente nova, que lhe ajuda a começar com um piloto do seu aplicativo. 
- Configure as políticas de autoatendimento com apenas alguns cliques. 
- Melhorias significativas no proxy de aplicativo, na configuração de logon único e nas experiências de trazer seu próprio aplicativo, permitindo que você faça mais do que antes.

## <a name="improvements-to-the-azure-active-directory-application-gallery"></a>Melhorias à Galeria de Aplicativos do Azure Active Directory

Adicione seus aplicativos favoritos, sejam eles da [galeria de aplicativos](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery), aplicativos personalizados que você está estendendo para a nuvem ou novos aplicativos que você está desenvolvendo.  Você pode começar com esta nova experiência clicando em **Adicionar** nas folhas da visão geral dos **Aplicativos Empresariais** ou **Todos os aplicativos**.
 
  ![Adicionando um aplicativo](./media/active-directory-enterprise-apps-whats-new-azure-portal/01.png)

Uma vez na galeria, você verá todos os nossos aplicativos em destaque, o que auxilia no provisionamento do usuário com sua exibição na frente e no centro.  Você pode procurar todos os tipos de categorias diferentes para detalhar os aplicativos importantes, ou pode usar a experiência de pesquisa para localizar rapidamente os aplicativos que você deseja integrar.

  ![A galeria de aplicativos](./media/active-directory-enterprise-apps-whats-new-azure-portal/02.png)

## <a name="add-custom-applications-from-one-place"></a>Adicionar aplicativos personalizados de um só lugar

Além de adicionar aplicativos pré-integrados da galeria, todas as experiências de configuração do aplicativo personalizado que você já conhece do portal de gerenciamento clássico agora são possíveis no novo portal. Se você estiver estendendo um aplicativo no local usando o proxy de aplicativo, integrando sua própria senha ou aplicativo SSO federado, ou criando um aplicativo totalmente novo usando o registro do aplicativo, tudo isso poderá ser feito nesse único local.

  ![Adicionar seu próprio aplicativo](./media/active-directory-enterprise-apps-whats-new-azure-portal/03.png)

 
**Para começar a adicionar seu próprio aplicativo**:

1. Clique no **link adicionar seu próprio** na parte superior da galeria de aplicativos. 
2. Você verá duas opções na sua frente: **implantar um aplicativo existente** ou **desenvolver um novo aplicativo**. Continue lendo para saber a diferença entre as duas opções e como usá-las.

### <a name="deploying-existing-applications"></a>Implantação de aplicativos existentes

1. Se você já tiver um aplicativo em execução e deseja apenas para integrá-lo ao Azure AD para logon único ou provisionamento, escolha a opção **Implantar um aplicativo existente**. Escolha um nome para seu aplicativo, clique em **Adicionar**.
2. É isso! Em vez de precisar saber todos os detalhes sobre seu aplicativo com antecedência, você pode definir o funcionamento do seu novo aplicativo navegando por meio do menu à esquerda e configurar o aplicativo de sua preferência a qualquer momento.

  ![Adicionar um aplicativo existente com um clique](./media/active-directory-enterprise-apps-whats-new-azure-portal/04.png)
 
### <a name="developing-new-applications"></a>Desenvolver novos aplicativos

1. Se você estiver desenvolvendo um novo aplicativo, há uma maneira fácil de acessar o Registro de Aplicativo à direita da galeria:
2. Clique na opção **adicionar seu próprio** na Galeria de Aplicativos, selecione a opção **desenvolver um aplicativo existente** e você verá um link à direita para a experiência de adição de aplicativo.

  ![Adicionar um aplicativo recém-desenvolvido em alguns cliques](./media/active-directory-enterprise-apps-whats-new-azure-portal/05.png)


>[!NOTE]
>Depois de adicionar um aplicativo usando o Registro de Aplicativo, ele será mostrado na lista de Aplicativos Empresariais, onde você poderá configurar o logon único e gerenciar políticas de acesso para o novo aplicativo.

  ![Gerenciamento do acesso a seu novo aplicativo em Aplicativos Empresariais](./media/active-directory-enterprise-apps-whats-new-azure-portal/06.png)


## <a name="quick-start-get-going-with-your-new-application-right-away"></a>Início rápido: Começar a usar seu novo aplicativo imediatamente 

Depois de adicionar um aplicativo, independentemente de ser pré-integrado ou seu próprio aplicativo, criamos uma experiência personalizada de início rápido para ajudá-lo a começar a usar rapidamente o novo aplicativo. Se você seguir sistematicamente cada opção, vamos conduzi-lo por meio da interface do isuário e mostraremos como começar com um piloto do seu novo aplicativo o mais rápido possível. 
 
  ![Experiência de início rápido de novos aplicativos](./media/active-directory-enterprise-apps-whats-new-azure-portal/07.png)

 Você pode visitar esta nova experiência de início rápido, a qualquer momento e de qualquer aplicativo, clicando em **Início rápido** no menu de navegação à esquerda do aplicativo.


## <a name="updated-application-proxy-configuration"></a>Configuração de proxy de aplicativo atualizada
Agora, digamos que um dos novos aplicativos que você adicionou esteja em execução no seu ambiente local e você deseja integrá-lo ao Azure AD.  Uma das novidades interessantes sobre a nova experiência de configuração de aplicativo no novo portal do Azure AD é que ao dividir o modo de logon do aplicativo da configuração de proxy de aplicativo, agora você pode facilmente expor os aplicativos de SSO com senha ou federados executados em sua rede corporativa diretamente na nuvem, sem a necessidade de criar várias instâncias do aplicativo.

Além disso, agora também é possível configurar qualquer um dos novos aplicativos que você adicionou para uso com o direito de Proxy de Aplicativo do Azure AD do novo portal, incluindo os aplicativos que oferecem suporte a experiências nativas de autenticação do Windows.

  ![Configurar um aplicativo para usar a opção de logon de Autenticação Integrada do Windows](./media/active-directory-enterprise-apps-whats-new-azure-portal/08.png)
 

Para começar a configurar um aplicativo nativo de autenticação do Windows com o Proxy de Aplicativo:
1. Clique no item de navegação Logon Único e escolha **Autenticação Integrada do Windows** da folha configurações de logon e defina as configurações de sua preferência.
2. Sobre o suporte a esses novos modos de autenticação, agora você pode também carregar certificados de domínios personalizados para oferecer suporte a aplicativos executados em pontos de extremidade seguros dentro da sua organização.  
 
   ![Carregar um certificado a ser usado com o Proxy de Aplicativo](./media/active-directory-enterprise-apps-whats-new-azure-portal/09.png)

3. Para carregar um novo certificado para o seu aplicativo local favorito, clique na opção **Proxy de aplicativo** no menu de navegação à esquerda, clique no seletor **Certificado** e carregue um arquivo de certificado que podemos usar para criptografar solicitações de nosso ponto de extremidade de nuvem para seu aplicativo.

## <a name="advanced-federated-single-sign-on-configuration"></a>Configuração avançada de logon único federado

Para aqueles que utilizam aplicativos federados hoje, há muitos novos recursos na folha de configuração de logon único baseado em SAML. Para começar, agora você pode personalizar, adicionar, remover e mapear totalmente os atributos de usuário existentes emitidos como declarações em tokens SAML.
 
  ![Personalizar os atributos de usuário do token SAML passado para um aplicativo federado](./media/active-directory-enterprise-apps-whats-new-azure-portal/10.png)


Para conferir a nova configuração de SSO federado:
1. Abra a folha **logon único** de um aplicativo federado no menu de navegação esquerdo e verifique se o modo '*Logon Único Baseado em SAML** está selecionado. 
2. Uma vez lá, habilite a caixa de seleção no título **Atributos de Usuário** para modificar todos os atributos incluídos no token SAML passado para esse aplicativo.

Você pode também criar, substituir e gerenciar os certificados usados para logon único federado, bem como editar quem será notificado quando o certificado estiver prestes a expirar. Você verá essas novas opções no título **Certificados** na mesma folha Logon único.
 
  ![Criar um novo certificado, personalizando o email de notificação de expiração e opções de assinatura de certificado](./media/active-directory-enterprise-apps-whats-new-azure-portal/11.png)

### <a name="relay-state-paramenter"></a>Parâmetro de estado de retransmissão
Por fim, também ampliamos o conjunto de parâmetros de URL do SAML, oferecemos suporte para incluir o **parâmetro Estado de Retransmissão**, que é a página em que os usuários aterrissarão em um aplicativo federado depois que a entrada for concluída. Essa é uma configuração muito útil para configurar se deseja enviar os usuários para um local específico dentro do aplicativo para que eles comecem a usar rapidamente.

  ![Definição do parâmetro Estado de Retransmissão de SAML](./media/active-directory-enterprise-apps-whats-new-azure-portal/12.png)
 
**Para definir o parâmetro de estado de retransmissão**:

1. Habilite a caixa de seleção **Mostrar configurações avançadas de URL** sob o título **Domínio e URLs** na folha de configuração do logon único. 
2. Depois de fazer isso, você verá um conjunto de novas caixas de entrada de URL que permitirá a definição dessas e de outras configurações de URL de SAML.

## <a name="bring-your-own-password-sso-applications"></a>Aplicativos SSO BYOP

Sabemos que nem todos os aplicativos dão suporte à federação nativamente. Por exemplo, talvez um dos novos aplicativos que você adicionou tenha uma tela de logon personalizada em que os usuários usam um nome de usuário e uma senha para entrar. Você ainda pode integrar esses tipos de aplicativos ao Azure AD usando nosso recurso **BYOA**, que agora está disponível para a configuração no novo portal.
 
  ![Integração de aplicativos de cofre de senha personalizados ao Azure AD](./media/active-directory-enterprise-apps-whats-new-azure-portal/13.png)

**Para conferir o recurso ‘BYOA’**:

1. Depois de definir o modo de logon único para um novo aplicativo personalizado que você adicionou ao **Logon Único Baseado em Senha**, digite a URL onde o aplicativo processa sua tela de logon e clique em **Salvar**.  
2. Depois de fazer isso, vamos extrair automaticamente essa URL para uma caixa de entrada de nome de usuário e de senha e permitir que você use o Azure AD para transmitir com segurança as senhas para o aplicativo usando a extensão de navegador de painel de acesso.

## <a name="configure-self-service-application-access"></a>Configurar o acesso ao aplicativo de autoatendimento

Depois de adicionar muitos aplicativos novos, talvez você queira permitir que os usuários procurem e adicionem os aplicativos a seus próprios painéis de acesso, sem que seja necessária a intervenção de um administrador. Com essa versão mais recente, você pode configurar e gerenciar o acesso a aplicativos de autoatendimento desde o novo portal.

  ![Configurar acesso ao aplicativo de autoatendimento para um aplicativo do SSO de senha](./media/active-directory-enterprise-apps-whats-new-azure-portal/14.png)
 
**Para configurar e gerenciar o acesso do aplicativo de autoatendimento**:

1. Para começar, você pode selecionar a opção **Autoatendimento** no menu de navegação à esquerda do aplicativo e definir a opção **Permitir que os usuários solicitem acesso a este aplicativo?** como '**Sim**'. 
2. Isso permitirá que você configure quem tem permissão para aprovar o acesso ao aplicativo e quais usuários de autoatendimento de grupo serão adicionados. Além disso, se o aplicativo estiver configurado para a senha de logon único, você também verá outra opção para permitir que os aprovadores gerenciem as senhas atribuídas ao aplicativo.

##<a name="feedback"></a>Comentários

Esperamos que você goste de usar a experiência aprimorada do Azure AD. Continue a fazer seus comentários! Poste seus comentários e suas ideias para aprimoramento na seção **Portal de Administração** do nosso [fórum de comentários](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Nós estamos empolgados para criar algo novo e interessante diariamente e usar suas diretrizes para formar e definir o que devemos criar a seguir.

## <a name="next-steps"></a>Próximas etapas

Para obter mais detalhes, confira [Gerenciamento de aplicativos com o Azure Active Directory](active-directory-enable-sso-scenario.md).



