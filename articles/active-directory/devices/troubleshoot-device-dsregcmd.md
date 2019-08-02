---
title: Solucionando problemas de dispositivos usando o comando dsregcmd-Azure Active Directory
description: Usando a saída de dsregcmd para entender o estado dos dispositivos no Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 189e4f280e8aba28c4d1af449aa8a3428e303911
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68298406"
---
# <a name="troubleshooting-devices-using-the-dsregcmd-command"></a>Solucionando problemas de dispositivos usando o comando dsregcmd

O utilitário dsregcmd/status deve ser executado como uma conta de usuário de domínio.

## <a name="device-state"></a>Estado do dispositivo

Esta seção lista os parâmetros de estado de ingresso no dispositivo. A tabela a seguir lista os critérios para que o dispositivo esteja em vários Estados de junção.

| AzureAdJoined | EnterpriseJoined | DomainJoined | Estado do dispositivo |
| ---   | ---   | ---   | ---   |
| SIM | NÃO | NÃO | Ingressado no Azure AD |
| NÃO | NÃO | SIM | Ingressado no domínio |
| SIM | NÃO | SIM | Ingressado no AD híbrido |
| NÃO | SIM | SIM | DRS local Unido |

> [!NOTE]
> O estado do Workplace Join (Azure AD registrado) é exibido na seção "estado do usuário"

- **AzureAdJoined:** – defina como "Sim" se o dispositivo for ingressado no Azure AD. "Não" caso contrário.
- **EnterpriseJoined:** – defina como "Sim" se o dispositivo estiver ingressado em um DRS local. Um dispositivo não pode ser EnterpriseJoined e AzureAdJoined.
- **DomainJoined:** – defina como "Sim" se o dispositivo tiver ingressado em um domínio (AD).
- **Nome_do_domínio:** – defina como o nome do domínio se o dispositivo for ingressado em um domínio.

### <a name="sample-device-state-output"></a>Saída de estado do dispositivo de exemplo

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+
             AzureAdJoined : YES
          EnterpriseJoined : NO
              DomainJoined : YES
                DomainName : HYBRIDADFS
+----------------------------------------------------------------------+
```

## <a name="device-details"></a>Detalhes do Dispositivo

Exibido somente quando o dispositivo for ingressado no Azure ad ou ingressado no Azure AD híbrido (não o Azure AD registrado). Esta seção lista os detalhes de identificação de dispositivo armazenados na nuvem.

- **DeviceID:** -ID exclusiva do dispositivo no locatário do Azure AD
- **Impressão digital:** -impressão digital do certificado do dispositivo 
- **DeviceCertificateValidity:** -validade do certificado do dispositivo
- **Keycontainerid:** -ContainerId da chave privada do dispositivo associada ao certificado do dispositivo
- **:** -(Hardware/software) usado para armazenar a chave privada do dispositivo.
- **TpmProtected:** -"Sim" se a chave privada do dispositivo estiver armazenada em um TPM de hardware.

### <a name="sample-device-details-output"></a>Saída de detalhes do dispositivo de exemplo

```
+----------------------------------------------------------------------+
| Device Details                                                       |
+----------------------------------------------------------------------+

                  DeviceId : e92325d0-xxxx-xxxx-xxxx-94ae875dxxxx
                Thumbprint : D293213EF327483560EED8410CAE36BB67208179
 DeviceCertificateValidity : [ 2019-01-11 21:02:50.000 UTC -- 2029-01-11 21:32:50.000 UTC ]
            KeyContainerId : 13e68a58-xxxx-xxxx-xxxx-a20a2411xxxx
               KeyProvider : Microsoft Software Key Storage Provider
              TpmProtected : NO
+----------------------------------------------------------------------+
```

## <a name="tenant-details"></a>Detalhes do locatário

Exibido somente quando o dispositivo for ingressado no Azure ad ou ingressado no Azure AD híbrido (não o Azure AD registrado). Esta seção lista os detalhes comuns do locatário quando um dispositivo é ingressado no Azure AD.

> [!NOTE]
> Mesmo que você veja URLs de MDM, isso não significa que o dispositivo é gerenciado por um MDM. As informações serão exibidas se o locatário tiver a configuração de MDM para registro automático, mesmo que o próprio dispositivo não seja gerenciado. 

### <a name="sample-tenant-details-output"></a>Saída de detalhes de locatário de exemplo

```
+----------------------------------------------------------------------+
| Tenant Details                                                       |
+----------------------------------------------------------------------+

                TenantName : HybridADFS
                  TenantId : 96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
                       Idp : login.windows.net
               AuthCodeUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/authorize
            AccessTokenUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/token
                    MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
                 MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
          MdmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance
               SettingsUrl : eyJVxxxxIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyxxxx==
            JoinSrvVersion : 1.0
                JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/
                 JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net
             KeySrvVersion : 1.0
                 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/
                  KeySrvId : urn:ms-drs:enterpriseregistration.windows.net
        WebAuthNSrvVersion : 1.0
            WebAuthNSrvUrl : https://enterpriseregistration.windows.net/webauthn/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
             WebAuthNSrvId : urn:ms-drs:enterpriseregistration.windows.net
    DeviceManagementSrvVer : 1.0
    DeviceManagementSrvUrl : https://enterpriseregistration.windows.net/manage/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
     DeviceManagementSrvId : urn:ms-drs:enterpriseregistration.windows.net
+----------------------------------------------------------------------+
```

## <a name="user-state"></a>Estado do usuário

Esta seção lista o status de vários atributos para o usuário atualmente conectado ao dispositivo.

> [!NOTE]
> O comando deve ser executado em um contexto de usuário para recuperar o status válido.

- **NgcSet:** – defina como "Sim" se uma chave do Windows Hello for definida para o usuário conectado no momento.
- **NgcKeyId:** -ID da chave do Windows Hello se uma estiver definida para o usuário conectado no momento.
- **Redefinição:** -indica se a chave do Windows Hello pode ser redefinida pelo usuário. 
- **Valores possíveis:** -DestructiveOnly, NonDestructiveOnly, DestructiveAndNonDestructive ou Unknown If Error. 
- **WorkplaceJoined:** – defina como "Sim" se as contas registradas do Azure ad tiverem sido adicionadas ao dispositivo no contexto Ntuser atual.
- **WamDefaultSet:** – definido como "Sim" se uma conta Webpadrão WAM for criada para o usuário conectado. Esse campo poderá exibir um erro se dsreg/status for executado no contexto de administrador. 
- **WamDefaultAuthority:** -definido como "organizações" para o Azure AD.
- **WamDefaultId:** -sempre "https://login.microsoft.com" para o Azure AD.
- **WamDefaultGUID:** -o GUID do provedor de WAM (Azure AD/conta Microsoft) para a conta da webdefault do WAM. 

### <a name="sample-user-state-output"></a>Saída de estado do usuário de exemplo

```
+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

                    NgcSet : YES
                  NgcKeyId : {FA0DB076-A5D7-4844-82D8-50A2FB42EC7B}
                  CanReset : DestructiveAndNonDestructive
           WorkplaceJoined : NO
             WamDefaultSet : YES
       WamDefaultAuthority : organizations
              WamDefaultId : https://login.microsoft.com
            WamDefaultGUID : { B16898C6-A148-4967-9171-64D755DA8520 } (AzureAd)

+----------------------------------------------------------------------+
```

## <a name="sso-state"></a>Estado do SSO

Esta seção pode ser ignorada para dispositivos registrados no Azure AD.

> [!NOTE]
> O comando deve ser executado em um contexto de usuário para recuperar o status válido para esse usuário.

- **AzureAdPrt:** -definido como "Sim" se um PRT estiver presente no dispositivo para o usuário conectado.
- **AzureAdPrtUpdateTime:** -defina como a hora em UTC quando o PRT foi atualizado pela última vez.
- **AzureAdPrtExpiryTime:** -defina para a hora em UTC em que o PRT irá expirar se não for renovado.
- **AzureAdPrtAuthority:** -URL de autoridade do Azure AD
- **EnterprisePrt:** – defina como "Sim" se o dispositivo tiver o PRT do ADFS local. Para dispositivos ingressados no Azure AD híbrido, o dispositivo poderia ter o PRT do Azure AD e do AD local simultaneamente. Os dispositivos ingressados no local terão apenas um PRT corporativo.
- **EnterprisePrtUpdateTime:** -defina como a hora em UTC quando o PRT empresarial foi atualizado pela última vez.
- **EnterprisePrtExpiryTime:** -defina para a hora em UTC em que o PRT irá expirar se não for renovado.
- **EnterprisePrtAuthority:** -URL da autoridade de ADFS

### <a name="sample-sso-state-output"></a>Saída de estado de SSO de exemplo

```
+----------------------------------------------------------------------+
| SSO State                                                            |
+----------------------------------------------------------------------+

                AzureAdPrt : YES
      AzureAdPrtUpdateTime : 2019-01-24 19:15:26.000 UTC
      AzureAdPrtExpiryTime : 2019-02-07 19:15:26.000 UTC
       AzureAdPrtAuthority : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
             EnterprisePrt : YES
   EnterprisePrtUpdateTime : 2019-01-24 19:15:33.000 UTC
   EnterprisePrtExpiryTime : 2019-02-07 19:15:33.000 UTC
    EnterprisePrtAuthority : https://fs.hybridadfs.nttest.microsoft.com:443/adfs

+----------------------------------------------------------------------+
```

## <a name="diagnostic-data"></a>Dados de diagnóstico

### <a name="pre-join-diagnostics"></a>Diagnóstico de pré-junção

Esta seção será exibida somente se o dispositivo estiver ingressado no domínio e não puder ingressar no Azure AD híbrido.

Esta seção executa vários testes para ajudar a diagnosticar falhas de junção. Esta seção também inclui os detalhes do (?) anterior. Essas informações incluem a fase de erro, o código de erro, a ID de solicitação do servidor, o status HTTP de resposta do servidor e a mensagem de erro de resposta do servidor.

- **Contexto do usuário:** -o contexto no qual o diagnóstico é executado. Valores possíveis: SISTEMA, usuário não elevado, usuário elevado. 

   > [!NOTE]
   > Como a junção real é executada no contexto do sistema, a execução do diagnóstico no contexto do sistema é mais próxima do cenário de junção real. Para executar o diagnóstico no contexto do sistema, o comando dsregcmd/status deve ser executado em um prompt de comandos com privilégios elevados.

- **Hora do cliente:** -a hora do sistema em UTC.
- **Teste de conectividade do AD:** -o teste executa um teste de conectividade para o controlador de domínio. O erro neste teste provavelmente resultará em erros de junção na fase de verificação prévia.
- **Teste de configuração do AD:** – o Test lê e verifica se o objeto SCP está configurado corretamente na floresta do AD local. Os erros nesse teste provavelmente resultarão em erros de junção na fase de descoberta com o código de erro 0x801c001d.
- **Teste de descoberta do DRS:** -Test Obtém os pontos de extremidade do DRS do ponto de extremidades dos metadados de descoberta e executa uma solicitação de realm do usuário. Os erros nesse teste provavelmente resultarão em erros de junção na fase de descoberta.
- **Teste de conectividade do DRS:** -Test executa o teste de conectividade básica para o ponto de extremidade do Drs.
- **Teste de aquisição de token:** -Test tenta obter um token de autenticação do Azure ad se o locatário do usuário é federado. Os erros nesse teste provavelmente resultarão em erros de junção na fase de autenticação. Se a autenticação falhar, a junção de sincronização será tentada como fallback, a menos que fallback seja explicitamente desabilitado com uma chave do registro.
- **Fallback para sincronização-junção:** -defina como "habilitado" se a chave do registro, para evitar o fallback para sincronizar a junção com falhas de autenticação, não estiver presente. Essa opção está disponível no Windows 10 1803 e posterior.
- **Registro anterior:** -hora em que a tentativa de junção anterior ocorreu. Somente tentativas de junção com falha são registradas.
- **Fase de erro:** -o estágio da junção em que foi anulado. Os valores possíveis são pré-verificação, descoberta, autenticação, junção.
- **ErrorCode do cliente:** -código de erro do cliente RETORNADO (HRESULT).
- **Servidor ErrorCode:** -código de erro do servidor se uma solicitação foi enviada ao servidor e o servidor respondeu de volta com um código de erro. 
- **Mensagem do servidor:** -mensagem do servidor retornada junto com o código de erro.
- **Status de https:** -status http retornado pelo servidor.
- **ID da solicitação:** -o cliente requestId foi enviado ao servidor. Útil para correlacionar com logs do lado do servidor.

### <a name="sample-pre-join-diagnostics-output"></a>Exemplo de saída de diagnóstico de pré-junção

O exemplo a seguir mostra a falha do teste de diagnóstico com um erro de descoberta.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:25:31.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x801c000c]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:23:30.000 UTC
               Error Phase : discover
          Client ErrorCode : 0x801c0021

+----------------------------------------------------------------------+
```

O exemplo a seguir mostra que os testes de diagnóstico estão passando, mas a tentativa de registro falhou com um erro de diretório, que é esperado para a junção de sincronização. Depois que o trabalho de sincronização do Azure AD Connect for concluído, o dispositivo poderá ingressar.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:16:50.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : PASS
     DRS Connectivity Test : PASS
    Token acquisition Test : PASS
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-7ac4-4714-88a1-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e

+----------------------------------------------------------------------+
```

### <a name="post-join-diagnostics"></a>Diagnóstico pós-junção

Esta seção exibe a saída de verificações de sanidade executadas em um dispositivo ingressado na nuvem.

- **AadRecoveryEnabled:** -se "Sim", as chaves armazenadas no dispositivo não poderão ser usadas e o dispositivo será marcado para recuperação. A próxima entrada irá disparar o fluxo de recuperação e registrar novamente o dispositivo.
- **KeySignTest:** -se "aprovada", as chaves do dispositivo estão em boas condições. Se KeySignTest falhar, o dispositivo geralmente será marcado para recuperação. A próxima entrada irá disparar o fluxo de recuperação e registrar novamente o dispositivo. Para dispositivos ingressados no Azure AD híbrido, a recuperação é silenciosa. Embora o Azure AD ingressado ou o Azure AD seja registrado, os dispositivos solicitarão a autenticação do usuário para recuperar e registrar novamente o dispositivo, se necessário. **O KeySignTest requer privilégios elevados.**

#### <a name="sample-post-join-diagnostics-output"></a>Exemplo de saída de diagnóstico pós-junção

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

         AadRecoveryEnabled: NO
               KeySignTest : PASSED
+----------------------------------------------------------------------+
```

## <a name="ngc-prerequisite-check"></a>Verificação de pré-requisitos do NGC

Esta seção executa as verificações de perquisite para o provisionamento de uma chave do NGC. 

### <a name="sample-ngc-prerequisite-check-output"></a>Exemplo de saída de verificação de pré-requisitos de NGC

```
+----------------------------------------------------------------------+
| Ngc Prerequisite Check                                               |
+----------------------------------------------------------------------+

            IsDeviceJoined : YES
             IsUserAzureAD : YES
             PolicyEnabled : YES
          PostLogonEnabled : YES
            DeviceEligible : YES
        SessionIsNotRemote : YES
            CertEnrollment : enrollment authority
          AdfsRefreshToken : YES
             AdfsRaIsReady : YES
    LogonCertTemplateReady : YES ( StateReady )
              PreReqResult : WillProvision
+----------------------------------------------------------------------+
```

## <a name="next-steps"></a>Próximas etapas

Para perguntas, consulte as [Perguntas frequentes sobre o gerenciamento de dispositivos](faq.md)
