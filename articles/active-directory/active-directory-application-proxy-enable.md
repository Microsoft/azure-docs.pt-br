---
title: "Proxy do aplicativo do Azure AD – introdução à instalação do conector | Microsoft Docs"
description: Ative o Proxy de Aplicativo no Portal do Azure e instale os Conectores para o proxy reverso.
services: active-directory
documentationcenter: 
author: billmath
manager: femila
ms.assetid: c7186f98-dd80-4910-92a4-a7b8ff6272b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2017
ms.author: billmath
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 3b0a3e315ecd98565a852b3a8190d78ccdefe42d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-application-proxy-and-install-the-connector"></a>Introdução ao Proxy de Aplicativo e instale o conector
Este artigo orienta você pelas etapas para habilitar o Proxy de Aplicativo do Microsoft Azure AD para seu diretório de nuvem no Azure AD.

Se você ainda não conhece os benefícios de produtividade e segurança que o Proxy de Aplicativo traz para sua organização, saiba mais sobre [Como fornecer acesso remoto seguro a aplicativos locais](active-directory-application-proxy-get-started.md).

## <a name="application-proxy-prerequisites"></a>Pré-requisitos de Proxy de aplicativo
Antes de habilitar e usar os serviços de Proxy de aplicativo, você precisa ter:

* Uma [assinatura premium ou básica](active-directory-editions.md) do Microsoft Azure AD e um diretório do Azure AD do qual você seja administrador global.
* Um servidor executando o Windows Server 2012 R2 ou 2016, em que você pode instalar o Conector de Proxy de Aplicativo. O servidor precisa ser capaz de se conectar aos serviços do Proxy de Aplicativo na nuvem e aos aplicativos locais que você está publicando.
  * Para fazer logon único em seus aplicativos publicados usando a Delegação restrita de Kerberos, esse computador deve ser ingressado no mesmo domínio do AD que os aplicativos que você está publicando. Para obter mais informações, consulte [KCD para logon único com o Proxy de Aplicativo](active-directory-application-proxy-sso-using-kcd.md).

Se a sua organização usa servidores proxy para se conectar à Internet, leia [Trabalhar com servidores proxy locais existentes](application-proxy-working-with-proxy-servers.md) para obter detalhes de como configurá-los antes de começar com o Proxy de Aplicativo.

## <a name="open-your-ports"></a>Abrir as portas

Para preparar o ambiente para o proxy de aplicativo do Azure AD, primeiro você precisa habilitar a comunicação com data centers do Azure. Se houver um firewall no caminho, verifique se o ele está aberto para que o Conector possa fazer solicitações HTTPS (TCP) para o Proxy de Aplicativo.

1. Abra as seguintes portas para o tráfego de **saída**:

   | Número da porta | Como ele é usado |
   | --- | --- |
   | 80 | Baixando as CRLs (listas de certificados revogados) ao validar o certificado SSL |
   | 443 | Toda a comunicação de saída com o serviço do proxy de aplicativo |

   Se o firewall impõe o tráfego de acordo com os usuários originadores, abra essas portas para o tráfego proveniente dos serviços do Windows que são executados como um serviço de rede.

   > [!IMPORTANT]
   > A tabela reflete os requisitos de porta para as versões do conector 1.5.132.0 e mais recentes. Se você ainda tem uma versão mais antiga do conector, também precisa habilitar as seguintes portas, além da 80 e da 443: 5671, 8080, 9090-9091, 9350, 9352, 10100 – 10120.
   >
   >Para obter informações sobre como atualizar os conectores para a versão mais recente, consulte [Noções básicas sobre conectores de Proxy de Aplicativo do Azure AD](application-proxy-understand-connectors.md#automatic-updates).

2. Se seu firewall ou proxy permitir lista de permissões de DNS, você poderá adicionar as conexões a msappproxy.net e servicebus.windows.net à lista de permissões. Caso contrário, precisa permitir o acesso aos [Intervalos de IP do DataCenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653), que são atualizados a cada semana.

3. A Microsoft usa quatro endereços para verificar os certificados. Permita o acesso às URLs a seguir se você ainda não tiver feito isso para outros produtos:
   * mscrl.microsoft.com:80
   * crl.microsoft.com:80
   * ocsp.msocsp.com:80
   * www.microsoft.com:80

4. O conector deve acessar login.windows.net e login.microsoftonline.com para o processo de registro.

5. Use a [Ferramenta de Teste de Portas do Conector de Proxy de Aplicativo Azure AD](https://aadap-portcheck.connectorporttest.msappproxy.net/) para verificar se o conector pode alcançar o serviço Proxy de Aplicativo. No mínimo, verifique se a região EUA Central e a região mais próxima de você tem todas as marcas de seleção verdes. Além disso, um número maior de marcas de seleção verdes significa maior resiliência.

## <a name="install-and-register-a-connector"></a>Instalar e registrar um conector
1. Entre como administrador no [Portal do Azure](https://portal.azure.com/).
2. O diretório atual é exibido sob seu nome de usuário no canto superior direito. Se você precisar alterar os diretórios, selecione esse ícone.
3. Vá para **Azure Active Directory** > **Proxy de Aplicativo**.

   ![Navegue até o Proxy de Aplicativo](./media/active-directory-application-proxy-enable/app_proxy_navigate.png)

4. Selecione **Baixar o Conector**.

   ![Baixar o Conector](./media/active-directory-application-proxy-enable/download_connector.png)

5. Execute **AADApplicationProxyConnectorInstaller.exe** no servidor que você preparou de acordo com os pré-requisitos.
6. Siga as instruções no Assistente para instalar. Durante a instalação, é solicitado que você registre o conector com o Proxy de Aplicativo do seu locatário do Azure AD.

   * Forneça suas credenciais de administrador global do AD do Azure. Seu locatário de administrador global pode ser diferente das suas credenciais do Microsoft Azure.
   * Verifique se o administrador que registra o Conector está no mesmo diretório onde você habilitou o serviço Proxy de Aplicativo. Por exemplo, se o domínio de locatário for contoso.com, o administrador deve ser admin@contoso.com ou qualquer outro alias nesse domínio.
   * Se a **Configuração de Segurança Aprimorada do IE** estiver **Ativada** no servidor em que você está instalando o conector, a tela de registro poderá não ser vista. Siga as instruções na mensagem de erro para obter o acesso. Certifique-se de que a Segurança Melhorada do Internet Explorer está desativada.

Para fins de alta disponibilidade, você deve implantar pelo menos dois conectores. Cada conector deve ser registrado separadamente.

## <a name="test-that-the-connector-installed-correctly"></a>Testar se o conector foi instalado corretamente

Você pode confirmar se um novo conector foi instalado corretamente verificando-o no Portal do Azure ou em seu servidor. 

No Portal do Azure, entre em seu locatário e navegue até **Azure Active Directory** > **Proxy do Aplicativo**. Todos os seus conectores e grupos de conector são exibidos nesta página. Selecione um conector para ver os respectivos detalhes ou mova-o para um grupo de conector diferente. 

Em seu servidor, verifique a lista de serviços ativos para o conector e o atualizador do conector. Os dois serviços devem iniciar a execução imediatamente, caso contrário, ative-os: 

   * **Conector de Proxy de Aplicativo do Microsoft AAD** habilita a conectividade

   * O **Atualizador de conector do Proxy de Aplicativo do Microsoft AAD** é um serviço de atualização automática. O atualizador verifica novas versões do conector e o atualiza conforme necessário.

   ![Serviços do Conector de Proxy de Aplicativo - captura de tela](./media/active-directory-application-proxy-enable/app_proxy_services.png)

Para saber mais sobre conectores e como eles permanecem atualizados, veja [Noções básicas sobre conectores de Proxy de Aplicativo do Azure AD](application-proxy-understand-connectors.md).


## <a name="next-steps"></a>Próximas etapas
Agora você está pronto para [Publicar aplicativos com o Proxy de Aplicativo](application-proxy-publish-azure-portal.md).

Se você tem aplicativos que estão em redes separadas ou em locais diferentes, use grupos de conector para organizar os diferentes conectores em unidades lógicas. Saiba mais sobre como [Trabalhar com conectores de Proxy de Aplicativo](active-directory-application-proxy-connectors-azure-portal.md).
