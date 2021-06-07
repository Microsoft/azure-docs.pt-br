---
title: Acesso híbrido seguro do Azure AD com F5 VPN | Microsoft Docs
description: Tutorial para Azure Active Directory integração de SSO (logon único) com F5 BIG-IP para VPN sem senha
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e177f1ce55d803f54bb2553078441557e5c191
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98730880"
---
# <a name="tutorial-for-azure-active-directory-single-sign-on-integration-with-f5-big-ip-for-password-less-vpn"></a>Tutorial para a integração de logon único do Azure Active Directory ao F5 BIG-IP para VPN sem senha

Neste tutorial, saiba como integrar a solução de rede privada virtual (SSL-VPN) de camada de soquete seguro se BigIP BIG-IP com o Azure Active Directory (AD) para acesso híbrido seguro (SHA).

A integração de uma grande IP SSL VPN com o Azure AD oferece [muitos benefícios importantes](f5-aad-integration.md), incluindo:

- Melhoria da governança de confiança sem a [pré-autenticação e autorização do Azure ad](../../app-service/overview-authentication-authorization.md)

- [Autenticação sem senha para o serviço de VPN](https://www.microsoft.com/security/business/identity/passwordless)

- Gerenciar identidades e acesso de um único plano de controle-o [portal do Azure](https://portal.azure.com/#home)

Apesar desses ótimos acréscimos de valor, a VPN clássica, no entanto, permanece predicada na noção de um perímetro de rede, no qual a confiança está dentro e não é confiável para fora. Esse modelo não é mais eficaz para alcançar uma verdadeira postura de confiança zero, já que os ativos corporativos não são mais confinados às paredes de um data center empresarial, mas em ambientes de várias nuvens sem limites fixos. Por esse motivo, incentivamos nossos clientes a considerar a mudança para uma abordagem mais orientada por identidade no gerenciamento de [acesso por aplicativo](../fundamentals/five-steps-to-full-application-integration-with-azure-ad.md).

## <a name="scenario-description"></a>Descrição do cenário

Nesse cenário, a instância do APM de IP do serviço SSL-VPN será configurada como um provedor de serviços SAML (SP) e o Azure AD se tornará o IDP SAML confiável, fornecendo pré-autenticação. O SSO (logon único) do Azure AD é fornecido por meio da autenticação baseada em declarações para o APM do BIG-IP, fornecendo uma experiência de acesso VPN direta.

![Imagem mostra a arquitetura SSL-VPN](media/f5-sso-vpn/ssl-vpn-architecture.png)

>[!NOTE]
>Todas as cadeias de caracteres de exemplo ou valores referenciados em todo este guia devem ser substituídos pelos do seu ambiente real.

## <a name="prerequisites"></a>Pré-requisitos

A experiência anterior ou o conhecimento de F5 BIG-IP não é necessário, no entanto, você precisará de:

- Uma [assinatura gratuita](https://azure.microsoft.com/trial/get-started-active-directory/) do Azure ad ou superior

- As identidades de usuário devem ser [sincronizadas de seu diretório local para o](../hybrid/how-to-connect-sync-whatis.md) Azure AD.

- Uma conta com [permissões](../roles/permissions-reference.md#application-administrator) de administrador de aplicativo do Azure AD

- Uma infraestrutura de BIG-IP existente com roteamento de tráfego de cliente para e do BIG-IP ou [implante uma edição virtual Big-IP no Azure](f5-bigip-deployment-guide.md).

- Um registro para o serviço VPN publicado de BIG-IP precisará existir no DNS público ou no arquivo localhost de um cliente de teste durante o teste

- O BIG-IP deve ser provisionado com os certificados SSL necessários para publicar serviços por HTTPS.

Familiarizar-se com a [terminologia de Big-IP F5](https://www.f5.com/services/resources/glossary) também ajudará a entender os vários componentes que são referenciados em todo o tutorial.

>[!NOTE]
>O Azure está constantemente evoluindo, não se surpreenda se você encontrar nuances entre as instruções deste guia e o que vê na portal do Azure. As capturas de tela são de v15 BIG-IP, no entanto, permanecem relativamente semelhantes de v 13.1.

## <a name="add-f5-big-ip-from-the-azure-ad-gallery"></a>Adicionar F5 BIG-IP da galeria do Azure AD

A configuração de uma relação de confiança de Federação SAML entre o BIG-IP permite que o BIG-IP do Azure AD libere a pré-autenticação e o [acesso condicional](../conditional-access/overview.md) ao Azure AD, antes de conceder acesso ao serviço de VPN publicado.

1. Entre no portal do AD do Azure usando uma conta com direitos de administrador do aplicativo

2. No painel de navegação à esquerda, selecione o **serviço Azure Active Directory**

3. Vá para **aplicativos empresariais** e, na faixa de opções superior, selecione **novo aplicativo**.

4. Procure F5 na galeria e selecione **F5 Big-IP APM integração do Azure ad**.

5. Forneça um nome para o aplicativo, seguido por **Adicionar/criar** para que ele seja adicionado ao seu locatário. O usuário pode ver o nome como um ícone nos portais de aplicativo do Azure e do Office 365. O nome deve refletir esse serviço específico. Por exemplo, VPN.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

1. Com suas novas propriedades de aplicativo F5 no modo de exibição, vá para **gerenciar**  >  **logon único**

2. Na página **Selecionar um método de logon único**, escolha **SAML**. Ignore o prompt para salvar as configurações de logon único selecionando **não, salvarei mais tarde**.

3. No menu **Configurar logon único com SAML** , selecione o ícone de caneta para a **configuração básica do SAML** para fornecer os seguintes detalhes:

   - Substitua a **URL de identificador** predefinida pela URL do seu serviço publicado de Big IP. Por exemplo, `https://ssl-vpn.contoso.com`

   - Faça o mesmo com a caixa de texto **URL de resposta** , incluindo o caminho do ponto de extremidade do SAML. Por exemplo, `https://ssl-vpn.contoso.com/saml/sp/profile/post/acs`

   - Nessa configuração, o aplicativo funcionaria em um modo iniciado pelo IDP, em que o Azure AD emite ao usuário uma Asserção SAML antes de redirecionar para o serviço SAML BIG-IP. Para aplicativos que não dão suporte ao modo iniciado pelo IDP, especifique a **URL de logon** para o serviço SAML Big-IP. Por exemplo, `https://ssl-vpn.contoso.com`.

   - Para a URL de logout, insira o ponto de extremidade do SLO (logoff único) do APM de IP de grande volume pelo cabeçalho de host do serviço que está sendo publicado. Por exemplo, `https://ssl-vpn.contoso.com/saml/sp/profile/redirect/slr`

   Fornecer uma URL de SLO garante que uma sessão de usuário seja encerrada em ambas as extremidades, o BIG-IP e o Azure AD, depois que o usuário sair. O APM de BIG-IP também fornece uma [opção](https://support.f5.com/csp/article/K12056) para encerrar todas as sessões ao chamar uma URL de aplicativo específica.

![Imagem mostra a configuração básica do SAML](media/f5-sso-vpn/basic-saml-configuration.png).

>[!NOTE]
>De TMOS v16 o ponto de extremidade de SLO SAML foi alterado para/SAML/SP/Profile/Redirect/SLO

4. Selecione **salvar** antes de sair do menu de configuração do SAML e ignorar o prompt de teste de SSO.

Observe as propriedades da seção **atributos de usuário & declarações** , pois o Azure ad as emitirá para os usuários para autenticação APM de Big IP.

![Imagem mostra declarações de atributos de usuário](media/f5-sso-vpn/user-attributes-claims.png)

Sinta-se à vontade para adicionar outras declarações específicas que seu serviço publicado por BIG-IP pode esperar, enquanto observando que todas as declarações definidas além do conjunto padrão serão emitidas somente se existirem no Azure AD, como atributos preenchidos. Da mesma forma, as funções de diretório ou as associações de [grupo](../hybrid/how-to-connect-fed-group-claims.md) também precisam ser definidas em um objeto de usuário no Azure ad antes que possam ser emitidas como uma declaração.

![Imagem mostra o link de download de metadados de Federação](media/f5-sso-vpn/saml-signing-certificate.png)

Os certificados de autenticação SAML criados pelo Azure AD têm um ciclo de vida de três anos, portanto, precisará de gerenciamento usando as diretrizes publicadas do Azure AD.

### <a name="azure-ad-authorization"></a>Autorização do Azure AD

Por padrão, o Azure AD emitirá apenas tokens para usuários que receberam acesso a um serviço.

1. Ainda no modo de exibição de configuração do aplicativo, selecione **usuários e grupos**

2. Selecione **+ Adicionar usuário** e, no menu Adicionar atribuição, selecione **usuários e grupos**

3. Na caixa de diálogo **usuários e grupos** , adicione os grupos de usuários que estão autorizados a acessar a VPN, seguido por **selecionar**  >  **atribuir**

![Imagem mostra adicionar link de usuário ](media/f5-sso-vpn/add-user-link.png)

4. Isso conclui a parte do Azure AD da relação de confiança de Federação SAML. O APM de BIG-IP agora pode ser configurado para publicar o serviço SSL-VPN e configurado com um conjunto correspondente de propriedades para concluir a confiança, para pré-autenticação SAML.

## <a name="big-ip-apm-configuration"></a>Configuração do APM de BIG-IP

### <a name="saml-federation"></a>Federação SAML

A seção a seguir cria o provedor de serviços SAML de BIG-IP e os objetos IDP do SAML correspondentes necessários para concluir a Federação do serviço VPN com o Azure AD.

1. Vá para **acessar**  >  **Federação** do  >  **provedor de serviços SAML**  >  **Serviços do SP local** e selecione **criar**

![Imagem mostra a configuração de BIG-IP SAML](media/f5-sso-vpn/bigip-saml-configuration.png)

2. Insira um **nome** e o mesmo valor de **ID de entidade** que você definiu no Azure ad anteriormente e o FQDN do host que será usado para se conectar ao aplicativo

![Imagem mostra a criação de um novo serviço de SP do SAML](media/f5-sso-vpn/create-new-saml-sp.png)

   As configurações de **nome** do SP só serão necessárias se a ID da entidade não for uma correspondência exata da parte do nome do host da URL publicada ou se ela não estiver no formato de URL baseado em nome de host normal. Forneça o esquema externo e o nome do host do aplicativo que está sendo publicado se a ID da entidade for `urn:ssl-vpn:contosoonline` .

3. Role para baixo para selecionar o novo **objeto do SP do SAML** e selecione **ligar/desassociar conectores IDP**.

![Imagem mostra a criação de Federação com o serviço de SP local](media/f5-sso-vpn/federation-local-sp-service.png)

4. Selecione **criar novo conector IDP** e, no menu suspenso, selecione **dos metadados**

![Imagem mostra criar novo conector IDP](media/f5-sso-vpn/create-new-idp-connector.png)

5. Navegue até o arquivo XML de metadados de Federação que você baixou anteriormente e forneça um **nome de provedor de identidade** para o objeto APM que representará o IDP SAML externo

6. Selecione **Adicionar nova linha** para selecionar o novo conector de IDP externo do Azure AD.

![Imagem mostra o conector IDP externo](media/f5-sso-vpn/external-idp-connector.png)

7. Selecione **Atualizar** para associar o objeto do SP SAML ao objeto do IDP do SAML e selecione **OK**.

![Imagem mostra o IDP do SAML usando o SP](media/f5-sso-vpn/saml-idp-using-sp.png)

### <a name="webtop-configuration"></a>Configuração do Webtop

As etapas a seguir permitem que o SSL-VPN seja oferecido aos usuários por meio do portal da Web proprietário de BIG IP.

1. Acesse **acessar**  >  listas do Webtop do **webtops**  >   e selecione **criar**.

2. Dê um nome ao portal e defina o tipo como **completo**. Por exemplo, `Contoso_webtop`.

3. Ajuste as preferências restantes e selecione **concluído**.

![Imagem mostra a configuração do Webtop](media/f5-sso-vpn/webtop-configuration.png)

### <a name="vpn-configuration"></a>Configuração de VPN

O recurso de VPN é composto de vários elementos, cada um controlando um aspecto diferente do serviço geral.

1. Acesse **acesso**  >  **conectividade/**  >  **rede VPN (VPN)**  >  **pools de concessão IPv4** e selecione **criar**.

2. Forneça um nome para o pool de endereços IP que estão sendo alocados para clientes VPN. Por exemplo, Contoso_vpn_pool

3. Defina o tipo como **intervalo de endereços IP** e forneça um IP inicial e final, seguido de **Adicionar** e **concluído**.

![Imagem mostra a configuração de VPN](media/f5-sso-vpn/vpn-configuration.png)

Uma lista de acesso à rede provisiona o serviço com as configurações de IP e DNS do pool de VPN, permissões de roteamento de usuário e também pode iniciar aplicativos, se necessário.

1. Acesse **acesso**  >  **conectividade/VPN: listas de acesso à rede de acesso à rede (VPN)**  >   e selecione **criar**.

2. Forneça um nome para a lista de acesso VPN e legenda, por exemplo, contoso-VPN seguido por **concluído**.

![Imagem mostra a configuração de VPN na lista de acesso à rede](media/f5-sso-vpn/vpn-configuration-network-access-list.png)

3. Na faixa de opções superior, selecione **configurações de rede** e adicione as configurações abaixo:

   • **Versão de IP com suporte**: IPv4

   • **Pool de concessão IPv4**: selecione o pool de VPN criado anteriormente, por exemplo, Contoso_vpn_pool

![Imagem mostra o pool de VPN da contoso](media/f5-sso-vpn/contoso-vpn-pool.png)

   As opções de configurações do cliente podem ser usadas para impor restrições sobre como o tráfego do cliente é roteado quando uma VPN é estabelecida.

4. Selecione **concluído** e vá para a guia DNS/hosts para adicionar as configurações:

   • **Servidor de nome IPv4 primário**: IP do servidor DNS do seu ambiente

   • **Sufixo de domínio padrão DNS**: o sufixo de domínio para essa conexão VPN específica. Por exemplo, contoso.com

![Imagem mostra o sufixo de domínio padrão](media/f5-sso-vpn/domain-suffix.png)

O [artigo F5](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-network-access-11-5-0/2.html) fornece detalhes sobre como ajustar as configurações restantes de acordo com sua preferência.

Um perfil de conexão BIG-IP agora é necessário para definir as configurações para cada um dos tipos de cliente VPN que o serviço VPN precisa para dar suporte. Por exemplo, Windows, OSX e Android.

1. Acesse **acessar**  >  **conectividade/**  >    >  **perfis** de conectividade de VPN e selecione **Adicionar**.

2. Forneça um nome de perfil e defina o perfil pai como **/Common/Connectivity**, por exemplo, Contoso_VPN_Profile.

![Imagem mostra criar novo perfil de conectividade](media/f5-sso-vpn/create-connectivity-profile.png)

A [documentação](https://techdocs.f5.com/kb/en-us/bigip-edge-apps.html) do se BigIP fornece mais detalhes sobre o suporte do cliente.

## <a name="access-profile-configuration"></a>Configuração do perfil de acesso

Com os objetos VPN configurados, uma política de acesso é necessária para habilitar o serviço para autenticação SAML.

1. Acesse perfis **de acesso**  >  **/políticas**  >  **perfis de acesso (políticas por sessão)** e selecione **criar**

2. Forneça um nome de perfil e para o tipo de perfil, selecione **tudo**, por exemplo, Contoso_network_access

3. Role para baixo para adicionar pelo menos um idioma à lista de **idiomas aceitos** e selecione **concluído**

![Imagem mostra as propriedades gerais](media/f5-sso-vpn/general-properties.png)

4. Selecione **Editar** no campo política de Per-Session do novo perfil de acesso, para que o editor de política Visual seja iniciado em uma guia separada do navegador.

![Imagem mostra a política por sessão](media/f5-sso-vpn/per-session-policy.png)

5. Selecione o **+** sinal e, no pop-up, selecione **autenticação**  >  **SAML auth**  >  **Adicionar item**.

6. Na configuração de SP de autenticação SAML, selecione o objeto VPN SAML SP que você criou anteriormente, seguido por **salvar**.

![Imagem mostra a autenticação SAML](media/f5-sso-vpn/saml-authentication.png)

7. Selecione **+** para o Branch bem-sucedido da autenticação SAML.

8. Na guia atribuição, selecione **avançado recurso atribuir** seguido por **Adicionar item**

![A imagem mostra a atribuição de recursos avançados](media/f5-sso-vpn/advance-resource-assign.png)

9. No pop-up, selecione **nova entrada** e **adicionar/excluir**.

10. Na janela filho, selecione **acesso à rede** e, em seguida, selecione o perfil de acesso à rede criado anteriormente

![Imagem mostra adição de nova entrada de acesso à rede](media/f5-sso-vpn/add-new-entry.png)

11. Alterne para a guia **Webtop** e adicione o objeto do Webtop criado anteriormente.

![Imagem mostra adição de objeto do Webtop](media/f5-sso-vpn/add-webtop-object.png)

12. Selecione **atualização** seguida por **salvar**.

13. Selecione o link na caixa de negação superior para alterar o Branch bem-sucedido  para permitir **salvar**.

![Imagem mostra o novo editor de política Visual](media/f5-sso-vpn/vizual-policy-editor.png)

14. Confirme essas configurações selecionando **aplicar política de acesso** e feche a guia Editor de política Visual.

![Imagem mostra o novo Gerenciador de políticas de acesso](media/f5-sso-vpn/access-policy-manager.png)

## <a name="publish-the-vpn-service"></a>Publicar o serviço de VPN

Com todas as configurações em vigor, o APM agora requer um servidor virtual front-end para escutar clientes que se conectam à VPN.

1. Selecione servidor **local tráfego**  >  **servidores** virtuais  >  **lista** de servidores virtuais e selecione **criar**.

2. Forneça um **nome** para o servidor virtual VPN, por exemplo, **VPN_Listener**.

3. Forneça ao servidor virtual um **endereço de destino IP** não usado que tenha roteamento no local para receber o tráfego do cliente

4. Defina a porta de serviço como **443 https** e verifique se o estado mostra **habilitado**

![Imagem mostra o novo servidor virtual](media/f5-sso-vpn/new-virtual-server.png)

5. Defina o **perfil http** como http e adicione o perfil SSL (cliente) para o certificado SSL público que você provisionou como parte dos pré-requisitos.

![Imagem mostra o perfil SSL](media/f5-sso-vpn/ssl-profile.png)

6. Em política de acesso, defina o perfil de **acesso** e o **perfil de conectividade** para usar os objetos de VPN criados.

![Imagem mostra a política de acesso](media/f5-sso-vpn/access-policy.png)

7. Selecione **concluído** quando terminar.

8.  Seu serviço SSL-VPN agora é publicado e acessível via SHA, diretamente por meio de sua URL ou por meio de portais de aplicativo da Microsoft.

## <a name="additional-resources"></a>Recursos adicionais

- [O fim das senhas, vá para senha](https://www.microsoft.com/security/business/identity/passwordless)

- [O que é Acesso Condicional?](../conditional-access/overview.md)

- [Estrutura de confiança zero da Microsoft para habilitar o trabalho remoto](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [Cinco etapas para a integração total de aplicativos com o Azure AD](../fundamentals/five-steps-to-full-application-integration-with-azure-ad.md)

## <a name="next-steps"></a>Próximas etapas

Abra um navegador em um cliente Windows remoto e navegue até a URL do **serviço de VPN Big-IP**. Depois de autenticar no Azure AD, você verá o portal do Webtop de BIG IP e o inicializador de VPN.

![Imagem mostra o iniciador de VPN](media/f5-sso-vpn/vpn-launcher.png)

A seleção do bloco VPN instalará o cliente de borda BIG-IP e estabelecerá uma conexão VPN configurada para SHA.
O aplicativo de VPN F5 também deve estar visível como um recurso de destino no acesso condicional do Azure AD. Consulte nossas [diretrizes](../conditional-access/concept-conditional-access-policies.md) para criar políticas de acesso condicional e também habilitar usuários para [autenticação sem senha](https://www.microsoft.com/security/business/identity/passwordless)do Azure AD.