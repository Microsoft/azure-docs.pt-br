---
title: Definir uma home page personalizada para aplicativos publicados usando o Proxy de Aplicativo Azure AD | Microsoft Docs
description: Abora as noções básicas sobre os conectores do Proxy de Aplicativo Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51596e4db8999de5089748e40f9b24bd46c84e56
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807841"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Definir uma home page personalizada para aplicativos publicados usando o Proxy de Aplicativo Azure AD

Este artigo discute como configurar um aplicativo para direcionar um usuário para uma página inicial personalizada. Quando você publica um aplicativo com o Proxy de aplicativo, você define uma URL interna, mas, às vezes, isso não é a página de que um usuário deverá ver pela primeira vez. Defina uma home page personalizada para que um usuário obtém a página correta quando acessam o aplicativo. O usuário verá a home page personalizada que você defina, independentemente se eles acessam o aplicativo do painel de acesso do Azure Active Directory ou o lançador de aplicativo do Office 365.

Quando um usuário inicia o aplicativo, eles são direcionados por padrão para a URL do domínio raiz para o aplicativo publicado. A página de aterrissagem é normalmente definida como a URL da home page. Use o módulo do PowerShell do Azure AD para definir uma URL da home page personalizada quando desejar que um usuário do aplicativo cheguem em uma página específica dentro do aplicativo.

Aqui está um cenário que explica por que sua empresa definiria uma página inicial personalizada:

- Dentro da rede corporativa, um usuário vai para `https://ExpenseApp/login/login.aspx` para entrar e acessar seu aplicativo.
- Como você tem outros ativos (como imagens) que o Proxy de aplicativo precisa acessar no nível superior da estrutura de pasta, você publica o aplicativo com `https://ExpenseApp` como a URL interna.
- A URL externa padrão é `https://ExpenseApp-contoso.msappproxy.net`, que não leva a um usuário externo para a página de entrada.
- Você deseja definir `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` como a URL da home page em vez disso, portanto, um usuário externo vê a página de entrada primeiro.

> [!NOTE]
> Quando você conceder aos usuários acesso a aplicativos publicados, os aplicativos são exibidos no [painel de acesso do Azure AD](../user-help/my-apps-portal-end-user-access.md) e [lançador de aplicativo do Office 365](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/).

## <a name="before-you-start"></a>Antes de começar

Antes de definir a URL da página inicial, tenha em mente os seguintes requisitos:

- O caminho que você especifica deve ser um caminho de subdomínio da URL raiz do domínio.

  Por exemplo, se a URL do domínio raiz é `https://apps.contoso.com/app1/`, a URL da home page que você configura deve começar com `https://apps.contoso.com/app1/`.

- Se você fizer uma alteração no aplicativo publicado, essa alteração poderá redefinir o valor da URL da home page. Quando você decidir atualizar o aplicativo no futuro, você deverá verificar novamente e, se necessário, atualizar a URL da home page.

Você pode definir a URL da home page no portal do Azure ou usando o PowerShell.

## <a name="change-the-home-page-in-the-azure-portal"></a>Altere a home page no portal do Azure

Para alterar a URL da home page do seu aplicativo por meio do portal do Azure AD, siga estas etapas:

1. Entre no [Portal do Azure](https://portal.azure.com/) como administrador.
1. Selecione **Azure Active Directory**e então **registros de aplicativo**. É exibida a lista de aplicativos registrados.
1. Escolha seu aplicativo na lista. Aparece uma página mostrando os detalhes do aplicativo registrado.
1. Sob **Manage**, selecione **identidade visual**.
1. Atualizar o **URL da Home page** com o novo caminho.

   ![Página da identidade visual para um aplicativo registrado indicando que o campo de URL da Home Page](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. Clique em **Salvar**.

## <a name="change-the-home-page-with-powershell"></a>Alterar a home page com o PowerShell

Para configurar a home page de um aplicativo usando o PowerShell, você precisa:

1. Instale o módulo do PowerShell do Azure AD.
1. Localize o valor ObjectId do aplicativo.
1. Atualize a URL da home page do aplicativo usando comandos do PowerShell.

### <a name="install-the-azure-ad-powershell-module"></a>Instalar o módulo do Powershell do Azure AD

Antes de definir uma URL personalizada de página inicial usando o PowerShell, instale o módulo PowerShell do Azure AD. Baixe esse pacote da [Galeria do PowerShell](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16), que usa o ponto de extremidade de API do Graph.

Para instalar o pacote, siga estas etapas:

1. Abra uma janela padrão do PowerShell e, em seguida, execute o seguinte comando:

   ```powershell
   Install-Module -Name AzureAD
   ```

    Se você estiver executando o comando como um não administrador, use a opção `-scope currentuser`.

1. Durante a instalação, selecione **Y** para instalar dois pacotes do Nuget.org. Ambos os pacotes são necessários.

### <a name="find-the-objectid-of-the-app"></a>Localizar a ObjectId do aplicativo

Você pode obter a ObjectId do aplicativo por meio de pesquisa para o aplicativo por seu nome de exibição ou página inicial.

1. Na janela do PowerShell, importe o módulo do Azure AD.

   ```powershell
   Import-Module AzureAD
   ```

1. Entre no módulo Azure AD como o administrador de locatários.

   ```powershell
   Connect-AzureAD
   ```

1. Encontre o aplicativo. Este exemplo usa o PowerShell para localizar a ObjectId por meio de pesquisa para o aplicativo com um nome de exibição de `SharePoint`.

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   Você deve obter um resultado semelhante ao mostrado aqui. Copie o GUID do ObjectId para usar na próxima seção.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   Como alternativa, você poderia apenas receber uma lista de todos os aplicativos, pesquise a lista para o aplicativo com um nome de exibição específico ou a home page e copiar ObjectId do aplicativo depois que o aplicativo for encontrado.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>Atualizar a URL da home page

Crie a URL da home page e atualizar seu aplicativo com esse valor. Continuar usando a mesma janela do PowerShell, ou se você estiver usando uma nova janela do PowerShell, entre no módulo do Azure AD novamente usando `Connect-AzureAD`. Depois, siga estas etapas:

1. Crie uma variável para armazenar o valor de ID do objeto que você copiou na seção anterior. (Substitua o valor de ID do objeto usado para neste exemplo do SharePoint com o valor de ID de objeto do seu aplicativo.)

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. Confirme se você tem o aplicativo correto, executando o comando a seguir. A saída deve ser idêntica de saída que você viu na seção anterior ([localizar a ObjectId do aplicativo](#find-the-objectid-of-the-app)).

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

1. Crie um objeto de aplicativo em branco para armazenar as alterações que você deseja fazer.

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

1. Defina a URL da home page como o valor desejado. O valor deve ser um caminho de subdomínio do aplicativo publicado. Por exemplo, se você alterar o URL da página inicial de `https://sharepoint-iddemo.msappproxy.net/` para `https://sharepoint-iddemo.msappproxy.net/hybrid/`, os usuários do aplicativo vão diretamente para a página inicial personalizada.

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

1. Faça a atualização da página inicial.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

1. Para confirmar que a alteração foi bem-sucedida, execute o seguinte comando da etapa 2 novamente.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   Para nosso exemplo, a saída agora deve aparecer da seguinte maneira:

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

1. Reinicie o aplicativo para confirmar que a home page é exibida como a primeira tela, conforme o esperado.

> [!NOTE]
> Todas as alterações que você fizer no aplicativo poderão redefinir a URL da home page. Se a URL da página inicial for redefinida, repita as etapas nesta seção para defini-la novamente.

## <a name="next-steps"></a>Próximas etapas

- [Habilitar acesso remoto ao SharePoint com o Proxy de Aplicativo Azure AD](application-proxy-integrate-with-sharepoint-server.md)
- [Tutorial: Adicionar um aplicativo local para acesso remoto por meio do Proxy de aplicativo no Azure Active Directory](application-proxy-add-on-premises-application.md)