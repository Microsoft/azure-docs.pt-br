---
title: Configurar o domínio do Publicador de um aplicativo | Azure
titleSuffix: Microsoft identity platform
description: Saiba como configurar o domínio do Publicador de um aplicativo para permitir que os usuários saibam onde suas informações estão sendo enviadas.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/23/2020
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.openlocfilehash: 9b3840ffb39a204cfa24dcb0430f20ac16587582
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100104171"
---
# <a name="how-to-configure-an-applications-publisher-domain"></a>Como configurar um domínio do Publicador de um aplicativo

O domínio do Publicador de um aplicativo é exibido aos usuários no [prompt de consentimento do aplicativo](application-consent-experience.md) para permitir que os usuários saibam onde suas informações estão sendo enviadas. Aplicativos multilocatários que são registrados após 21 de maio de 2019 que não têm um domínio do Publicador aparecem como não **verificados**. Aplicativos multilocatários são aplicativos que dão suporte a contas fora de um único diretório organizacional; por exemplo, dar suporte a todas as contas do Azure AD ou dar suporte a todas as contas do Azure AD e contas pessoais da Microsoft.

## <a name="new-applications"></a>Novos aplicativos

Quando você registra um novo aplicativo, o domínio do Publicador do seu aplicativo pode ser definido como um valor padrão. O valor depende de onde o aplicativo está registrado, principalmente se o aplicativo está registrado em um locatário e se o locatário tem domínios verificados por locatário.

Se houver domínios verificados por locatário, o domínio do editor do aplicativo usará como padrão o domínio verificado primário do locatário. Se não houver domínios verificados de locatário (que é o caso quando o aplicativo não está registrado em um locatário), o domínio do Publicador do aplicativo será definido como nulo.

A tabela a seguir resume o comportamento padrão do valor de domínio do Publicador.  

| Domínios verificados por locatário | Valor padrão do domínio do Publicador |
|-------------------------|----------------------------|
| nulo | nulo |
| *. onmicrosoft.com | *. onmicrosoft.com |
| -*. onmicrosoft.com<br/>-domain1.com<br/>-domain2.com (primário) | domain2.com |

Se um domínio do Publicador de um aplicativo multilocatário não estiver definido ou se estiver definido como um domínio que termina em. onmicrosoft.com, o prompt de consentimento do aplicativo mostrará não **verificado** no lugar do domínio do Publicador.

## <a name="grandfathered-applications"></a>Aplicativos mais exavôs

Se seu aplicativo foi registrado antes de 21 de maio de 2019, o prompt de consentimento do seu aplicativo não será exibido sem **verificação** se você não tiver definido um domínio do Publicador. Recomendamos que você defina o valor de domínio do Publicador para que os usuários possam ver essas informações no prompt de consentimento do aplicativo.

## <a name="configure-publisher-domain-using-the-azure-portal"></a>Configurar o domínio do Publicador usando o portal do Azure

Para definir o domínio do Publicador do aplicativo, siga estas etapas.

1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário no qual deseja registrar o aplicativo.
1. Navegue até [Azure Active Directory > registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) para localizar e selecionar o aplicativo que você deseja configurar.

   Depois de selecionar o aplicativo, você verá a página **visão geral** do aplicativo.
1. Em **gerenciar**, selecione a **identidade visual**.
1. Localize o campo **domínio do Publicador** e selecione uma das seguintes opções:

   - Selecione **configurar um domínio** se ainda não tiver configurado um domínio.
   - Selecione **Atualizar domínio** se um domínio já tiver sido configurado.

Se seu aplicativo estiver registrado em um locatário, você verá duas guias para selecionar: **Selecione um domínio verificado** e **Verifique um novo domínio**.

Se seu aplicativo não estiver registrado em um locatário, você verá apenas a opção para verificar um novo domínio para seu aplicativo.

### <a name="to-verify-a-new-domain-for-your-app"></a>Para verificar um novo domínio para seu aplicativo

1. Crie um arquivo chamado `microsoft-identity-association.json` e cole o seguinte trecho de código JSON.

   ```json
   {
      "associatedApplications": [
         {
            "applicationId": "{YOUR-APP-ID-HERE}"
         },
         {
            "applicationId": "{YOUR-OTHER-APP-ID-HERE}"
         }
      ]
    }
   ```

1. Substitua o espaço reservado *{Your-app-ID-aqui}* pela ID do aplicativo (cliente) que corresponde ao seu aplicativo.

1. Hospede o arquivo em: `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json` . Substitua o espaço reservado *{seu-domínio-aqui}* para corresponder ao domínio verificado.

1. Clique no botão **verificar e salvar domínio** .

### <a name="to-select-a-verified-domain"></a>Para selecionar um domínio verificado

- Se o seu locatário tiver domínios verificados, selecione um dos domínios na lista suspensa **selecionar um domínio verificado** .

>[!Note]
> O cabeçalho ' Content-Type ' esperado que deve ser retornado é `application/json` . Você pode receber um erro, conforme mencionado abaixo, se você usar qualquer outra coisa como `application/json; charset=utf-8` 
> 
>``` "Verification of publisher domain failed. Error getting JSON file from https:///.well-known/microsoft-identity-association. The server returned an unexpected content type header value. " ```
>

## <a name="implications-on-the-app-consent-prompt"></a>Implicações no prompt de consentimento do aplicativo

Configurar o domínio do Publicador tem um impacto sobre o que os usuários veem no prompt de consentimento do aplicativo. Para entender totalmente os componentes do prompt de consentimento, consulte [noções básicas sobre as experiências de consentimento do aplicativo](application-consent-experience.md).

A tabela a seguir descreve o comportamento de aplicativos criados antes de 21 de maio de 2019.

![Prompt de consentimento para aplicativos criados antes de 21 de maio de 2019](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

O comportamento para novos aplicativos criados após 21 de maio de 2019 dependerá do domínio do Publicador e do tipo de aplicativo. A tabela a seguir descreve as alterações que você deve esperar ver com as diferentes combinações de configurações.

![Prompt de consentimento para aplicativos criados após 21 de maio de 2019](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>Implicações em URIs de redirecionamento

Os aplicativos que conectam usuários com qualquer conta corporativa ou de estudante ou contas pessoais da Microsoft ([multilocatário](single-and-multi-tenant-apps.md)) estão sujeitos a algumas restrições ao especificar URIs de redirecionamento.

### <a name="single-root-domain-restriction"></a>Restrição de domínio de raiz única

Quando o valor de domínio do Publicador de aplicativos multilocatário é definido como NULL, os aplicativos são restritos a compartilhar um único domínio raiz para os URIs de redirecionamento. Por exemplo, a seguinte combinação de valores não é permitida porque o domínio raiz, contoso.com, não corresponde a fabrikam.com.

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>Restrições de subdomínio

Os subdomínios são permitidos, mas você deve registrar explicitamente o domínio raiz. Por exemplo, embora os URIs a seguir compartilhem um único domínio raiz, a combinação não é permitida.

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

No entanto, se o desenvolvedor adicionar explicitamente o domínio raiz, a combinação será permitida.

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>Exceções

Os casos a seguir não estão sujeitos à restrição de domínio de raiz única:

- Aplicativos de locatário único ou aplicativos que se destinam a contas em um único diretório
- Uso de localhost como URIs de redirecionamento
- Redirecionar URIs com esquemas personalizados (não HTTP ou HTTPS)

## <a name="configure-publisher-domain-programmatically"></a>Configurar o domínio do Publicador programaticamente

Atualmente, não há nenhuma API REST ou suporte do PowerShell para configurar o domínio do Publicador programaticamente.
