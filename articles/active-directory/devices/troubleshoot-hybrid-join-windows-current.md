---
title: Solução de problemas de dispositivos ingressados no Azure Active Directory híbrido-Azure Active Directory
description: Solução de problemas do Azure Active Directory híbrido ingressado em dispositivos do Windows 10 e do Windows Server 2016.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: c4b0b5bd5972e544c4254ee0f425e27cc8c465f0
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297581"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-devices"></a>Solução de problemas de dispositivos ingressados no Azure Active Directory híbrido 

O conteúdo deste artigo é aplicável a dispositivos que executam o Windows 10 ou o Windows Server 2016.

Para outros clientes do Windows, consulte o artigo [solução de problemas de Azure Active Directory híbridos associados a dispositivos de nível inferior](troubleshoot-hybrid-join-windows-legacy.md).

Este artigo pressupõe que você tenha [dispositivos configurados e ingressados no Azure Active Directory híbrido](hybrid-azuread-join-plan.md) para dar suporte aos seguintes cenários:

- Acesso condicional com base no dispositivo
- [Roaming corporativo de configurações](../active-directory-windows-enterprise-state-roaming-overview.md)
- [Configurar o Hello for Business](../active-directory-azureadjoin-passport-deployment.md)

Este documento fornece diretrizes de solução de problemas para resolver possíveis problemas. 

Para Windows 10 e Windows Server 2016, o ingresso do Azure Active Directory híbrido oferece suporte à atualização de 10 de novembro de 2015 e superior do Windows 10.

## <a name="troubleshoot-join-failures"></a>Solucionar problemas de falhas de junção

### <a name="step-1-retrieve-the-join-status"></a>Etapa 1: Recuperar o status de ingresso 

**Para recuperar o status do ingresso:**

1. Abra um prompt de comando como administrador
2. Digite `dsregcmd /status`

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+

    AzureAdJoined: YES
 EnterpriseJoined: NO
         DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7
       Thumbprint: B753A6679CE720451921302CA873794D94C6204A
   KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9
      KeyProvider: Microsoft Platform Crypto Provider
     TpmProtected: YES
     KeySignTest: : MUST Run elevated to test.
              Idp: login.windows.net
         TenantId: 72b988bf-xxxx-xxxx-xxxx-2d7cd011xxxx
       TenantName: Contoso
      AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize
   AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token
           MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
        MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
  dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance
      SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ==
   JoinSrvVersion: 1.0
       JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/
        JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net
    KeySrvVersion: 1.0
        KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/
         KeySrvId: urn:ms-drs:enterpriseregistration.windows.net
     DomainJoined: YES
       DomainName: CONTOSO

+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

             NgcSet: YES
           NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
    WorkplaceJoined: NO
      WamDefaultSet: YES
WamDefaultAuthority: organizations
       WamDefaultId: https://login.microsoft.com
     WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)
         AzureAdPrt: YES
```

### <a name="step-2-evaluate-the-join-status"></a>Etapa 2: Avaliar o status do ingresso 

Examine os seguintes campos e garanta que eles tenham os valores esperados:

#### <a name="domainjoined--yes"></a>DomainJoined : SIM  

Esse campo indica se o dispositivo ingressou em um Active Directory local ou não. Se o valor for **NO**, o dispositivo não poderá executar um ingresso do Azure AD híbrido.  

#### <a name="workplacejoined--no"></a>WorkplaceJoined : NÃO  

Esse campo indica se o dispositivo está registrado no Azure AD como um dispositivo pessoal (marcado como *Ingressado no Espaço de Trabalho*). Esse valor deve ser **NO** para um computador ingressado no domínio, que também é ingressado no Azure AD híbrido. Se o valor for **YES**, uma conta corporativa ou de estudante terá sido adicionada antes da conclusão do ingresso do Azure AD híbrido. Nesse caso, a conta é ignorada ao usar a versão de Atualização de Aniversário do Windows 10 (1607).

#### <a name="azureadjoined--yes"></a>AzureAdJoined : SIM  

Esse campo indica se o dispositivo ingressou no Azure AD. Se o valor for **NO**, a associação ao Azure AD ainda não terá sido concluída. 

Prossiga para as próximas etapas para solução de problemas.

### <a name="step-3-find-the-phase-in-which-join-failed-and-the-errorcode"></a>Etapa 3: Localize a fase em que a junção falhou e o ErrorCode

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 e posterior

Procure a subseção ' registro anterior ' na seção ' dados de diagnóstico ' da saída de status de ingresso.
O campo ' fase de erro ' denota a fase da falha de junção enquanto ' Client ErrorCode ' denota o código de erro da operação de junção.

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-xxxx-xxxx-xxxx-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="older-windows-10-versions"></a>Versões mais antigas do Windows 10

Use logs de Visualizador de Eventos para localizar a fase e o código de erro para as falhas de junção.

1. Abra os logs de eventos de **registro de dispositivo do usuário** no Visualizador de eventos. Localizado em >  **logs de aplicativos e serviços registro de dispositivo de** **usuário** **do Microsoft** > **Windows** > 
2. Procure eventos com as seguintes eventIDs 304, 305, 307.

![Evento log de falhas](./media/troubleshoot-hybrid-join-windows-current/1.png)

![Evento log de falhas](./media/troubleshoot-hybrid-join-windows-current/2.png)

### <a name="step-4-check-for-possible-causes-and-resolutions-from-the-lists-below"></a>Etapa 4: Verifique se há possíveis causas e resoluções das listas abaixo

#### <a name="pre-check-phase"></a>Fase de pré-verificação

Possíveis motivos para a falha:

- O dispositivo não tem nenhuma linha de visão para o controlador de domínio.
   - O dispositivo deve estar na rede interna da organização ou na VPN com a linha de visão de rede para um controlador de domínio de Active Directory local (AD).

#### <a name="discover-phase"></a>Fase de descoberta

Possíveis motivos para a falha:

- Objeto de ponto de conexão de serviço (SCP) configurado incorretamente/não é possível ler o objeto SCP do controlador de domínio.
   - Um objeto SCP válido é necessário na floresta do AD, à qual o dispositivo pertence, que aponta para um nome de domínio verificado no Azure AD.
   - Os detalhes podem ser encontrados na seção [configurar um ponto de conexão de serviço](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join).
- Falha ao conectar e buscar os metadados de descoberta do ponto de extremidade de descoberta.
   - O dispositivo deve ser capaz de acessar `https://enterpriseregistration.windows.net`o, no contexto do sistema, para descobrir os pontos de extremidade de registro e autorização. 
   - Se o ambiente local exigir um proxy de saída, o administrador de ti deverá garantir que a conta de computador do dispositivo seja capaz de descobrir e autenticar silenciosamente no proxy de saída.
- Falha ao conectar ao ponto de extremidade de realm do usuário e executar a descoberta de realm. (Somente Windows 10 versão 1809 e posterior)
   - O dispositivo deve ser capaz de acessar `https://login.microsoftonline.com`o, no contexto do sistema, para executar a descoberta de realm para o domínio verificado e determinar o tipo de domínio (gerenciado/federado).
   - Se o ambiente local exigir um proxy de saída, o administrador de ti deverá garantir que o contexto do sistema no dispositivo seja capaz de descobrir e autenticar silenciosamente no proxy de saída.

**Códigos de erro comuns:**

- **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611)
   - Motivo: Não é possível ler o objeto SCP e obter as informações de locatário do Azure AD.
   - Resolução: Consulte a seção [configurar um ponto de conexão de serviço](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join).
- **DSREG_AUTOJOIN_DISC_FAILED** (0x801c0021/-2145648607)
   - Motivo: Falha de descoberta genérica. Falha ao obter os metadados de descoberta do DRS.
   - Resolução: Localize o suberro abaixo para investigar mais.
- **DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT**  (0x801c001f/-2145648609)
   - Motivo: Tempo limite da operação atingido ao executar a descoberta.
   - Resolução: Verifique se o está acessível no contexto do sistema. `https://enterpriseregistration.windows.net` Para obter mais informações, consulte a seção [requisitos de conectividade de rede](hybrid-azuread-join-managed-domains.md#prerequisites).
- **DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED** (0x801c0021/-2145648611)
   - Motivo: Falha na descoberta de realm genérico. Falha ao determinar o tipo de domínio (gerenciado/federado) do STS. 
   - Resolução: Localize o suberro abaixo para investigar mais.

**Códigos de suberro comuns:**

Para localizar o código de suberro do código de erro de descoberta, use um dos métodos a seguir.

##### <a name="windows-10-1803-and-above"></a>Windows 10 1803 e posterior

Procure ' teste de descoberta do DRS ' na seção ' dados de diagnóstico ' da saída de status de ingresso.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : UN-ELEVATED User
               Client Time : 2019-06-05 08:25:29.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x80072ee2]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

+----------------------------------------------------------------------+
```

##### <a name="older-windows-10-versions"></a>Versões mais antigas do Windows 10

Use logs de Visualizador de Eventos para localizar a fase e ErrorCode para as falhas de junção.

1. Abra os logs de eventos de **registro de dispositivo do usuário** no Visualizador de eventos. Localizado em >  **logs de aplicativos e serviços registro de dispositivo de** **usuário** **do Microsoft** > **Windows** > 
2. Procure eventos com as seguintes eventIDs 201

![Evento log de falhas](./media/troubleshoot-hybrid-join-windows-current/5.png)

###### <a name="network-errors"></a>Erros de rede

- **WININET_E_CANNOT_CONNECT** (0x80072efd/-2147012867)
   - Motivo: A conexão com o servidor não pôde ser estabelecida
   - Resolução: Garanta a conectividade de rede com os recursos da Microsoft necessários. Para obter mais informações, consulte [requisitos de conectividade de rede](hybrid-azuread-join-managed-domains.md#prerequisites).
- **WININET_E_TIMEOUT** (0x80072EE2/-2147012894)
   - Motivo: Tempo limite de rede geral.
   - Resolução: Garanta a conectividade de rede com os recursos da Microsoft necessários. Para obter mais informações, consulte [requisitos de conectividade de rede](hybrid-azuread-join-managed-domains.md#prerequisites).
- **WININET_E_DECODING_FAILED** (0x80072f8f/-2147012721)
   - Motivo: A pilha de rede não pôde decodificar a resposta do servidor.
   - Resolução: Verifique se o proxy de rede não está interferindo e modificando a resposta do servidor.

###### <a name="http-errors"></a>Erros de HTTP

- **DSREG_DISCOVERY_TENANT_NOT_FOUND** (0x801c003a/-2145648582)
   - Motivo: Objeto SCP configurado com a ID de locatário incorreta. Ou nenhuma assinatura ativa foi encontrada no locatário.
   - Resolução: Verifique se o objeto SCP está configurado com a ID de locatário do Azure AD correta e as assinaturas ativas ou presentes no locatário.
- **DSREG_SERVER_BUSY** (0x801c0025/-2145648603)
   - Motivo: HTTP 503 do servidor DRS.
   - Resolução: O servidor não está disponível no momento. tentativas de junção futuras provavelmente serão bem sucedidas quando o servidor estiver novamente online.

###### <a name="other-errors"></a>Outros erros

- **E_INVALIDDATA** (0x8007000d/-2147024883)
   - Motivo: Não foi possível analisar o JSON de resposta do servidor. Provavelmente devido ao proxy retornar HTTP 200 com uma página de autenticação HTML.
   - Resolução: Se o ambiente local exigir um proxy de saída, o administrador de ti deverá garantir que o contexto do sistema no dispositivo seja capaz de descobrir e autenticar silenciosamente no proxy de saída.

#### <a name="authentication-phase"></a>Fase de autenticação

Aplicável somente para contas de domínio federado.

Motivos da falha:

- Não é possível obter um token de acesso silenciosamente para o recurso DRS.
   - Os dispositivos Windows 10 adquirem o token de autenticação do serviço de Federação usando a autenticação integrada do Windows para um ponto de extremidade WS-Trust ativo. Detalhes: [Configuração de Serviço de Federação](hybrid-azuread-join-manual.md##set-up-issuance-of-claims)

**Códigos de erro comuns:**

Use os logs de Visualizador de Eventos para localizar o código de erro, o código de suberro, o código de erro do servidor e a mensagem de erro do servidor.

1. Abra os logs de eventos de **registro de dispositivo do usuário** no Visualizador de eventos. Localizado em >  **logs de aplicativos e serviços registro de dispositivo de** **usuário** **do Microsoft** > **Windows** > 
2. Procure eventos com o seguinte eventID 305

![Evento log de falhas](./media/troubleshoot-hybrid-join-windows-current/3.png)

##### <a name="configuration-errors"></a>Erros de configuração

- **ERROR_ADAL_PROTOCOL_NOT_SUPPORTED** (0xcaa90017/-894894057)
   - Motivo: O protocolo de autenticação não é WS-Trust.
   - Resolução: O provedor de identidade local deve dar suporte a WS-Trust 
- **ERROR_ADAL_FAILED_TO_PARSE_XML** (0xcaa9002c/-894894036)
   - Motivo: O serviço de federação local não retornou uma resposta XML.
   - Resolução: Verifique se o ponto de extremidade MEX está retornando um XML válido. Verifique se o proxy não está interferindo e retornando respostas não XML.
- **ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT** (0xcaa90023/-894894045)
   - Motivo: Não foi possível descobrir o ponto de extremidade para a autenticação de nome de usuário/senha.
   - Resolução: Verifique as configurações do provedor de identidade local. Verifique se os pontos de extremidade WS-Trust estão habilitados e certifique-se de que a resposta MEX contenha esses pontos de extremidade corretos.

##### <a name="network-errors"></a>Erros de rede

- **ERROR_ADAL_INTERNET_TIMEOUT** (0xcaa82ee2/-894947614)
   - Motivo: Tempo limite de rede geral.
   - Resolução: Verifique se o está acessível no contexto do sistema. `https://login.microsoftonline.com` Verifique se o provedor de identidade local está acessível no contexto do sistema. Para obter mais informações, consulte [requisitos de conectividade de rede](hybrid-azuread-join-managed-domains.md#prerequisites).
- **ERROR_ADAL_INTERNET_CONNECTION_ABORTED** (0xcaa82efe/-894947586)
   - Motivo: A conexão com o ponto de extremidade de autenticação foi anulada.
   - Resolução: Tente novamente após algum tempo ou tente ingressar em um local de rede estável alternativo.
- **ERROR_ADAL_INTERNET_SECURE_FAILURE** (0xcaa82f8f/-894947441)
   - Motivo: O certificado de protocolo SSL (SSL) enviado pelo servidor não pôde ser validado.
   - Resolução: Verifique a distorção de tempo do cliente. Tente novamente após algum tempo ou tente ingressar em um local de rede estável alternativo. 
- **ERROR_ADAL_INTERNET_CANNOT_CONNECT** (0xcaa82efd/-894947587)
   - Motivo: Falha na tentativa de conexão `https://login.microsoftonline.com` com.
   - Resolução: Verifique a conexão de `https://login.microsoftonline.com`rede para.

##### <a name="other-errors"></a>Outros erros

- **ERROR_ADAL_SERVER_ERROR_INVALID_GRANT** (0xcaa20003/-895352829)
   - Motivo: O token SAML do provedor de identidade local não foi aceito pelo Azure AD.
   - Resolução: Verifique as configurações do servidor de Federação. Procure o código de erro do servidor nos logs de autenticação.
- **ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED** (0xcaa90014/-894894060)
   - Motivo: A resposta WS-Trust do servidor relatou uma exceção de falha e falhou ao obter a asserção
   - Resolução: Verifique as configurações do servidor de Federação. Procure o código de erro do servidor nos logs de autenticação.
- **ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL** (0xcaa90006/-894894074)
   - Motivo: Recebeu um erro ao tentar obter o token de acesso do ponto de extremidade do token.
   - Resolução: Procure o erro subjacente no log ADAL. 
- **ERROR_ADAL_OPERATION_PENDING** (0xcaa1002d/-895418323)
   - Motivo: Falha geral do ADAL
   - Resolução: Procure o código de suberro ou o código de erro do servidor dos logs de autenticação.
    
#### <a name="join-phase"></a>Fase de junção

Motivos da falha:

Localize o tipo de registro e procure o código de erro na lista abaixo.

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 e posterior

Procure a subseção ' registro anterior ' na seção ' dados de diagnóstico ' da saída de status de ingresso.
O campo ' tipo de registro ' denota o tipo de junção executada.

```
+----------------------------------------------------------------------+
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

#### <a name="older-windows-10-versions"></a>Versões mais antigas do Windows 10

Use logs de Visualizador de Eventos para localizar a fase e ErrorCode para as falhas de junção.

1. Abra os logs de eventos de **registro de dispositivo do usuário** no Visualizador de eventos. Localizado em >  **logs de aplicativos e serviços registro de dispositivo de** **usuário** **do Microsoft** > **Windows** > 
2. Procure eventos com as seguintes eventIDs 204

![Evento log de falhas](./media/troubleshoot-hybrid-join-windows-current/4.png)

##### <a name="http-errors-returned-from-drs-server"></a>Erros de HTTP retornados do servidor DRS

- **DSREG_E_DIRECTORY_FAILURE** (0x801c03f2/-2145647630)
   - Motivo: Recebida uma resposta de erro do DRS com ErrorCode: DirectoryError
   - Resolução: Consulte o código de erro do servidor para obter possíveis razões e resoluções.
- **DSREG_E_DEVICE_AUTHENTICATION_ERROR** (0x801c0002/-2145648638)
   - Motivo: Recebida uma resposta de erro do DRS com ErrorCode: "AuthenticationError" e ErrorSubCode não é "DeviceNotFound". 
   - Resolução: Consulte o código de erro do servidor para obter possíveis razões e resoluções.
- **DSREG_E_DEVICE_INTERNALSERVICE_ERROR** (0x801c0006/-2145648634)
   - Motivo: Recebida uma resposta de erro do DRS com ErrorCode: DirectoryError
   - Resolução: Consulte o código de erro do servidor para obter possíveis razões e resoluções.

##### <a name="tpm-errors"></a>Erros do TPM

- **NTE_BAD_KEYSET** (0x80090016/-2146893802)
   - Motivo: Falha na operação do TPM ou ela era inválida
   - Resolução: Provavelmente devido a uma imagem inadequada do Sysprep. Verifique se o computador do qual a imagem Sysprep foi criada não é ingressado no Azure AD, ingressado no Azure AD híbrido ou o Azure AD registrado.
- **TPM_E_PCP_INTERNAL_ERROR** (0x80290407/-2144795641)
   - Motivo: Erro genérico do TPM. 
   - Resolução: Desabilite o TPM em dispositivos com este erro. O Windows 10 versão 1809 e superior detecta automaticamente falhas do TPM e conclui a junção híbrida do Azure AD sem usar o TPM.
- **TPM_E_NOTFIPS** (0x80280036/-2144862154)
   - Motivo: No momento, não há suporte para TPM no modo FIPS.
   - Resolução: Desabilite o TPM em dispositivos com este erro. O Windows 1809 detecta automaticamente falhas do TPM e conclui a junção híbrida do Azure AD sem usar o TPM.
- **NTE_AUTHENTICATION_IGNORED** (0x80090031/-2146893775)
   - Motivo: TPM bloqueado.
   - Resolução: Erro transitório. Aguarde o período de cooldown. A tentativa de junção depois de algum tempo deve ser realizada com sucesso. Mais informações podem ser encontradas no artigo [conceitos básicos do TPM](https://docs.microsoft.com/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering)

##### <a name="network-errors"></a>Erros de rede

- **WININET_E_TIMEOUT** (0x80072EE2/-2147012894)
   - Motivo: Tempo limite de rede geral ao tentar registrar o dispositivo no DRS
   - Resolução: Verifique a conectividade de `https://enterpriseregistration.windows.net`rede para.
- **WININET_E_NAME_NOT_RESOLVED** (0x80072ee7/-2147012889)
   - Motivo: O nome ou endereço do servidor não pôde ser resolvido.
   - Resolução: Verifique a conectividade de `https://enterpriseregistration.windows.net`rede para. Verifique se a resolução DNS para o nome de host está correta no n/w e no dispositivo.
- **WININET_E_CONNECTION_ABORTED** (0x80072efe/-2147012866)
   - Motivo: A conexão com o servidor foi encerrada de forma anormal.
   - Resolução: Tente novamente após algum tempo ou tente ingressar em um local de rede estável alternativo.

##### <a name="federated-join-server-errors"></a>Erros do servidor de junção federada

| Código de erro do servidor | Mensagem de erro do servidor | Motivos possíveis | Resolução |
| --- | --- | --- | --- |
| DirectoryError | Sua solicitação está limitada temporariamente. Tente após 300 segundos. | Erro esperado. Possivelmente devido à realização de várias solicitações de registro em sucessão rápida. | Tentar novamente o ingresso após o período de cooldown |

##### <a name="sync-join-server-errors"></a>Sincronizar erros do servidor de junção

| Código de erro do servidor | Mensagem de erro do servidor | Motivos possíveis | Resolução |
| --- | --- | --- | --- |
| DirectoryError | AADSTS90002: Locatário <UUID> não encontrado. Esse erro pode ocorrer se não houver nenhuma assinatura ativa para o locatário. Verifique com seu administrador de assinatura. | A ID do locatário no objeto SCP está incorreta | Verifique se o objeto SCP está configurado com a ID de locatário do Azure AD e as assinaturas ativas corretas e presentes no locatário. |
| DirectoryError | O objeto de dispositivo pela ID fornecida não foi encontrado. | Erro esperado para a junção de sincronização. O objeto de dispositivo não foi sincronizado do AD para o Azure AD | Aguarde a conclusão da sincronização do Azure AD Connect e a próxima tentativa de junção após a conclusão da sincronização resolverá o problema |
| AuthenticationError | A verificação do SID do computador de destino | O certificado no dispositivo do Azure AD não corresponde ao certificado usado para assinar o blob durante a junção de sincronização. Esse erro normalmente significa que a sincronização ainda não foi concluída. |  Aguarde a conclusão da sincronização do Azure AD Connect e a próxima tentativa de junção após a conclusão da sincronização resolverá o problema |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>Etapa 5: Coletar logs e contatar Suporte da Microsoft

Obtenha scripts públicos aqui: [ https://1drv.ms/u/s! AkyTjQ17vtfagYkZ6VJzPg78e3o7PQ]( https://1drv.ms/u/s!AkyTjQ17vtfagYkZ6VJzPg78e3o7PQ)

1. Abra um prompt de comando do administrador `start_ngc_tracing_public.cmd`e execute.
2. Execute as etapas para reproduzir o problema.
3. Pare de executar o script de log `stop_ngc_tracing_public.cmd`executando.
4. Zip e enviar os logs em `%SYSTEMDRIVE%\TraceDJPP\*` para análise.

## <a name="troubleshoot-post-join-issues"></a>Solucionar problemas de pós-junção

### <a name="retrieve-the-join-status"></a>Recuperar o status de ingresso 

#### <a name="wamdefaultset-yes-and-azureadprt-yes"></a>WamDefaultSet Sim e AzureADPrt: SIM
  
Esses campos indicam se o usuário foi autenticado com êxito no Azure AD ao se conectar ao dispositivo. Se os valores forem **NO**, talvez o motivo seja:

- Chave de armazenamento inadequada no TPM associado ao dispositivo após o registro (verifique o KeySignTest ao executar com privilégios elevados).
- ID de logon alternativo
- Proxy HTTP não encontrado

## <a name="next-steps"></a>Próximas etapas

Continuar a [solução de problemas de dispositivos usando o comando dsregcmd](troubleshoot-device-dsregcmd.md)

Para perguntas, consulte as [Perguntas frequentes sobre o gerenciamento de dispositivos](faq.md)
