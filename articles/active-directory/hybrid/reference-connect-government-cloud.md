---
title: 'Azure AD Connect: Considerações de identidade híbrida para a nuvem do Azure Governamental'
description: Considerações especiais para a implantação do Azure AD Connect com a nuvem do Azure Governamental.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e163ea34948906060996ee952f45ec0cdb467557
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97504348"
---
# <a name="hybrid-identity-considerations-for-the-azure-government-cloud"></a>Considerações de identidade híbrida para a nuvem do Azure Governamental

Este artigo descreve as considerações para a integração de um ambiente híbrido com a nuvem do Microsoft Azure Governamental. Essas informações são fornecidas como uma referência para administradores e arquitetos que trabalham com a nuvem do Azure Governamental.

> [!NOTE]
> Para integrar um ambiente de Active Directory da Microsoft (local ou hospedado em uma IaaS que faz parte da mesma instância de nuvem) com a nuvem do Azure governamental, você precisa atualizar para a versão mais recente do [Azure ad Connect](https://www.microsoft.com/download/details.aspx?id=47594).

> [!NOTE]
> Este artigo contém referências ao termo *lista* de permissões, um termo que a Microsoft não usa mais. Quando o termo for removido do software, nós o removeremos deste artigo.

Para obter uma lista completa dos pontos de extremidade do departamento de defesa do governo dos Estados Unidos, consulte a [documentação](/office365/enterprise/office-365-u-s-government-dod-endpoints).

## <a name="azure-ad-pass-through-authentication"></a>O que é a Autenticação de Passagem do Azure AD

As informações a seguir descrevem a implementação da Autenticação de Passagem e da nuvem do Azure Governamental.

### <a name="allow-access-to-urls"></a>Permitir acesso às URLs

Antes de implantar o agente de Autenticação de Passagem, verifique se existe um firewall entre os servidores e o Azure AD. Se o firewall ou o proxy permitir programas bloqueados ou seguros do Sistema de Nomes de Domínio (DNS), adicione as seguintes conexões.

> [!NOTE]
> As diretrizes a seguir também se aplicam à instalação do [conector de Proxy de Aplicativo do Azure AD](../manage-apps/what-is-application-proxy.md) para ambientes do Azure Governamental.

|URL |Como ele é usado|
|-----|-----|
|&#42;. msappproxy.us</br>&#42;.servicebus.usgovcloudapi.net|O agente usa essas URLs para se comunicar com o serviço de nuvem do Azure AD. |
|`mscrl.microsoft.us:80` </br>`crl.microsoft.us:80` </br>`ocsp.msocsp.us:80` </br>`www.microsoft.us:80`| O agente usa essas URLs para verificar certificados.|
|login.windows.us </br>Secure.aadcdn.microsoftonline p.com </br>&#42;.microsoftonline.us </br>&#42;.microsoftonline-p.us </br>&#42;.msauth.net </br>&#42;.msauthimages.net </br>&#42;.msecnd.net</br>&#42;.msftauth.net </br>&#42;.msftauthimages.net</br>&#42;.phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctldl.windowsupdate.us:80| O agente usa essas URLs durante o processo de registro.

### <a name="install-the-agent-for-the-azure-government-cloud"></a>Instalar o agente para a nuvem do Azure Governamental

Siga estas etapas para instalar o agente para a nuvem do Azure Governamental:

1. No terminal da linha de comando, vá para a pasta que contém o arquivo executável que instala o agente.
1. Execute os comandos a seguir, que especificam que a instalação é para o Azure Governamental.

   Para a Autenticação de Passagem:

   ```
   AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
   ```

   Para o Proxy de Aplicativo:

   ```
   AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
   ```

## <a name="single-sign-on"></a>Logon único

### <a name="set-up-your-azure-ad-connect-server"></a>Configurar seu servidor do Azure AD Connect

Se você usa a Autenticação de Passagem como seu método de entrada, não é necessária nenhuma verificação de pré-requisitos adicional. Se você usa a sincronização de hash de senha como seu método de entrada e se há um firewall entre o Azure AD Connect e o Azure AD, verifique se:

- Você está usando a versão 1.1.644.0 ou superior do Azure AD Connect.
- Se o firewall ou o proxy permitir programas bloqueados ou protegidos por DNS, adicione as conexões às URLs &#42;.msappproxy.us pela porta 443.

  Caso contrário, permita o acesso aos intervalos de IP do datacenter do Azure, os quais são atualizados semanalmente. Esse pré-requisito é aplicável somente quando você habilita o recurso. Não é obrigatório para logons reais de usuários.

### <a name="roll-out-seamless-single-sign-on"></a>Distribuir logon único contínuo

Você pode distribuir gradualmente o logon único contínuo do Azure AD para seus usuários usando as instruções a seguir. Comece adicionando a URL `https://autologon.microsoft.us` do Azure AD às configurações de zona de intranet de todos ou de alguns usuários selecionados usando a Política de Grupo no Active Directory.

Você também precisa habilitar a configuração da política de zona de intranet **Permitir atualizações à barra de status por meio de script usando a Política de Grupo**.

## <a name="browser-considerations"></a>Considerações de navegador

### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (todas as plataformas)

O Mozilla Firefox não usa a autenticação Kerberos automaticamente. Cada usuário precisa adicionar manualmente as URLs do Azure Active Directory às configurações do Firefox com as seguintes etapas:

1. Execute o Firefox e insira **about: config** na barra de endereços. Ignore todas as notificações que possam aparecer.
1. Procure a preferência  **network.negotiate-auth.trusted-uris** . Esta preferência lista os sites confiáveis do Firefox para a autenticação Kerberos.
1. Clique com o botão direito do mouse no nome de preferência e selecione  **Modificar**.
1. Insira `https://autologon.microsoft.us` na caixa.
1. Selecione  **OK**  e, em seguida, reabra o navegador.

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge com base em Chromium (todas as plataformas)

Se você tiver substituído as configurações de política  `AuthNegotiateDelegateAllowlist`  ou `AuthServerAllowlist`  em seu ambiente, certifique-se de adicionar a URL `https://autologon.microsoft.us` do Azure AD a elas.

### <a name="google-chrome-all-platforms"></a>Google Chrome (todas as plataformas)

Se você tiver substituído as configurações de política  `AuthNegotiateDelegateWhitelist`  ou `AuthServerWhitelist`  em seu ambiente, certifique-se de adicionar a URL `https://autologon.microsoft.us` do Azure AD a elas.

## <a name="next-steps"></a>Próximas etapas

- [Autenticação de Passagem](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
- [Logon único](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites)
