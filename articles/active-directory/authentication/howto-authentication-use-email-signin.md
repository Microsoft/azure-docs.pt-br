---
title: Entrar com o email como uma ID de logon alternativa para o Azure Active Directory
description: Saiba como configurar e habilitar os usuários para entrarem no Azure Active Directory usando o endereço de email como uma ID de logon alternativa (versão preliminar)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 10/01/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: calui
ms.openlocfilehash: 4e39d7f15e3ca3c6e241c767a5f881d7170c6379
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99255960"
---
# <a name="sign-in-to-azure-active-directory-using-email-as-an-alternate-login-id-preview"></a>Entrar no Azure Active Directory usando o email como uma ID de logon alternativa (versão prévia)

> [!NOTE]
> A entrada no Azure Active Directory com o email como uma ID de logon alternativa é uma versão prévia do recurso do Azure Active Directory. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Muitas organizações desejam permitir que os usuários entrem no Azure Active Directory (Azure AD) usando as mesmas credenciais que o seu ambiente de diretório local. Com essa abordagem, conhecida como autenticação híbrida, os usuários só precisam se lembrar de um conjunto de credenciais.

Algumas organizações não migraram para autenticação híbrida pelos seguintes motivos:

* Por padrão, o UPN (nome principal de usuário) do Azure AD é definido com o mesmo UPN que o diretório local.
* Alterar o UPN do Azure AD cria uma correspondência incorreta entre ambientes locais e do Azure AD que podem causar problemas com determinados aplicativos e serviços.
* Devido a motivos de conformidade ou de negócios, a organização não deseja usar o UPN local para entrar no Azure AD.

Para ajudar com a mudança para a autenticação híbrida, agora você pode configurar o Azure AD para permitir que os usuários entrem com um email em seu domínio verificado como uma ID de logon alternativa. Por exemplo, se *Contoso* for rebatizado como *Fabrikam*, em vez de continuar a entrar com o UPN herdado `balas@contoso.com`, será possível usar o email como uma ID de logon alternativa. Para acessar um aplicativo ou serviços, os usuários entrarão no Azure AD usando seus emails atribuídos, como `balas@fabrikam.com` .

Este artigo mostra como habilitar e usar email como uma ID de logon alternativa. Esse recurso está disponível na edição do Azure AD Gratuito e superior.

> [!NOTE]
> Esse recurso destina-se somente a usuários autenticados na nuvem do Azure AD.

> [!NOTE]
> Atualmente, esse recurso não tem suporte em dispositivos Windows 10 ingressados no Azure AD para locatários com autenticação na nuvem. Esse recurso não é aplicável a dispositivos ingressados no Azure AD híbrido.

## <a name="overview-of-azure-ad-sign-in-approaches"></a>Visão geral das abordagens de entrada no Azure Active Directory

Para entrar no Azure AD, os usuários inserem um nome que identifica exclusivamente sua conta. Historicamente, você só podia usar o UPN do Azure AD como o nome de entrada.

Para organizações em que o UPN local é o email de entrada preferencial do usuário, essa abordagem foi excelente. Essas organizações definiram o UPN do Azure AD com o mesmo valor exato que o UPN local, e os usuários teriam uma experiência de entrada consistente.

No entanto, em algumas organizações, o UPN local não é usado como um nome de entrada. Nos ambientes locais, você configuraria o AD DS local para permitir a entrada com uma ID de logon alternativa. Definir o UPN do Azure AD com o mesmo valor que o UPN local não é uma opção, pois o Azure AD exigiria que os usuários entrassem com esse valor.

A solução alternativa típica para esse problema era definir o UPN do Azure AD para o endereço de email no qual o usuário espera entrar. Essa abordagem funciona, embora resulte em UPNs diferentes entre o AD local e o Azure AD, e essa configuração não é compatível com todas as cargas de trabalho de Microsoft 365.

Uma abordagem diferente é sincronizar o Azure AD e os UPNs locais com o mesmo valor e, em seguida, configurar o Azure AD para permitir que os usuários entrem no Azure AD com um email verificado. Para fornecer essa capacidade, você define um ou mais endereços de email no atributo *proxyAddresses* do usuário no diretório local. Os *proxyAddresses* são então sincronizados com o Azure ad automaticamente usando Azure ad Connect.

## <a name="preview-limitations"></a>Limitações de visualização

Entre no Azure AD com o email como uma ID de logon alternativa está disponível na edição do Azure AD Gratuito e superior.

No estado de visualização atual, as seguintes limitações se aplicam quando um usuário entra com um email não UPN como uma ID de logon alternativa:

* Os usuários podem ver seu UPN, mesmo quando conectado com seus emails não UPN. O seguinte comportamento de exemplo pode ser visto:
    * É solicitado que o usuário entre com o UPN quando direcionado para a entrada do Azure AD com o `login_hint=<non-UPN email>` .
    * Quando um usuário entra com um email não UPN e insere uma senha incorreta, a página *"inserir sua senha"* é alterada para exibir o UPN.
    * Em alguns sites e aplicativos da Microsoft, como [https://portal.azure.com](https://portal.azure.com) e Microsoft Office, o controle de **gerente de conta** normalmente exibido no canto superior direito pode exibir o UPN do usuário em vez do email não UPN usado para entrar.

* Alguns fluxos atualmente não são compatíveis com o email não UPN, como o seguinte:
    * Atualmente, a proteção de identidade não corresponde às IDs de logon alternativas de email com a detecção de risco de *credenciais vazadas* . Essa detecção de risco usa o UPN para corresponder as credenciais que foram vazadas. Para obter mais informações, consulte [Azure ad Identity Protection detecção e correção de riscos][identity-protection].
    * Os convites B2B enviados a um email de ID de logon alternativo não são totalmente suportados. Depois de aceitar um convite enviado a um email como uma ID de logon alternativa, entrar com o email alternativo pode não funcionar para o usuário no ponto de extremidade locatário.

## <a name="synchronize-sign-in-email-addresses-to-azure-ad"></a>Sincronizar endereços de email para entrada no Azure Active Directory

A autenticação tradicional do Active Directory Domain Services (AD DS) ou dos Serviços de Federação do Active Directory (AD FS) ocorre diretamente em sua rede e é tratada pela sua infraestrutura de AD DS. Com a autenticação híbrida, os usuários podem entrar diretamente no Azure Active Directory.

Para permitir essa abordagem de autenticação híbrida, sincronize seu ambiente de AD DS local para o Azure Active Directory usando o [Azure AD Connect][azure-ad-connect] e configure-o para usar a PHS (sincronização de hash de senha) ou a PTA (autenticação de passagem).

Nas duas opções de configuração, o usuário envia seu nome de usuário e senha para o Azure Active Directory, que valida as credenciais e emite um tíquete. Quando os usuários entram no Azure Active Directory, ele acaba com a necessidade de sua organização hospedar e gerenciar uma infraestrutura de AD FS.

![Diagrama da Identidade híbrida do Azure Active Directory com sincronização de hash de senha](media/howto-authentication-use-email-signin/hybrid-password-hash-sync.png)

![Diagrama da Identidade híbrida do Azure Active Directory com autenticação de passagem](media/howto-authentication-use-email-signin/hybrid-pass-through-authentication.png)

Um dos atributos de usuário que é sincronizado automaticamente pelo Azure Active Directory Connect é *ProxyAddresses*. Se os usuários tiverem um endereço de email definido no ambiente de AD DS local como parte do atributo *ProxyAddresses*, ele será sincronizado automaticamente com o Azure Active Directory. Esse endereço de email pode ser usado diretamente no processo de entrada com credenciais do Azure Active Directory como uma ID de logon alternativa.

> [!IMPORTANT]
> Somente os emails nos domínios verificados para o locatário são sincronizados com o Azure Active Directory. Cada locatário do Azure Active Directory tem um ou mais domínios verificados, dos quais você tem propriedade comprovada e estão associados exclusivamente a seu locatário.
>
> Para saber mais, veja [Adicionar e verificar um nome de domínio personalizado no Azure Active Directory][verify-domain].

Para saber mais, veja [Escolher o método de autenticação certo para sua solução de identidade híbrida do Azure Active Directory | Microsoft Docs][hybrid-auth-methods].

## <a name="enable-user-sign-in-with-an-email-address"></a>Habilitar a entrada do usuário com um endereço de email

Depois que os usuários com o atributo *ProxyAddresses* aplicado forem sincronizados com o Azure Active Directory usando Azure Active Directory Connect, será preciso habilitar o recurso para que os usuários entrem com o email como uma ID de logon alternativa para seu locatário. Esse recurso informa os servidores de logon do Azure Active Directory para não apenas verificar o nome de entrada em relação aos valores UPN, mas também em relação aos valores de *ProxyAddresses* para o endereço de email.

Na versão preliminar, só é possível habilitar a entrada com o email como um recurso de ID de logon alternativo usando o PowerShell. Você precisa de permissões de *administrador de locatários* para concluir as seguintes etapas:

1. Abra uma sessão do PowerShell como administrador e instale o módulo *AzureADPreview* usando o cmdlet [Install-Module][Install-Module]:

    ```powershell
    Install-Module AzureADPreview
    ```

    Se solicitado, escolha **Y** para instalar o NuGet ou instalar a partir de um repositório não confiável.

1. Entre no seu locatário do Azure Active Directory como um *administrador de locatários* usando o cmdlet do [Connect-AzureAD][Connect-AzureAD]:

    ```powershell
    Connect-AzureAD
    ```

    O comando retorna informações sobre sua conta, seu ambiente e sua ID de locatário.

1. Verifique se a política *HomeRealmDiscoveryPolicy* já existe em seu locatário usando o cmdlet [Get-AzureADPolicy][Get-AzureADPolicy] da seguinte maneira:

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```

1. Se não houver políticas configuradas no momento, o comando não retornará nada. Se uma política for retornada, ignore essa etapa e vá para a próxima etapa para atualizar uma política atual.

    Para adicionar a política *HomeRealmDiscoveryPolicy* ao locatário, use o cmdlet [New-AzureADPolicy][New-AzureADPolicy] e defina o atributo *AlternateIdLogin* como *"Enabled": true*, conforme mostrado no exemplo a seguir:

    ```powershell
    $AzureADPolicyDefinition = @(
      @{
         "HomeRealmDiscoveryPolicy" = @{
            "AlternateIdLogin" = @{
               "Enabled" = $true
            }
         }
      } | ConvertTo-JSON -Compress
    )
    $AzureADPolicyParameters = @{
      Definition            = $AzureADPolicyDefinition
      DisplayName           = "BasicAutoAccelerationPolicy"
      IsOrganizationDefault = $true
      Type                  = "HomeRealmDiscoveryPolicy"
    }
    New-AzureADPolicy @AzureADPolicyParameters
    ```

    Quando a política tiver sido criada com êxito, o comando retornará a ID da política, conforme mostrado na seguinte saída de exemplo:

    ```powershell
    Id                                   DisplayName                 Type                     IsOrganizationDefault
    --                                   -----------                 ----                     ---------------------
    5de3afbe-4b7a-4b33-86b0-7bbe308db7f7 BasicAutoAccelerationPolicy HomeRealmDiscoveryPolicy True
    ```

1. Se já houver uma política configurada, verifique se o atributo  *AlternateIdLogin*  está habilitado, conforme mostrado no seguinte exemplo de saída de política:

    ```powershell
    Id : 5de3afbe-4b7a-4b33-86b0-7bbe308db7f7
    OdataType :
    AlternativeIdentifier :
    Definition : {{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}}
    DisplayName : BasicAutoAccelerationPolicy
    IsOrganizationDefault : True
    KeyCredentials : {}
    Type : HomeRealmDiscoveryPolicy
    ```

    Se a política existir, mas o atributo *AlternateIdLogin* não estiver presente ou habilitado, ou se outros atributos existirem na política que você quer preservar, atualize a política existente usando o cmdlet [Set-AzureADPolicy][Set-AzureADPolicy].

    > [!IMPORTANT]
    > Ao atualizar a política, inclua as configurações antigas e o novo atributo  *AlternateIdLogin*.

    O exemplo a seguir adiciona o atributo  *AlternateIdLogin* e preserva o atributo  *AllowCloudPasswordValidation*, que pode já ter sido definido:

    ```powershell
    $AzureADPolicyDefinition = @(
      @{
         "HomeRealmDiscoveryPolicy" = @{
            "AllowCloudPasswordValidation" = $true
            "AlternateIdLogin" = @{
               "Enabled" = $true
            }
         }
      } | ConvertTo-JSON -Compress
    )
    $AzureADPolicyParameters = @{
      ID                    = "b581c39c-8fe3-4bb5-b53d-ea3de05abb4b"
      Definition            = $AzureADPolicyDefinition
      DisplayName           = "BasicAutoAccelerationPolicy"
      IsOrganizationDefault = $true
      Type                  = "HomeRealmDiscoveryPolicy"
    }
    
    Set-AzureADPolicy @AzureADPolicyParameters
    ```

    Confirme se a política atualizada mostra as alterações e se o atributo *AlternateIdLogin* agora está habilitado:

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```

Com a política aplicada, pode levar até uma hora para se propagar e para que os usuários possam entrar usando sua ID de logon alternativa.

## <a name="test-user-sign-in-with-email"></a>Testar entrada do usuário com email

Para testar se os usuários conseguem entrar com o email, navegue até [https://myprofile.microsoft.com][my-profile] e entre com uma conta de usuário a partir do endereço de email, como `balas@fabrikam.com`, não a partir do UPN, como `balas@contoso.com`. A experiência de entrada deve ser parecida com a de um evento de entrada baseado em UPN.

## <a name="enable-staged-rollout-to-test-user-sign-in-with-an-email-address"></a>Habilitar a distribuição em etapas para testar a entrada do usuário com um endereço de email  

A [distribuição em etapas][staged-rollout] permite que os administradores de inquilinos habilitem recursos para grupos específicos. É recomendável que os administradores de locatários usem a distribuição em etapas para testar a entrada do usuário com um endereço de email. Quando os administradores estão prontos para implantar esse recurso em todo o locatário, eles devem usar uma política de descoberta de realm inicial.  


Você precisa de permissões de *administrador de locatários* para concluir as seguintes etapas:

1. Abra uma sessão do PowerShell como administrador e, em seguida, instale o módulo *AzureADPreview* usando o cmdlet [install-Module][Install-Module] :

    ```powershell
    Install-Module AzureADPreview
    ```

    Se solicitado, escolha **Y** para instalar o NuGet ou instalar a partir de um repositório não confiável.

2. Entre no seu locatário do Azure Active Directory como um *administrador de locatários* usando o cmdlet do [Connect-AzureAD][Connect-AzureAD]:

    ```powershell
    Connect-AzureAD
    ```

    O comando retorna informações sobre sua conta, seu ambiente e sua ID de locatário.

3. Liste todas as políticas de distribuição em etapas existentes usando o seguinte cmdlet:
   
   ```powershell
   Get-AzureADMSFeatureRolloutPolicy
   ``` 

4. Se não houver políticas de distribuição em etapas existentes para esse recurso, crie uma nova política de distribuição em etapas e anote a ID da política:

   ```powershell
   $AzureADMSFeatureRolloutPolicy = @{
      Feature    = "EmailAsAlternateId"
      DisplayName = "EmailAsAlternateId Rollout Policy"
      IsEnabled   = $true
   }
   New-AzureADMSFeatureRolloutPolicy @AzureADMSFeatureRolloutPolicy
   ```

5. Localize a ID do diretórioobject para o grupo a ser adicionado à política de distribuição em etapas. Observe o valor retornado para o parâmetro *ID* , pois ele será usado na próxima etapa.
   
   ```powershell
   Get-AzureADMSGroup -SearchString "Name of group to be added to the staged rollout policy"
   ```

6. Adicione o grupo à política de distribuição em etapas, conforme mostrado no exemplo a seguir. Substitua o valor no parâmetro *-ID* pelo valor retornado para a ID de política na etapa 4 e substitua o valor no parâmetro *-RefObjectId* pela *ID* anotada na etapa 5. Pode levar até 1 hora para que os usuários no grupo possam usar seus endereços de proxy para entrar.

   ```powershell
   Add-AzureADMSFeatureRolloutPolicyDirectoryObject -Id "ROLLOUT_POLICY_ID" -RefObjectId "GROUP_OBJECT_ID"
   ```
   
Para novos membros adicionados ao grupo, pode levar até 24 horas para que eles possam usar seus endereços de proxy para entrar.

### <a name="removing-groups"></a>Removendo grupos

Para remover um grupo de uma política de distribuição em etapas, execute o seguinte comando:

```powershell
Remove-AzureADMSFeatureRolloutPolicyDirectoryObject -Id "ROLLOUT_POLICY_ID" -ObjectId "GROUP_OBJECT_ID" 
```

### <a name="removing-policies"></a>Removendo políticas

Para remover uma política de distribuição em etapas, primeiro desabilite a política e remova-a do sistema:

```powershell
Set-AzureADMSFeatureRolloutPolicy -Id "ROLLOUT_POLICY_ID" -IsEnabled $false 
Remove-AzureADMSFeatureRolloutPolicy -Id "ROLLOUT_POLICY_ID"
```

## <a name="troubleshoot"></a>Solucionar problemas

Se os usuários tiverem problemas com eventos de entrada usando o endereço de email, examine as seguintes etapas de solução de problemas:

1. Verifique se a conta de usuário tem seu endereço de email definido para o atributo *ProxyAddresses* no ambiente do AD DS local.
1. Verifique se o Azure Active Directory Connect está configurado e sincroniza com êxito as contas de usuário do ambiente de AD DS local no Azure Active Directory.
1. Confirme se a política *HomeRealmDiscoveryPolicy* do Azure Active Directory tem o atributo *AlternateIdLogin* definido como *"Enabled": true*:

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a identidade híbrida, como o Proxy do Aplicativo Azure AD ou o Azure Active Directory Domain Services, veja [Identidade híbrida do Azure Active Directory para acesso e gerenciamento de cargas de trabalho locais][hybrid-overview].

Para obter mais informações sobre operações de identidade híbrida, veja [Como a sincronização de hash de senha][phs-overview] ou a [autenticação de passagem][pta-overview] funcionam.

<!-- INTERNAL LINKS -->
[verify-domain]: ../fundamentals/add-custom-domain.md
[hybrid-auth-methods]: ../hybrid/choose-ad-authn.md
[azure-ad-connect]: ../hybrid/whatis-azure-ad-connect.md
[hybrid-overview]: ../hybrid/cloud-governed-management-for-on-premises.md
[phs-overview]: ../hybrid/how-to-connect-password-hash-synchronization.md
[pta-overview]: ../hybrid/how-to-connect-pta-how-it-works.md
[identity-protection]: ../identity-protection/overview-identity-protection.md#risk-detection-and-remediation

<!-- EXTERNAL LINKS -->
[Install-Module]: /powershell/module/powershellget/install-module
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
[Get-AzureADPolicy]: /powershell/module/azuread/get-azureadpolicy
[New-AzureADPolicy]: /powershell/module/azuread/new-azureadpolicy
[Set-AzureADPolicy]: /powershell/module/azuread/set-azureadpolicy
[staged-rollout]: /powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#staged-rollout
[my-profile]: https://myprofile.microsoft.com
