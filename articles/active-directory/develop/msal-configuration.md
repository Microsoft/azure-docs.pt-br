---
title: Arquivo de configuração do Android MSAL | Azure
titleSuffix: Microsoft identity platform
description: Uma visão geral do arquivo de configuração do MSAL (biblioteca de autenticação da Microsoft) do Android, que representa a configuração de um aplicativo no Azure Active Directory.
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 09/12/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: aa0ce6a5f909e67f0551c8667bb7e5c5e6d7eb04
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92275600"
---
# <a name="android-microsoft-authentication-library-configuration-file"></a>Arquivo de configuração da biblioteca de autenticação do Android da Microsoft

A MSAL (biblioteca de autenticação da Microsoft) do Android é fornecida com um [arquivo JSON de configuração padrão](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json) que você personaliza para definir o comportamento do seu aplicativo cliente público para coisas como a autoridade padrão, quais autoridades você usará e assim por diante.

Este artigo o ajudará a entender as várias configurações no arquivo de configuração e a especificar o arquivo de configuração a ser usado em seu aplicativo baseado em MSAL.

## <a name="configuration-settings"></a>Definições de configuração

### <a name="general-settings"></a>Configurações gerais

| Propriedade | Tipo de Dados | Obrigatório | Observações |
|-----------|------------|-------------|-------|
| `client_id` | String | Sim | A ID do cliente do aplicativo na [página de registro do aplicativo](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) |
| `redirect_uri`   | String | Sim | O URI de redirecionamento do seu aplicativo da [página de registro do aplicativo](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) |
| `broker_redirect_uri_registered` | Boolean | Não | Valores possíveis: `true` , `false` |
| `authorities` | Lista\<Authority> | Não | A lista de autoridades que seu aplicativo precisa |
| `authorization_user_agent` | AuthorizationAgent (enum) | Não | Valores possíveis: `DEFAULT` , `BROWSER` , `WEBVIEW` |
| `http` | HttpConfiguration | Não | Configurar `HttpUrlConnection` `connect_timeout` e `read_timeout` |
| `logging` | LoggingConfiguration | Não | Especifica o nível de detalhes de log. As configurações opcionais incluem: `pii_enabled` , que usa um valor booliano e `log_level` , que usa `ERROR` , `WARNING` , `INFO` ou `VERBOSE` . |

### <a name="client_id"></a>client_id

A ID do cliente ou a ID do aplicativo que foi criada quando você registrou seu aplicativo.

### <a name="redirect_uri"></a>redirect_uri

O URI de redirecionamento que você registrou quando registrou seu aplicativo. Se o URI de redirecionamento for para um aplicativo de agente, consulte [URI de redirecionamento para aplicativos cliente públicos](msal-client-application-configuration.md#redirect-uri-for-public-client-apps) para garantir que você esteja usando o formato de URI de redirecionamento correto para seu aplicativo de agente.

### <a name="broker_redirect_uri_registered"></a>broker_redirect_uri_registered

Se você quiser usar a autenticação orientada, a `broker_redirect_uri_registered` Propriedade deverá ser definida como `true` . Em um cenário de autenticação orientada, se o aplicativo não estiver no formato correto para se comunicar com o agente conforme descrito em [URI de redirecionamento para aplicativos cliente públicos](msal-client-application-configuration.md#redirect-uri-for-public-client-apps), o aplicativo validará o URI de redirecionamento e lançará uma exceção quando ele for iniciado.

### <a name="authorities"></a>pelas

A lista de autoridades que são conhecidas e confiáveis por você. Além das autoridades listadas aqui, o MSAL também consulta a Microsoft para obter uma lista de nuvens e autoridades conhecidas da Microsoft. Nessa lista de autoridades, especifique o tipo da autoridade e quaisquer parâmetros opcionais adicionais, como `"audience"` , que devem ser alinhados com o público do seu aplicativo com base no registro do aplicativo. Veja a seguir um exemplo de lista de autoridades:

```javascript
// Example AzureAD and Personal Microsoft Account
{
    "type": "AAD",
    "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
    },
    "default": true // Indicates that this is the default to use if not provided as part of the acquireToken call
},
// Example AzureAD My Organization
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMyOrg",
        "tenant_id": "contoso.com" // Provide your specific tenant ID here
    }
},
// Example AzureAD Multiple Organizations
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMultipleOrgs"
    }
},
//Example PersonalMicrosoftAccount
{
    "type": "AAD",
    "audience": {
        "type": "PersonalMicrosoftAccount"
    }
}
```

#### <a name="map-aad-authority--audience-to-microsoft-identity-platform-endpoints"></a>Mapear a autoridade do AAD & público para pontos de extremidade da plataforma Microsoft Identity

| Tipo | Público | ID do locatário | Authority_Url | Ponto de extremidade resultante | Observações |
|------|------------|------------|----------------|----------------------|---------|
| AAD | AzureADandPersonalMicrosoftAccount | | | `https://login.microsoftonline.com/common` | `common` é um alias de locatário para onde a conta é. Como um locatário específico de Azure Active Directory ou o sistema conta Microsoft. |
| AAD | AzureADMyOrg | contoso.com | | `https://login.microsoftonline.com/contoso.com` | Somente as contas presentes no contoso.com podem adquirir um token. Qualquer domínio verificado, ou o GUID do locatário, pode ser usado como a ID do locatário. |
| AAD | AzureADMultipleOrgs | | | `https://login.microsoftonline.com/organizations` | Somente contas Azure Active Directory podem ser usadas com esse ponto de extremidade. As contas da Microsoft podem ser membros de organizações. Para adquirir um token usando um conta Microsoft para um recurso em uma organização, especifique o locatário organizacional do qual você deseja o token. |
| AAD | PersonalMicrosoftAccount | | | `https://login.microsoftonline.com/consumers` | Somente contas da Microsoft podem usar esse ponto de extremidade. |
| B2C | | | Consulte o ponto de extremidade resultante | `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/` | Somente as contas presentes no locatário contoso.onmicrosoft.com podem adquirir um token. Neste exemplo, a política B2C faz parte do caminho da URL da autoridade. |

> [!NOTE]
> A validação de autoridade não pode ser habilitada e desabilitada em MSAL.
> As autoridades são conhecidas por você como o desenvolvedor especificado por meio da configuração ou são conhecidas pela Microsoft por meio de metadados.
> Se MSAL receber uma solicitação de um token para uma autoridade desconhecida, um `MsalClientException` dos resultados do tipo `UnknownAuthority` .
> A autenticação orientada não funciona para Azure AD B2C.

#### <a name="authority-properties"></a>Propriedades da autoridade

| Propriedade | Tipo de dados  | Necessária | Observações |
|-----------|-------------|-----------|--------|
| `type` | String | Sim | Espelha o público ou o tipo de conta de destino do seu aplicativo. Valores possíveis: `AAD` , `B2C` |
| `audience` | Objeto | Não | Aplica-se somente quando Type = `AAD` . Especifica a identidade de destino do seu aplicativo. Usar o valor do registro do aplicativo |
| `authority_url` | String | Sim | Necessário somente quando Type = `B2C` . Especifica a URL ou a política de autoridade que seu aplicativo deve usar  |
| `default` | booleano | Sim | Um único `"default":true` é necessário quando uma ou mais autoridades são especificadas. |

#### <a name="audience-properties"></a>Propriedades do público

| Propriedade | Tipo de Dados  | Obrigatório | Observações |
|-----------|-------------|------------|-------|
| `type` | String | Sim | Especifica o público-alvo que seu aplicativo deseja direcionar. Valores possíveis: `AzureADandPersonalMicrosoftAccount` , `PersonalMicrosoftAccount` , `AzureADMultipleOrgs` , `AzureADMyOrg` |
| `tenant_id` | String | Sim | Necessário somente quando `"type":"AzureADMyOrg"` . Opcional para outros `type` valores. Pode ser um domínio de locatário, como `contoso.com` , ou uma ID de locatário, como `72f988bf-86f1-41af-91ab-2d7cd011db46` ) |

### <a name="authorization_user_agent"></a>authorization_user_agent

Indica se deve ser usado um WebView inserido ou o navegador padrão no dispositivo, ao entrar em uma conta ou autorizar o acesso a um recurso.

Valores possíveis:
- `DEFAULT`: Prefere o navegador do sistema. Usa a exibição da Web inserida se um navegador não estiver disponível no dispositivo.
- `WEBVIEW`: Use a exibição da Web inserida.
- `BROWSER`: Usa o navegador padrão no dispositivo.

### <a name="multiple_clouds_supported"></a>multiple_clouds_supported

Para clientes que dão suporte a várias nuvens nacionais, especifique `true` . A plataforma de identidade da Microsoft será redirecionada automaticamente para a nuvem nacional correta durante a autorização e o resgate de token. Você pode determinar a nuvem nacional da conta conectada examinando a autoridade associada ao `AuthenticationResult` . Observe que o `AuthenticationResult` não fornece o endereço de ponto de extremidade específico da nuvem nacional do recurso para o qual você solicita um token.

### <a name="broker_redirect_uri_registered"></a>broker_redirect_uri_registered

Um booliano que indica se você está usando um URI de redirecionamento no agente compatível com o Microsoft Identity Broker. Defina como `false` se você não quiser usar o agente em seu aplicativo.

Se você estiver usando a autoridade do AAD com o público definido como `"MicrosoftPersonalAccount"` , o agente não será usado.

### <a name="http"></a>http

Defina configurações globais para tempos limite de HTTP, como:

| Propriedade | Tipo de dados | Necessária | Observações |
| ---------|-----------|------------|--------|
| `connect_timeout` | int | Não | Tempo em milissegundos |
| `read_timeout` | int | Não | Tempo em milissegundos |

### <a name="logging"></a>registro em log

As seguintes configurações globais são para registro em log:

| Propriedade | Tipo de Dados  | Obrigatório | Observações |
| ----------|-------------|-----------|---------|
| `pii_enabled`  | boolean | Não | Se os dados pessoais devem ser emitidos |
| `log_level`   | Cadeia de caracteres | No | As mensagens de log para saída. Os níveis de log com suporte incluem `ERROR` ,, `WARNING` `INFO` e `VERBOSE` . |
| `logcat_enabled` | boolean | Não | Se deve-se gerar uma saída para o Cat de log além da interface de log |

### <a name="account_mode"></a>account_mode

Especifica quantas contas podem ser usadas em seu aplicativo por vez. Os valores possíveis são:

- `MULTIPLE` (Padrão)
- `SINGLE`

Construir um `PublicClientApplication` usando um modo de conta que não corresponda a essa configuração resultará em uma exceção.

Para obter mais informações sobre as diferenças entre contas únicas e múltiplas, consulte [aplicativos de conta única e múltipla](single-multi-account.md).

### <a name="browser_safelist"></a>browser_safelist

Uma lista de permissões de navegadores que são compatíveis com o MSAL. Esses navegadores manipulam corretamente os redirecionamentos para as intenções personalizadas. Você pode adicionar a esta lista. O padrão é fornecido na configuração padrão mostrada abaixo.
``
## <a name="the-default-msal-configuration-file"></a>O arquivo de configuração padrão do MSAL

A configuração padrão do MSAL fornecida com MSAL é mostrada abaixo. Você pode ver a versão mais recente no [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json).

Essa configuração é complementada por valores que você fornece. Os valores que você fornece substituem os padrões.

```javascript
{
  "authorities": [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      },
      "default": true
    }
  ],
  "authorization_user_agent": "DEFAULT",
  "multiple_clouds_supported": false,
  "broker_redirect_uri_registered": false,
  "http": {
    "connect_timeout": 10000,
    "read_timeout": 30000
  },
  "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": false
  },
  "shared_device_mode_supported": false,
  "account_mode": "MULTIPLE",
  "browser_safelist": [
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "45"
    },
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "57"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "4.0"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.cloudmosa.puffinFree",
      "browser_signature_hashes": [
        "1WqG8SoK2WvE4NTYgr2550TRhjhxT-7DWxu6C_o6GrOLK6xzG67Hq7GCGDjkAFRCOChlo2XUUglLRAYu3Mn8Ag=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.duckduckgo.mobile.android",
      "browser_signature_hashes": [
        "S5Av4cfEycCvIvKPpKGjyCuAE5gZ8y60-knFfGkAEIZWPr9lU5kA7iOAlSZxaJei08s0ruDvuEzFYlmH-jAi4Q=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.explore.web.browser",
      "browser_signature_hashes": [
        "BzDzBVSAwah8f_A0MYJCPOkt0eb7WcIEw6Udn7VLcizjoU3wxAzVisCm6bW7uTs4WpMfBEJYf0nDgzTYvYHCag=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.ksmobile.cb",
      "browser_signature_hashes": [
        "lFDYx1Rwc7_XUn4KlfQk2klXLufRyuGHLa3a7rNjqQMkMaxZueQfxukVTvA7yKKp3Md3XUeeDSWGIZcRy7nouw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.microsoft.emmx",
      "browser_signature_hashes": [
        "Ivy-Rk6ztai_IudfbyUrSHugzRqAtHWslFvHT0PTvLMsEKLUIgv7ZZbVxygWy_M5mOPpfjZrd3vOx3t-cA6fVQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.browser",
      "browser_signature_hashes": [
        "FIJ3IIeqB7V0qHpRNEpYNkhEGA_eJaf7ntca-Oa_6Feev3UkgnpguTNV31JdAmpEFPGNPo0RHqdlU0k-3jWJWw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.mini.native",
      "browser_signature_hashes": [
        "TOTyHs086iGIEdxrX_24aAewTZxV7Wbi6niS2ZrpPhLkjuZPAh1c3NQ_U4Lx1KdgyhQE4BiS36MIfP6LbmmUYQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "mobi.mgeek.TunnyBrowser",
      "browser_signature_hashes": [
        "RMVoXuK1sfJZuGZ8onG1yhMc-sKiAV2NiB_GZfdNlN8XJ78XEE2wPM6LnQiyltF25GkHiPN2iKQiGwaO2bkyyQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "org.mozilla.focus",
      "browser_signature_hashes": [
        "L72dT-stFqomSY7sYySrgBJ3VYKbipMZapmUXfTZNqOzN_dekT5wdBACJkpz0C6P0yx5EmZ5IciI93Q0hq0oYA=="
      ],
      "browser_use_customTab" : false
    }
  ]
}
```
## <a name="example-basic-configuration"></a>Exemplo de configuração básica

O exemplo a seguir ilustra uma configuração básica que especifica a ID do cliente, o URI de redirecionamento, se um redirecionamento do agente está registrado e uma lista de autoridades.

```javascript
{
  "client_id" : "4b0db8c2-9f26-4417-8bde-3f0e3656f8e0",
  "redirect_uri" : "msauth://com.microsoft.identity.client.sample.local/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      }
      "default": true
    }
  ]
}
```

## <a name="how-to-use-a-configuration-file"></a>Como usar um arquivo de configuração

1. Crie um arquivo de configuração. Recomendamos que você crie seu arquivo de configuração personalizada no `res/raw/auth_config.json` . Mas você pode colocá-lo em qualquer lugar que desejar.
2. Informe ao MSAL onde procurar sua configuração ao construir o `PublicClientApplication` . Por exemplo:

   ```java
   //On Worker Thread
   IMultipleAccountPublicClientApplication sampleApp = null; 
   sampleApp = new PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.auth_config);
   ```
