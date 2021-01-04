---
title: Tutorial – Adicionar um aplicativo local – Proxy de aplicativo no Azure AD
description: O Azure AD (Active Directory) tem um serviço de Proxy de Aplicativo que permite que os usuários acessem aplicativos locais ao entrar com sua conta do Azure AD. Este tutorial mostra como preparar o ambiente para uso com o Proxy de Aplicativo. Em seguida, ele usa o portal do Azure para adicionar um aplicativo local ao locatário do Azure AD.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 12/10/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperf-fy21q2
ms.openlocfilehash: bcb484d62b7c4add7e1ab5562c19417a90cfb7e1
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587546"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Tutorial: Adicionar um aplicativo local para acesso remoto por meio do Proxy de Aplicativo no Azure Active Directory

O Azure AD (Active Directory) tem um serviço de Proxy de Aplicativo que permite que os usuários acessem aplicativos locais ao entrar com sua conta do Azure AD. Esse tutorial prepara seu ambiente para uso com o Proxy de Aplicativo. Depois que seu ambiente estiver pronto, você usará o portal do Azure para adicionar um aplicativo local ao seu locatário do Azure AD.

Os conectores são uma parte fundamental do Proxy de Aplicativo. Para saber mais sobre conectores, confira [Noções básicas sobre conectores do Proxy de Aplicativo do Azure AD](application-proxy-connectors.md).

Este tutorial:

> [!div class="checklist"]
> * Abre as portas para tráfego de saída e permite o acesso a URLs específicas
> * Instala o conector em seu servidor Windows e o registra com o Proxy de Aplicativo
> * Verifica se o conector está instalado e registrado corretamente
> * Adiciona um aplicativo local ao locatário do Azure AD
> * Verifica se um usuário de teste pode fazer logon no aplicativo usando uma conta do Azure AD

## <a name="prerequisites"></a>Pré-requisitos

Para adicionar um aplicativo local ao Azure AD, você precisará:

* Uma [assinatura do Microsoft Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory)
* Uma conta de administrador de aplicativo
* As identidades de usuário precisam ser sincronizadas de um diretório local ou criadas diretamente nos locatários do Azure AD. A Sincronização de Identidades permite que o Azure AD pré-autentique os usuários antes de permitir acesso a eles aos aplicativos publicados pelo Proxy de Aplicativo e tenha as informações de identificador de usuário necessárias para realizar o SSO (logon único).

### <a name="windows-server"></a>Windows Server

Para usar o Proxy de Aplicativo, você precisa de um Windows Server que executa o Windows Server 2012 R2 ou posterior. Você instalará o Conector de Proxy de Aplicativo no servidor. Esse servidor do conector precisa se conectar aos serviços do Proxy de Aplicativo no Azure e aos aplicativos locais que planeja publicar.

Para alta disponibilidade no seu ambiente de produção, é recomendável ter mais de um servidor Windows. Para este tutorial, um servidor Windows é suficiente.

> [!IMPORTANT]
> Se você estiver instalando o conector no Windows Server 2019, deverá desabilitar o suporte do protocolo HTTP2 no componente WinHttp para que a Delegação restrita do Kerberos funcione de maneira apropriada. Isso é desabilitado por padrão em versões anteriores de sistemas operacionais compatíveis. Adicionar a chave do Registro a seguir e reiniciar o servidor a desabilitará no Windows Server 2019. Observe que essa é uma chave do Registro em todo o computador.
>
> ```
> Windows Registry Editor Version 5.00
> 
> HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp\EnableDefaultHttp2 Value: 0
> ```
>
> A chave pode ser definida por meio do PowerShell com o comando a seguir.
> ```
> Set-ItemProperty 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp\' -Name EnableDefaultHTTP2 -Value 0
>

#### <a name="recommendations-for-the-connector-server"></a>Recomendações para o servidor do conector

1. Localize o servidor do conector fisicamente próximo aos servidores de aplicativos para otimizar o desempenho entre o conector e o aplicativo. Para obter mais informações, confira [Considerações sobre a topologia de rede](application-proxy-network-topology.md).
1. O servidor do conector e os servidores de aplicativos Web devem pertencer ao mesmo domínio do Active Directory ou abranger domínios confiáveis. Ter os servidores no mesmo domínio ou em domínios confiáveis é um requisito para usar o SSO (logon único) com IWA (Autenticação Integrada do Windows) e KCD (Delegação Restrita do Kerberos). Se o servidor do conector e os servidores de aplicativos Web estiverem em domínios do Active Directory diferentes, você precisará usar a delegação baseada em recursos para o logon único. Para obter mais informações, consulte [KCD para logon único com Proxy de aplicativo](application-proxy-configure-single-sign-on-with-kcd.md).

> [!WARNING]
> Se você não tiver implantado o Proxy da Proteção de Senha do Azure AD, não instale o Proxy de Aplicativo do Azure AD nem o Proxy de Proteção de Senha do Azure AD juntos no mesmo computador. O Proxy de Aplicativo do Azure AD e o Proxy da Proteção de Senha do Azure AD instalam diferentes versões do serviço do Atualizador de Agente do Azure AD Connect. Essas diferentes versões são incompatíveis quando instaladas juntas no mesmo computador.

#### <a name="tls-requirements"></a>Requisitos de TLS

O servidor do conector Windows precisa ter o TLS 1.2 habilitado antes de instalar o conector do Proxy de Aplicativo.

Para habilitar o TLS 1.2:

1. Defina as seguintes chaves do registro:

   ```
   Windows Registry Editor Version 5.00

   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
   "DisabledByDefault"=dword:00000000
   "Enabled"=dword:00000001
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server]
   "DisabledByDefault"=dword:00000000
   "Enabled"=dword:00000001
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
   "SchUseStrongCrypto"=dword:00000001
   ```

1. Reinicie o servidor.

> [!Note]
> A Microsoft está atualizando os serviços do Azure para que eles usem certificados TLS de outro conjunto de ACs (autoridades de certificação) raiz. Essa alteração está sendo feita porque os Certificados de Autoridade de Certificação atuais não estão em conformidade com um dos requisitos de linha de base do fórum do navegador/da AC. Confira [Alterações no certificado TLS do Azure](https://docs.microsoft.com/azure/security/fundamentals/tls-certificate-changes) para obter mais informações.

## <a name="prepare-your-on-premises-environment"></a>Preparar o ambiente local

Primeiro, habilite a comunicação com os datacenters do Azure para preparar o ambiente para o Proxy de Aplicativo do Azure AD. Se houver um firewall no caminho, verifique se ele está aberto. Com o firewall aberto, o conector pode enviar solicitações HTTPS (TCP) para o Proxy de Aplicativo.

> [!IMPORTANT]
> Se você estiver instalando o conector para a nuvem do Azure Governamental, siga os [pré-requisitos](../hybrid/reference-connect-government-cloud.md#allow-access-to-urls) e as [etapas de instalação](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud). Isso requer a habilitação do acesso a um conjunto diferente de URLs e um parâmetro adicional para executar a instalação.

### <a name="open-ports"></a>Abrir portas

Abra as seguintes portas para o tráfego de **saída**.

   | Número da porta | Como ele é usado |
   | --- | --- |
   | 80 | Baixando as CRLs (listas de certificados revogados) ao validar o certificado TLS/SSL |
   | 443 | Toda a comunicação de saída com o serviço do proxy de aplicativo |

Se o firewall impor o tráfego de acordo com os usuários originadores, abra também as portas 80 e 443 para o tráfego proveniente dos serviços do Windows que são executados como um serviço de rede.

### <a name="allow-access-to-urls"></a>Permitir acesso às URLs

Permita o acesso às seguintes URLs:

| URL | Porta | Como ele é usado |
| --- | --- | --- |
| &ast;.msappproxy.net<br>&ast;.servicebus.windows.net | 443/HTTPS | Comunicação entre o conector e o serviço de nuvem do Proxy de Aplicativo |
| crl3.digicert.com<br>crl4.digicert.com<br>ocsp.digicert.com<br>crl.microsoft.com<br>oneocsp.microsoft.com<br>ocsp.msocsp.com<br> | 80/HTTP |O conector usa essas URLs para verificar certificados. |
| login.windows.net<br>Secure.aadcdn.microsoftonline p.com<br>&ast;.microsoftonline.com<br>&ast;.microsoftonline-p.com<br>&ast;.msauth.net<br>&ast;.msauthimages.net<br>&ast;.msecnd.net<br>&ast;.msftauth.net<br>&ast;.msftauthimages.net<br>&ast;.phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctldl.windowsupdate.com<br>www.microsoft.com/pkiops | 443/HTTPS |O conector usa essas URLs durante o processo de registro. |
| ctldl.windowsupdate.com | 80/HTTP |O conector usa essa URL durante o processo de registro. |

Se o firewall ou o proxy permitir a configuração de listas de permissões de DNS, você poderá permitir conexões a &ast;.msappproxy.net, &ast;.servicebus.windows.net e outras URLs acima. Caso contrário, você precisa permitir o acesso aos [Intervalos de IP e Marcas de Serviço – Nuvem Pública do Azure](https://www.microsoft.com/download/details.aspx?id=56519). Os intervalos de IP são atualizados a cada semana.

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

    ![Conectores do Proxy de Aplicativo do Azure AD](./media/application-proxy-add-on-premises-application/app-proxy-connectors.png)

Para obter mais ajuda sobre a instalação de um conector, confira [Problemas ao instalar o conector de Proxy de Aplicativo](application-proxy-connector-installation-problem.md).

### <a name="verify-the-installation-through-your-windows-server"></a>Verificar a instalação por meio do servidor Windows

Para confirmar se o conector foi instalado e registrado corretamente:

1. Abra o Gerenciador de Serviços do Windows clicando na tecla **Windows** e inserindo *services.msc*.
1. Verifique se o status dos dois serviços a seguir é **Em Execução**.
   - O **Conector de Proxy de Aplicativo do Microsoft AAD** habilita a conectividade.
   - O **Atualizador de conector do Proxy de Aplicativo do Microsoft AAD** é um serviço de atualização automática. O atualizador verifica novas versões do conector e o atualiza conforme necessário.

     ![Serviços do Conector de Proxy de Aplicativo - captura de tela](./media/application-proxy-add-on-premises-application/app_proxy_services.png)

1. Se o status dos serviços não for **Em execução**, clique com o botão direito do mouse em cada serviço e escolha **Iniciar**.

## <a name="add-an-on-premises-app-to-azure-ad"></a>Adicionar um aplicativo local ao Azure AD

Agora que você preparou seu ambiente e instalou um conector, está pronto para adicionar aplicativos de locais ao Azure AD.  

1. Entre como administrador no [Portal do Azure](https://portal.azure.com/).
2. No painel de navegação à esquerda, escolha **Azure Active Directory**.
3. Escolha **Aplicativos empresariais** e, em seguida, **Novos aplicativos**.
4. Selecione o botão **Adicionar um aplicativo local** exibido aproximadamente na metade da página na seção **Aplicativos locais**. Como alternativa, você pode escolher **Criar seu aplicativo** na parte superior da página e selecionar **Configurar o Proxy de Aplicativo para acesso remoto seguro a um aplicativo local**.
5. Na seção **Adicionar seu próprio aplicativo local**, forneça as seguintes informações sobre o aplicativo:

    | Campo | Descrição |
    | :---- | :---------- |
    | **Nome** | O nome do aplicativo que será exibido em Meus Aplicativos e no portal do Azure. |
    | **URL Interna** | A URL para acessar o aplicativo de dentro de sua rede privada. Você pode fornecer um caminho específico no servidor back-end para publicar, enquanto o restante do servidor é não publicado. Assim, você pode publicar sites diferentes no mesmo servidor como diferentes aplicativos, e dar a cada um deles seu próprio nome e suas regras de acesso.<br><br>Se você publicar um caminho, verifique se ele inclui todas as imagens, scripts e folhas de estilo necessários para seu aplicativo. Por exemplo, se seu aplicativo estiver em https:\//yourapp/app e usar as imagens localizadas em https:\//yourapp/media, você deverá publicar https:\//yourapp/ como o caminho. Essa URL interna não precisa ser a página de aterrissagem que os usuários veem. Para obter mais informações, consulte [Definir uma página inicial personalizada para aplicativos publicados](application-proxy-configure-custom-home-page.md). |
    | **URL Externa** | O endereço para os usuários acessarem o aplicativo de fora da sua rede. Se você não quiser usar o domínio padrão de Proxy de Aplicativo, leia sobre [domínios personalizados no Proxy de Aplicativo do Azure AD](application-proxy-configure-custom-domain.md).|
    | **Pré-autenticação** | Como o Proxy de Aplicativo verifica os usuários antes de conceder a eles o acesso ao aplicativo.<br><br>**Azure Active Directory** – o Proxy de Aplicativo redireciona os usuários para entrar com o Azure AD, que autentica as permissões para o diretório e o aplicativo. Recomendamos manter essa opção como padrão para que você possa aproveitar os recursos de segurança do Microsoft Azure Active Directory como Acesso condicional e Autenticação Multifator. O **Azure Active Directory** é necessário para monitorar o aplicativo com o Microsoft Cloud App Security.<br><br>**Passagem** – Os usuários não precisam se autenticar no Azure AD para acessar o aplicativo. Você ainda pode configurar os requisitos de autenticação no back-end. |
    | **Grupo de Conectores** | Conectores processam o acesso remoto ao seu aplicativo e o ajudam a organizar conectores e aplicativos por região, rede ou finalidade. Se você ainda não tiver grupos de conectores criados, seu aplicativo é atribuído a **Padrão**.<br><br>Se o aplicativo usa WebSockets para se conectar, todos os conectores do grupo devem ser da versão 1.5.612.0 ou posterior.|

6. Se necessário, defina **Configurações adicionais**. Para a maioria dos aplicativos, você deve manter essas configurações em seus estados padrão. 

    | Campo | Descrição |
    | :---- | :---------- |
    | **Tempo Limite do Aplicativo Back-end** | Defina esse valor como **Longo** somente se o aplicativo estiver lento para se autenticar e se conectar. No padrão, o tempo limite do aplicativo de back-end tem uma duração de 85 segundos. Quando definido como longo, o tempo limite de back-end é aumentado para 180 segundos. |
    | **Usar Cookie Somente HTTP** | Defina esse valor como **Sim** para que os cookies do Proxy de Aplicativo incluam o sinalizador HTTPOnly no cabeçalho da resposta HTTP. Se estiver usando os Serviços de Área de Trabalho Remota, defina esse valor como **Não**.|
    | **Usar um Cookie Seguro**| Defina esse valor como **Sim** para transmitir cookies em um canal seguro, como uma solicitação HTTPS criptografada.
    | **Usar cookie persistente**| Mantenha esse valor definido como **Não**. Use essa configuração somente para aplicativos que não conseguem compartilhar cookies entre processos. Para saber mais sobre configurações de cookies, confira [Configurações de cookies para acessar aplicativos locais no Azure Active Directory](./application-proxy-configure-cookie-settings.md).
    | **Converter URLs nos Cabeçalhos** | Mantenha esse valor como **Sim** a menos que seu aplicativo exija o cabeçalho de host original na solicitação de autenticação. |
    | **Converter URLs no Corpo do Aplicativo** | Mantenha esse valor como **Não** a menos que você tenha inserido links HTML em código para outros aplicativos locais e não use domínios personalizados. Para saber mais, consulte [Conversão de link com o Proxy de Aplicativo](application-proxy-configure-hard-coded-link-translation.md).<br><br>Defina esse valor como **Sim** se você pretende monitorar esse aplicativo com o MCAS (Microsoft Cloud App Security). Para saber mais, confira o tópico [Configurar o monitoramento de acesso do aplicativo em tempo real com o Microsoft Cloud App Security e o Azure Active Directory](application-proxy-integrate-with-microsoft-cloud-application-security.md). |

7. Selecione **Adicionar**.

## <a name="test-the-application"></a>Testar o aplicativo

Você está pronto para testar se o aplicativo foi adicionado corretamente. Nas etapas a seguir, você adicionará uma conta de usuário ao aplicativo e tentará entrar nele.

### <a name="add-a-user-for-testing"></a>Adicionar um usuário para teste

Antes de adicionar um usuário ao aplicativo, verifique se que a conta de usuário já tem permissões para acessar o aplicativo de dentro da rede corporativa.

Para adicionar um usuário de teste:

1. Escolha **Aplicativos empresariais** e, em seguida, escolha o aplicativo que você deseja testar.
2. Escolha **Introdução** e, em seguida, **Atribuir um usuário para teste**.
3. Em **Usuários e grupos**, escolha **Adicionar usuário**.
4. Em **Adicionar atribuição**, escolha **Usuários e grupos**. A seção **Usuário e grupos** será exibida.
5. Escolha a conta que você deseja adicionar.
6. Escolha **Selecionar** e, em seguida, **Atribuir**.

### <a name="test-the-sign-on"></a>Testar o logon

Para testar o logon no aplicativo:

1. No aplicativo que você deseja testar, selecione **Proxy de Aplicativo**.
2. Na parte superior da página, selecione **Aplicativo de Teste** para executar um teste no aplicativo e verificar se há algum problema de configuração.
3. Inicie primeiro o aplicativo para testar a conexão e, em seguida, baixe o relatório de diagnóstico para examinar as diretrizes de resolução para os problemas detectados.

Para solucionar problemas, confira [Solucionar problemas e mensagens de erro do Proxy do Aplicativo](application-proxy-troubleshoot.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, exclua todos os recursos criados neste tutorial.

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
> [Configurar Logon Único](sso-options.md#choosing-a-single-sign-on-method)
