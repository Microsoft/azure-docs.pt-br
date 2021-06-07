---
title: Rotação de emergência dos certificados de AD FS | Microsoft Docs
description: Este artigo explica como revogar e atualizar AD FS certificados imediatamente.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 9035c0a91bbbd7493437c692540fcbb3136a094e
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612966"
---
# <a name="emergency-rotation-of-the-ad-fs-certificates"></a>Rotação de emergência dos certificados de AD FS
Caso você precise girar os certificados AD FS imediatamente, você pode seguir as etapas descritas abaixo nesta seção.

> [!IMPORTANT]
> A condução das etapas abaixo no ambiente de AD FS revogará imediatamente os certificados antigos.  Como isso é feito imediatamente, o tempo normal normalmente permitido para seus parceiros de Federação consumirem seu novo certificado é aprovado. Isso pode resultar em uma interrupção do serviço como confiança atualizar para usar os novos certificados.  Isso deve ser resolvido uma vez que todos os parceiros de Federação tenham os novos certificados.

> [!NOTE]
> A Microsoft recomenda o uso de um HSM (módulo de segurança de hardware) para proteger e proteger certificados.
> Para obter mais informações, consulte [módulo de segurança de hardware](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#hardware-security-module-hsm) em práticas recomendadas para proteger AD FS.

## <a name="determine-your-token-signing-certificate-thumbprint"></a>Determinar a impressão digital do certificado de assinatura de token
Para revogar o antigo certificado de assinatura de token que AD FS está usando atualmente, você precisa determinar a impressão digital do certificado token-sigining.  Para fazer isso, use as seguintes etapas abaixo:

 1. Conectar-se ao Microsoft Online Service `PS C:\>Connect-MsolService`
 2. Documente as datas de impressão e expiração do certificado de autenticação de tokens local e de nuvem.
`PS C:\>Get-MsolFederationProperty -DomainName <domain>` 
 3.  Copie a impressão digital.  Ele será usado posteriormente para remover os certificados existentes.

Você também pode obter a impressão digital usando o gerenciamento de AD FS, navegando até serviço/certificados, clicando com o botão direito do mouse no certificado, selecionando Exibir certificado e, em seguida, selecionando detalhes. 

## <a name="determine-whether-ad-fs-renews-the-certificates-automatically"></a>Determinar se AD FS renova os certificados automaticamente
Por padrão, o AD FS está configurado para gerar os certificados de autenticação e descriptografia de tokens automaticamente durante a configuração inicial e quando os certificados estiverem próximos do vencimento.

Você pode executar o seguinte comando do Windows PowerShell: `PS C:\>Get-AdfsProperties | FL AutoCert*, Certificate*` .

A propriedade AutoCertificateRollover descreve se AD FS está configurado para renovar a assinatura de token e certificados de descriptografia de token automaticamente.  Se AutoCertificateRollover for definido como TRUE, siga as instruções descritas abaixo em [gerando novo certificado autoassinado se AutoCertificateRollover for definido como TRUE].  Se AutoCertificateRollover for definido como FALSE, siga as instruções descritas abaixo em [gerando novos certificados manualmente se AutoCertificateRollover estiver definido como FALSE]


## <a name="generating-new-self-signed-certificate-if-autocertificaterollover-is-set-to-true"></a>Gerando novo certificado autoassinado se AutoCertificateRollover estiver definido como TRUE
Nesta seção, você criará **dois** certificados de autenticação de tokens.  O primeiro usará o sinalizador **-urgente** , que substituirá o certificado primário atual imediatamente.  O segundo será usado para o certificado secundário.  

>[!IMPORTANT]
>O motivo pelo qual estamos criando dois certificados é porque o Azure se mantém em informações sobre o certificado anterior.  Ao criar um segundo, estamos forçando o Azure a liberar informações sobre o certificado antigo e substituí-lo por informações sobre o segundo certificado.
>
>Se você não criar o segundo certificado e atualizar o Azure com ele, pode ser possível que o certificado de autenticação de tokens antigo autentique usuários.

Você pode usar as etapas a seguir para gerar os novos certificados de autenticação de tokens.

 1. Verifique se você está conectado ao servidor de AD FS primário.
 2. Abra o Windows PowerShell como administrador. 
 3. Verifique se o AutoCertificateRollover está definido como true.
`PS C:\>Get-AdfsProperties | FL AutoCert*, Certificate*`
 4. Para gerar um novo certificado de autenticação de tokens: `Update-ADFSCertificate –CertificateType token-signing -Urgent` .
 5. Verifique a atualização executando o seguinte comando: `Get-ADFSCertificate –CertificateType token-signing`
 6. Agora, gere o segundo certificado de autenticação de tokens: `Update-ADFSCertificate –CertificateType token-signing` .
 7. Você pode verificar a atualização executando o seguinte comando novamente: `Get-ADFSCertificate –CertificateType token-signing`


## <a name="generating-new-certificates-manually-if-autocertificaterollover-is-set-to-false"></a>Gerar novos certificados manualmente se AutoCertificateRollover estiver definido como FALSE
Se você não estiver usando o padrão gerado automaticamente, assinatura de token autoassinado e certificados de descriptografia de token, será necessário renovar e configurar esses certificados manualmente.  Isso envolve criar dois novos certificados de assinatura de token e importá-los.  Em seguida, promova um para primário, revogue o certificado antigo e configure o segundo certificado como o certificado secundário.

Primeiro, você deve obter dois novos certificados de sua autoridade de certificação e importá-los para o repositório de certificados pessoais do computador local em cada servidor de Federação. Para obter instruções, consulte o artigo [importar um certificado](https://technet.microsoft.com/library/cc754489.aspx) .

>[!IMPORTANT]
>O motivo pelo qual estamos criando dois certificados é porque o Azure se mantém em informações sobre o certificado anterior.  Ao criar um segundo, estamos forçando o Azure a liberar informações sobre o certificado antigo e substituí-lo por informações sobre o segundo certificado.
>
>Se você não criar o segundo certificado e atualizar o Azure com ele, pode ser possível que o certificado de autenticação de tokens antigo autentique usuários.

### <a name="to-configure-a-new-certificate-as-a-secondary-certificate"></a>Para configurar um novo certificado como um certificado secundário
Em seguida, você deve configurar um certificado como o certificado de assinatura ou descriptografia de token de AD FS secundário e promovê-lo para o primário

1. Depois de importar o certificado. Abra o console de **Gerenciamento do AD FS** .
2. Expanda **Serviço** e, em seguida, selecione **Certificados**.
3. No painel Ações, clique em **adicionar Token-Signing certificado**.
4. Selecione o novo certificado a partir da lista de certificados mostrados e, em seguida, clique em OK.

### <a name="to-promote-the-new-certificate-from-secondary-to-primary"></a>Para promover o novo certificado de secundário para primário
Agora que o novo certificado foi importado e configurado no AD FS, você precisará definir como o certificado principal.
1. Abra o console de **Gerenciamento do AD FS** .
2. Expanda **Serviço** e, em seguida, selecione **Certificados**.
3. Clique no certificado de autenticação de token secundário.
4. No painel **Ações**, clique em **Configurar como Primário**. Clique Sim no prompt de confirmação.
5. Depois de promover o novo certificado como o certificado principal, você deve remover o certificado antigo, pois ele ainda pode ser usado. Consulte a seção [remover seus certificados antigos](#remove-your-old-certificates) abaixo.  

### <a name="to-configure-the-second-certificate-as-a-secondary-certificate"></a>Para configurar o segundo certificado como um certificado secundário
Agora que você adicionou o primeiro certificado e o tornou primário e removeu o antigo, importe o segundo certificado.  Em seguida, você deve configurar o certificado como o certificado de autenticação de token de AD FS secundário

1. Depois de importar o certificado. Abra o console de **Gerenciamento do AD FS** .
2. Expanda **Serviço** e, em seguida, selecione **Certificados**.
3. No painel Ações, clique em **adicionar Token-Signing certificado**.
4. Selecione o novo certificado a partir da lista de certificados mostrados e, em seguida, clique em OK.

## <a name="update-azure-ad-with-the-new-token-signing-certificate"></a>Atualizar o Azure AD com o novo certificado de autenticação de tokens
Abra o Módulo Microsoft Azure Active Directory para Windows PowerShell. Como alternativa, abra o Windows PowerShell e execute o comando `Import-Module msonline`

Conecte-se ao Azure AD executando o seguinte comando: `Connect-MsolService` e, em seguida, insira suas credenciais de administrador global.

>[!Note]
> Se você estiver executando esses comandos em um computador que não seja o servidor de Federação primário, insira o seguinte comando primeiro: `Set-MsolADFSContext –Computer <servername>` . Substitua <servername> pelo nome do servidor de AD FS. Em seguida, insira as credenciais de administrador para o servidor de AD FS quando solicitado.

Opcionalmente, verifique se uma atualização é necessária verificando as informações do certificado atual no Azure AD. Para fazer isso, execute o seguinte comando: `Get-MsolFederationProperty` . Insira o nome do domínio federado quando solicitado.

Para atualizar as informações de certificado no Azure AD, execute o seguinte comando: `Update-MsolFederatedDomain` e insira o nome de domínio quando solicitado.

>[!Note]
> Se você vir um erro ao executar esse comando, execute o seguinte comando: `Update-MsolFederatedDomain –SupportMultipleDomain` e insira o nome de domínio quando solicitado.

## <a name="replace-ssl-certificates"></a>Substituir certificados SSL
Caso você precise substituir seu certificado de assinatura de token devido a um comprometimento, você também deve revogar e substituir os certificados SSL para AD FS e seus servidores WAP.  

A revogação de seus certificados SSL deve ser feita na autoridade de certificação (CA) que emitiu o certificado.  Esses certificados são geralmente emitidos por provedores de terceiros, como GoDaddy.  Para obter um exemplo, consulte (revogar um certificado | Certificados SSL-GoDaddy nos ajudar).  Para obter mais informações, consulte [como a revogação de certificado funciona](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee619754(v=ws.10)?redirectedfrom=MSDN).

Depois que o certificado SSL antigo tiver sido revogado e um novo for emitido, você poderá substituir os certificados SSL. Para obter mais informações, consulte [substituindo o certificado SSL para AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap#replacing-the-ssl-certificate-for-ad-fs).


## <a name="remove-your-old-certificates"></a>Remover os certificados antigos
Depois de substituir os certificados antigos, você deve remover o certificado antigo porque ele ainda pode ser usado. . Para fazer isso, siga as etapas abaixo:.  Para fazer isso, siga as etapas abaixo:

1. Verifique se você está conectado ao servidor de AD FS primário.
2. Abra o Windows PowerShell como administrador. 
4. Para remover o certificado de autenticação de token antigo: `Remove-ADFSCertificate –CertificateType token-signing -thumbprint <thumbprint>` .

## <a name="updating-federation-partners-who-can-consume-federation-metadata"></a>Atualizando parceiros de Federação que podem consumir metadados de Federação
Se você tiver renovado e configurado um novo certificado de assinatura de token ou de descriptografia de token, certifique-se de que todos os seus parceiros de Federação (parceiros de recursos ou da organização de conta que são representados no seu AD FS por confianças de terceira parte confiável e confianças de provedor de declarações) tenham selecionado os novos certificados.

## <a name="updating-federation-partners-who-can-not-consume-federation-metadata"></a>Atualizando parceiros de Federação que não podem consumir metadados de Federação
Se seus parceiros de Federação não puderem consumir seus metadados de Federação, você deverá enviá-los manualmente para a chave pública do novo certificado de assinatura de token/descriptografia de token. Envie sua nova chave pública de certificado (arquivo. cer ou. p7b se desejar incluir toda a cadeia) em todos os parceiros de organização de recursos ou de organizações de conta (representados em sua AD FS por relações de confiança de terceira parte confiável e provedor de declarações). Os parceiros implementam alterações em seu lado para confiar nos novos certificados.



## <a name="revoke-refresh-tokens-via-powershell"></a>Revogar tokens de atualização por meio do PowerShell
Agora, desejamos revogar tokens de atualização para usuários que podem tê-los e forçá-los a fazer logon novamente e obter novos tokens.  Isso fará o logoff dos usuários de seu telefone, das sessões de webmail atuais, junto com outros itens que estão usando tokens e tokens de atualização.  As informações podem ser encontradas [aqui](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0&preserve-view=true) e você também pode fazer referência a como [revogar o acesso do usuário no Azure Active Directory](../../active-directory/enterprise-users/users-revoke-access.md).

## <a name="next-steps"></a>Próximas etapas

- [Gerenciamento de certificados SSL no AD FS e no Windows Server 2016](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap#replacing-the-ssl-certificate-for-ad-fs)
- [Obter e configurar certificados de assinatura de token e de descriptografia de token para AD FS](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn781426(v=ws.11)#updating-federation-partners)
- [Renovar certificados de Federação para Microsoft 365 e Azure Active Directory](how-to-connect-fed-o365-certs.md)



















