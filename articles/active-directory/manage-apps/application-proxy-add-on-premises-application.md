---
title: Adicionar um aplicativo local – Proxy de Aplicativo no Azure Active Directory | Microsoft Docs
description: O Azure AD (Active Directory) tem um serviço de Proxy de Aplicativo que permite que os usuários acessem aplicativos locais ao entrar com sua conta do Azure AD. Este tutorial mostra como preparar o ambiente para uso com o Proxy de Aplicativo. Em seguida, ele usa o portal do Azure para adicionar um aplicativo local ao locatário do Azure AD.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecff60d1a1f808c4021476d136fe014175451672
ms.sourcegitcommit: 0ebc62257be0ab52f524235f8d8ef3353fdaf89e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723969"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Tutorial: Adicionar um aplicativo local para acesso remoto por meio do Proxy de Aplicativo no Azure Active Directory

O Azure AD (Active Directory) tem um serviço de Proxy de Aplicativo que permite que os usuários acessem aplicativos locais ao entrar com sua conta do Azure AD. Esse tutorial prepara seu ambiente para uso com o Proxy de Aplicativo. Depois que seu ambiente estiver pronto, você usará o portal do Azure para adicionar um aplicativo local ao seu locatário do Azure AD.

Este tutorial:

> [!div class="checklist"]
> * Abre as portas para tráfego de saída e permite o acesso a URLs específicas
> * Instala o conector em seu servidor Windows e o registra com o Proxy de Aplicativo
> * Verifica se o conector está instalado e registrado corretamente
> * Adiciona um aplicativo local ao locatário do Azure AD
> * Verifica se um usuário de teste pode fazer logon no aplicativo usando uma conta do Azure AD

## <a name="before-you-begin"></a>Antes de começar

Para adicionar um aplicativo local ao Azure AD, você precisará:

* Uma [assinatura premium ou básica do Azure AD](https://azure.microsoft.com/pricing/details/active-directory)
* Uma conta de administrador de aplicativo
* As identidades de usuário precisam ser sincronizadas de um diretório local ou criadas diretamente nos locatários do Azure AD. A Sincronização de Identidades permite que o Azure AD pré-autentique os usuários antes de permitir acesso a eles aos aplicativos publicados pelo Proxy de Aplicativo e tenha as informações de identificador de usuário necessárias para realizar o SSO (logon único).

### <a name="windows-server"></a>Windows Server

Para usar o Proxy de Aplicativo, você precisa de um Windows Server que executa o Windows Server 2012 R2 ou posterior. Você instalará o Conector de Proxy de Aplicativo no servidor. Esse servidor do conector precisa se conectar aos serviços do Proxy de Aplicativo no Azure e aos aplicativos locais que planeja publicar.

Para alta disponibilidade no seu ambiente de produção, é recomendável ter mais de um servidor Windows. Para este tutorial, um servidor Windows é suficiente.

#### <a name="recommendations-for-the-connector-server"></a>Recomendações para o servidor do conector

1. Localize o servidor do conector fisicamente próximo aos servidores de aplicativos para otimizar o desempenho entre o conector e o aplicativo. Para obter mais informações, confira [Considerações sobre a topologia de rede](application-proxy-network-topology.md).
1. O servidor do conector e os servidores de aplicativos Web devem pertencer ao mesmo domínio do Active Directory ou abranger domínios confiáveis. Ter os servidores no mesmo domínio ou em domínios confiáveis é um requisito para usar o SSO (logon único) com IWA (Autenticação Integrada do Windows) e KCD (Delegação Restrita do Kerberos). Se o servidor do conector e os servidores de aplicativos Web estiverem em domínios do Active Directory diferentes, você precisará usar a delegação baseada em recursos para o logon único. Para obter mais informações, consulte [KCD para logon único com Proxy de aplicativo](application-proxy-configure-single-sign-on-with-kcd.md).

#### <a name="tls-requirements"></a>Requisitos de TLS

O servidor do conector Windows precisa ter o TLS 1.2 habilitado antes de instalar o conector do Proxy de Aplicativo.

Para habilitar o TLS 1.2:

1. Defina as seguintes chaves do registro:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Reinicie o servidor.

> [!IMPORTANT]
> Para fornecer a melhor criptografia da categoria para nossos clientes, estamos atualizando o serviço do Proxy de Aplicativo para limitar o acesso somente aos protocolos TLS 1.2. Com base nas mudanças de prontidão do cliente, as alterações serão gradualmente implementadas para os clientes que estiverem usando somente os protocolos TLS 1.2 e não sofrerão os efeitos dessa alteração. A suspensão do TLS 1.0 e 1.1 será concluída em 31 de agosto de 2019 e os clientes receberão uma notificação com antecedência para se prepararem para essa alteração. Para se preparar para essa alteração, certifique-se de que todas as combinações cliente-servidor e navegador-servidor estejam atualizadas para usar o TLS 1.2 para manter a conexão com o serviço do Proxy de Aplicativo. Elas incluem clientes que seus usuários estão usando para acessar aplicativos publicados pelo Proxy de Aplicativo. Confira Preparação para [TLS 1.2 no Office 365](https://support.microsoft.com/help/4057306/preparing-for-tls-1-2-in-office-365) para obter referências e recursos.

## <a name="prepare-your-on-premises-environment"></a>Preparar o ambiente local

Primeiro, habilite a comunicação com os datacenters do Azure para preparar o ambiente para o Proxy de Aplicativo do Azure AD. Se houver um firewall no caminho, verifique se ele está aberto. Com o firewall aberto, o conector pode enviar solicitações HTTPS (TCP) para o Proxy de Aplicativo.

### <a name="open-ports"></a>Abrir portas

Abra as seguintes portas para o tráfego de **saída**.

   | Número da porta | Como ele é usado |
   | --- | --- |
   | 80 | Baixando as CRLs (listas de certificados revogados) ao validar o certificado SSL |
   | 443 | Toda a comunicação de saída com o serviço do proxy de aplicativo |

Se o firewall impor o tráfego de acordo com os usuários originadores, abra também as portas 80 e 443 para o tráfego proveniente dos serviços do Windows que são executados como um serviço de rede.

Se já estiver usando o Proxy de Aplicativo, é provável que você tenha instalado uma versão mais antiga do conector. Siga este tutorial para instalar a versão mais recente do conector. As versões anteriores à 1.5.132.0 também exigem que as seguintes portas estejam abertas: 5671, 8080, 9090-9091, 9350, 9352, 10100–10120.

### <a name="allow-access-to-urls"></a>Permitir acesso às URLs

Permita o acesso às seguintes URLs:

| URL | Como ele é usado |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Comunicação entre o conector e o serviço de nuvem do Proxy de Aplicativo |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | O Azure usa essas URLs para verificar certificados. |
| login.windows.net<br>login.microsoftonline.com<br>Secure.aadcdn.microsoftonline p.com  | O conector usa essas URLs durante o processo de registro. |

Se o firewall ou o proxy permitir a configuração de listas de permissões de DNS, você poderá permitir conexões ao \*.msappproxy.net e \*.servicebus.windows.net. Caso contrário, você precisará permitir o acesso aos [intervalos IP do Datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653). Os intervalos de IP são atualizados a cada semana.

## <a name="install-and-register-a-connector"></a>Instalar e registrar um conector

Para usar o Proxy de Aplicativo, instale um conector em cada servidor Windows que estiver em uso com o serviço do Proxy de Aplicativo. O conector é um agente que gerencia a conexão de saída de servidores de aplicativos locais para o Proxy de Aplicativo no Azure AD. Você pode instalar um conector em servidores que também tenham outros agentes de autenticação instalados, como o Azure AD Connect.

Para instalar o conector:

1. Entre no [portal do Azure](https://portal.azure.com/) como um administrador de aplicativos do diretório que usa o Proxy de Aplicativo. Por exemplo, se o domínio de locatário for contoso.com, o administrador deverá ser admin@contoso.com ou qualquer outro alias de administrador nesse domínio.
1. Escolha o nome de usuário no canto superior direito. Verifique se você está conectado a um diretório que usa o Proxy de Aplicativo. Se for necessário alterar diretórios, escolha **Mudar diretório** e escolha um diretório que usa o Proxy de Aplicativo.
1. No painel de navegação à esquerda, escolha **Azure Active Directory**.
1. Em **Gerenciar**, escolha **Proxy de Aplicativo**.
1. Escolha **Baixar o serviço do conector**.

    ![Baixe o serviço do conector para ver os Termos de serviço](./media/application-proxy-add-on-premises-application/application-proxy-download-connector-service.png)

1. Leia os Termos de serviço. Quando estiver pronto, escolha **Aceitar termos e baixar**.
1. Na parte inferior da janela, escolha **Executar** para instalar o conector. Um assistente de instalação é aberto.
1. Siga as instruções do assistente para instalar o serviço. Quando for solicitado que você registre o conector com o Proxy de Aplicativo para seu locatário do Azure AD, forneça suas credenciais de administrador do aplicativo.
    - Para IE (Internet Explorer), se a **Configuração de Segurança Aprimorada do IE** estiver **Ativada**, você poderá não ver a tela de registro. Siga as instruções na mensagem de erro para obter o acesso. Verifique se a **Configuração da Segurança Aprimorada do Internet Explorer** está definida como **Desativada**.

### <a name="general-remarks"></a>Comentários gerais

Caso já tenha instalado um conector, reinstale-o para obter a versão mais recente. Para saber mais sobre as versões lançadas anteriormente e quais alterações elas incluem, confira [Proxy de Aplicativo: histórico de lançamento de versões](application-proxy-release-version-history.md).

Se você optar por ter mais de um servidor Windows para seus aplicativos locais, precisará instalar e registrar o conector em cada servidor. Você pode organizar os conectores em grupos de conectores. Para obter mais informações, confira [Grupos de conectores](application-proxy-connector-groups.md).

Se sua organização usa servidores proxy para se conectar à internet, você precisará configurá-los para o Proxy de aplicativo.  Para obter mais informações, consulte [trabalhar com existentes locais servidores proxy](application-proxy-configure-connectors-with-proxy-servers.md). 

Para saber mais sobre conectores, planejamento de capacidade e como eles permanecem atualizados, confira [Noções básicas sobre conectores de Proxy de Aplicativo do Azure AD](application-proxy-connectors.md).

## <a name="verify-the-connector-installed-and-registered-correctly"></a>Verificar se o conector está instalado e registrado corretamente

Você pode usar o portal do Azure ou seu servidor Windows para confirmar que um novo conector foi instalado corretamente.

### <a name="verify-the-installation-through-azure-portal"></a>Verificar a instalação por meio do portal do Azure

Para confirmar se o conector foi instalado e registrado corretamente:

1. Entre no seu diretório de locatário no [portal do Azure](https://portal.azure.com).
1. No painel de navegação à esquerda, escolha **Azure Active Directory** e, em seguida, escolha **Proxy de Aplicativo** na seção **Gerenciar**. Todos os seus conectores e grupos de conector são exibidos nesta página.
1. Exiba um conector para verificar os detalhes. Os conectores devem ser expandidos por padrão. Se o conector que você deseja exibir não estiver expandido, expanda-o para exibir os detalhes. Um rótulo verde ativo indica que seu conector pode se conectar ao serviço. No entanto, mesmo que o rótulo esteja verde, um problema de rede ainda poderá bloquear o recebimento de mensagens pelo conector.

    ![Conectores do Proxy de Aplicativo do Azure AD](./media/application-proxy-connectors/app-proxy-connectors.png)

Para obter mais ajuda sobre a instalação de um conector, confira [Problemas ao instalar o conector de Proxy de Aplicativo](application-proxy-connector-installation-problem.md).

### <a name="verify-the-installation-through-your-windows-server"></a>Verificar a instalação por meio do servidor Windows

Para confirmar se o conector foi instalado e registrado corretamente:

1. Abra o Gerenciador de Serviços do Windows clicando na tecla **Windows** e inserindo *services.msc*.
1. Verifique se o status dos dois serviços a seguir é **Em Execução**.
   - O **Conector de Proxy de Aplicativo do Microsoft AAD** habilita a conectividade.
   - O **Atualizador de conector do Proxy de Aplicativo do Microsoft AAD** é um serviço de atualização automática. O atualizador verifica novas versões do conector e o atualiza conforme necessário.

     ![Serviços do Conector de Proxy de Aplicativo - captura de tela](./media/application-proxy-enable/app_proxy_services.png)

1. Se o status dos serviços não for **Em execução**, clique com o botão direito do mouse em cada serviço e escolha **Iniciar**.

## <a name="add-an-on-premises-app-to-azure-ad"></a>Adicionar um aplicativo local ao Azure AD

Agora que você preparou seu ambiente e instalou um conector, está pronto para adicionar aplicativos de locais ao Azure AD.  

1. Entre como administrador no [Portal do Azure](https://portal.azure.com/).
1. No painel de navegação à esquerda, escolha **Azure Active Directory**.
1. Escolha **Aplicativos empresariais** e, em seguida, **Novos aplicativos**.
1. Selecione **Aplicativo local**.  
1. Na seção **Adicionar seu próprio aplicativo local**, forneça as seguintes informações sobre o aplicativo:

    | Campo | DESCRIÇÃO |
    | :---- | :---------- |
    | **Nome** | O nome do aplicativo que será exibido no painel de acesso e no portal do Azure. |
    | **URL Interna** | A URL para acessar o aplicativo de dentro de sua rede privada. Você pode fornecer um caminho específico no servidor back-end para publicar, enquanto o restante do servidor é não publicado. Assim, você pode publicar sites diferentes no mesmo servidor como diferentes aplicativos, e dar a cada um deles seu próprio nome e suas regras de acesso.<br><br>Se você publicar um caminho, verifique se ele inclui todas as imagens, scripts e folhas de estilo necessários para seu aplicativo. Por exemplo, se seu aplicativo estiver em https:\//yourapp/app e usar as imagens localizadas em https:\//yourapp/media, você deverá publicar https:\//yourapp/ como o caminho. Essa URL interna não precisa ser a página de aterrissagem que os usuários veem. Para obter mais informações, consulte [Definir uma página inicial personalizada para aplicativos publicados](application-proxy-configure-custom-home-page.md). |
    | **URL Externa** | O endereço para os usuários acessarem o aplicativo de fora da sua rede. Se você não quiser usar o domínio padrão de Proxy de Aplicativo, leia sobre [domínios personalizados no Proxy de Aplicativo do Azure AD](application-proxy-configure-custom-domain.md).|
    | **Pré-autenticação** | Como o Proxy de Aplicativo verifica os usuários antes de conceder a eles o acesso ao aplicativo.<br><br>**Azure Active Directory** – o Proxy de Aplicativo redireciona os usuários para entrar com o Azure AD, que autentica as permissões para o diretório e o aplicativo. Recomendamos manter essa opção como padrão para que você possa aproveitar os recursos de segurança do Microsoft Azure Active Directory como Acesso condicional e Autenticação Multifator. O **Azure Active Directory** é necessário para monitorar o aplicativo com o Microsoft Cloud App Security.<br><br>**Passagem** – Os usuários não precisam se autenticar no Azure AD para acessar o aplicativo. Você ainda pode configurar os requisitos de autenticação no back-end. |
    | **Grupo de Conectores** | Conectores processam o acesso remoto ao seu aplicativo e o ajudam a organizar conectores e aplicativos por região, rede ou finalidade. Se você ainda não tiver grupos de conectores criados, seu aplicativo é atribuído a **Padrão**.<br><br>Se o aplicativo usa WebSockets para se conectar, todos os conectores do grupo devem ser da versão 1.5.612.0 ou posterior.|

1. Se necessário, defina **Configurações adicionais**. Para a maioria dos aplicativos, você deve manter essas configurações em seus estados padrão. 

    | Campo | DESCRIÇÃO |
    | :---- | :---------- |
    | **Tempo Limite do Aplicativo Back-end** | Defina esse valor como **Longo** somente se o aplicativo estiver lento para se autenticar e se conectar. |
    | **Usar Cookie Somente HTTP** | Defina esse valor como **Sim** para que os cookies do Proxy de Aplicativo incluam o sinalizador HTTPOnly no cabeçalho da resposta HTTP. Se estiver usando os Serviços de Área de Trabalho Remota, defina esse valor como **Não**.|
    | **Usar um Cookie Seguro**| Defina esse valor como **Sim** para transmitir cookies em um canal seguro, como uma solicitação HTTPS criptografada.
    | **Usar cookie persistente**| Mantenha esse valor definido como **Não**. Use essa configuração somente para aplicativos que não conseguem compartilhar cookies entre processos. Para saber mais sobre configurações de cookies, confira [Configurações de cookies para acessar aplicativos locais no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).
    | **Converter URLs nos Cabeçalhos** | Mantenha esse valor como **Sim** a menos que seu aplicativo exija o cabeçalho de host original na solicitação de autenticação. |
    | **Converter URLs no Corpo do Aplicativo** | Mantenha esse valor como **Não** a menos que você tenha inserido links HTML em código para outros aplicativos locais e não use domínios personalizados. Para saber mais, consulte [Conversão de link com o Proxy de Aplicativo](application-proxy-configure-hard-coded-link-translation.md).<br><br>Defina esse valor como **Sim** se você pretende monitorar esse aplicativo com o MCAS (Microsoft Cloud App Security). Para saber mais, confira o tópico [Configurar o monitoramento de acesso do aplicativo em tempo real com o Microsoft Cloud App Security e o Azure Active Directory](application-proxy-integrate-with-microsoft-cloud-application-security.md). |

1. Selecione **Adicionar**.

## <a name="test-the-application"></a>Testar o aplicativo

Você está pronto para testar se o aplicativo foi adicionado corretamente. Nas etapas a seguir, você adicionará uma conta de usuário ao aplicativo e tentará entrar nele.

### <a name="add-a-user-for-testing"></a>Adicionar um usuário para teste

Antes de adicionar um usuário ao aplicativo, verifique se que a conta de usuário já tem permissões para acessar o aplicativo de dentro da rede corporativa.

Para adicionar um usuário de teste:

1. Escolha **Aplicativos empresariais** e, em seguida, escolha o aplicativo que você deseja testar.
1. Escolha **Introdução** e, em seguida, **Atribuir um usuário para teste**.
1. Em **Usuários e grupos**, escolha **Adicionar usuário**.
1. Em **Adicionar atribuição**, escolha **Usuários e grupos**. A seção **Usuário e grupos** será exibida.
1. Escolha a conta que você deseja adicionar.
1. Escolha **Selecionar** e, em seguida, **Atribuir**.

### <a name="test-the-sign-on"></a>Testar o logon

Para testar o logon no aplicativo:

1. No seu navegador, navegue até a URL externa configurada durante a etapa de publicação. Você deverá ver a tela inicial.
1. Entre no aplicativo como o usuário que você criou na seção anterior.

Para solucionar problemas, confira [Solucionar problemas e mensagens de erro do Proxy do Aplicativo](application-proxy-troubleshoot.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você preparou seu ambiente local para trabalhar com o Proxy de Aplicativo e, em seguida, instalou e registrou o conector de Proxy de Aplicativo. Em seguida, você adicionou um aplicativo ao locatário do Azure AD. Você verificou que um usuário pode entrar no aplicativo usando uma conta do Azure AD.

Você fez essas coisas:
> [!div class="checklist"]
> * Abriu as portas para tráfego de saída e permitiu o acesso a URLs específicas
> * Instalou o conector em seu servidor Windows e o registrou com o Proxy de Aplicativo
> * Verificou se o conector estava instalado e registrado corretamente
> * Adicionou um aplicativo local ao locatário do Azure AD
> * Verificou se um usuário de teste podia fazer logon no aplicativo usando uma conta do Azure AD

Você está pronto para configurar o aplicativo para logon único. Use o link a seguir para escolher um método de logon único e encontrar tutoriais sobre logon único.

> [!div class="nextstepaction"]
> [Configurar Logon Único](what-is-single-sign-on.md#choosing-a-single-sign-on-method)
