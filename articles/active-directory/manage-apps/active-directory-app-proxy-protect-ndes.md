---
title: Integrar com Proxy de Aplicativo do AD em um servidor NDES
titleSuffix: Azure Active Directory
description: Orientação sobre como implantar um Proxy de Aplicativo do Azure Active Directory para proteger seu servidor NDES.
services: active-directory
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/17/2020
ms.author: kenwith
ms.reviewer: mimart
ms.openlocfilehash: 69193296069765ae6e94ffe97913c136a0d033d9
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99257889"
---
# <a name="integrate-with-azure-ad-application-proxy-on-a-network-device-enrollment-service-ndes-server"></a>Integrar com o Azure Proxy de Aplicativo do AD em um servidor NDES (serviço de registro de dispositivo de rede)

O proxy de aplicativo Azure Active Directory (AD) permite que você publique aplicativos dentro de sua rede. Esses aplicativos são aqueles como sites do SharePoint, Microsoft Outlook Web App e outros aplicativos Web. Ele também fornece acesso seguro aos usuários fora da sua rede por meio do Azure.

Se você for novo no Azure Proxy de Aplicativo do AD e quiser saber mais, consulte [acesso remoto a aplicativos locais por meio do azure proxy de aplicativo do AD](application-proxy.md).

O Proxy de Aplicativo do AD do Azure é criado no Azure. Ele oferece uma grande quantidade de largura de banda de rede e infraestrutura de servidor para melhor proteção contra ataques de DDOS (negação de serviço distribuído) e excelente disponibilidade. Além disso, não há necessidade de abrir portas de firewall externas para sua rede local e nenhum servidor DMZ é necessário. Todo o tráfego é originado de entrada. Para obter uma lista completa de portas de saída, consulte [tutorial: adicionar um aplicativo local para acesso remoto por meio do proxy de aplicativo no Azure Active Directory](./application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

> O Azure Proxy de Aplicativo do AD é um recurso que está disponível somente se você estiver usando as edições Premium ou Basic do Azure Active Directory. Para obter mais informações, consulte [Preço do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). 
> Se você tiver licenças do Enterprise Mobility Suite (EMS), você estará qualificado para usar essa solução.
> O conector de Proxy de Aplicativo do AD do Azure é instalado apenas no Windows Server 2012 R2 ou posterior. Isso também é um requisito do servidor NDES.

## <a name="install-and-register-the-connector-on-the-ndes-server"></a>Instalar e registrar o conector no servidor NDES

1. Entre no [portal do Azure](https://portal.azure.com/) como um administrador de aplicativos do diretório que usa o Proxy de Aplicativo. Por exemplo, se o domínio de locatário for contoso.com, o administrador deverá ser admin@contoso.com ou qualquer outro alias de administrador nesse domínio.
1. Escolha o nome de usuário no canto superior direito. Verifique se você está conectado a um diretório que usa o Proxy de Aplicativo. Se for necessário alterar diretórios, escolha **Mudar diretório** e escolha um diretório que usa o Proxy de Aplicativo.
1. No painel de navegação à esquerda, escolha **Azure Active Directory**.
1. Em **Gerenciar**, escolha **Proxy de Aplicativo**.
1. Escolha **Baixar o serviço do conector**.

    ![Baixe o serviço do conector para ver os Termos de serviço](./media/active-directory-app-proxy-protect-ndes/application-proxy-download-connector-service.png)

1. Leia os Termos de serviço. Quando estiver pronto, escolha **Aceitar termos e baixar**.
1. Copie o arquivo de instalação do conector do Azure Proxy de Aplicativo do AD para o servidor NDES. 
   > Você pode instalar o conector em qualquer servidor dentro de sua rede corporativa com acesso ao NDES. Você não precisa instalá-lo no próprio servidor de NDES.
1. Execute o arquivo de instalação, como *AADApplicationProxyConnectorInstaller.exe*. Aceite os termos de licença de software.
1. Durante a instalação, você será solicitado a registrar o conector com o proxy de aplicativo no seu diretório do Azure AD.
   * Forneça as credenciais para um administrador global ou de aplicativo em seu diretório do Azure AD. As credenciais globais do Azure AD ou do administrador do aplicativo podem ser diferentes das suas credenciais do Azure no Portal.

        > [!NOTE]
        > A conta global ou de administrador de aplicativos usada para registrar o conector deve pertencer ao mesmo diretório em que você habilita o serviço de proxy de aplicativo.
        >
        > Por exemplo, se o domínio do Azure AD for *contoso.com*, o administrador global/do aplicativo deverá ser `admin@contoso.com` ou outro alias válido nesse domínio.

   * Se a configuração de segurança reforçada do Internet Explorer estiver ativada para o servidor em que você instalar o conector, a tela de registro poderá ser bloqueada. Para permitir o acesso, siga as instruções na mensagem de erro ou desative a segurança aprimorada do Internet Explorer durante o processo de instalação.
   * Se o registro do conector falhar, consulte [solucionar problemas do Application Proxy](application-proxy-troubleshoot.md).
1. No final da instalação, uma observação é mostrada para ambientes com um proxy de saída. Para configurar o conector de Proxy de Aplicativo do AD do Azure para trabalhar com o proxy de saída, execute o script fornecido, como `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1` .
1. Na página proxy de aplicativo no portal do Azure, o novo conector é listado com o status *ativo*, conforme mostrado no exemplo a seguir:

    ![O novo conector de Proxy de Aplicativo do AD do Azure mostrado como ativo no portal do Azure](./media/active-directory-app-proxy-protect-ndes/connected-app-proxy.png)

    > [!NOTE]
    > Para fornecer alta disponibilidade para aplicativos que se autenticam por meio do Proxy de Aplicativo do AD do Azure, você pode instalar conectores em várias VMs. Repita as mesmas etapas listadas na seção anterior para instalar o conector em outros servidores ingressados no domínio gerenciado AD DS do Azure.

1. Após a instalação bem-sucedida, volte para a portal do Azure.

1. Selecione **Aplicativos empresariais**.

   ![Verifique se você está participando dos participantes certos](./media/active-directory-app-proxy-protect-ndes/azure-active-directory-enterprise-applications.png)

1. Selecione **+ novo aplicativo** e, em seguida, selecione **aplicativo local**. 

1. Em **Adicionar seu próprio aplicativo local**, configure os seguintes campos:

   * **Nome**: Insira um nome para o aplicativo.
   * **URL interna**: Insira a URL/FQDN interna do seu servidor de NDES no qual você instalou o conector.
   * **Pré-autenticação**: selecione **passagem**. Não é possível usar nenhuma forma de pré-autenticação. O protocolo usado para solicitações de certificado (SCEP) não fornece essa opção.
   * Copie a **URL externa** fornecida para a área de transferência.

1. Selecione **+ Adicionar** para salvar seu aplicativo.

1. Teste se você pode acessar seu servidor de NDES por meio do proxy de aplicativo do Azure AD colando o link que você copiou na etapa 15 em um navegador. Você deverá ver uma página de boas-vindas do IIS padrão.

1. Como um teste final, adicione o caminho *mscep.dll* à URL existente colada na etapa anterior:

   https://scep-test93635307549127448334.msappproxy.net/certsrv/mscep/mscep.dll

1. Você deverá ver uma resposta de **erro HTTP 403 – Proibido** .

1. Altere a URL de NDES fornecida (via Microsoft Intune) para dispositivos. Essa alteração pode estar no Microsoft Endpoint Configuration Manager ou no centro de administração do Microsoft Endpoint Manager.

   * Para Configuration Manager, vá para o ponto de registro de certificado e ajuste a URL. Essa URL é o que os dispositivos chamam e apresentam seu desafio.
   * Para o Intune autônomo, edite ou crie uma nova política de SCEP e adicione a nova URL.

## <a name="next-steps"></a>Próximas etapas

Com o Proxy de Aplicativo do AD do Azure integrado com o NDES, publique aplicativos para que os usuários acessem. Para obter mais informações, consulte [publicar aplicativos usando o Azure proxy de aplicativo do AD](./application-proxy-add-on-premises-application.md).
