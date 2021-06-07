---
title: Personalizar uma instalação do Azure Active Directory Connect
description: Este artigo explica sobre as opções de instalação personalizada para o Azure AD Connect. Use estas instruções para instalar o Active Directory por meio do Azure AD Connect.
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92096344"
---
# <a name="custom-installation-of-azure-active-directory-connect"></a>Instalação personalizada do Azure Active Directory Connect
Use as *configurações personalizadas* no Azure Active Directory (Azure AD) Connect quando desejar mais opções para a instalação. Use essas configurações, por exemplo, se você tiver várias florestas ou se quiser configurar recursos opcionais. Use configurações personalizadas em todos os casos em que a [instalação expressa](how-to-connect-install-express.md) não atenda às suas necessidades de implantação ou de topologia.

Pré-requisitos:
- [Baixar o Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771).
- Conclua as etapas de pré-requisito em [Azure AD Connect: Hardware e pré-requisitos](how-to-connect-install-prerequisites.md). 
- Verifique se você tem as contas descritas em [Contas e permissões do Azure AD Connect](reference-connect-accounts-permissions.md).

## <a name="custom-installation-settings"></a>Configurações de instalação personalizada 

Para configurar uma instalação personalizada para o Azure AD Connect, percorra as páginas do assistente que as seções a seguir descrevem.

### <a name="express-settings"></a>Configurações expressas
Na página **Configurações Expressas**, selecione **Personalizar** para iniciar uma instalação de configurações personalizadas.  O restante deste artigo orientará você no processo de instalação personalizada. Use os seguintes links para acessar rapidamente as informações de uma página específica:

- [Componentes necessários](#install-required-components)
- [Entrada do usuário](#user-sign-in)
- [Conecte-se ao AD do Azure](#connect-to-azure-ad)
- [Sincronizar](#sync-pages)

### <a name="install-required-components"></a>Instalar componentes necessários
Quando você instala os serviços de sincronização, pode deixar a seção de configuração opcional desmarcada. O Azure AD Connect configura tudo automaticamente. Ele configura uma instância do LocalDB do SQL Server 2012 Express, cria os grupos apropriados e atribui permissões a eles. Se você quiser alterar os padrões, desmarque as caixas apropriadas.  A tabela a seguir resume essas opções e fornece links para informações adicionais. 

![Captura de tela mostrando seleções opcionais para os componentes de instalação necessários no Azure AD Connect.](./media/how-to-connect-install-custom/requiredcomponents2.png)

| Configuração opcional | Descrição |
| --- | --- |
|Especificar um local de instalação personalizado| Permite que você altere o caminho de instalação padrão para o Azure AD Connect.|
| Usar um SQL Server existente |Permite que você especifique o nome do SQL Server e o nome da instância. Escolha essa opção se já tiver um servidor de banco de dados que deseja usar. Para **Nome da Instância**, insira o nome da instância, uma vírgula e o número da porta se a instância de SQL Server não tiver a navegação habilitada.  Em seguida, especifique o nome do banco de dados do Azure AD Connect.  Os privilégios SQL determinam se será criado um banco de dados ou se o administrador do SQL deve criar o banco de dados com antecedência.  Se você tiver permissões de administrador (SA) do SQL Server, confira [Instalar Azure AD Connect usando um banco de dados existente](how-to-connect-install-existing-database.md).  Se você tiver permissões delegadas (DBO), confira [Instalar o Azure AD Connect com permissões de administrador delegado do SQL](how-to-connect-install-sql-delegation.md). |
| Usar uma conta de serviço existente |Por padrão, o Azure AD Connect fornece uma conta de serviço virtual para os serviços de sincronização. Se você usa uma instância remota do SQL Server ou usa um proxy que exija autenticação, use uma *conta de serviço gerenciado* ou uma conta de serviço protegida por senha no domínio. Nesses casos, insira a conta que deseja usar. Para executar a instalação, você precisa ser um SA no SQL para criar as credenciais de entrada para a conta de serviço. Para saber mais, confira [Contas e permissões do Azure AD Connect](reference-connect-accounts-permissions.md#adsync-service-account). </br></br>Usando a compilação mais recente, o administrador do SQL agora pode provisionar o banco de dados fora da banda. Em seguida, o administrador do Azure AD Connect pode instalá-lo com direitos de proprietário do banco de dados.  Para obter mais informações, confira [Instale o Azure AD Connect usando permissões de administrador do SQL delegado](how-to-connect-install-sql-delegation.md).|
| Especificar grupos de sincronização personalizados |Por padrão, quando os serviços de sincronização estiverem instalados, o Azure AD Connect cria quatro grupos locais para o servidor. Esses grupos são Administradores, Operadores, Navegação e Redefinição de Senha. Você pode especificar seus próprios grupos aqui. Os grupos precisam estar localizados no servidor. Eles não podem estar localizados no domínio. |
|Importar configurações de sincronização (versão prévia)|Permite que você importe configurações de outras versões do Azure AD Connect.  Para obter mais informações, confira [Importando e exportando definições de configuração do Azure AD Connect](how-to-connect-import-export-config.md).|

### <a name="user-sign-in"></a>Entrada do usuário
Depois de instalar os componentes necessários, selecione o método de logon único dos usuários. A tabela a seguir descreve brevemente as opções disponíveis. Para obter uma descrição completa dos métodos de entrada, consulte [Entrada do usuário](plan-connect-user-signin.md).

![Captura de tela que mostra a página “Entrada de Usuário”. A opção "Sincronização de Hash de Senha" está selecionada.](./media/how-to-connect-install-custom/usersignin4.png)

| Opção de logon único | Descrição |
| --- | --- |
| Sincronização de hash de senha |Os usuários podem entrar em serviços em nuvem da Microsoft, como o Microsoft 365, usando a mesma senha usada na rede local deles. As senhas dos usuários são sincronizadas ao Azure AD como um hash de senha. A autenticação ocorre na nuvem. Para obter mais informações, confira [Sincronização de hash de senha](how-to-connect-password-hash-synchronization.md). |
|Autenticação de passagem|Os usuários podem entrar em serviços em nuvem da Microsoft, como o Microsoft 365, usando a mesma senha usada na rede local deles.  A senha dos usuários é validada ao ser passada pelo controlador de domínio do Active Directory local.
| Federação com o AD FS |Os usuários podem entrar em serviços em nuvem da Microsoft, como o Microsoft 365, usando a mesma senha usada na rede local deles.  Os usuários são redirecionados à instância do AD FS (serviços de Federação do Azure Directory) local para entrar. A autenticação ocorre localmente. |
| Federação com PingFederate|Os usuários podem entrar em serviços em nuvem da Microsoft, como o Microsoft 365, usando a mesma senha usada na rede local deles.  Os usuários são redirecionados para a instância local do PingFederate para entrar. A autenticação ocorre localmente. |
| Não configurar |Nenhum recurso de entrada do usuário está instalado ou configurado. Escolha essa opção se você já tiver um servidor de federação de terceiros ou outra solução em vigor. |
|Habilitar logon único|Essa opção está disponível com a sincronização de hash de senha e a autenticação de passagem. Ele fornece uma experiência de logon único para usuários de desktop em redes corporativas. Para obter mais informações, confira [Logon único](how-to-connect-sso.md). </br></br>**Observação:** Para clientes do AD FS, essa opção não está disponível. O AD FS já oferece o mesmo nível de logon único.</br>

### <a name="connect-to-azure-ad"></a>Conecte-se ao AD do Azure
Na página **Conectar ao Azure AD**, digite uma conta de administrador global e uma senha. Se você tiver selecionado **Federação com o AD FS** na página anterior, não entre com uma conta de um domínio que você planeja habilitar para a federação. 

Uma recomendação é usar uma conta no domínio *onmicrosoft.com* padrão, fornecida com o locatário do Azure AD. Essa conta será usada apenas para criar uma conta de serviço no Azure AD. Ela não será usada após a conclusão da instalação.
  
![Captura de tela mostrando a página "Conectar ao Azure AD".](./media/how-to-connect-install-custom/connectaad.png)

Se sua conta de administrador global tiver a autenticação multifator habilitada, você fornecerá a senha novamente na janela de entrada e deverá concluir o desafio de autenticação multifator. O desafio poderá ser um código de verificação ou uma chamada telefônica.  

![Captura de tela mostrando a página "Conectar ao Azure AD". Um campo de autenticação multifator solicitará ao usuário um código.](./media/how-to-connect-install-custom/connectaadmfa.png)

A conta de administrador global também pode ter o [privileged identity management](../privileged-identity-management/pim-getting-started.md) habilitado.

Se você encontrar um erro ou tiver problemas de conectividade, confira [Solucionar problemas de conectividade](tshoot-connect-connectivity.md).

## <a name="sync-pages"></a>Sincronizar páginas

As seções a seguir descrevem as páginas na seção **Sincronizar**.

### <a name="connect-your-directories"></a>Conectar seus diretórios
Para se conectar ao Azure AD DS (Active Directory Domain Services) do Azure, o Azure AD Connect precisa do nome da floresta e das credenciais de uma conta com permissões suficientes.

![Captura de tela que mostra a página "Conectar seus diretórios".](./media/how-to-connect-install-custom/connectdir01.png)

Depois de inserir o nome da floresta e selecionar  **Adicionar Diretório**, uma janela será exibida. A tabela a seguir descreve as opções.

| Opção | Descrição |
| --- | --- |
| Criar nova conta | Crie a conta do Azure AD DS que o Azure AD Connect precisa para se conectar à floresta do Active Directory durante a sincronização de diretórios. Depois de selecionar essa opção, insira o nome de usuário e a senha de uma conta do admin corporativo.  O Azure AD Connect usa a conta de admin corporativo fornecida para criar a conta do Azure AD DS necessária. Você pode inserir a parte do domínio no formato NetBIOS ou no formato FQDN. Ou seja, insira *FABRIKAM\administrator* ou *fabrikam.com\administrator*. |
| Usar conta existente | Forneça uma conta existente do Azure AD DS que o Azure AD Connect possa usar para se conectar à floresta do Active Directory durante a sincronização de diretórios. Você pode inserir a parte do domínio no formato NetBIOS ou no formato FQDN. Ou seja, insira *FABRIKAM\syncuser* ou *fabrikam.com\syncuser*. Essa conta pode ser uma conta de usuário comum, pois ela só precisa das permissões de leitura padrão. Mas dependendo do cenário, talvez sejam necessárias mais permissões. Para saber mais, confira [Contas e permissões do Azure AD Connect](reference-connect-accounts-permissions.md#create-the-ad-ds-connector-account). |

![Captura de tela mostrando a página "Conectar Diretório" e a janela da conta da floresta A D, na qual você pode optar por criar uma conta ou usar uma conta existente.](./media/how-to-connect-install-custom/connectdir02.png)

>[!NOTE]
> A partir do Build 1.4.18.0, você não pode usar uma conta de admin corporativo ou de administrador de domínio como a conta do conector do Azure AD DS. Ao selecionar **Usar conta existente**, se você tentar inserir uma conta de admin corporativo ou uma conta de administrador de domínio, você verá o seguinte erro: "Não é permitido usar uma conta de administrador Corporativo ou de Domínio para sua conta de floresta do AD. Permita que o Azure AD Connect crie a conta para você ou especifique uma conta de sincronização com as permissões corretas”.
>

### <a name="azure-ad-sign-in-configuration"></a>Configuração de entrada do Azure AD
Na página **Configuração de entrada do AD do Azure**, examine os domínios do nome UPN no Azure AD DS local. Esses domínios UPN foram verificados no Azure AD. Nesta página, você configura o atributo a ser usado para userPrincipalName.

![Captura de tela mostrando domínios não verificados na página "Configuração de entrada do Azure A D".](./media/how-to-connect-install-custom/aadsigninconfig2.png)  

Examine cada domínio marcado como **Não Adicionado** ou **Não Verificado**. Verifique se os domínios que você usa foram verificados no Azure AD. Depois de verificar os domínios, selecione o ícone de atualização circular. Para saber mais, confira [Adicionar e verificar o domínio](../fundamentals/add-custom-domain.md).

Os usuários usam o atributo *userPrincipalName* quando entram no Azure AD e no Microsoft 365. O Azure AD deve verificar os domínios, também conhecidos como sufixo UPN, antes que os usuários sejam sincronizados. A Microsoft recomenda manter o atributo padrão userPrincipalName. 

Se o atributo userPrincipalName for não roteável e não puder ser verificado, você poderá selecionar outro atributo. Por exemplo, você pode selecionar o email como o atributo que contém a ID de entrada. Quando você usa um atributo diferente de userPrincipalName, ele é conhecido como uma *ID alternativa*. 

O valor de atributo da ID alternativa deve seguir o padrão RFC 822. Você pode usar uma ID alternativa com sincronização de hash de senha, autenticação de passagem e federação. No Active Directory, o atributo não pode ser definido como multivalor, mesmo que ele tenha apenas um único valor. Para obter mais informações sobre a ID alternativa, confira [Autenticação de passagem: Perguntas frequentes](./how-to-connect-pta-faq.md#does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname).

>[!NOTE]
> Quando habilitar a autenticação de passagem, você deverá ter pelo menos um domínio verificado para continuar o processo de instalação personalizada.

> [!WARNING]
> As IDs alternativas não são compatíveis com todas as cargas de trabalho do Microsoft 365. Para obter mais informações, confira [Configurando IDs de entrada alternativas](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id).
>

### <a name="domain-and-ou-filtering"></a>Domínio e filtragem de unidade organizacional
Por padrão, todos os domínios e UOs (unidades organizacionais) são sincronizados. Se não quiser sincronizar alguns domínios ou UOs com o Azure AD, você poderá desmarcar as seleções apropriadas.  

![Captura de tela mostrando a página de filtragem de Domínio e UO.](./media/how-to-connect-install-custom/domainoufiltering.png)  

Esta página configura a filtragem baseada em domínio e a filtragem baseada em UO. Para saber mais, confira [Filtragem baseada em domínio](how-to-connect-sync-configure-filtering.md#domain-based-filtering) e [Filtragem baseada em UO](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). Algumas UOs são essenciais para a funcionalidade, portanto, você deve deixá-las selecionadas.

Se você usar a filtragem baseada em UO com uma versão do Azure AD Connect mais antiga que 1.1.524.0, as novas UOs serão sincronizadas por padrão. Se você não quiser que novas UOs sejam sincronizadas, poderá ajustar o comportamento padrão após a etapa de [Filtragem baseada em UO](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). Para o Azure AD Connect 1.1.524.0 ou posterior, você pode indicar se deseja que as novas UOs sejam sincronizadas.

Se você planeja usar a [filtragem baseada em grupo](#sync-filtering-based-on-groups), verifique se a UO com o grupo está incluída e não filtrada com filtragem de UO. A filtragem de UO é avaliada antes da filtragem baseada em grupo.

Também é possível que alguns domínios estejam inacessíveis devido a restrições de firewall. Esses domínios estão desmarcados por padrão e mostram um aviso.  

![Captura de tela mostrando domínios inacessíveis.](./media/how-to-connect-install-custom/unreachable.png)  

Se for mostrado este aviso, verifique se os domínios realmente não podem ser acessados e se o aviso é esperado.

### <a name="uniquely-identifying-your-users"></a>Identificando seus usuários com exclusividade

Na página **Identificando usuários**, escolha como identificar usuários nos diretórios locais e como identificá-los usando o atributo sourceAnchor.

#### <a name="select-how-users-should-be-identified-in-your-on-premises-directories"></a>Selecione como os usuários devem ser identificados em seus diretórios locais
Usando o recurso *Correspondência entre florestas*, você pode definir como os usuários das suas florestas do Azure AD DS são representados no AD do Azure. Um usuário pode ser representado somente uma vez em todas as florestas ou ter uma combinação de contas habilitadas e desabilitadas. O usuário também pode ser representado como um contato em algumas florestas.

![Captura de tela mostrando a página em que você pode identificar exclusivamente os usuários.](./media/how-to-connect-install-custom/unique2.png)

| Configuração | Descrição |
| --- | --- |
| [Os usuários são representados apenas uma vez em todas as florestas](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Todos os usuários são criados como objetos individuais no AD do Azure. Os objetos não são associados ao metaverso. |
| [Atributo de email](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Essa opção associa usuários e contatos quando o atributo de email tem o mesmo valor em florestas diferentes. Use esta opção quando seus contatos forem criados usando GALSync. Se você escolher essa opção, os objetos de usuário cujo atributo de email não estiver preenchido não serão sincronizados com o Azure AD. |
| [ObjectSID e msExchangeMasterAccountSID/ msRTCSIP-OriginatorSID](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |Essa opção associa um usuário habilitado em uma floresta de conta com um usuário desabilitado em uma floresta de recursos. No Exchange, essa configuração é conhecida como uma caixa de correio vinculada. Você pode usar essa opção se usar somente o Lync e se o Exchange não estiver presente na floresta de recursos. |
| Atributos SAMAccountName e MailNickName |Essa opção une os atributos em que se espera que a ID de entrada do usuário seja encontrada. |
| Escolher um atributo específico |Essa opção permite que você selecione seu próprio atributo. Se você escolher essa opção, os objetos de usuário cujo atributo (selecionado) não estiver preenchido não serão sincronizados com o Azure AD. **Limitação:** Somente os atributos que já estão no metaverso estão disponíveis para essa opção. |

#### <a name="select-how-users-should-be-identified-by-using-a-source-anchor"></a>Selecionar como os usuários devem ser identificados usando uma âncora de origem
O atributo *sourceAnchor* é imutável durante o tempo de vida de um objeto de usuário. É a chave primária que vincula o usuário local com o usuário no Azure AD.

| Configuração | Descrição |
| --- | --- |
| Permitir que o Azure gerencie a âncora de origem | Selecione esta opção se deseja que Azure AD escolha o atributo para você. Se você selecionar essa opção, o Azure AD Connect aplicará a lógica de seleção de atributo sourceAnchor descrita em [Usando o atributo ms-DS-ConsistencyGuid como sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). Após a conclusão da instalação personalizada, você verá qual atributo foi escolhido como o atributo sourceAnchor. |
| Escolher um atributo específico | Selecione esta opção se desejar especificar um atributo existente do AD como o atributo sourceAnchor. |

Como o atributo sourceAnchor não pode ser alterado, você deve escolher um atributo apropriado. Um bom candidato é objectGUID. Esse atributo não é alterado, a menos que a conta de usuário seja movida entre florestas ou domínios. Não escolha atributos que podem ser alterados quando uma pessoa se casa ou muda de cargo. 

Você não pode usar atributos que incluem um sinal de arroba (@), portanto, você não pode usar email e userPrincipalName. O atributo também diferencia maiúsculas de minúsculas, portanto, se você mover um objeto entre florestas, preserve as maiúsculas e minúsculas. Os atributos binários são codificados em Base64, mas outros tipos de atributo permanecem no estado não codificado. 

Em cenários de federação e em algumas interfaces do Azure AD, o atributo sourceAnchor também é conhecido como *immutableID*. 

Para obter mais informações sobre a âncora de origem, confira [Conceitos de design](plan-connect-design-concepts.md#sourceanchor).

### <a name="sync-filtering-based-on-groups"></a>Filtragem de sincronização com base em grupos
O recurso filtrar por grupos permite que você sincronize apenas um pequeno subconjunto de objetos para um piloto. Para usar esse recurso, crie um grupo para essa finalidade em sua instância local do Active Directory. Em seguida, adicione usuários e grupos que devem ser sincronizados ao AD do Azure como membros diretos. Posteriormente, você pode adicionar e remover usuários nesse grupo para manter a lista de objetos que devem estar presentes no Azure AD. 

Todos os objetos que você deseja sincronizar devem ser membros diretos do grupo. Usuários, grupos, contatos e computadores ou dispositivos devem ser membros diretos. A associação de grupos aninhados não é resolvida. Quando você adiciona um grupo como um membro, apenas o grupo em si é adicionado. Seus membros não são adicionados.

![Captura de tela mostrando a página em que você pode escolher como filtrar usuários e dispositivos.](./media/how-to-connect-install-custom/filter2.png)

> [!WARNING]
> Esse recurso destina-se a oferecer suporte somente a uma implantação piloto. Não o utilize em uma implantação de produção completa.
>

Em uma implantação de produção completa, será difícil manter um único grupo com todos os objetos a sincronizar. Em vez do recurso de filtragem em grupos, use um dos métodos descritos em [Configurar filtragem](how-to-connect-sync-configure-filtering.md).

### <a name="optional-features"></a>Recursos opcionais
Na próxima página, você pode selecionar recursos opcionais para seu cenário.

>[!WARNING]
>O Azure AD Connect versão 1.0.8641.0 e anteriores se baseia no Serviço de Controle de Acesso do Azure para write-back de senha.  Esse serviço foi desativado em 7 de novembro de 2018.  Se você usar qualquer uma dessas versões do Azure AD Connect e tiver habilitado o write-back de senha, os usuários poderão perder a capacidade de alterar ou redefinir suas senhas quando o serviço for desativado. Essas versões do Azure AD Connect não oferecem suporte a write-back de senha.
>
>Para obter mais informações, confira [Migrar do Serviço de Controle de Acesso do Azure](../azuread-dev/active-directory-acs-migration.md).
>
>Se você quiser usar o write-back de senha, baixe a [versão mais recente do Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

![Captura de tela mostrando a página "Recursos Opcionais".](./media/how-to-connect-install-custom/optional2a.png)

> [!WARNING]
> Se o Azure AD Sync ou a Sincronização Direta (DirSync) estiver ativa, não ative nenhum recurso de write-back no Azure AD Connect.



| Recursos opcionais | Descrição |
| --- | --- |
| Implantação híbrida do Exchange |O recurso de implantação híbrida do Exchange permite a coexistência de caixas de correio do Exchange no local e no Microsoft 365. O Azure AD Connect sincroniza um conjunto específico de [atributos](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) do AD do Azure em seu diretório local. |
| Pastas públicas do Exchange Mail | O recurso Pastas públicas do Exchange Mail permite sincronizar objetos de pasta pública habilitada para email do seu Active Directory local para o Azure AD. |
| Aplicativo AD do Azure e filtragem de atributos |Ao habilitar o aplicativo do Azure AD e a filtragem de atributo, você pode adaptar o conjunto de atributos sincronizados. Essa opção adiciona mais duas páginas de configuração ao assistente. Para saber mais, confira [Aplicativo e filtragem de atributos do Azure AD](#azure-ad-app-and-attribute-filtering). |
| Sincronização de hash de senha |Se você selecionou federação como a solução de entrada, você pode habilitar a sincronização de hash de senha. Em seguida, você pode usá-la como uma opção de backup.  </br></br>Se você selecionou a autenticação de passagem, poderá habilitar essa opção para garantir o suporte para clientes herdados e para fornecer um backup.</br></br> Para obter mais informações, confira [Sincronização de hash de senha](how-to-connect-password-hash-synchronization.md).|
| write-back de senha |Use esta opção para garantir que as alterações de senha originadas no Azure AD sejam gravadas no diretório local. Para saber mais, confira [Introdução ao gerenciamento de senhas](../authentication/tutorial-enable-sspr.md). |
| Write-back de grupo |Se utiliza o Microsoft 365 Groups, então você pode representar grupos em sua instância local do Active Directory. Essa opção só estará disponível se você tiver o Exchange em sua instância local do Active Directory. Para saber mais, confira [Write-back de grupo do Azure AD Connect](how-to-connect-group-writeback.md).|
| Write-back de dispositivo |Para cenários de acesso condicional, use esta opção para gravar novamente objetos de dispositivo no Azure AD em sua instância local do Active Directory. Para saber mais, confira [Habilitar o write-back de dispositivo no Azure AD Connect](how-to-connect-device-writeback.md). |
| Sincronização de atributo de extensão de diretório |Selecione esta opção para sincronizar os atributos especificados para o Azure AD. Para saber mais, confira [Extensões de diretório](how-to-connect-sync-feature-directory-extensions.md). |

### <a name="azure-ad-app-and-attribute-filtering"></a>Aplicativo AD do Azure e filtragem de atributos
Se você quiser limitar quais atributos serão sincronizados com o Azure AD, comece selecionando os serviços que você usa. Se você alterar as seleções nesta página, precisará selecionar explicitamente um novo serviço executando novamente o assistente de instalação.

![Captura de tela mostrando recursos opcionais de aplicativos do AD do Azure.](./media/how-to-connect-install-custom/azureadapps2.png)

Com base nos serviços que você selecionou na etapa anterior, essa página mostra todos os atributos que são sincronizados. Essa lista é uma combinação de todos os tipos de objetos que estão sendo sincronizados. Se precisar que alguns atributos permaneçam não sincronizados, você poderá limpar a seleção desses atributos.

![Captura de tela mostrando recursos opcionais de atributos do AD do Azure.](./media/how-to-connect-install-custom/azureadattributes2.png)

> [!WARNING]
> A remoção de atributos pode afetar a funcionalidade. Para obter as práticas recomendadas e recomendações, confira [Atributos para sincronizar](reference-connect-sync-attributes-synchronized.md#attributes-to-synchronize).
>

### <a name="directory-extension-attribute-sync"></a>Sincronização de atributo de extensão de diretório
Você pode estender o esquema no Azure AD usando atributos personalizados que sua organização adicionou ou usando outros atributos no Active Directory. Para usar esse recurso, na página **Recursos Opcionais**, selecione **Sincronização de atributo de Extensão de Diretório**. Na página **Extensões de Diretório**, você pode selecionar mais atributos para sincronizar.

>[!NOTE]
>O campo **Atributos Disponíveis** diferencia maiúsculas de minúsculas.

![Captura de tela mostrando a página "Extensões de Diretório".](./media/how-to-connect-install-custom/extension2.png)

Para saber mais, confira [Extensões de diretório](how-to-connect-sync-feature-directory-extensions.md).

### <a name="enabling-single-sign-on"></a>Habilitando logon único
Na página **Logon único**, você configura o logon único para uso com a sincronização de senha ou autenticação de passagem. Você faz essa etapa uma vez para cada floresta que está sendo sincronizada com o Azure AD. A configuração envolve duas etapas:

1.  Criar a conta de computador necessária em sua instância local do Active Directory.
2.  Configurar a zona da intranet dos computadores cliente para dar suporte a logon único.

#### <a name="create-the-computer-account-in-active-directory"></a>Crie a conta de computador no Active Directory
Para cada floresta que tenha sido adicionada por meio do Azure AD Connect, você precisará fornecer credenciais de administrador de domínio para que a conta de computador possa ser criada em cada floresta. As credenciais serão usadas somente para criar a conta. Elas não serão armazenadas ou usadas para qualquer outra operação. Adicione as credenciais na página **Habilitar logon único**, como mostra a imagem a seguir.

![Captura de tela mostrando a página "Habilitar logon único". As credenciais de floresta serão adicionadas.](./media/how-to-connect-install-custom/enablesso.png)

>[!NOTE]
>Você poderá ignorar as florestas em que não deseja usar o logon único.

#### <a name="configure-the-intranet-zone-for-client-machines"></a>Configurar a zona da intranet para computadores cliente
Para garantir que o cliente entrará automaticamente na zona da intranet, verifique se a URL faz parte da zona da intranet. Isso garantirá que o computador conectado ao domínio enviará automaticamente um tíquete Kerberos ao Azure AD quando estiver conectado à rede corporativa.

Em um computador que tem as ferramentas de gerenciamento de Política de Grupo:

1.  Abra as ferramentas de gerenciamento de Política de Grupo.
2.  Edite a política de grupo que será aplicada a todos os usuários. Por exemplo, a política de Domínio Padrão.
3.  Navegue até **Configuração do Usuário** > **Modelos Administrativos** > **Componentes do Windows** > **Internet Explorer** > **Painel de Controle da Internet** > **Página de Segurança**. Em seguida, selecione **Lista de atribuição de sites a zonas**.
4.  Habilite a política. Em seguida, na caixa de diálogo, insira um nome de valor `https://autologon.microsoftazuread-sso.com` e o valor de `1`. Seu setup deve ser semelhante à imagem a seguir.
  
    ![Captura de tela mostrando zonas de intranet.](./media/how-to-connect-install-custom/sitezone.png)

6.  Selecione **OK** duas vezes.

## <a name="configuring-federation-with-ad-fs"></a>Configurando a federação com o AD FS
Você pode configurar o AD FS com o Azure AD Connect com apenas alguns cliques. Antes de começar, é necessário:

* Windows Server 2012 R2 ou posterior para o servidor de federação. O gerenciamento remoto deve ser habilitado.
* Windows Server 2012 R2 ou posterior para o servidor proxy de aplicativo Web. O gerenciamento remoto deve ser habilitado.
* Um certificado TLS/SSL para o nome do serviço de federação que você pretende usar (por exemplo, sts.contoso.com).

>[!NOTE]
>Você pode atualizar o certificado TLS/SSL para o farm do AD FS usando o Azure AD Connect, mesmo se não usá-lo para gerenciar sua relação de confiança de federação.

### <a name="ad-fs-configuration-prerequisites"></a>Pré-requisitos de configuração do AD FS
Para configurar o farm do AD FS usando o Azure AD Connect, verifique se o WinRM está habilitado nos servidores remotos. Verifique se você concluiu as outras tarefas em [Pré-requisitos de federação](how-to-connect-install-prerequisites.md#prerequisites-for-federation-installation-and-configuration). Além disso, verifique se está seguindo os requisitos de portas listados na tabela [Azure AD Connect e servidores de federação/WAP](reference-connect-ports.md#table-3---azure-ad-connect-and-ad-fs-federation-serverswap).

### <a name="create-a-new-ad-fs-farm-or-use-an-existing-ad-fs-farm"></a>Criar um novo farm do AD FS ou usar um farm do AD FS existente
Você pode usar um farm do AD FS existente ou criar um. Se optar por criar um, você precisará fornecer o certificado TLS/SSL. Se o certificado TLS/SSL estiver protegido por senha, a senha será solicitada.

![Captura de tela mostrando a página "Farm do AD FS"](./media/how-to-connect-install-custom/adfs1.png)

Se optar por usar um farm existente do AD FS, você verá a tela de configuração da relação de confiança entre o AD FS e o Azure AD.

>[!NOTE]
>O Azure AD Connect pode ser usado para gerenciar somente um farm do AD FS. Se você tiver uma relação de confiança de federação existente com o Azure AD configurado no farm do AD FS selecionado, o Azure AD Connect recriará a relação de confiança do zero.

### <a name="specify-the-ad-fs-servers"></a>Especificar os servidores do AD FS
Especifique os servidores em que você deseja instalar o AD FS. Você pode adicionar um ou mais servidores com base em suas necessidades de capacidade. Antes de configurar essa configuração, una todos os servidores do AD FS ao Active Directory. Essa etapa não é necessária para os servidores proxy de aplicativo Web. 

A Microsoft recomenda instalar um único servidor do AD FS para implantações de teste e piloto. Após a configuração inicial, adicione e implante mais servidores para atender às suas necessidades de dimensionamento executando o Azure AD Connect novamente.

> [!NOTE]
> Antes de configurar essa configuração, verifique se todos os servidores estão unidos a um domínio do Azure AD.
>


![Captura de tela mostrando a página "Servidores de Federação".](./media/how-to-connect-install-custom/adfs2.png)

### <a name="specify-the-web-application-proxy-servers"></a>Especificar os servidores proxy de aplicativo Web
Especificar os servidores proxy de aplicativo Web. O servidor proxy de aplicativo Web é implantado em sua rede de perímetro, voltada para a extranet. Ele dá suporte a solicitações de autenticação da extranet. Você pode adicionar um ou mais servidores com base em suas necessidades de capacidade. 

A Microsoft recomenda instalar um único servidor proxy de aplicativo Web para implantações de teste e piloto. Após a configuração inicial, adicione e implante mais servidores para atender às suas necessidades de dimensionamento executando o Azure AD Connect novamente. Recomendamos que você tenha um número equivalente de servidores proxy para satisfazer a autenticação da intranet.

> [!NOTE]
> - Se a conta usada não for de um administrador local nos servidores proxy de aplicativo Web, suas credenciais de administrador serão solicitadas.
> - Antes de especificar servidores proxy de aplicativo Web, verifique se há conectividade HTTP/HTTPS entre o servidor do Azure AD Connect e o servidor proxy de aplicativo Web.
> - Verifique se há conectividade HTTP/HTTPS entre o Servidor de Aplicativo Web e o servidor do AD FS para permitir o fluxo de solicitações de autenticação.
>


![Captura de tela mostrando a página servidores proxy de aplicativo Web.](./media/how-to-connect-install-custom/adfs3.png)

Você deverá inserir as credenciais para que o Servidor de Aplicativo Web possa estabelecer uma conexão segura com o servidor do AD FS. Essas credenciais precisam ser de uma conta de administrador local no servidor do AD FS.

![Captura de tela mostrando a página “Credenciais”. As credenciais de administrador serão inseridas no campo nome de usuário e senha.](./media/how-to-connect-install-custom/adfs4.png)

### <a name="specify-the-service-account-for-the-ad-fs-service"></a>Especifique a conta de serviço para o serviço AD FS
O serviço do AD FS requer uma conta de serviço de domínio para autenticar usuários e procurar informações de usuário no Active Directory. Ele pode dar suporte a dois tipos de contas de serviço:

* **Conta de serviço gerenciado de grupo**: esse tipo de conta foi introduzido no AD DS pelo Windows Server 2012. Esse tipo de conta fornece serviços como o AD FS. É uma única conta na qual você não precisa atualizar a senha regularmente. Use essa opção se você já tiver controladores de domínio do Windows Server 2012 no domínio ao qual os servidores do AD FS pertencem.
* **Conta de usuário de domínio**: Esse tipo de conta exige que você forneça uma senha e atualize-a regularmente quando ela expirar. Use essa opção somente quando você não tiver controladores de domínio do Windows Server 2012 no domínio ao qual os servidores do AD FS pertencem.

Se você selecionou **Criar uma Conta de Serviço Gerenciado de grupo** e esse recurso nunca tiver sido usado no Active Directory, insira suas credenciais de admin corporativo. Essas credenciais são usadas para iniciar o repositório de chaves e para habilitar o recurso no Active Directory.

> [!NOTE]
> O Azure AD Connect verifica se o serviço do AD FS já está registrado como um SPN (nome da entidade de serviço) no domínio.  O Azure AD DS não permite que SPNs duplicados sejam registrados ao mesmo tempo.  Se um SPN duplicado for encontrado, você não poderá continuar até que o SPN seja removido.

![Captura de tela mostrando a página "conta de serviço A D F S".](./media/how-to-connect-install-custom/adfs5.png)

### <a name="select-the-azure-ad-domain-that-you-want-to-federate"></a>Selecione o domínio do Azure AD que você deseja federar
Use a página **Domínio do Azure AD** para configurar a relação de federação entre o AD FS e o AD do Azure. Aqui, você configura o AD FS para fornecer tokens de segurança ao Azure AD. Você também configura o Azure AD para confiar nos tokens dessa instância do AD FS. 

Nessa página, você pode configurar apenas um único domínio na instalação inicial. Você pode configurar mais domínios mais tarde executando novamente o Azure AD Connect.

![Captura de tela que mostra a página "Domínio do Azure A D".](./media/how-to-connect-install-custom/adfs6.png)

### <a name="verify-the-azure-ad-domain-selected-for-federation"></a>Verificar o domínio do Azure AD selecionado para federação
Quando você seleciona o domínio que deseja federar, o Azure AD Connect fornece informações que você pode usar para verificar um domínio não verificado. Para saber mais, confira [Adicionar e verificar o domínio](../fundamentals/add-custom-domain.md).

![Captura de tela mostrando a página "Domínio do Azure A D", incluindo informações que você pode usar para verificar o domínio.](./media/how-to-connect-install-custom/verifyfeddomain.png)

> [!NOTE]
> O Azure AD Connect tenta verificar o domínio durante o estágio de configuração. Se você não adicionar os registros DNS (sistema de nomes de domínio) necessários, a configuração não poderá ser concluída.
>


## <a name="configuring-federation-with-pingfederate"></a>Configurando a federação com o PingFederate
Você pode configurar o PingFederate com o Azure AD Connect com apenas alguns cliques. Os seguintes pré-requisitos são necessários:
- PingFederate 8.4 ou posterior.  Para obter mais informações, confira [Integração do PingFederate com o Azure Active Directory e o Microsoft 365](https://docs.pingidentity.com/bundle/O365IG20_sm_integrationGuide/page/O365IG_c_integrationGuide.html).
- Um certificado TLS/SSL para o nome do serviço de federação que você pretende usar (por exemplo, sts.contoso.com).

### <a name="verify-the-domain"></a>Verifique o domínio
Depois de optar por configurar a federação usando o PingFederate, você deverá verificar o domínio que deseja federar.  Selecione o domínio no menu suspenso.

![Captura de tela que mostra a página "Domínio do Azure A D". O domínio de exemplo "contoso.com" está selecionado.](./media/how-to-connect-install-custom/ping1.png)

### <a name="export-the-pingfederate-settings"></a>Exportar configurações do PingFederate


Configure o PingFederate como o servidor de federação para cada domínio do Azure federado.  Selecione **Exportar Configurações** para compartilhar essas informações com o administrador do PingFederate.  O administrador do servidor de federação atualizará a configuração e, em seguida, fornecerá a URL do servidor PingFederate e número da porta para que o Azure AD Connect possa verificar as configurações de metadados.  

![Captura de tela mostrando a página "configurações de PingFederate". O botão "Exportar Configurações" é exibido próximo à parte superior da página.](./media/how-to-connect-install-custom/ping2.png)

Entre em contato com o administrador do PingFederate para resolver os problemas de validação.  A imagem a seguir mostra informações sobre um servidor PingFederate que não tem uma relação de confiança válida com o Azure.

![Captura de tela mostrando informações do servidor: O servidor PingFederate foi encontrado, mas a conexão do provedor de serviços com o Azure está ausente ou desabilitada.](./media/how-to-connect-install-custom/ping5.png)




### <a name="verify-federation-connectivity"></a>Verificar conectividade da federação
O Azure AD Connect tentará validar os pontos de extremidade de autenticação recuperados dos metadados do PingFederate na etapa anterior.  O Azure AD Connect primeiro tentará resolver os pontos de extremidade usando seus servidores DNS locais.  Em seguida, tentará resolver os pontos de extremidade usando um provedor DNS externo.  Entre em contato com o administrador do PingFederate para resolver os problemas de validação.  

![Captura de tela mostrando a página "Verificar Conectividade".](./media/how-to-connect-install-custom/ping3.png)

### <a name="verify-federation-sign-in"></a>Verificar entrada da federação
Por fim, você pode verificar o fluxo de logon federado recém-configurado ao entrar em um domínio federado. Se sua entrada for bem-sucedida, a federação com o PingFederate será configurada com êxito.

![Captura de tela mostrando a página "Verificar logon federado". Uma mensagem na parte inferior indica uma entrada bem-sucedida.](./media/how-to-connect-install-custom/ping4.png)

## <a name="configure-and-verify-pages"></a>Configurar e verificar páginas
A configuração ocorre na página **Configurar**.

> [!NOTE]
> Se você configurou a federação, verifique se também configurou a [Resolução de nomes para servidores de federação](how-to-connect-install-prerequisites.md#name-resolution-for-federation-servers) antes de continuar a instalação.
>



![Captura de tela mostrando a página "Pronto para configurar".](./media/how-to-connect-install-custom/readytoconfigure2.png)

### <a name="use-staging-mode"></a>Usar o modo de preparo
É possível configurar um novo servidor de sincronização em paralelo com o modo de preparo. Se você quiser usar essa configuração, somente um servidor de sincronização poderá exportar para um diretório na nuvem. Mas se você quiser mover de outro servidor, por exemplo, um que esteja executando o DirSync, poderá habilitar o Azure AD Connect no modo de preparo. 

Quando você habilitar a configuração de preparo, o mecanismo de sincronização importará e sincronizará os dados normalmente. Mas não exportará dados para o Azure AD ou Active Directory. No modo de preparo, o recurso de sincronização de senha e o recurso write-back de senha estão desabilitados.

![Captura de tela mostrando a opção "Habilitar modo de preparo".](./media/how-to-connect-install-custom/stagingmode.png)

No modo de preparo, você pode fazer as alterações necessárias no mecanismo de sincronização e examinar o que será exportado. Quando a configuração parecer adequada, execute novamente o assistente de instalação e desabilite o modo de preparo. 

Agora, os dados serão exportados para o Azure AD deste servidor. Desabilite o outro servidor ao mesmo tempo, para que somente um servidor esteja exportando ativamente.

Para saber mais, confira [Modo de preparo](how-to-connect-sync-staging-server.md).

### <a name="verify-your-federation-configuration"></a>Verificar a configuração de federação
O Azure AD Connect verificará as configurações de DNS quando você clicar no botão **Verificar**. Ele verificará as seguintes configurações:

* **Conectividade com a intranet**
    * Resolver o FQDN da federação: o Azure AD Connect verifica se o FQDN da federação pode ser resolvido pelo DNS a fim de garantir a conectividade. Se o Azure AD Connect não puder resolver o FQDN, a verificação falhará. Para concluir a verificação, verifique se existe um registro DNS para o FQDN do serviço de federação.
    * Registro A de DNS: O Azure AD Connect verifica se o serviço de federação tem um registro A. Na ausência de um registro A, a verificação falhará. Para concluir a verificação, crie um registro A (não um registro CNAME) para o FQDN da federação.
* **Conectividade com a Extranet**
    * Resolver o FQDN da federação: o Azure AD Connect verifica se o FQDN da federação pode ser resolvido pelo DNS a fim de garantir a conectividade.

      ![Captura de tela mostrando a página "Instalação concluída".](./media/how-to-connect-install-custom/completed.png)

      ![Captura de tela mostrando a página "Instalação concluída". Uma mensagem indica que a configuração da intranet foi verificada.](./media/how-to-connect-install-custom/adfs7.png)

Para validar a autenticação descentralizada, execute manualmente um ou mais dos seguintes testes:

* Quando a sincronização for concluída, no Azure AD Connect, use a tarefa adicional **Verificar logon federado** para verificar a autenticação para uma conta de usuário local de sua escolha.
* Em um computador conectado ao domínio na intranet, verifique se você pode entrar em um navegador. Conecte-se ao https://myapps.microsoft.com. Em seguida, use sua conta conectada para verificar a entrada. A conta interna de administrador do Azure AD DS não está sincronizada e você não pode usá-la para verificação.
* Verifique se você pode entrar de um dispositivo na Extranet. Em um computador doméstico ou em um dispositivo móvel, conecte-se a https://myapps.microsoft.com. Em seguida, forneça suas credenciais.
* Valide a entrada do cliente avançado. Conecte-se ao https://testconnectivity.microsoft.com. Em seguida, selecione **Office 365** > **Teste de Logon único do Office 365**.

## <a name="troubleshoot"></a>Solucionar problemas
Esta seção contém informações de solução de problemas que podem ser usadas se você tiver um problema durante a instalação do Azure AD Connect.

Ao personalizar uma instalação do Azure AD Connect, na página **Instalar componentes necessários**, você pode selecionar **Usar um SQL Server existente**. Você pode ver o seguinte erro: "O banco de dados ADSync já contém dados e não pode ser substituído. Remova o banco de dados existente e tente novamente”.

![Captura de tela que mostra a página "Instalar componentes necessários". Um erro será exibido na parte inferior da página.](./media/how-to-connect-install-custom/error1.png)

Você verá esse erro porque um banco de dados chamado *ADSync* já existe na instância SQL do SQL Server especificado.

Normalmente, você verá esse erro depois de ter desinstalado o Azure AD Connect.  O banco de dados não é excluído do computador que executa o SQL Server quando você desinstala o Azure AD Connect.

Para corrigir esse problema:

1. Verifique o banco de dados ADSync que o Azure AD Connect usou antes de ser desinstalado. Verifique se o banco de dados não está mais sendo usado.

2. Fazer backup do banco de dados.

3. Exclua o banco de dados:
    1. Use o **Microsoft SQL Server Management Studio** para se conectar à instância do SQL Server. 
    1. Localize o banco de dados **ADSync** e clique nele com o botão direito do mouse.
    1. No menu de contexto, selecione **Excluir**.
    1. Selecione **OK** para excluir o banco de dados.

![Captura de tela mostrando o Microsoft SQL Server Management Studio. A D Sync está selecionado.](./media/how-to-connect-install-custom/error2.png)

Depois de excluir o banco de dados ADSync, selecione **Instalar** para repetir a instalação.

## <a name="next-steps"></a>Próximas etapas
Após a conclusão da instalação, saia do Windows. Em seguida, entre novamente antes de usar o Synchronization Service Manager ou o Editor de Regra de Sincronização.

Agora que você instalou o Azure AD Connect, é possível [verificar a instalação e atribuir licenças](how-to-connect-post-installation.md).

Para obter mais informações sobre os recursos que você habilitou durante a instalação, confira [Impedir exclusões acidentais](how-to-connect-sync-feature-prevent-accidental-deletes.md) e [Azure AD Connect Health](how-to-connect-health-sync.md).

Para obter mais informações sobre outros tópicos comuns, confira [Sincronização do Azure AD Connect: Agendador](how-to-connect-sync-feature-scheduler.md) e [Integrar suas identidades locais ao Azure AD](whatis-hybrid-identity.md).
