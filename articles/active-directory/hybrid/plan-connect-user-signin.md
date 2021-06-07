---
title: 'Azure AD Connect: Conexão do usuário | Microsoft Docs'
description: Conexão do usuário do Azure AD Connect para configurações personalizadas.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 547b118e-7282-4c7f-be87-c035561001df
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/31/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3365a58a0c667ca55b74a5120cdd7a78ad0abc79
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95997776"
---
# <a name="azure-ad-connect-user-sign-in-options"></a>Opções de entrada de usuário do Azure AD Connect
O Azure AD (Azure Active Directory) Connect permite que os usuários se conectem a recursos de nuvem e locais usando as mesmas senhas. Este artigo descreve os principais conceitos de cada modelo de identidade, a fim de ajudá-lo a escolher a identidade que você deseja usar para entrar no Azure AD.

Se já estiver familiarizado com o modelo de identidade do Azure AD e desejar saber mais sobre um método específico, consulte o link apropriado:

* [Sincronização de hash de senha](#password-hash-synchronization) com [SSO (Logon Único) Contínuo](how-to-connect-sso.md)
* [Autenticação de passagem](how-to-connect-pta.md) com [SSO (Logon Único) Contínuo](how-to-connect-sso.md)
* [SSO federado (com Serviços de Federação do Active Directory (AD FS))](#federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2)
* [Federação com PingFederate](#federation-with-pingfederate)

> [!NOTE] 
> É importante lembrar que ao configurar a federação do Azure Active Directory, você estabelece relação de confiança entre seu locatário do Azure Active Directory e seus domínios federados. Com este domínio federado de relação de confiança, os usuários terão acesso aos recursos de nuvem do Azure Active Directory no locatário.  
>

## <a name="choosing-the-user-sign-in-method-for-your-organization"></a>Escolhendo um método de conexão do usuário para sua organização
A primeira decisão de implementar o Azure AD Connect é escolher qual método de autenticação seus usuários usarão para fazer login. É importante que você escolha o método certo que atenda aos requisitos avançados e de segurança de sua organização. A autenticação é fundamental, pois validará as identidades dos usuários para acessar aplicativos e dados na nuvem. Para escolher o método de autenticação correto, você precisa considerar o tempo, a infraestrutura existente, a complexidade e o custo da implementação da sua escolha. Esses fatores são diferentes para cada organização e podem mudar ao longo do tempo.

O Azure AD suporta os seguintes métodos de autenticação: 

* **Autenticação de nuvem** - quando você escolher esse método de autenticação do Azure AD gerencia o processo de autenticação do usuário entrar. Com a autenticação na nuvem, é possível escolher entre duas opções: 
   * **Sincronização de hash de senha (PHS)** - A sincronização de hash de senha permite que os usuários usem o mesmo nome de usuário e senha que usam no local sem precisar implantar nenhuma infraestrutura adicional além do Azure AD Connect. 
   * **Autenticação de passagem (PTA)** - Essa opção é semelhante à hash sync de senha, mas fornece uma validação de senha simples usando agentes de software locais para organizações com políticas de segurança e conformidade fortes.
* **Autenticação federada** - quando você escolhe esse método de autenticação, o Azure AD transferirá o processo de autenticação para um sistema de autenticação confiável separado, como o AD FS ou um sistema de federação de terceiros, para validar a entrada do usuário. 

Para a maioria das organizações que desejam apenas habilitar a entrada do usuário para Microsoft 365, aplicativos SaaS e outros recursos baseados no Azure AD, recomendamos a opção de sincronização de hash de senha padrão.
 
Para obter informações detalhadas sobre como escolher um método de autenticação, confira [Escolher o método de autenticação adequado para sua solução de identidade híbrida do Azure Active Directory](./choose-ad-authn.md)

### <a name="password-hash-synchronization"></a>Sincronização de hash de senha
Com a sincronização de hash de senha, hashes de senhas de usuário são sincronizados do Active Directory local para o Azure AD. Quando as senhas são alteradas ou redefinidas localmente, os novos hashes de senha são sincronizados com o Azure AD imediatamente, para que os usuários sempre possam usar a mesma senha para recursos de nuvem e recursos locais. As senhas nunca são enviadas ao Azure AD nem armazenadas no Azure AD em texto não criptografado. É possível usar a sincronização de hash de senha em conjunto com write-back de senha para habilitar a redefinição de senha de autoatendimento no Azure AD.

Além disso, você também pode habilitar o [SSO contínuo](how-to-connect-sso.md) para usuários em computadores ingressados no domínio que estão na rede corporativa. Com o logon único, os usuários habilitados só precisarão inserir um nome de usuário para ajudá-los a acessar com segurança os recursos de nuvem.

![Sincronização de hash de senha](./media/plan-connect-user-signin/passwordhash.png)

Para obter mais informações, consulte o artigo [Sincronização de hash de senha](how-to-connect-password-hash-synchronization.md).

### <a name="pass-through-authentication"></a>Autenticação de passagem
Com a autenticação de passagem, a senha do usuário é validada no controlador do Active Directory local. A senha não precisa estar presente no Azure AD em nenhum formato. Isso permite que as políticas locais, como restrições de horário de conexão, sejam avaliadas durante a autenticação em serviços de nuvem.

A autenticação de passagem usa um agente simples em um computador ingressado em domínio do Windows Server 2012 R2 no ambiente local. Esse agente escuta as solicitações de validação de senha. Ele não exige que nenhuma porta de entrada seja aberta para a Internet.

Além disso, você também pode habilitar o logon único para usuários em computadores ingressados no domínio que estão na rede corporativa. Com o logon único, os usuários habilitados só precisarão inserir um nome de usuário para ajudá-los a acessar com segurança os recursos de nuvem.
![Autenticação de passagem](./media/plan-connect-user-signin/pta.png)

Para obter mais informações, consulte:
- [Autenticação de passagem](how-to-connect-pta.md)
- [Logon Único](how-to-connect-sso.md)

### <a name="federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2"></a>Federação que usa um farm novo ou existente com o AD FS no Windows Server 2012 R2
Com a conexão federada, os usuários podem se conectar aos serviços baseados no Azure AD com suas senhas locais. Embora eles estejam na rede corporativa, eles nem precisam inserir suas senhas. Ao usar a opção de federação com o AD FS, é possível implantar um farm novo ou existente com o AD FS no Windows Server 2012 R2. Se você optar por especificar um farm existente, o Azure AD Connect configurará a relação de confiança entre o farm e o Azure AD, para que os usuários possam se conectar.

<center>

![Federação com o AD FS no Windows Server 2012 R2](./media/plan-connect-user-signin/federatedsignin.png)</center>

#### <a name="deploy-federation-with-ad-fs-in-windows-server-2012-r2"></a>Implantar a federação com o AD FS no Windows Server 2012 R2

Se você estiver implantando um novo farm, será necessário:

* Um servidor Windows Server 2012 R2 para o servidor de federação.
* Um servidor Windows Server 2012 R2 para o proxy de aplicativo Web.
* Um arquivo. pfx com um certificado TLS/SSL para o nome do serviço de Federação desejado. Por exemplo: fs.contoso.com.

Se você estiver implantando um novo farm ou usando um farm existente, será necessário:

* Credenciais de administrador local nos servidores de federação.
* Credenciais de administrador local em servidores de grupo de trabalho (não ingressados no domínio) nos quais você pretende implantar a função de Proxy de Aplicativo Web.
* O computador no qual você executa o assistente precisa conseguir se conectar aos outros computadores nos quais você deseja instalar o AD FS ou o Proxy de Aplicativo Web usando o Gerenciamento Remoto do Windows.

Para obter mais informações, consulte [Configurando o SSO com o AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs).

### <a name="federation-with-pingfederate"></a>Federação com PingFederate
Com a conexão federada, os usuários podem se conectar aos serviços baseados no Azure AD com suas senhas locais. Embora eles estejam na rede corporativa, eles nem precisam inserir suas senhas.

Para mais informações em como configurar o PingFederate para usá-lo com o Azure Active Directory, veja [Integração PingFederate com o Azure Active Directory e Office 365](https://www.pingidentity.com/AzureADConnect)

Para mais informações em como configurar o Azure AD Connect usando o PingFederate, veja [instalação personalizada do Azure AD Connect](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)

#### <a name="sign-in-by-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Conectar usando uma versão anterior do AD FS ou uma solução de terceiros
Se você já tiver configurado a conexão na nuvem usando uma versão anterior do AD FS (como o AD FS 2.0) ou um provedor de federação de terceiros, poderá optar por ignorar a configuração de conexão do usuário por meio do Azure AD Connect. Isso permitirá obter a última sincronização e outras funcionalidades do Azure AD Connect enquanto estiver usando a solução existente para conexão.

Para obter mais informações, consulte a [lista de compatibilidade de federação de terceiros do Azure AD](how-to-connect-fed-compatibility.md).


## <a name="user-sign-in-and-user-principal-name"></a>Conexão do usuário e nome UPN
### <a name="understanding-user-principal-name"></a>Entendendo o nome UPN
No Active Directory, o sufixo de nome UPN padrão é o nome DNS do domínio no qual a conta de usuário foi criada. Na maioria dos casos, esse é o nome de domínio registrado como o domínio corporativo na Internet. No entanto, é possível adicionar mais sufixos UPN usando Domínios e Relações de Confiança do Active Directory.

O UPN do usuário está no formato username@domain. Por exemplo, para um Domínio do Active Directory chamado “contoso.com”, um usuário chamado Julio pode ter o UPN “john@contoso.com”. O UPN do usuário baseia-se em RFC 822. Embora o UPN e o email compartilhem o mesmo formato, o valor do UPN de um usuário pode ou não ser igual ao endereço de email do usuário.

### <a name="user-principal-name-in-azure-ad"></a>Nome UPN no Azure AD
O assistente do Azure AD Connect usa o atributo userPrincipalName ou permite especificar o atributo (em uma instalação personalizada) a ser usado de locais como o nome UPN no Azure AD. Esse é o valor que será usado para entrar no Azure AD. Se o valor do atributo userPrincipalName não corresponder a um domínio verificado no Azure AD, o Azure AD o substituirá por um valor .onmicrosoft.com padrão.

Cada diretório no Azure Active Directory é fornecido com um nome de domínio interno, com o formato contoso.onmicrosoft.com, que permite começar a usar o Azure ou outros serviços da Microsoft. É possível melhorar e simplificar a experiência de conexão usando domínios personalizados. Para obter informações sobre nomes de domínio personalizados no Azure AD e como verificar um domínio, consulte [Adicionar seu nome de domínio personalizado ao Azure Active Directory](../fundamentals/add-custom-domain.md).

## <a name="azure-ad-sign-in-configuration"></a>Configuração de entrada do Azure AD
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Configuração de entrada do Azure AD entrar com o Azure AD Connect
A experiência de conexão do Azure AD depende da capacidade do Azure AD de corresponder o sufixo do nome UPN de um usuário que está sendo sincronizado a um dos domínios personalizados verificados no diretório do Azure AD. O Azure AD Connect fornece ajuda ao definir as configuração de conexão no Azure AD, para que a experiência de conexão do usuário na nuvem seja semelhante à experiência local.

O Azure AD Connect lista os sufixos UPN que estão definidos para os domínios e tenta correspondê-los a um domínio personalizado no Azure AD. Em seguida, ele ajuda você com a ação apropriada que precisa ser tomada.
A página de conexão do Azure AD lista os sufixos UPN definidos para o Active Directory local e exibe o status correspondente em cada sufixo. Os valores de status podem ser um dos seguintes:

| Estado | Descrição | Ação necessária |
|:--- |:--- |:--- |
| Verificado |O Azure AD Connect encontrou uma correspondência de domínio verificado no Azure AD. Todos os usuários deste domínio podem se conectar usando suas credenciais locais. |Nenhuma ação é necessária. |
| Não verificado |O Azure AD Connect encontrou uma correspondência de domínio personalizado, mas ele não é verificado. O sufixo UPN dos usuários desse domínio será alterado para o sufixo .onmicrosoft.com padrão após a sincronização, caso o domínio não seja verificado. | [Verifique o domínio personalizado no Azure AD.](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) |
| Não adicionado |O Azure AD Connect não encontrou um domínio personalizado que correspondesse ao sufixo UPN. O sufixo UPN dos usuários desse domínio será alterado para o sufixo .onmicrosoft.com padrão, caso o domínio não seja adicionado e verificado no Azure. | [Adicione e verifique um domínio personalizado que corresponde ao sufixo UPN.](../fundamentals/add-custom-domain.md) |

A página de conexão do Azure AD lista os sufixos UPN definidos para o Active Directory local e o domínio personalizado correspondente no Azure AD com o status de verificação atual. Em uma instalação personalizada, agora é possível selecionar o atributo do nome UPN na página de **conexão do Azure AD**.

![Página de entrada do Azure AD](./media/plan-connect-user-signin/custom_azure_sign_in.png)

É possível clicar no botão atualizar para buscar novamente o último status dos domínios personalizados do Azure AD.

### <a name="selecting-the-attribute-for-the-user-principal-name-in-azure-ad"></a>Selecionando o atributo para o nome UPN no Azure AD
O atributo userPrincipalName é o atributo que os usuários usam ao entrarem no Azure AD e Microsoft 365. É necessário verificar os domínios (também conhecidos como sufixos UPN) usados no Azure AD antes de sincronizar os usuários.

É altamente recomendável manter o atributo userPrincipalName padrão. Se esse atributo não for roteável e se não puder ser verificado, será possível selecionar outro atributo (email, por exemplo) como o atributo que contém a ID de conexão. Isso é conhecido como a ID Alternativa. O valor de atributo da ID Alternativa deve seguir o padrão RFC 822. É possível usar uma ID Alternativa tanto com o SSO de senha quanto com o SSO de federação como a solução de conexão.

> [!NOTE]
> O uso de uma ID alternativa não é compatível com todas as cargas de trabalho de Microsoft 365. Para obter mais informações, consulte [Configurando uma ID de logon alternativa](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id).
>
>

#### <a name="different-custom-domain-states-and-their-effect-on-the-azure-sign-in-experience"></a>Diferentes estados de domínio personalizado e seu efeito sobre a experiência de conexão do Azure
É muito importante entender o relacionamento entre os estados de domínio personalizado no diretório do Azure AD e os sufixos UPN definidos localmente. Vamos examinar as diferentes experiências de conexão possíveis no Azure ao configurar a sincronização usando o Azure AD Connect.

Para as informações a seguir, suponhamos que o sufixo UPN contoso.com seja de nosso interesse, que é usado no diretório local como parte do UPN – por exemplo user@contoso.com.

###### <a name="express-settingspassword-hash-synchronization"></a>Configurações expressas/sincronização de hash de senha

| Estado | Efeito sobre a experiência de entrada do usuário do Azure |
|:---:|:--- |
| Não adicionado |Nesse caso, nenhum domínio personalizado para contoso.com foi adicionado no diretório do Azure AD. Os usuários que têm o UPN local com o sufixo @contoso.com não poderão usar seus UPNs locais para entrar no Azure. Em vez disso, eles precisarão usar um novo UPN fornecido pelo Azure AD adicionando o sufixo do diretório padrão do Azure AD. Por exemplo, se você estiver sincronizando usuários com o diretório do Azure AD azurecontoso.onmicrosoft.com, o usuário local user@contoso.com receberá um UPN igual a user@azurecontoso.onmicrosoft.com. |
| Não verificado |Nesse caso, temos um domínio personalizado contoso.com que é adicionado ao diretório do Azure AD. No entanto, ele ainda não foi verificado. Se você continuar a sincronização dos usuários sem verificar o domínio, os usuários serão atribuídos a um novo UPN do Azure AD, exatamente como no cenário “Não adicionado”. |
| Verificado |Nesse caso, temos um domínio personalizado contoso.com já adicionado e verificado no Azure AD para o sufixo UPN. Os usuários poderão usar seu nome UPN local, por exemplo user@contoso.com, para entrarem no Azure depois de serem sincronizados com o Azure AD. |

###### <a name="ad-fs-federation"></a>Federação do AD FS
Não é possível criar uma federação com o domínio .onmicrosoft.com padrão no Azure AD nem com um domínio personalizado não verificado no Azure AD. Ao executar o assistente do Azure AD Connect, se você selecionar um domínio não verificado para criar uma federação, o Azure AD Connect solicitará que os registros necessários sejam criados no local em que o DNS está hospedado para o domínio. Para obter mais informações, consulte [Verificar o domínio do Azure AD selecionado para federação](how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Se você selecionou a opção de conexão do usuário **Federação com o AD FS**, você deverá ter um domínio personalizado para continuar a criação de uma federação no Azure AD. Para nossa discussão, isso significa que devemos ter um domínio personalizado contoso.com adicionado ao diretório do Azure AD.

| Estado | Efeito sobre a experiência de conexão do usuário do Azure |
|:---:|:--- |
| Não adicionado |Nesse caso, o Azure AD Connect não encontrou um domínio personalizado correspondente para o sufixo UPN contoso.com no diretório do Azure AD. Será necessário adicionar um domínio contoso.com personalizado se você precisar que os usuários se conectem usando o AD FS com seus UPNs locais (como user@contoso.com). |
| Não verificado |Nesse caso, o Azure AD Connect solicitará os detalhes adequados sobre como é possível verificar o domínio em um estágio posterior. |
| Verificado |Nesse caso, é possível continuar a configuração sem nenhuma ação adicional. |

## <a name="changing-the-user-sign-in-method"></a>Alterando o método de conexão do usuário
É possível alterar o método de conexão do usuário de federação, sincronização de hash de senha ou autenticação de passagem usando as tarefas disponíveis no Azure AD Connect após a configuração inicial do Azure AD Connect com o assistente. Execute o assistente do Azure AD Connect novamente e você verá uma lista de tarefas que podem ser executadas. Selecione **Alterar a entrada do usuário** na lista de tarefas.

![Alterar a entrada do usuário](./media/plan-connect-user-signin/changeusersignin.png)

Na próxima página, você deverá fornecer as credenciais do Azure AD.

![Captura de tela que mostra onde você deve digitar as credenciais do Azure AD.](./media/plan-connect-user-signin/changeusersignin2.png)

Na página **Entrada de usuário**, selecione a entrada do usuário desejada.

![Conecte-se ao AD do Azure](./media/plan-connect-user-signin/changeusersignin2a.png)

> [!NOTE]
> Se estiver fazendo apenas uma mudança temporária para a sincronização de hash de senha, marque a caixa de seleção **Não converter contas de usuário**. Não marcar a opção converterá cada usuário em federado, o que pode levar várias horas.
>
>

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre como [integrar suas identidades locais com o Azure Active Directory](whatis-hybrid-identity.md).
- Saiba mais sobre os [conceitos de design do Azure AD Connect](plan-connect-design-concepts.md).