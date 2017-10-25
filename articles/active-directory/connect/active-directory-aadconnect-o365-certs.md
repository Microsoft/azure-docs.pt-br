---
title: "Renovação de certificado para usuários do Office 365 e do Azure AD | Microsoft Azure"
description: "Este artigo explica aos usuários do Office 365 como resolver problemas com emails que notificam sobre a renovação de um certificado."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: 543b7dc1-ccc9-407f-85a1-a9944c0ba1be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 7f1a3303eff9c413602e745b702baa659343eba6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="renew-federation-certificates-for-office-365-and-azure-active-directory"></a>Renovar certificados de federação para o Office 365 e o Azure Active Directory
## <a name="overview"></a>Visão geral
Para a federação bem-sucedida entre o Azure AD (Azure Active Directory) e o AD FS (Serviços de Federação do Active Directory), os certificados usados pelo AD FS para assinar tokens de segurança no Azure AD devem corresponder ao que está configurado no Azure AD. Qualquer incompatibilidade pode interromper a relação de confiança. O Azure AD garante que essas informações sejam mantidas em sincronia quando você implanta o AD FS e o Proxy de Aplicativo Web (para acesso à extranet).

Este artigo fornece informações adicionais para o gerenciamento de seus certificados de assinatura de token e as mantém em sincronia com o Azure AD nestes casos:

* Você não está implantando o Proxy de Aplicativo Web e, assim, os metadados de federação não estão disponíveis na extranet.
* Você não está usando a configuração padrão do AD FS para certificados de assinatura de token.
* Você está usando um provedor de identidade de terceiros.

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>Configuração padrão do AD FS para certificados de assinatura de token
Os certificados de assinatura de token e de descriptografia de token geralmente são autoassinados e têm validade de um ano. Por padrão, o AD FS inclui um processo de renovação automática chamado **AutoCertificateRollover**. Se você estiver usando o AD FS 2.0 ou posterior, o Office 365 e o Azure AD atualizarão automaticamente o certificado antes que ele expire.

### <a name="renewal-notification-from-the-office-365-portal-or-an-email"></a>Notificação de renovação do portal do Office 365 ou um email
> [!NOTE]
> Se você recebeu um email ou uma notificação no portal solicitando a renovação do certificado do Office, confira [Gerenciamento de alterações para certificados de assinatura de token](#managecerts) para saber se precisa tomar alguma providência. A Microsoft está ciente de um possível problema que pode levar ao envio de notificações para renovação de certificados, mesmo quando nenhuma ação é necessária.
>
>

O Azure AD tenta monitorar os metadados de federação e atualizar os certificados de assinatura de token como indicado pelos metadados. Trinta dias antes da expiração dos certificados de assinatura de token, o Azure AD verifica se há novos certificados disponíveis por meio de sondagem dos metadados de federação.

* Se ele puder sondar os metadados de federação e recuperar os novos certificados com êxito, nenhuma notificação por email ou aviso no portal do Office 365 será emitido para o usuário.
* Se não for possível recuperar os novos certificados de assinatura de token, porque os metadados de Federação não estão acessíveis ou a substituição automática de certificado não está habilitada, o Azure AD enviará uma notificação por email e um aviso no portal do Office 365.

![Notificação do portal do Office 365](./media/active-directory-aadconnect-o365-certs/notification.png)

> [!IMPORTANT]
> Se você estiver usando o AD FS para garantir a continuidade de negócios, verifique se seus servidores têm as atualizações a seguir para que não ocorram falhas de autenticação causadas por problemas conhecidos. Isso reduz os problemas conhecidos de servidor de proxy do AD FS para essa renovação e períodos futuros de renovação:
>
> Server 2012 R2 - [Pacote cumulativo de atualizações do Windows Server de maio de 2014](http://support.microsoft.com/kb/2955164)
>
> Server 2008 R2 e 2012 - [falha de autenticação por meio do proxy no Windows Server 2008 ou no Windows 2012 R2 SP1](http://support.microsoft.com/kb/3094446)
>
>

## Verifique se os certificados precisam ser atualizados <a name="managecerts"></a>
### <a name="step-1-check-the-autocertificaterollover-state"></a>Etapa 1: verificar o estado de AutoCertificateRollover
No servidor do AD FS, abra o PowerShell. Verifique se o valor AutoCertificateRollover está definido como True.

    Get-Adfsproperties

![AutoCertificateRollover](./media/active-directory-aadconnect-o365-certs/autocertrollover.png)

>[!NOTE] 
>Se você estiver usando o AD FS 2.0, primeiro execute Add-Pssnapin Microsoft.Adfs.Powershell.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>Etapa 2: confirmar se o AD FS e o Azure AD estão em sincronia
No servidor do AD FS, abra o prompt do Powershell do Azure AD e conecte-se ao Azure AD.

> [!NOTE]
> Você pode baixar o PowerShell do Azure AD [aqui](https://technet.microsoft.com/library/jj151815.aspx).
>
>

    Connect-MsolService

Verifique os certificados configurados no AD FS e as propriedades de confiança do Azure AD para o domínio especificado.

    Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/active-directory-aadconnect-o365-certs/certsync.png)

Se as impressões digitais em ambas as saídas forem correspondentes, isso indicará que os certificados estão em sincronia com o Azure AD.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>Etapa 3: verificar se o certificado está prestes a expirar
Na saída de Get-MsolFederationProperty ou de Get-AdfsCertificate, verifique a data em "Não Após". Se a data for inferior a daqui a 30 dias, você deverá executar a ação.

| AutoCertificateRollover | Certificados em sincronia com o Azure AD | Os metadados de federação do AD FS estão acessíveis publicamente | Validade | Ação |
|:---:|:---:|:---:|:---:|:---:|
| Sim |Sim |Sim |- |Nenhuma ação necessária. Confira [Renovar o certificado de assinatura de token automaticamente](#autorenew). |
| Sim |Não |- |Menos de 15 dias |Renovar imediatamente. Confira [Renovar manualmente o certificado de assinatura de token ](#manualrenew). |
| Não |- |- |Menos de 30 dias |Renovar imediatamente. Confira [Renovar manualmente o certificado de assinatura de token ](#manualrenew). |

\[-] Não importa

## Renovar o certificado de assinatura de token automaticamente (recomendado) <a name="autorenew"></a>
Você não precisará executar nenhuma etapa manual se os seguintes itens forem verdadeiros:

* Você já implantou o Proxy de Aplicativo Web, que pode habilitar o acesso a metadados de federação da extranet.
* Você está usando a configuração padrão do AD FS (AutoCertificateRollover está habilitado).

Verifique o seguinte para confirmar se o certificado pode ser atualizado automaticamente.

**1. A propriedade AutoCertificateRollover do AD FS deve ser definida como True.** Isso indica que o AD FS gerará automaticamente novos certificados de assinatura de token e de descriptografia de token antes da expiração dos antigos.

**2. Os metadados de federação do AD FS estão acessíveis publicamente.** Verifique se os metadados de federação são acessíveis publicamente, navegando até a seguinte URL em um computador na Internet pública (fora da rede corporativa):

https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

em que `(your_FS_name) `é substituído pelo nome de host de serviço de federação que sua organização usa, por exemplo, fs.contoso.com.  Se você puder verificar ambas as configurações com êxito, não terá de fazer mais nada.  

Exemplo: https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml

## Renovar manualmente o certificado de assinatura de token <a name="manualrenew"></a>
Você pode optar por renovar os certificados de assinatura de token manualmente. Por exemplo, os seguintes cenários podem funcionar melhor para a renovação manual:

* Os certificados de assinatura de token não são certificados autoassinados. O motivo mais comum para isso é que sua organização gerencia certificados do AD FS inscritos de uma autoridade de certificação organizacional.
* A segurança de rede não permite que os metadados de federação fiquem publicamente disponíveis.

Nesses cenários, sempre que atualizar os certificados de assinatura de token, você também deverá atualizar seu domínio do Office 365 usando o comando Update-MsolFederatedDomain do PowerShell.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>Etapa 1: verifique se o AD FS tem novos certificados de assinatura de token
**Configuração não padrão**

Se estiver usando uma configuração não padrão do AD FS (em que **AutoCertificateRollover** está definido como **False**), você provavelmente está usando certificados personalizados (não autoassinados). Para obter mais informações sobre como renovar os certificados de assinatura de token do AD FS, confira [Orientação para os clientes que não usam certificados autoassinados do AD FS](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert).

**Os metadados de federação não estão disponíveis publicamente**

Por outro lado, se **AutoCertificateRollover** estiver definido como **True**, mas se os metadados de federação não estiverem acessíveis publicamente, primeiro verifique se novos certificados de assinatura de token foram gerados pelo AD FS. Confirme se você tem os novos certificados de assinatura de token por meio das seguintes etapas:

1. Verifique se você fez logon no servidor do AD FS primário.
2. Verifique os certificados de autenticação atuais no AD FS abrindo uma janela Comando do PowerShell e executando o seguinte comando:

    PS C:\>Get-ADFSCertificate –CertificateType token-signing

   > [!NOTE]
   > Se estiver usando o AD FS 2.0, você deverá executar Add-Pssnapin Microsoft.Adfs.Powershell primeiro.
   >
   >
3. Examine a saída do comando em todos os certificados listados. Se o AD FS tiver gerado um novo certificado, você verá dois certificados na saída: um para o qual o valor **IsPrimary** é **True** e a data **NotAfter** está dentro de cinco dias e um para o qual **IsPrimary** é **False** e **NotAfter** está cerca de um ano no futuro.
4. Se você vir apenas um certificado e a data **NotAfter** for dentro de cinco dias, precisará gerar um novo certificado.
5. Para gerar um novo certificado, execute o seguinte comando em um prompt de comando do PowerShell: `PS C:\>Update-ADFSCertificate –CertificateType token-signing`.
6. Verifique a atualização executando o seguinte comando novamente: PS C:\>Get-ADFSCertificate –CertificateType token-signing

Dois certificados deverão ser listados agora, um dos quais tem uma data **NotAfter** de aproximadamente um ano no futuro e para o qual o valor **IsPrimary** é **False**.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-office-365-trust"></a>Etapa 2: atualizar os novos certificados de assinatura de token para a relação de confiança do Office 365
Atualize o Office 365 com os novos certificados de assinatura de token a serem usados para a relação de confiança, da maneira a seguir.

1. Abra o Módulo Microsoft Azure Active Directory para Windows PowerShell.
2. Execute $cred = Get-Credential. Quando este cmdlet solicitar credenciais, digite as credenciais da conta de administrador de serviços de nuvem.
3. Execute Connect-MsolService –Credential $cred. Esse cmdlet conecta você ao serviço de nuvem. A criação de um contexto que conecta você ao serviço de nuvem é necessária antes de executar qualquer um dos cmdlets adicionais instalados pela ferramenta.
4. Se você estiver executando esses comandos em um computador que não seja o servidor de federação primário do AD FS, execute Set-MSOLAdfscontext -Computer <AD FS primary server>, em que <AD FS primary server> é o nome FQDN interno do servidor primário do AD FS. Esse cmdlet cria um contexto que conecta você ao AD FS.
5. Execute Update-MSOLFederatedDomain –DomainName <domain>. Esse cmdlet atualiza as configurações do AD FS no serviço de nuvem e configura a relação de confiança entre os dois.

> [!NOTE]
> Se você precisar oferecer suporte a vários domínios de nível superior, como contoso.com e fabrikam.com, deve usar a opção **SupportMultipleDomain** com todos os cmdlets. Para obter mais informações, veja [Suporte para vários domínios de nível superior](active-directory-aadconnect-multiple-domains.md).
>
>

## Reparar a relação de confiança do Azure AD usando o Azure AD Connect <a name="connectrenew"></a>
Se tiver configurado o farm do AD FS e a relação de confiança do Azure AD usando o Azure AD Connect, você poderá usar o Azure AD Connect para detectar se precisa realizar alguma ação para seus certificados de assinatura de token. Se precisar renovar os certificados, você poderá usar o Azure AD Connect para fazê-lo.

Para obter mais informações, confira [Reparar a relação de confiança](active-directory-aadconnect-federation-management.md).
