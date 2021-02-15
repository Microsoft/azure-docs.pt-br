---
title: Personalizar uma instalação do Azure Active Directory Connect
description: Este artigo explica as opções de instalação personalizadas para Azure AD Connect. Use estas instruções para instalar o Active Directory por meio do Azure AD Connect.
services: active-directory
keywords: o que é o Azure AD Connect, instalar o Active Directory, componentes necessários do AD do Azure
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 6d42fb79-d9cf-48da-8445-f482c4c536af
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/10/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3afeadff71bd373354b891bd6690d94d28fc0805
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096344"
---
# <a name="custom-installation-of-azure-active-directory-connect"></a>Instalação personalizada do Azure Active Directory Connect
Use *as configurações personalizadas* no Azure Active Directory (Azure AD) Connect quando desejar mais opções para a instalação. Use essas configurações, por exemplo, se você tiver várias florestas ou se quiser configurar recursos opcionais. Use configurações personalizadas em todos os casos em que a [instalação expressa](how-to-connect-install-express.md) não atenda às suas necessidades de implantação ou de topologia.

Pré-requisitos:
- [Baixar o Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771).
- Conclua as etapas de pré-requisito em [Azure ad Connect: hardware e pré-requisitos](how-to-connect-install-prerequisites.md). 
- Verifique se você tem as contas descritas em [Azure ad Connect contas e permissões](reference-connect-accounts-permissions.md).

## <a name="custom-installation-settings"></a>Configurações de instalação personalizada 

Para configurar uma instalação personalizada para Azure AD Connect, percorra as páginas do assistente que as seções a seguir descrevem.

### <a name="express-settings"></a>Configurações expressas
Na página **configurações expressas** , selecione **Personalizar** para iniciar uma instalação de configurações personalizadas.  O restante deste artigo o orientará no processo de instalação personalizada. Use os links a seguir para acessar rapidamente as informações de uma página específica:

- [Componentes necessários](#install-required-components)
- [Entrada do usuário](#user-sign-in)
- [Conecte-se ao AD do Azure](#connect-to-azure-ad)
- [Sincronizar](#sync-pages)

### <a name="install-required-components"></a>Instalar componentes necessários
Ao instalar os serviços de sincronização, você pode deixar a seção de configuração opcional desmarcada. Azure AD Connect configura tudo automaticamente. Ele configura uma instância LocalDB do SQL Server 2012 Express, cria os grupos apropriados e atribui permissões. Se você quiser alterar os padrões, desmarque as caixas apropriadas.  A tabela a seguir resume essas opções e fornece links para informações adicionais. 

![Captura de tela mostrando seleções opcionais para os componentes de instalação necessários no Azure AD Connect.](./media/how-to-connect-install-custom/requiredcomponents2.png)

| Configuração opcional | Descrição |
| --- | --- |
|Especificar um local de instalação personalizado| Permite que você altere o caminho de instalação padrão para Azure AD Connect.|
| Usar um SQL Server existente |Permite especificar o nome do SQL Server e o nome da instância. Escolha esta opção se você já tiver um servidor de banco de dados que deseja usar. Para **nome da instância**, insira o nome da instância, uma vírgula e o número da porta se sua instância de SQL Server não tiver a navegação habilitada.  Em seguida, especifique o nome do banco de dados do Azure AD Connect.  Seus privilégios SQL determinam se um novo banco de dados pode ser criado ou se o administrador do SQL deve criar o banco de dados com antecedência.  Se você tiver permissões de administrador (SA) SQL Server, consulte [instalar Azure ad Connect usando um banco de dados existente](how-to-connect-install-existing-database.md).  Se você tiver permissões delegadas (DBO), consulte [instalar Azure ad Connect usando as permissões de administrador do SQL delegado](how-to-connect-install-sql-delegation.md). |
| Usar uma conta de serviço existente |Por padrão, o Azure AD Connect fornece uma conta de serviço virtual para os serviços de sincronização. Se você usar uma instância remota do SQL Server ou usar um proxy que exija autenticação, poderá usar uma *conta de serviço gerenciado* ou uma conta de serviço protegida por senha no domínio. Nesses casos, insira a conta que você deseja usar. Para executar a instalação, você precisa ser uma SA no SQL para que possa criar credenciais de entrada para a conta de serviço. Para saber mais, confira [Contas e permissões do Azure AD Connect](reference-connect-accounts-permissions.md#adsync-service-account). </br></br>Usando a compilação mais recente, o administrador do SQL agora pode provisionar o banco de dados fora da banda. Em seguida, o administrador do Azure AD Connect pode instalá-lo com direitos de proprietário do banco de dados.  Para obter mais informações, confira [Instale o Azure AD Connect usando permissões de administrador do SQL delegado](how-to-connect-install-sql-delegation.md).|
| Especificar grupos de sincronização personalizados |Por padrão, quando os serviços de sincronização são instalados, Azure AD Connect cria quatro grupos que são locais para o servidor. Esses grupos são administradores, operadores, navegação e redefinição de senha. Você pode especificar seus próprios grupos aqui. Os grupos devem ser locais no servidor. Eles não podem estar localizados no domínio. |
|Importar configurações de sincronização (versão prévia)|Permite que você importe configurações de outras versões do Azure AD Connect.  Para obter mais informações, consulte [importando e exportando definições de configuração de Azure ad Connect](how-to-connect-import-export-config.md).|

### <a name="user-sign-in"></a>Entrada do usuário
Depois de instalar os componentes necessários, selecione o método de logon único dos usuários. A tabela a seguir descreve brevemente as opções disponíveis. Para obter uma descrição completa dos métodos de entrada, consulte [Entrada do usuário](plan-connect-user-signin.md).

![Captura de tela que mostra a página "entrada do usuário". A opção "sincronização de hash de senha" está selecionada.](./media/how-to-connect-install-custom/usersignin4.png)

| Opção de logon único | Descrição |
| --- | --- |
| Sincronização de hash de senha |Os usuários podem entrar nos serviços de nuvem da Microsoft, como Microsoft 365, usando a mesma senha que eles usam em sua rede local. As senhas de usuário são sincronizadas com o Azure AD como um hash de senha. A autenticação ocorre na nuvem. Para obter mais informações, consulte [sincronização de hash de senha](how-to-connect-password-hash-synchronization.md). |
|Autenticação de passagem|Os usuários podem entrar nos serviços de nuvem da Microsoft, como Microsoft 365, usando a mesma senha que eles usam em sua rede local.  As senhas de usuário são validadas ao serem passadas para o controlador de domínio Active Directory local.
| Federação com o AD FS |Os usuários podem entrar nos serviços de nuvem da Microsoft, como Microsoft 365, usando a mesma senha que eles usam em sua rede local.  Os usuários são redirecionados à instância do AD FS (serviços de Federação do Azure Directory) locais para entrar. A autenticação ocorre localmente. |
| Federação com PingFederate|Os usuários podem entrar nos serviços de nuvem da Microsoft, como Microsoft 365, usando a mesma senha que eles usam em sua rede local.  Os usuários são redirecionados para sua instância de PingFederate local para entrar. A autenticação ocorre localmente. |
| Não configurar |Nenhum recurso de entrada do usuário está instalado ou configurado. Escolha esta opção se você já tiver um servidor de Federação de terceiros ou outra solução em vigor. |
|Habilitar logon único|Essa opção está disponível com a sincronização de hash de senha e a autenticação de passagem. Ele fornece uma experiência de logon único para usuários de área de trabalho em redes corporativas. Para obter mais informações, consulte [logon único](how-to-connect-sso.md). </br></br>**Observação:** Para clientes AD FS, essa opção não está disponível. O AD FS já oferece o mesmo nível de logon único.</br>

### <a name="connect-to-azure-ad"></a>Conecte-se ao AD do Azure
Na página **conectar ao Azure ad** , insira uma conta de administrador global e uma senha. Se você selecionou **Federação com AD FS** na página anterior, não entre com uma conta que esteja em um domínio que você planeja habilitar para Federação. 

Talvez você queira usar uma conta no domínio *onmicrosoft.com* padrão, que vem com seu locatário do Azure AD. Essa conta é usada somente para criar uma conta de serviço no Azure AD. Ele não é usado após a conclusão da instalação.
  
![Captura de tela mostrando a página "conectar ao Azure AD".](./media/how-to-connect-install-custom/connectaad.png)

Se sua conta de administrador global tiver a autenticação multifator habilitada, você fornecerá a senha novamente na janela de entrada e deverá concluir o desafio de autenticação multifator. O desafio pode ser um código de verificação ou uma chamada telefônica.  

![Captura de tela mostrando a página "conectar ao Azure AD". Um campo de autenticação multifator solicita ao usuário um código.](./media/how-to-connect-install-custom/connectaadmfa.png)

A conta de administrador global também pode ter o [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md) habilitado.

Se você vir um erro ou tiver problemas de conectividade, consulte [solucionar problemas de conectividade](tshoot-connect-connectivity.md).

## <a name="sync-pages"></a>Sincronizar páginas

As seções a seguir descrevem as páginas na seção **sincronizar** .

### <a name="connect-your-directories"></a>Conectar seus diretórios
Para se conectar ao Active Directory Domain Services (AD DS do Azure), Azure AD Connect precisa do nome da floresta e das credenciais de uma conta que tenha permissões suficientes.

![Captura de tela que mostra a página "conectar seus diretórios".](./media/how-to-connect-install-custom/connectdir01.png)

Depois de inserir o nome da floresta e selecionar  **Adicionar diretório**, uma janela será exibida. A tabela a seguir descreve suas opções.

| Opção | Descrição |
| --- | --- |
| Criar nova conta | Crie a conta de AD DS do Azure que Azure AD Connect precisa se conectar à floresta do Active Directory durante a sincronização de diretórios. Depois de selecionar essa opção, insira o nome de usuário e a senha de uma conta de administrador corporativo.  Azure AD Connect usa a conta de administrador corporativo fornecida para criar a conta de AD DS do Azure necessária. Você pode inserir a parte do domínio no formato NetBIOS ou no formato FQDN. Ou seja, insira *FABRIKAM\administrator* ou *fabrikam. com\administrator*. |
| Usar conta existente | Forneça uma conta existente do Azure AD DS que Azure AD Connect possa usar para se conectar à floresta do Active Directory durante a sincronização de diretórios. Você pode inserir a parte do domínio no formato NetBIOS ou no formato FQDN. Ou seja, insira *FABRIKAM\syncuser* ou *fabrikam. com\syncuser*. Essa conta pode ser uma conta de usuário comum, pois precisa apenas das permissões de leitura padrão. Mas, dependendo do seu cenário, talvez você precise de mais permissões. Para saber mais, confira [Contas e permissões do Azure AD Connect](reference-connect-accounts-permissions.md#create-the-ad-ds-connector-account). |

![Captura de tela mostrando a página "conectar diretório" e a janela da conta da floresta D, na qual você pode optar por criar uma nova conta ou usar uma conta existente.](./media/how-to-connect-install-custom/connectdir02.png)

>[!NOTE]
> A partir do Build 1.4.18.0, você não pode usar uma conta de administrador corporativo ou de administrador de domínio como a conta do conector de AD DS do Azure. Ao selecionar **usar conta existente**, se você tentar inserir uma conta de administrador corporativo ou uma conta de administrador de domínio, você verá o seguinte erro: "não é permitido usar uma conta de administrador corporativo ou de domínio para sua conta de floresta do AD. Permita que Azure AD Connect crie a conta para você ou especifique uma conta de sincronização com as permissões corretas. "
>

### <a name="azure-ad-sign-in-configuration"></a>Configuração de entrada do Azure AD
Na página de **configuração de entrada do Azure ad** , examine os domínios do nome principal do usuário (UPN) no Azure AD DS local. Esses domínios UPN foram verificados no Azure AD. Nessa página, você configura o atributo a ser usado para o userPrincipalName.

![Captura de tela mostrando domínios não verificados na página "configuração de entrada do Azure A D".](./media/how-to-connect-install-custom/aadsigninconfig2.png)  

Examine todos os domínios marcados como **não adicionados** ou **não verificados**. Verifique se os domínios que você usa foram verificados no Azure AD. Depois de verificar seus domínios, selecione o ícone de atualização circular. Para obter mais informações, consulte [Adicionar e verificar o domínio](../fundamentals/add-custom-domain.md).

Os usuários usam o atributo *userPrincipalName* quando entram no Azure AD e Microsoft 365. O Azure AD deve verificar os domínios, também conhecidos como sufixo UPN, antes que os usuários sejam sincronizados. A Microsoft recomenda que você mantenha o atributo padrão userPrincipalName. 

Se o atributo userPrincipalName for não roteável e não puder ser verificado, você poderá selecionar outro atributo. Você pode, por exemplo, selecionar email como o atributo que contém a ID de entrada. Quando você usa um atributo diferente de userPrincipalName, ele é conhecido como uma *ID alternativa*. 

O valor do atributo de ID alternativo deve seguir o padrão RFC 822. Você pode usar uma ID alternativa com sincronização de hash de senha, autenticação de passagem e Federação. No Active Directory, o atributo não pode ser definido como multivalor, mesmo que ele tenha apenas um único valor. Para obter mais informações sobre a ID alternativa, consulte [autenticação de passagem: perguntas](./how-to-connect-pta-faq.md#does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname)frequentes.

>[!NOTE]
> Ao habilitar a autenticação de passagem, você deve ter pelo menos um domínio verificado para continuar o processo de instalação personalizada.

> [!WARNING]
> As IDs alternativas não são compatíveis com todas as cargas de trabalho de Microsoft 365. Para obter mais informações, consulte [Configurando IDs de entrada alternativas](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id).
>

### <a name="domain-and-ou-filtering"></a>Domínio e filtragem de unidade organizacional
Por padrão, todos os domínios e UOs (unidades organizacionais) são sincronizados. Se não quiser sincronizar alguns domínios ou UOs com o Azure AD, você poderá desmarcar as seleções apropriadas.  

![Captura de tela mostrando a página de filtragem de domínio e O U.](./media/how-to-connect-install-custom/domainoufiltering.png)  

Esta página configura a filtragem baseada em domínio e na UO. Se você planeja fazer alterações, consulte [filtragem baseada em domínio](how-to-connect-sync-configure-filtering.md#domain-based-filtering) e [filtragem baseada em UO](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). Algumas UOs são essenciais para a funcionalidade, portanto, você deve deixá-las selecionadas.

Se você usar a filtragem baseada em UO com uma versão Azure AD Connect mais antiga que 1.1.524.0, as novas UOs serão sincronizadas por padrão. Se você não quiser que novas UOs sejam sincronizadas, poderá ajustar o comportamento padrão após a etapa de [filtragem baseada em UO](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) . Para Azure AD Connect 1.1.524.0 ou posterior, você pode indicar se deseja que novas UOs sejam sincronizadas.

Se você planeja usar a [filtragem baseada em grupo](#sync-filtering-based-on-groups), verifique se a UO com o grupo está incluída e não está filtrada usando a filtragem de UO. A filtragem de UO é avaliada antes que a filtragem baseada em grupo seja avaliada.

Também é possível que alguns domínios estejam inacessíveis devido a restrições de firewall. Esses domínios são desmarcados por padrão e exibem um aviso.  

![Captura de tela mostrando domínios inacessíveis.](./media/how-to-connect-install-custom/unreachable.png)  

Se você vir esse aviso, verifique se esses domínios estão realmente inacessíveis e se o aviso é esperado.

### <a name="uniquely-identifying-your-users"></a>Identificando seus usuários com exclusividade

Na página **identificando usuários** , escolha como identificar usuários em seus diretórios locais e como identificá-los usando o atributo sourceAnchor.

#### <a name="select-how-users-should-be-identified-in-your-on-premises-directories"></a>Selecione como os usuários devem ser identificados em seus diretórios locais
Usando o recurso *correspondência entre florestas* , você pode definir como os usuários de suas florestas AD DS do Azure são representados no Azure AD. Um usuário pode ser representado apenas uma vez em todas as florestas ou pode ter uma combinação de contas habilitadas e desabilitadas. O usuário também pode ser representado como um contato em algumas florestas.

![Captura de tela mostrando a página onde você pode identificar exclusivamente os usuários.](./media/how-to-connect-install-custom/unique2.png)

| Configuração | Descrição |
| --- | --- |
| [Os usuários são representados apenas uma vez em todas as florestas](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Todos os usuários são criados como objetos individuais no AD do Azure. Os objetos não são Unidos no metaverso. |
| [Atributo de email](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Essa opção associa usuários e contatos quando o atributo de email tem o mesmo valor em florestas diferentes. Use esta opção quando seus contatos forem criados usando GALSync. Se você escolher essa opção, os objetos de usuário cujo atributo de email é não preenchido não serão sincronizados com o Azure AD. |
| [Atributos objectSid e msExchangeMasterAccountSID/msRTCSIP-OriginatorSID](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Essa opção associa um usuário habilitado em uma floresta de conta com um usuário desabilitado em uma floresta de recursos. No Exchange, essa configuração é conhecida como uma caixa de correio vinculada. Você pode usar essa opção se usar somente o Lync e se o Exchange não estiver presente na floresta de recursos. |
| Atributos SAMAccountName e MailNickName |Essa opção une os atributos em que se espera que a ID de entrada do usuário seja encontrada. |
| Escolher um atributo específico |Essa opção permite que você selecione seu próprio atributo. Se você escolher essa opção, os objetos de usuário cujo atributo (selecionado) não será preenchido não serão sincronizados com o Azure AD. **Limitação:** Somente os atributos que já estão no metaverso estão disponíveis para essa opção. |

#### <a name="select-how-users-should-be-identified-by-using-a-source-anchor"></a>Selecionar como os usuários devem ser identificados usando uma âncora de origem
O atributo *sourceAnchor* é imutável durante o tempo de vida de um objeto de usuário. É a chave primária que vincula o usuário local ao usuário no Azure AD.

| Configuração | Descrição |
| --- | --- |
| Permitir que o Azure gerencie a âncora de origem | Selecione esta opção se deseja que Azure AD escolha o atributo para você. Se você selecionar essa opção, Azure AD Connect aplicará a lógica de seleção de atributo sourceAnchor descrita em [usando MS-DS-ConsistencyGuid como sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). Após a conclusão da instalação personalizada, você verá qual atributo foi escolhido como o atributo sourceAnchor. |
| Escolher um atributo específico | Selecione esta opção se desejar especificar um atributo existente do AD como o atributo sourceAnchor. |

Como o atributo sourceAnchor não pode ser alterado, você deve escolher um atributo apropriado. Um bom candidato é objectGUID. Esse atributo não é alterado, a menos que a conta de usuário seja movida entre florestas ou domínios. Não escolha atributos que podem ser alterados quando uma pessoa casasse ou altera atribuições. 

Você não pode usar atributos que incluem um sinal de arroba (@), portanto, você não pode usar email e userPrincipalName. O atributo também diferencia maiúsculas de minúsculas, portanto, quando você move um objeto entre florestas, lembre-se de preservar letras maiúsculas e minúsculas. Os atributos binários são codificados em base64, mas outros tipos de atributo permanecem em seu estado não codificado. 

Em cenários de Federação e algumas interfaces do Azure AD, o atributo sourceAnchor também é conhecido como *imutávelid*. 

Para obter mais informações sobre a âncora de origem, consulte [conceitos de design](plan-connect-design-concepts.md#sourceanchor).

### <a name="sync-filtering-based-on-groups"></a>Filtragem de sincronização com base em grupos
O recurso filtrar por grupos permite sincronizar apenas um pequeno subconjunto de objetos para um piloto. Para usar esse recurso, crie um grupo para essa finalidade em sua instância local do Active Directory. Em seguida, adicione usuários e grupos que devem ser sincronizados ao AD do Azure como membros diretos. Posteriormente, você poderá adicionar usuários ou remover usuários desse grupo para manter a lista de objetos que devem estar presentes no Azure AD. 

Todos os objetos que você deseja sincronizar devem ser membros diretos do grupo. Usuários, grupos, contatos e computadores ou dispositivos devem ser membros diretos. A associação de grupo aninhada não foi resolvida. Quando você adiciona um grupo como um membro, somente o grupo em si é adicionado. Seus membros não são adicionados.

![Captura de tela mostrando a página onde você pode escolher como filtrar usuários e dispositivos.](./media/how-to-connect-install-custom/filter2.png)

> [!WARNING]
> Este recurso destina-se a dar suporte a apenas uma implantação piloto. Não o use em uma implantação de produção completa.
>

Em uma implantação de produção completa, seria difícil manter um único grupo e todos os seus objetos para sincronizar. Em vez do recurso de filtragem em grupos, use um dos métodos descritos em [Configurar filtragem](how-to-connect-sync-configure-filtering.md).

### <a name="optional-features"></a>Recursos opcionais
Na próxima página, você pode selecionar recursos opcionais para seu cenário.

>[!WARNING]
>Azure AD Connect versões 1.0.8641.0 e anteriores dependem do serviço de controle de acesso do Azure para Write-back de senha.  Esse serviço foi desativado em 7 de novembro de 2018.  Se você usar qualquer uma dessas versões do Azure AD Connect e tiver habilitado o Write-back de senha, os usuários poderão perder a capacidade de alterar ou redefinir suas senhas quando o serviço for desativado. Essas versões do Azure AD Connect não oferecem suporte a Write-back de senha.
>
>Para obter mais informações, consulte [migrar do serviço de controle de acesso do Azure](../azuread-dev/active-directory-acs-migration.md).
>
>Se você quiser usar o Write-back de senha, baixe a [versão mais recente do Azure ad Connect](https://www.microsoft.com/download/details.aspx?id=47594).

![Captura de tela mostrando a página "recursos opcionais".](./media/how-to-connect-install-custom/optional2a.png)

> [!WARNING]
> Se Azure AD Sync ou a sincronização direta (DirSync) estiverem ativas, não ative nenhum recurso de write-back no Azure AD Connect.



| Recursos opcionais | Descrição |
| --- | --- |
| Implantação híbrida do Exchange |O recurso de implantação híbrida do Exchange permite a coexistência de caixas de correio do Exchange locais e em Microsoft 365. Azure AD Connect sincroniza um conjunto específico de [atributos](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) do Azure AD de volta para seu diretório local. |
| Pastas públicas de email do Exchange | O recurso de pastas públicas do Exchange mail permite que você sincronize objetos de pasta pública habilitados para email de sua instância local do Active Directory para o Azure AD. |
| Aplicativo AD do Azure e filtragem de atributos |Habilitando o aplicativo do Azure AD e a filtragem de atributos, você pode personalizar o conjunto de atributos sincronizados. Essa opção adiciona mais duas páginas de configuração ao assistente. Para saber mais, confira [Aplicativo e filtragem de atributos do Azure AD](#azure-ad-app-and-attribute-filtering). |
| Sincronização de hash de senha |Se você selecionou Federação como a solução de entrada, você pode habilitar a sincronização de hash de senha. Em seguida, você pode usá-lo como uma opção de backup.  </br></br>Se você selecionou a autenticação de passagem, poderá habilitar essa opção para garantir o suporte para clientes herdados e para fornecer um backup.</br></br> Para obter mais informações, consulte [sincronização de hash de senha](how-to-connect-password-hash-synchronization.md).|
| write-back de senha |Use esta opção para garantir que as alterações de senha originadas no Azure AD sejam gravadas no diretório local. Para saber mais, confira [Introdução ao gerenciamento de senhas](../authentication/tutorial-enable-sspr.md). |
| Write-back de grupo |Se você usar grupos de Microsoft 365, poderá representar grupos em sua instância local do Active Directory. Essa opção só estará disponível se você tiver o Exchange em sua instância local do Active Directory. Para obter mais informações, consulte [Azure ad Connect write-back de grupo](how-to-connect-group-writeback.md).|
| Write-back de dispositivo |Para cenários de acesso condicional, use esta opção para gravar novamente objetos de dispositivo no Azure AD em sua instância local do Active Directory. Para saber mais, confira [Habilitar o write-back de dispositivo no Azure AD Connect](how-to-connect-device-writeback.md). |
| Sincronização de atributo de extensão de diretório |Selecione esta opção para sincronizar os atributos especificados para o Azure AD. Para saber mais, confira [Extensões de diretório](how-to-connect-sync-feature-directory-extensions.md). |

### <a name="azure-ad-app-and-attribute-filtering"></a>Aplicativo AD do Azure e filtragem de atributos
Se você quiser limitar quais atributos serão sincronizados com o Azure AD, comece selecionando os serviços que você usa. Se você alterar as seleções nesta página, precisará selecionar explicitamente um novo serviço executando novamente o assistente de instalação.

![Captura de tela mostrando recursos opcionais de aplicativos do Azure A D.](./media/how-to-connect-install-custom/azureadapps2.png)

Com base nos serviços que você selecionou na etapa anterior, essa página mostra todos os atributos que são sincronizados. Essa lista é uma combinação de todos os tipos de objetos que estão sendo sincronizados. Se precisar que alguns atributos permaneçam não sincronizados, você poderá limpar a seleção desses atributos.

![Captura de tela mostrando recursos opcionais de atributos de D do Azure.](./media/how-to-connect-install-custom/azureadattributes2.png)

> [!WARNING]
> A remoção de atributos pode afetar a funcionalidade. Para obter as práticas recomendadas e recomendações, consulte [atributos para sincronizar](reference-connect-sync-attributes-synchronized.md#attributes-to-synchronize).
>

### <a name="directory-extension-attribute-sync"></a>Sincronização de atributo de extensão de diretório
Você pode estender o esquema no Azure AD usando atributos personalizados que sua organização adicionou ou usando outros atributos em Active Directory. Para usar esse recurso, na página **recursos opcionais** , selecione a **extensão de diretório sincronização de atributos**. Na página **extensões de diretório** , você pode selecionar mais atributos para sincronizar.

>[!NOTE]
>O campo **atributos disponíveis** diferencia maiúsculas de minúsculas.

![Captura de tela mostrando a página "extensões de diretório".](./media/how-to-connect-install-custom/extension2.png)

Para saber mais, confira [Extensões de diretório](how-to-connect-sync-feature-directory-extensions.md).

### <a name="enabling-single-sign-on"></a>Habilitando o logon único
Na página **logon único** , você configura o logon único para uso com a sincronização de senha ou autenticação de passagem. Você faz essa etapa uma vez para cada floresta que está sendo sincronizada com o Azure AD. A configuração envolve duas etapas:

1.  Crie a conta de computador necessária em sua instância local do Active Directory.
2.  Configure a zona da intranet dos computadores cliente para dar suporte ao logon único.

#### <a name="create-the-computer-account-in-active-directory"></a>Crie a conta de computador no Active Directory
Para cada floresta que foi adicionada no Azure AD Connect, você precisa fornecer credenciais de administrador de domínio para que a conta de computador possa ser criada em cada floresta. As credenciais são usadas somente para criar a conta. Eles não são armazenados ou usados para qualquer outra operação. Adicione as credenciais na página **habilitar logon único** , como mostra a imagem a seguir.

![Captura de tela mostrando a página "habilitar logon único". As credenciais de floresta são adicionadas.](./media/how-to-connect-install-custom/enablesso.png)

>[!NOTE]
>Você pode ignorar as florestas em que não deseja usar o logon único.

#### <a name="configure-the-intranet-zone-for-client-machines"></a>Configurar a zona da intranet para computadores cliente
Para garantir que o cliente faça logon automaticamente na zona da intranet, verifique se a URL faz parte da zona da intranet. Essa etapa garante que o computador ingressado no domínio Envie automaticamente um tíquete Kerberos para o Azure AD quando ele estiver conectado à rede corporativa.

Em um computador com ferramentas de gerenciamento de Política de Grupo:

1.  Abra as ferramentas de gerenciamento de Política de Grupo.
2.  Edite a política de grupo que será aplicada a todos os usuários. Por exemplo, a política de domínio padrão.
3.  Vá para **configuração do usuário**  >  **modelos administrativos**  >  **componentes do Windows**  >  **Internet Explorer**  >  página segurança do**painel de controle Internet**do Internet Explorer  >  **Security Page**. Em seguida, selecione **Lista de atribuição de sites a zonas**.
4.  Habilite a política. Em seguida, na caixa de diálogo, insira um nome de valor `https://autologon.microsoftazuread-sso.com` e o valor de `1` . Sua configuração deve ser parecida com a imagem a seguir.
  
    ![Captura de tela mostrando zonas de intranet.](./media/how-to-connect-install-custom/sitezone.png)

6.  Selecione **OK** duas vezes.

## <a name="configuring-federation-with-ad-fs"></a>Configurando a federação com o AD FS
Você pode configurar AD FS com Azure AD Connect com apenas alguns cliques. Antes de começar, você precisa:

* Windows Server 2012 R2 ou posterior para o servidor de Federação. O gerenciamento remoto deve ser habilitado.
* Windows Server 2012 R2 ou posterior para o servidor de proxy de aplicativo Web. O gerenciamento remoto deve ser habilitado.
* Um certificado TLS/SSL para o nome do serviço de Federação que você pretende usar (por exemplo, sts.contoso.com).

>[!NOTE]
>Você pode atualizar um certificado TLS/SSL para seu farm de AD FS usando Azure AD Connect mesmo que você não o use para gerenciar a confiança da Federação.

### <a name="ad-fs-configuration-prerequisites"></a>Pré-requisitos de configuração do AD FS
Para configurar o farm de AD FS usando o Azure AD Connect, verifique se o WinRM está habilitado nos servidores remotos. Verifique se você concluiu as outras tarefas em [pré-requisitos de Federação](how-to-connect-install-prerequisites.md#prerequisites-for-federation-installation-and-configuration). Além disso, certifique-se de seguir os requisitos de portas listados na tabela [Azure ad Connect e servidores de Federação/WAP](reference-connect-ports.md#table-3---azure-ad-connect-and-ad-fs-federation-serverswap) .

### <a name="create-a-new-ad-fs-farm-or-use-an-existing-ad-fs-farm"></a>Criar um novo farm do AD FS ou usar um farm do AD FS existente
Você pode usar um farm de AD FS existente ou criar um novo. Se você optar por criar um novo, deverá fornecer o certificado TLS/SSL. Se o certificado TLS/SSL for protegido por uma senha, você será solicitado a fornecer a senha.

![Captura de tela mostrando a página "A D F S Farm"](./media/how-to-connect-install-custom/adfs1.png)

Se você optar por usar um farm de AD FS existente, verá a página em que é possível configurar a relação de confiança entre AD FS e o Azure AD.

>[!NOTE]
>Você pode usar Azure AD Connect para gerenciar apenas um farm de AD FS. Se você tiver uma relação de confiança de Federação existente na qual o Azure AD está configurado no farm de AD FS selecionado, Azure AD Connect recriará a relação de confiança do zero.

### <a name="specify-the-ad-fs-servers"></a>Especificar os servidores do AD FS
Especifique os servidores em que você deseja instalar AD FS. Você pode adicionar um ou mais servidores, dependendo de suas necessidades de capacidade. Antes de configurar essa configuração, junte-se a todos os servidores AD FS para Active Directory. Essa etapa não é necessária para os servidores de proxy de aplicativo Web. 

A Microsoft recomenda instalar um único servidor do AD FS para implantações de teste e piloto. Após a configuração inicial, você pode adicionar e implantar mais servidores para atender às suas necessidades de dimensionamento executando Azure AD Connect novamente.

> [!NOTE]
> Antes de configurar essa configuração, verifique se todos os servidores estão associados a um domínio do Azure AD.
>


![Captura de tela mostrando a página "servidores de Federação".](./media/how-to-connect-install-custom/adfs2.png)

### <a name="specify-the-web-application-proxy-servers"></a>Especificar os servidores proxy de aplicativo Web
Especifique seus servidores de proxy de aplicativo Web. O servidor proxy de aplicativo Web é implantado em sua rede de perímetro, voltada para a extranet. Ele dá suporte a solicitações de autenticação da extranet. Você pode adicionar um ou mais servidores, dependendo de suas necessidades de capacidade. 

A Microsoft recomenda a instalação de um único servidor proxy de aplicativo Web para implantações de teste e piloto. Após a configuração inicial, você pode adicionar e implantar mais servidores para atender às suas necessidades de dimensionamento executando Azure AD Connect novamente. Recomendamos que você tenha um número equivalente de servidores proxy para satisfazer a autenticação da intranet.

> [!NOTE]
> - Se a conta usada não for um administrador local nos servidores de proxy de aplicativo Web, as credenciais de administrador serão solicitadas.
> - Antes de especificar servidores proxy de aplicativo Web, verifique se há conectividade HTTP/HTTPS entre o servidor de Azure AD Connect e o servidor proxy de aplicativo Web.
> - Verifique se há conectividade HTTP/HTTPS entre o servidor de aplicativos da Web e o servidor de AD FS para permitir que as solicitações de autenticação fluam.
>


![Captura de tela mostrando a página servidores proxy de aplicativo Web.](./media/how-to-connect-install-custom/adfs3.png)

Você será solicitado a inserir as credenciais para que o servidor de aplicativos da Web possa estabelecer uma conexão segura com o servidor de AD FS. Essas credenciais devem ser para uma conta de administrador local no servidor de AD FS.

![Captura de tela mostrando a página "credenciais". As credenciais de administrador são inseridas no campo nome de usuário e senha.](./media/how-to-connect-install-custom/adfs4.png)

### <a name="specify-the-service-account-for-the-ad-fs-service"></a>Especifique a conta de serviço para o serviço AD FS
O serviço de AD FS requer uma conta de serviço de domínio para autenticar usuários e procurar informações de usuário no Active Directory. Ele pode dar suporte a dois tipos de contas de serviço:

* **Conta de serviço gerenciado de grupo**: esse tipo de conta foi introduzido no AD DS pelo Windows Server 2012. Esse tipo de conta fornece serviços como AD FS. É uma única conta na qual você não precisa atualizar a senha regularmente. Use essa opção se você já tiver controladores de domínio do Windows Server 2012 no domínio ao qual os servidores do AD FS pertencem.
* **Conta de usuário do domínio**: esse tipo de conta exige que você forneça uma senha e atualize-a regularmente quando ela expirar. Use essa opção somente quando não tiver controladores de domínio do Windows Server 2012 no domínio ao qual seus servidores de AD FS pertencem.

Se você selecionou **criar uma conta de serviço gerenciado de grupo** e esse recurso nunca foi usado em Active Directory, insira suas credenciais de administrador corporativo. Essas credenciais são usadas para iniciar o repositório de chaves e para habilitar o recurso no Active Directory.

> [!NOTE]
> Azure AD Connect verifica se o serviço de AD FS já está registrado como um SPN (nome da entidade de serviço) no domínio.  O AD DS do Azure não permite que SPNs duplicados sejam registrados ao mesmo tempo.  Se um SPN duplicado for encontrado, você não poderá prosseguir ainda mais até que o SPN seja removido.

![Captura de tela mostrando a página "uma conta de serviço F S".](./media/how-to-connect-install-custom/adfs5.png)

### <a name="select-the-azure-ad-domain-that-you-want-to-federate"></a>Selecione o domínio do Azure AD que você deseja federar
Use a página de **domínio do Azure ad** para configurar a relação de federação entre AD FS e o Azure AD. Aqui, você configura AD FS para fornecer tokens de segurança ao Azure AD. Você também configura o Azure AD para confiar nos tokens desta instância de AD FS. 

Nessa página, você pode configurar apenas um único domínio na instalação inicial. Você pode configurar mais domínios mais tarde executando novamente o Azure AD Connect.

![Captura de tela que mostra a página "domínio do Azure A D".](./media/how-to-connect-install-custom/adfs6.png)

### <a name="verify-the-azure-ad-domain-selected-for-federation"></a>Verificar o domínio do Azure AD selecionado para federação
Quando você seleciona o domínio que deseja federar, Azure AD Connect fornece informações que você pode usar para verificar um domínio não verificado. Para obter mais informações, consulte [Adicionar e verificar o domínio](../fundamentals/add-custom-domain.md).

![Captura de tela mostrando a página "domínio do Azure A D", incluindo informações que você pode usar para verificar o domínio.](./media/how-to-connect-install-custom/verifyfeddomain.png)

> [!NOTE]
> Azure AD Connect tenta verificar o domínio durante o estágio de configuração. Se você não adicionar os registros DNS (sistema de nomes de domínio) necessários, a configuração não poderá ser concluída.
>


## <a name="configuring-federation-with-pingfederate"></a>Configurando a federação com o PingFederate
Você pode configurar o PingFederate com Azure AD Connect com apenas alguns cliques. Os seguintes pré-requisitos são necessários:
- PingFederate 8,4 ou posterior.  Para obter mais informações, consulte [integração do PingFederate com Azure Active Directory e Microsoft 365](https://docs.pingidentity.com/bundle/O365IG20_sm_integrationGuide/page/O365IG_c_integrationGuide.html).
- Um certificado TLS/SSL para o nome do serviço de Federação que você pretende usar (por exemplo, sts.contoso.com).

### <a name="verify-the-domain"></a>Verifique o domínio
Depois de optar por configurar a Federação usando o PingFederate, você será solicitado a verificar o domínio que deseja federar.  Selecione o domínio no menu suspenso.

![Captura de tela que mostra a página "domínio do Azure A D". O domínio de exemplo "contoso.com" está selecionado.](./media/how-to-connect-install-custom/ping1.png)

### <a name="export-the-pingfederate-settings"></a>Exportar configurações do PingFederate


Configure o PingFederate como o servidor de Federação para cada domínio do Azure federado.  Selecione **exportar configurações** para compartilhar essas informações com o administrador do PingFederate.  O administrador do servidor de Federação atualiza a configuração e, em seguida, fornece a URL do servidor PingFederate e o número da porta para que Azure AD Connect possa verificar as configurações de metadados.  

![Captura de tela mostrando a página "configurações de PingFederate". O botão "configurações de exportação" é exibido próximo à parte superior da página.](./media/how-to-connect-install-custom/ping2.png)

Entre em contato com o administrador do PingFederate para resolver os problemas de validação.  A imagem a seguir mostra informações sobre um servidor PingFederate que não tem uma relação de confiança válida com o Azure.

![Captura de tela mostrando informações do servidor: o servidor PingFederate foi encontrado, mas a conexão do provedor de serviços para o Azure está ausente ou desabilitada.](./media/how-to-connect-install-custom/ping5.png)




### <a name="verify-federation-connectivity"></a>Verificar conectividade da federação
Azure AD Connect tenta validar os pontos de extremidade de autenticação recuperados dos metadados de PingFederate na etapa anterior.  Azure AD Connect primeiro tenta resolver os pontos de extremidade usando seus servidores DNS locais.  Em seguida, ele tenta resolver os pontos de extremidade usando um provedor DNS externo.  Entre em contato com o administrador do PingFederate para resolver os problemas de validação.  

![Captura de tela mostrando a página "verificar conectividade".](./media/how-to-connect-install-custom/ping3.png)

### <a name="verify-federation-sign-in"></a>Verificar entrada da Federação
Por fim, você pode verificar o fluxo de logon federado recém-configurado ao entrar em um domínio federado. Se sua entrada for bem-sucedida, a Federação com PingFederate será configurada com êxito.

![Captura de tela mostrando a página "verificar logon federado". Uma mensagem na parte inferior indica uma entrada bem-sucedida.](./media/how-to-connect-install-custom/ping4.png)

## <a name="configure-and-verify-pages"></a>Configurar e verificar páginas
A configuração ocorre na página **Configurar** .

> [!NOTE]
> Se você configurou a Federação, certifique-se de que também tenha configurado a [resolução de nomes para servidores de Federação](how-to-connect-install-prerequisites.md#name-resolution-for-federation-servers) antes de continuar a instalação.
>



![Captura de tela mostrando a página "pronto para configurar".](./media/how-to-connect-install-custom/readytoconfigure2.png)

### <a name="use-staging-mode"></a>Usar o modo de preparo
É possível configurar um novo servidor de sincronização em paralelo com o modo de preparo. Se você quiser usar essa configuração, somente um servidor de sincronização poderá exportar para um diretório na nuvem. Mas se você quiser mover de outro servidor, por exemplo, um servidor executando o DirSync, poderá habilitar Azure AD Connect no modo de preparo. 

Quando você habilita a configuração de preparo, o mecanismo de sincronização importa e sincroniza os dados normalmente. Mas não exporta dados para o Azure AD ou Active Directory. No modo de preparo, o recurso de sincronização de senha e o recurso write-back de senha estão desabilitados.

![Captura de tela mostrando a opção "Habilitar modo de preparo".](./media/how-to-connect-install-custom/stagingmode.png)

No modo de preparo, você pode fazer as alterações necessárias no mecanismo de sincronização e examinar o que será exportado. Quando a configuração parecer adequada, execute novamente o assistente de instalação e desabilite o modo de preparo. 

Agora, os dados são exportados para o Azure AD do servidor. Desabilite o outro servidor ao mesmo tempo, para que somente um servidor esteja exportando ativamente.

Para saber mais, confira [Modo de preparo](how-to-connect-sync-staging-server.md).

### <a name="verify-your-federation-configuration"></a>Verificar a configuração de federação
Azure AD Connect verifica as configurações de DNS quando você seleciona o botão **verificar** . Ele verifica as seguintes configurações:

* **Conectividade com a intranet**
    * Resolver o FQDN da Federação: Azure AD Connect verifica se o DNS pode resolver o FQDN da Federação para garantir a conectividade. Se Azure AD Connect não puder resolver o FQDN, a verificação falhará. Para concluir a verificação, verifique se um registro DNS está presente para o FQDN do serviço de Federação.
    * Registro A de DNS: Azure AD Connect verifica se o serviço de Federação tem um registro A. Na ausência de um registro a, a verificação falha. Para concluir a verificação, crie um registro A (não um registro CNAME) para o FQDN da Federação.
* **Conectividade de extranet**
    * Resolver o FQDN da Federação: Azure AD Connect verifica se o DNS pode resolver o FQDN da Federação para garantir a conectividade.

      ![Captura de tela mostrando a página "instalação concluída".](./media/how-to-connect-install-custom/completed.png)

      ![Captura de tela mostrando a página "instalação concluída". Uma mensagem indica que a configuração da intranet foi verificada.](./media/how-to-connect-install-custom/adfs7.png)

Para validar a autenticação de ponta a ponta, execute manualmente um ou mais dos seguintes testes:

* Quando a sincronização for concluída, em Azure AD Connect, use a tarefa **verificar logon federado** adicional para verificar a autenticação para uma conta de usuário local que você escolher.
* Em um computador ingressado no domínio na intranet, verifique se você pode entrar em um navegador. Conecte-se ao https://myapps.microsoft.com. Em seguida, use sua conta conectada para verificar a entrada. A conta interna do administrador do Azure AD DS não está sincronizada e você não pode usá-la para verificação.
* Verifique se você pode entrar de um dispositivo na Extranet. Em um computador doméstico ou em um dispositivo móvel, conecte-se ao https://myapps.microsoft.com . Em seguida, forneça suas credenciais.
* Valide a entrada do cliente avançado. Conecte-se ao https://testconnectivity.microsoft.com. Em seguida, selecione o teste de Sign-On único do Office **365**  >  **Office 365**.

## <a name="troubleshoot"></a>Solucionar problemas
Esta seção contém informações de solução de problemas que podem ser usadas se você tiver um problema durante a instalação do Azure AD Connect.

Ao personalizar uma instalação do Azure AD Connect, na página **instalar componentes necessários** , você pode selecionar **usar um SQL Server existente**. Você pode ver o seguinte erro: "o banco de dados ADSync já contém e não pode ser substituído. Remova o banco de dados existente e tente novamente. "

![Captura de tela que mostra a página "instalar componentes necessários". Um erro aparece na parte inferior da página.](./media/how-to-connect-install-custom/error1.png)

Você verá esse erro porque um banco de dados chamado *AdSync* já existe na instância do SQL do SQL Server especificado.

Normalmente, você vê esse erro depois de ter desinstalado Azure AD Connect.  O banco de dados não é excluído do computador que executa SQL Server quando você desinstala o Azure AD Connect.

Para corrigir esse problema:

1. Verifique o banco de dados ADSync que Azure AD Connect usado antes de ser desinstalado. Verifique se o banco de dados não está mais sendo usado.

2. Fazer backup do banco de dados.

3. Exclua o banco de dados:
    1. Use **Microsoft SQL Server Management Studio** para se conectar à instância do SQL. 
    1. Localize o banco de dados **AdSync** e clique nele com o botão direito do mouse.
    1. No menu de contexto, selecione **excluir**.
    1. Selecione **OK** para excluir o banco de dados.

![Captura de tela mostrando Microsoft SQL Server Management Studio. Uma sincronização D está selecionada.](./media/how-to-connect-install-custom/error2.png)

Depois de excluir o banco de dados ADSync, selecione **instalar** para tentar novamente a instalação.

## <a name="next-steps"></a>Próximas etapas
Após a conclusão da instalação, saia do Windows. Em seguida, entre novamente antes de usar Synchronization Service Manager ou o editor de regra de sincronização.

Agora que você instalou o Azure AD Connect, você pode [verificar a instalação e atribuir licenças](how-to-connect-post-installation.md).

Para obter mais informações sobre os recursos que você habilitou durante a instalação, consulte [impedir exclusões acidentais](how-to-connect-sync-feature-prevent-accidental-deletes.md) e [Azure ad Connect Health](how-to-connect-health-sync.md).

Para obter mais informações sobre outros tópicos comuns, consulte [Azure ad Connect sincronização: Agendador](how-to-connect-sync-feature-scheduler.md) e [integrar suas identidades locais ao Azure ad](whatis-hybrid-identity.md).
