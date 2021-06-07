---
title: Problema ao instalar o conector de agente de Application Proxy
description: Como solucionar problemas que você pode enfrentar ao instalar o conector do agente de proxy de aplicativo para Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/28/2021
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 0f76f03883746b6f4b87bb817f8adde850ed28b3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99253657"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Problema ao instalar o conector de agente de Application Proxy

Microsoft Azure Active Directory conector de proxy de aplicativo é um componente de domínio interno que usa conexões de saída para estabelecer a conectividade do ponto de extremidade disponível na nuvem para o domínio interno.

## <a name="general-problem-areas-with-connector-installation"></a>Áreas de problemas gerais com a instalação do conector

Quando a instalação de um conector falha, a causa raiz geralmente é uma das áreas a seguir. **Como um precurso para qualquer solução de problemas, certifique-se de reinicializar o conector.**

1.  **Conectividade** – para concluir uma instalação bem-sucedida, o novo conector precisa registrar e estabelecer as propriedades de confiança futuras. Isso é feito conectando-se ao serviço de nuvem Proxy de Aplicativo do Azure Active Directory.

2.  **Estabelecimento de confiança** – o novo conector cria um certificado autoassinado e registra ao serviço de nuvem.

3.  **Autenticação do administrador** – durante a instalação, o usuário deve fornecer credenciais de administrador para concluir a instalação do conector.

> [!NOTE]
> Os logs de instalação do conector podem ser encontrados na pasta% TEMP% e podem ajudar a fornecer informações adicionais sobre o que está causando uma falha de instalação.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Verifique a conectividade com o serviço de Application Proxy de nuvem e a página de Logon da Microsoft

**Objetivo:** Verifique se o computador do conector pode se conectar ao ponto de extremidade de registro do proxy de aplicativo, bem como à página de logon da Microsoft.

1.  No servidor do conector, execute um teste de porta usando [Telnet](/windows-server/administration/windows-commands/telnet) ou outra ferramenta de teste de porta para verificar se as portas 443 e 80 estão abertas.

2.  Se qualquer uma dessas portas não for bem-sucedida, verifique se o firewall ou proxy de back-end tem acesso aos domínios e portas necessários, confira [preparar seu ambiente local](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

3.  Abra um navegador (guia separada) e vá para a seguinte página da Web: `https://login.microsoftonline.com`, certifique-se de que você pode fazer logon nessa página.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-certificate"></a>Verificar suporte a componentes de computador e back-end para certificado de confiança de proxy de aplicativo

**Objetivo:** Verifique se o computador do conector, o proxy de back-end e o firewall podem dar suporte ao certificado criado pelo conector para confiança futura e se o certificado é válido.

>[!NOTE]
>O conector tenta criar um certificado SHA512 com suporte a TLS1.2. Se o computador ou o firewall de back-end e o proxy não suportarem o TLS 1.2, a instalação falhará.
>
>

**Examine os pré-requisitos necessários:**

1.  Verifique se o computador dá suporte a TLS1.2 – todas as versões do Windows após 2012 R2 devem oferecer suporte a TLS 1.2. Se seu computador de conector for de uma versão de 2012 R2 ou anterior, certifique-se de que os seguintes KBs estão instalados no computador: <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Entre em contato com seu administrador de rede e peça para verificar se o proxy de back-end e o firewall não bloqueiam SHA512 para tráfego de saída.

**Para verificar o certificado do cliente:**

Verifique a impressão digital do certificado do cliente atual. O repositório de certificados pode ser encontrado em `%ProgramData%\microsoft\Microsoft AAD Application Proxy Connector\Config\TrustSettings.xml` .

```
<?xml version="1.0" encoding="utf-8"?>
<ConnectorTrustSettingsFile xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <CloudProxyTrust>
    <Thumbprint>4905CC64B2D81BBED60962ECC5DCF63F643CCD55</Thumbprint>
    <IsInUserStore>false</IsInUserStore>
  </CloudProxyTrust>
</ConnectorTrustSettingsFile>
```

Os valores possíveis de **IsInUserStore** são **true** e **false**. Um valor **true** significa que o certificado renovado automaticamente é armazenado no contêiner pessoal no repositório de certificados do usuário do serviço de rede. Um valor **false** significa que o certificado do cliente foi criado durante a instalação ou o registro iniciado pelo comando Register-AppProxyConnector e ele é armazenado no contêiner pessoal no repositório de certificados do computador local.

Se o valor for **true**, siga estas etapas para verificar o certificado:
1. Baixar [PsTools.zip](/sysinternals/downloads/pstools)
2. Extraia o [PsExec](/sysinternals/downloads/psexec) do pacote e execute o **PsExec-i-u "NT authority\network Service" cmd.exe** em um prompt de comando elevado.
3. Executar **certmgr. msc** no prompt de comando exibido recentemente
4. No console de gerenciamento, expanda o contêiner pessoal e clique em certificados
5. Localize o certificado emitido por **connectorregistrationca.msappproxy.net**

Se o valor for **false**, siga estas etapas para verificar o certificado:
1. Executar **certlm. msc**
2. No console de gerenciamento, expanda o contêiner pessoal e clique em certificados
3. Localize o certificado emitido por **connectorregistrationca.msappproxy.net**

**Para renovar o certificado do cliente:**

Se um conector não estiver conectado ao serviço por vários meses, seus certificados poderão estar desatualizados. A falha da renovação do certificado leva a um certificado expirado. Isso faz com que o serviço do conector pare de funcionar. O evento 1000 é registrado no log do administrador do conector:

"Falha no novo registro do conector: o certificado de confiança do conector expirou. Execute o cmdlet do PowerShell Register-AppProxyConnector no computador no qual o conector está sendo executado para registrar novamente o conector. "

Nesse caso, desinstale e reinstale o conector para disparar o registro ou você poderá executar os seguintes comandos do PowerShell:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

Para saber mais sobre o comando Register-AppProxyConnector, consulte [criar um script de instalação autônoma para o conector de proxy de aplicativo do AD do Azure](./application-proxy-register-connector-powershell.md)

## <a name="verify-admin-is-used-to-install-the-connector"></a>Verifique se o administrador é usado para instalar o conector

**Objetivo:** Verifique se o usuário que tenta instalar o conector é um administrador com as credenciais corretas. No momento, o usuário deve ter pelo menos um administrador de aplicativos para que a instalação seja realizada com sucesso.

**Para verificar se as credenciais estão corretas:**

Conecte-se ao `https://login.microsoftonline.com` e use as mesmas credenciais. Verifique se o logon foi bem-sucedido. Você pode verificar a função de usuário acessando **Azure Active Directory**  - &gt; **usuários e grupos**  - &gt; **todos os usuários**. 

Selecione sua conta de usuário e, em seguida, "função de diretório" no menu resultante. Verifique se a função selecionada é "administrador do aplicativo". Se não for possível acessar qualquer uma das páginas ao longo dessas etapas, você tem a função exigida.

## <a name="next-steps"></a>Próximas etapas
[Noções básicas sobre conectores de Proxy de Aplicativo do Azure AD](application-proxy-connectors.md)
