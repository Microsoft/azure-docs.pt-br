---
title: Problemas conhecidos e solução de problemas para chaves de segurança FIDO2 híbridas-Azure Active Directory
description: Saiba mais sobre alguns problemas conhecidos e maneiras de solucionar problemas de entrada de chave de segurança FIDO2 híbrido sem senha usando o Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 435b27255ce58a3541d6b0d3a76bdf4080aa3962
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648763"
---
# <a name="troubleshooting-for-hybrid-deployments-of-fido2-security-keys-in-azure-ad"></a>Solução de problemas para implantações híbridas de chaves de segurança do FIDO2 no Azure AD 

Este artigo aborda as perguntas frequentes para dispositivos ingressados no Azure AD híbrido e entrada sem senha em recursos locais. Com esse recurso sem senha, você pode habilitar a autenticação do Azure AD em dispositivos Windows 10 para dispositivos ingressados no Azure AD híbrido usando chaves de segurança FIDO2. Os usuários podem entrar no Windows em seus dispositivos com credenciais modernas, como chaves FIDO2, e acessar recursos baseados em AD DS (Active Directory Domain Services tradicional) com uma experiência de SSO (logon único) contínuo para seus recursos locais.

Há suporte para os seguintes cenários para usuários em um ambiente híbrido:

* Entre em dispositivos ingressados no Azure AD híbrido usando chaves de segurança FIDO2 e obtenha acesso SSO a recursos locais.
* Entre em dispositivos ingressados no Azure AD usando chaves de segurança FIDO2 e obtenha acesso SSO a recursos locais.

Para começar a usar as chaves de segurança do FIDO2 e o acesso híbrido a recursos locais, consulte os seguintes artigos:

* [Chaves de segurança sem senha](howto-authentication-passwordless-security-key.md)
* [Windows 10 sem senha](howto-authentication-passwordless-security-key-windows.md)
* [Locais sem senha](howto-authentication-passwordless-security-key-on-premises.md)

## <a name="known-issues"></a>Problemas conhecidos

* [Os usuários não conseguem entrar usando chaves de segurança FIDO2, pois a face do Windows Hello é muito rápida e é o mecanismo de entrada padrão](#users-are-unable-to-sign-in-using-fido2-security-keys-as-windows-hello-face-is-too-quick-and-is-the-default-sign-in-mechanism)
* [Os usuários não podem usar chaves de segurança FIDO2 imediatamente depois de criarem um computador ingressado no Azure AD híbrido](#users-arent-able-to-use-fido2-security-keys-immediately-after-they-create-a-hybrid-azure-ad-joined-machine)
* [Os usuários não podem obter o SSO para meu recurso de rede NTLM depois de entrar com uma chave de segurança FIDO2 e receber uma solicitação de credencial](#users-are-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-a-fido2-security-key-and-receiving-a-credential-prompt)

### <a name="users-are-unable-to-sign-in-using-fido2-security-keys-as-windows-hello-face-is-too-quick-and-is-the-default-sign-in-mechanism"></a>Os usuários não conseguem entrar usando chaves de segurança FIDO2, pois a face do Windows Hello é muito rápida e é o mecanismo de entrada padrão

O Windows Hello face é a melhor experiência pretendida para um dispositivo em que um usuário está registrado. As chaves de segurança FIDO2 devem ser usadas em dispositivos compartilhados ou onde o registro do Windows Hello para empresas é uma barreira.

Se o rosto do Windows Hello impedir que os usuários experimentem o cenário de entrada da chave de segurança do FIDO2, os usuários poderão desativar a entrada do Hello face removendo o registro facial nas **configurações > Sign-In opções**.

### <a name="users-arent-able-to-use-fido2-security-keys-immediately-after-they-create-a-hybrid-azure-ad-joined-machine"></a>Os usuários não podem usar chaves de segurança FIDO2 imediatamente depois de criarem um computador ingressado no Azure AD híbrido

Após o processo de ingresso e reinicialização de domínio em uma instalação limpa de um computador ingressado no Azure AD híbrido, você deve entrar com uma senha e aguardar que a política seja sincronizada antes de usar o para usar uma chave de segurança FIDO2 para entrar.

Esse comportamento é uma limitação conhecida para dispositivos ingressados no domínio e não é específico para chaves de segurança FIDO2.

Para verificar o status atual, use o `dsregcmd /status` comando. Verifique se *AzureAdJoined* e *DomainJoined* mostram *Sim*.

### <a name="users-are-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-a-fido2-security-key-and-receiving-a-credential-prompt"></a>Os usuários não podem obter o SSO para meu recurso de rede NTLM depois de entrar com uma chave de segurança FIDO2 e receber uma solicitação de credencial

Certifique-se de que os DCs suficientes sejam corrigidos para responder no tempo de manutenção da solicitação de recurso. Para verificar se você pode ver um servidor que está executando o recurso, examine a saída de `nltest /dsgetdc:<dc name> /keylist /kdc`

Se você conseguir ver um controlador de domínio com esse recurso, a senha do usuário pode ter sido alterada desde que se conectou ou há outro problema. Colete logs conforme detalhado na seção a seguir para a equipe de suporte da Microsoft para depurar.

## <a name="troubleshoot"></a>Solucionar problemas

Há duas áreas para solucionar problemas de [clientes da janela](#windows-client-issues)ou [problemas de implantação](#deployment-issues).

### <a name="windows-client-issues"></a>Problemas do Windows Client

Para coletar dados que ajudam a solucionar problemas de entrada no Windows ou de acesso a recursos locais de dispositivos Windows 10, conclua as seguintes etapas:

1. Abra o aplicativo **Hub de comentários** . Verifique se seu nome está na parte inferior esquerda do aplicativo e, em seguida, selecione **criar um novo item de comentário**.

    Para o tipo de item de comentário, escolha *problema*.

1. Selecione a categoria *segurança e privacidade* e, em seguida, a subcategoria *Fido* .
1. Alterne a caixa de seleção para *enviar arquivos anexados e diagnósticos à Microsoft juntamente com meus comentários*.
1. Selecione *recriar meus problemas* e *Iniciar captura*.
1. Bloqueie e desbloqueie a máquina com a chave de segurança FIDO2. Se o problema ocorrer, tente desbloquear com outras credenciais.
1. Retorne ao **Hub de comentários**, selecione **parar captura** e envie seus comentários.
1. Vá para a página de *comentários* e, em seguida, a guia *meus comentários* . Selecione seus comentários enviados recentemente.
1. Selecione o botão *compartilhar* no canto superior direito para obter um link para os comentários. Inclua este link ao abrir um caso de suporte ou responda ao engenheiro atribuído a um caso de suporte existente.

Os seguintes logs de eventos e as informações da chave do registro são coletados:

**Chaves do Registro**

* *HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\FIDO [ \* ]*
* *HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\PasswordForWork\* [ \* ]*
* *HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Policies\PasswordForWork\* [ \* ]*

**Informações de diagnóstico**

* Despejo de kernel ao vivo
* Coletar informações do pacote AppX
* Arquivos de contexto UIF

**Logs de eventos**

* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-AAD%40Operational.evtx*
* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-WebAuthN%40Operational.evtx*
* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-HelloForBusiness%40Operational.evtx*

### <a name="deployment-issues"></a>Problemas de implantação

Para solucionar problemas com a implantação do servidor Kerberos do Azure AD, use o novo módulo do PowerShell incluído com Azure AD Connect.

#### <a name="viewing-the-logs"></a>Exibindo os logs

Os cmdlets do PowerShell do servidor Kerberos do Azure AD usam o mesmo log que o assistente de Azure AD Connect padrão. Para exibir informações ou detalhes de erro dos cmdlets, conclua as seguintes etapas:

1. No servidor Azure AD Connect, navegue até `C:\ProgramData\AADConnect\` . Esta pasta está oculta por padrão.
1. Abra e exiba o arquivo mais recente `trace-*.log` localizado no diretório.

#### <a name="viewing-the-azure-ad-kerberos-server-objects"></a>Exibindo os objetos de servidor Kerberos do Azure AD

Para exibir os objetos de servidor Kerberos do Azure AD e verificar se eles estão em boas condições, conclua as seguintes etapas:

1. No Azure AD Connect Server, abra o PowerShell e navegue até `C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. Execute os comandos do PowerShell a seguir para exibir o servidor Kerberos do Azure AD do Azure AD e AD DS locais.

    Substitua *Corp.contoso.com* pelo nome do seu domínio de AD DS local.

    ```powershell
    Import-Module ".\AzureAdKerberos.psd1"

    # Specify the on-premises AD DS domain.
    $domain = "corp.contoso.com"

    # Enter an Azure Active Directory Global Admin username and password.
    $cloudCred = Get-Credential

    # Enter a Domain Admin username and password.
    $domainCred = Get-Credential

    # Get the Azure AD Kerberos Server Object
    Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential
    $domainCred
    ```

O comando gera as propriedades do servidor Kerberos do Azure AD do Azure AD e do AD DS local. Examine as propriedades para verificar se tudo está em uma ordem boa. Use a tabela a seguir para verificar as propriedades.

O primeiro conjunto de propriedades é dos objetos no ambiente de AD DS local. A segunda metade (as propriedades que começam com * Cloud * *) são do objeto de servidor Kerberos no Azure AD:

| Propriedade           | Descrição  |
|--------------------|--------------|
| ID                 | A *ID* exclusiva do objeto do controlador de domínio AD DS. |
| DomainDnsName      | O nome de domínio DNS do domínio de AD DS. |
| ComputerAccount    | O objeto de conta de computador do objeto de servidor Kerberos do Azure AD (o DC). |
| UserAccount        | O objeto de conta de usuário desabilitado que contém a chave de criptografia de TGT do servidor Kerberos do Azure AD. O DN dessa conta é *CN = krbtgt_AzureAD, cn = users <Domain-DN>* |
| Keyversion         | A versão de chave da chave de criptografia de TGT do servidor Kerberos do Azure AD. A versão é atribuída quando a chave é criada. Em seguida, a versão é incrementada toda vez que a chave é girada. Os incrementos são baseados nos metadados de replicação e provavelmente serão maiores que um.<br /><br /> Por exemplo, a *keyversion* inicial poderia ser *192272*. Na primeira vez que a chave é girada, a versão pode avançar para *212621*.<br /><br /> A coisa importante a ser verificada é que o *keyversion* para o objeto local e o *CloudKeyVersion* para o objeto Cloud são os mesmos. |
| Atualização de       | A data e a hora em que a chave de criptografia de TGT do servidor Kerberos do Azure AD foi atualizada ou criada. |
| KeyUpdatedFrom     | O controlador de domínio em que a chave de criptografia TGT do servidor Kerberos do AD foi atualizada pela última vez. |
| CloudId            | A *ID* do objeto do Azure AD. Deve corresponder à *ID* acima. |
| CloudDomainDnsName | O *DomainDnsName* do objeto do Azure AD. Deve corresponder ao *DomainDnsName* acima. |
| CloudKeyVersion    | A *keyversion* do objeto do Azure AD. Deve corresponder à *keyversion* acima. |
| CloudKeyUpdatedOn  | A *atualização* do objeto do Azure AD. Deve corresponder ao *keyupdated* acima. |

## <a name="next-steps"></a>Próximas etapas

Para começar a usar as chaves de segurança do FIDO2 e o acesso híbrido a recursos locais, consulte os seguintes artigos:

* [Chaves de segurança FIDO2 sem senha](howto-authentication-passwordless-security-key.md)
* [Windows 10 sem senha](howto-authentication-passwordless-security-key-windows.md)
* [Locais sem senha](howto-authentication-passwordless-security-key-on-premises.md)
