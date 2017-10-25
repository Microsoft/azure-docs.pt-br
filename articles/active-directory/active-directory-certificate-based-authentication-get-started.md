---
title: "Autenticação baseada em certificado do Azure Active Directory - Introdução | Microsoft Docs"
description: "Aprenda a configurar a autenticação baseada em certificado no seu ambiente"
author: MarkusVi
documentationcenter: na
manager: femila
ms.assetid: c6ad7640-8172-4541-9255-770f39ecce0e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/02/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 8ebc6f2dd7502fd75ffdd4d5d68338382cb1a46b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-certificate-based-authentication-in-azure-active-directory"></a>Inicie com uma autenticação baseada em certificado do Azure Active Directory

A autenticação baseada em certificado permite que você seja autenticado pelo Azure Active Directory com um certificado de cliente em um dispositivo Windows, Android ou iOS ao conectar sua conta do Exchange Online com: 

- Aplicativos móveis do Office, como Microsoft Outlook e Microsoft Word   

- Clientes do EAS (Exchange ActiveSync) 

Configurar esse recurso elimina a necessidade de digitar uma combinação de nome de usuário e senha em determinados emails e aplicativos do Microsoft Office no seu dispositivo móvel. 

Este tópico:

- Este tópico mostra como configurar e utilizar a autenticação baseada em certificado para usuários de locatários nos planos Office 365 corporativo, Business, educacional e governamental. Esse recurso está disponível na visualização em planos do Office 365 para China, defesa governamental e governo federal. 

- Pressupõe que você já tem uma [infraestrutura de chave pública (PKI)](https://go.microsoft.com/fwlink/?linkid=841737) e [do AD FS](connect/active-directory-aadconnectfed-whatis.md) configurada.    


## <a name="requirements"></a>Requisitos

Para configurar a autenticação baseada em certificado, as seguintes afirmativas devem ser verdadeiras:  

- A CBA (Autenticação Baseada em Certificado) tem suporte apenas em ambientes Federados para aplicativos de navegador ou clientes nativos que usam autenticação moderna (ADAL). A única exceção é o EAS (Exchange Active Sync) para EXO, que pode ser usado para contas federadas e gerenciadas. 

- A autoridade de certificação raiz e qualquer autoridade de certificação intermediária devem ser configuradas no Azure Active Directory.  

- Cada autoridade de certificação deve ter uma CRL (Lista de Certificados Revogados) que pode ser referenciada por meio de uma URL para a Internet.  

- Você deve ter pelo menos uma autoridade de certificação configurada no Azure Active Directory. Você pode encontrar etapas relacionadas na seção [Configuração de autoridades de certificação](#step-2-configure-the-certificate-authorities).  

- Para clientes do Exchange ActiveSync, o certificado de cliente deve ter o email roteável do usuário no Exchange Online, seja no valor Nome principal ou Nome RFC822 do campo Nome Alternativo da Entidade. O Azure Active Directory mapeia o valor de RFC822 para o atributo Endereço de Proxy no diretório.  

- O dispositivo do cliente deve ter acesso a pelo menos uma autoridade de certificação que emite certificados de cliente.  

- Um certificado de cliente para autenticação de cliente deve ter sido emitido para seu cliente.  




## <a name="step-1-select-your-device-platform"></a>Etapa 1: selecione a plataforma do dispositivo

Para a plataforma do dispositivo sob sua responsabilidade, a primeira etapa é examinar o seguinte:

- O suporte a aplicativos móveis do Office 
- Os requisitos específicos de implementação  

As informações relacionadas existentes para as seguintes plataformas de dispositivos:

- [Android](active-directory-certificate-based-authentication-android.md)
- [iOS](active-directory-certificate-based-authentication-ios.md)


## <a name="step-2-configure-the-certificate-authorities"></a>Etapa 2: Configuração de autoridades de certificação 

Para configurar as autoridades de certificação no Azure Active Directory, carregue o seguinte para cada autoridade de certificação: 

* A parte pública do certificado, no formato *.cer* 
* As URLs para a Internet, em que as CRLs (Listas de Certificados Revogados) residem

Veja abaixo o esquema de uma autoridade de certificação: 

    class TrustedCAsForPasswordlessAuth 
    { 
       CertificateAuthorityInformation[] certificateAuthorities;    
    } 

    class CertificateAuthorityInformation 

    { 
        CertAuthorityType authorityType; 
        X509Certificate trustedCertificate; 
        string crlDistributionPoint; 
        string deltaCrlDistributionPoint; 
        string trustedIssuer; 
        string trustedIssuerSKI; 
    }                

    enum CertAuthorityType 
    { 
        RootAuthority = 0, 
        IntermediateAuthority = 1 
    } 

Para a configuração, você pode usar o [Azure Active Directory PowerShell versão 2](/powershell/azure/install-adv2?view=azureadps-2.0):  

1. Inicie o Windows PowerShell com os privilégios de administrador. 
2. Instale o módulo do Azure AD. Você precisa instalar a versão [2.0.0.33](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33) ou superior.  
   
        Install-Module -Name AzureAD –RequiredVersion 2.0.0.33 

Como essa é a primeira etapa de configuração, você precisa estabelecer uma conexão com seu locatário. Como existe uma conexão com o seu locatário, você pode revisar, adicionar, excluir e modificar autoridades de certificação confiáveis que são definidas em seu diretório. 

### <a name="connect"></a>Connect

Para estabelecer uma conexão com seu locatário, use o cmdlet [Connect-AzureAD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0):

    Connect-AzureAD 


### <a name="retrieve"></a>Recuperar 

Para recuperar as autoridades de certificação confiáveis que são definidas em seu diretório, use o cmdlet [Get-AzureADTrustedCertificateAuthority](/powershell/module/azuread/get-azureadtrustedcertificateauthority?view=azureadps-2.0). 

    Get-AzureADTrustedCertificateAuthority 
 

### <a name="add"></a>Adicionar

Para criar uma autoridade de certificação confiável, use o cmdlet [New-AzureADTrustedCertificateAuthority](/powershell/module/azuread/new-azureadtrustedcertificateauthority?view=azureadps-2.0) e defina o atributo **crlDistributionPoint** para um valor correto: 
   
    $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]" 
    $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation 
    $new_ca.AuthorityType=0 
    $new_ca.TrustedCertificate=$cert 
    $new_ca.crlDistributionPoint=”<CRL Distribution URL>”
    New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca 


### <a name="remove"></a>Remover

Para remover uma autoridade de certificação confiável, use o cmdlet [Remove-AzureADTrustedCertificateAuthority](/powershell/module/azuread/remove-azureadtrustedcertificateauthority?view=azureadps-2.0):
   
    $c=Get-AzureADTrustedCertificateAuthority 
    Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2] 


### <a name="modfiy"></a>Modificar

Para modificar uma autoridade de certificação confiável, use o cmdlet [Set-AzureADTrustedCertificateAuthority](/powershell/module/azuread/set-azureadtrustedcertificateauthority?view=azureadps-2.0):

    $c=Get-AzureADTrustedCertificateAuthority 
    $c[0].AuthorityType=1 
    Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0] 


## <a name="step-3-configure-revocation"></a>Etapa 3: configurar a revogação

Para revogar um certificado do cliente, o Azure Active Directory busca a CRL (Lista de Certificados Revogados) nas URLs carregadas como parte das informações da autoridade de certificado e a armazena em cache. O carimbo de data/hora da última publicação (propriedade**Effective Date** ) na CRL é usado para garantir que a CRL continua sendo válida. A CRL é referenciada periodicamente para revogar o acesso a certificados que fazem parte da lista.

Se uma revogação mais imediata for necessária (por exemplo, se um usuário perder um dispositivo), o token de autorização do usuário poderá ser invalidado. Para invalidar o token de autorização, defina o campo **StsRefreshTokenValidFrom** para esse usuário específico usando o Windows PowerShell. Você deve atualizar o campo **StsRefreshTokenValidFrom** para cada usuário do qual deseja revogar o acesso.

Para garantir que a revogação persista, é preciso definir **Effective Date** da CRL para uma data posterior ao valor definido por **StsRefreshTokenValidFrom** e garantir que o certificado em questão esteja na CRL.

As etapas a seguir descrevem o processo para atualizar e invalidar o token de autorização pela definição do campo **StsRefreshTokenValidFrom** . 

**Para configurar a revogação:** 

1. Conecte-se com credenciais de administrador ao serviço MSOL: 
   
        $msolcred = get-credential 
        connect-msolservice -credential $msolcred 

2. Recupere o valor StsRefreshTokensValidFrom atual para um usuário: 
   
        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com` 
        $user.StsRefreshTokensValidFrom 

3. Configure um novo valor StsRefreshTokensValidFrom para o usuário igual ao carimbo de data/hora atual: 
   
        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")

A data que você define deve estar no futuro. Se a data não estiver no futuro, a propriedade **StsRefreshTokensValidFrom** não será definida. Se a data estiver no futuro, **StsRefreshTokensValidFrom** será definida para a hora atual (não a data indicada pelo comando Set-MsolUser). 


## <a name="step-4-test-your-configuration"></a>Etapa 4: testar a sua configuração

### <a name="testing-your-certificate"></a>Teste do seu certificado

Como primeiro teste de configuração, entre no [Outlook Web Access](https://outlook.office365.com) ou no [SharePoint Online](https://microsoft.sharepoint.com) usando seu **navegador no dispositivo**.

Se a entrada for bem-sucedida, você saberá que:

- O certificado de usuário foi provisionado para o seu dispositivo de teste
- O AD FS está configurado corretamente  


### <a name="testing-office-mobile-applications"></a>Testando aplicativos móveis do Office

**Para testar a autenticação baseada em certificado no seu aplicativo móvel do Office:** 

1. No seu dispositivo de teste, instale um aplicativo móvel do Office (por exemplo, OneDrive).
3. Inicie o aplicativo. 
4. Insira seu nome de usuário e escolha o certificado de usuário que deseja usar. 

Você deve se conectar com êxito. 

### <a name="testing-exchange-activesync-client-applications"></a>Testando aplicativos cliente do Exchange ActiveSync

Para acessar o Exchange ActiveSync (EAS) por meio da autenticação baseada em certificado, um perfil do EAS que contém o certificado de cliente deve estar disponível para o aplicativo. 

O perfil do EAS deve conter as seguintes informações:

- O certificado do usuário a ser usado para autenticação 

- O ponto de extremidade do EAS (por exemplo, outlook.office365.com)

Um perfil do EAS pode ser configurado e colocado no dispositivo por meio da utilização de um gerenciamento de dispositivo móvel (MDM), como o Intune, ou colocando o certificado manualmente no perfil do EAS no dispositivo.  

### <a name="testing-eas-client-applications-on-android"></a>Testando os aplicativos cliente do EAS no Android

**Para testar a autenticação do certificado:**  

1. Configure um perfil do EAS no aplicativo que atenda aos requisitos acima.  
2. Abra o aplicativo e verifique a sincronização de email. 

