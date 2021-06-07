---
title: Home page personalizadas para aplicativos publicados-Azure Proxy de Aplicativo do AD
description: Abora as noções básicas sobre os conectores do Proxy de Aplicativo Azure AD
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/23/2019
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ebef93455414c114fb60df9af8ebc629769a33e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99254421"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Definir uma home page personalizada para aplicativos publicados usando o Proxy de Aplicativo Azure AD

Este artigo discute como configurar um aplicativo para direcionar um usuário para um home page personalizado. Ao publicar um aplicativo com o proxy de aplicativo, você define uma URL interna, mas às vezes essa não é a página que um usuário deve ver primeiro. Defina um home page personalizado para que um usuário obtenha a página correta quando acessar o aplicativo. Um usuário verá o home page personalizado que você definiu, independentemente de eles acessarem o aplicativo do Azure Active Directory o iniciador de aplicativos ou do Microsoft 365.

Quando um usuário inicia o aplicativo, ele é direcionado por padrão para a URL do domínio raiz para o aplicativo publicado. A página de aterrissagem é normalmente definida como a URL da home page. Use o módulo do PowerShell do Azure AD para definir uma URL de home page personalizada quando você quiser que um usuário de aplicativo pouse em uma página específica dentro do aplicativo.

Veja um cenário que explica por que sua empresa definiria um home page personalizado:

- Dentro de sua rede corporativa, um usuário vai para `https://ExpenseApp/login/login.aspx` entrar e acessar seu aplicativo.
- Como você tem outros ativos (como imagens) que o proxy de aplicativo precisa acessar no nível superior da estrutura de pastas, você publica o aplicativo com `https://ExpenseApp` como a URL interna.
- A URL externa padrão é `https://ExpenseApp-contoso.msappproxy.net` , que não leva um usuário externo à página de entrada.
- `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx`Em vez disso, você deseja definir como a URL de Home Page, para que um usuário externo Veja a página de entrada primeiro.

> [!NOTE]
> Quando você concede aos usuários acesso a aplicativos publicados, os aplicativos são exibidos em [meus aplicativos](../user-help/my-apps-portal-end-user-access.md) e no [iniciador de aplicativos do Office 365](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/).

## <a name="before-you-start"></a>Antes de começar

Antes de definir a URL da página inicial, tenha em mente os seguintes requisitos:

- O caminho especificado deve ser um caminho de subdomínio da URL do domínio raiz.

  Por exemplo, se a URL do domínio raiz for `https://apps.contoso.com/app1/` , a Home Page URL configurada deverá começar com `https://apps.contoso.com/app1/` .

- Se você fizer uma alteração no aplicativo publicado, essa alteração poderá redefinir o valor da URL da home page. Quando você decidir atualizar o aplicativo no futuro, você deverá verificar novamente e, se necessário, atualizar a URL da home page.

Você pode definir a URL de home page por meio do portal do Azure ou usando o PowerShell.

## <a name="change-the-home-page-in-the-azure-portal"></a>Altere a home page no portal do Azure

Para alterar a URL de home page do seu aplicativo por meio do portal do AD do Azure, siga estas etapas:

1. Entre no [Portal do Azure](https://portal.azure.com/) como administrador.
1. Selecione **Azure Active Directory** e, em seguida, **registros de aplicativo**. A lista de aplicativos registrados é exibida.
1. Escolha seu aplicativo na lista. É exibida uma página mostrando os detalhes do aplicativo registrado.
1. Em **gerenciar**, selecione **identidade visual**.
1. Atualize a **URL da Home Page**  com o novo caminho.

   ![Página de identidade visual para um aplicativo registrado mostrando o campo URL da Home Page](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. Clique em **Salvar**.

## <a name="change-the-home-page-with-powershell"></a>Alterar a home page com o PowerShell

Para configurar o home page de um aplicativo usando o PowerShell, você precisa:

1. Instale o módulo do PowerShell do Azure AD.
1. Localize o valor ObjectId do aplicativo.
1. Atualize a URL de home page do aplicativo usando comandos do PowerShell.

### <a name="install-the-azure-ad-powershell-module"></a>Instalar o módulo do Powershell do Azure AD

Antes de definir uma URL personalizada de página inicial usando o PowerShell, instale o módulo PowerShell do Azure AD. Baixe esse pacote da [Galeria do PowerShell](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16), que usa o ponto de extremidade de API do Graph.

Para instalar o pacote, siga estas etapas:

1. Abra uma janela padrão do PowerShell e, em seguida, execute o seguinte comando:

   ```powershell
   Install-Module -Name AzureAD
   ```

    Se você estiver executando o comando como um não administrador, use a opção `-scope currentuser`.

1. Durante a instalação, selecione **Y** para instalar dois pacotes do NuGet.org. Ambos os pacotes são necessários.

### <a name="find-the-objectid-of-the-app"></a>Localizar o ObjectId do aplicativo

Você Obtém o ObjectId do aplicativo procurando pelo aplicativo por seu nome de exibição ou home page.

1. Na janela do PowerShell, importe o módulo do Azure AD.

   ```powershell
   Import-Module AzureAD
   ```

1. Entre no módulo Azure AD como o administrador de locatários.

   ```powershell
   Connect-AzureAD
   ```

1. Localize o aplicativo. Este exemplo usa o PowerShell para localizar o ObjectId pesquisando o aplicativo com um nome de exibição de `SharePoint` .

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   Você deve obter um resultado semelhante ao mostrado aqui. Copie o GUID do ObjectId a ser usado na próxima seção.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   Como alternativa, você pode apenas efetuar pull da lista de todos os aplicativos, Pesquisar na lista o aplicativo com um nome de exibição ou home page específico e copiar o ObjectId do aplicativo quando o aplicativo for encontrado.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>Atualizar a URL da home page

Crie a URL de home page e atualize seu aplicativo com esse valor. Continue usando a mesma janela do PowerShell ou, se você estiver usando uma nova janela do PowerShell, entre no módulo do Azure AD novamente usando `Connect-AzureAD` . Depois, execute estas etapas:

1. Crie uma variável para conter o valor de ObjectId que você copiou na seção anterior. (Substitua o valor ObjectId usado para neste exemplo do SharePoint pelo valor ObjectId do seu aplicativo.)

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. Confirme se você tem o aplicativo correto executando o comando a seguir. A saída deve ser idêntica à saída que você viu na seção anterior ([localize o ObjectID do aplicativo](#find-the-objectid-of-the-app)).

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

1. Faça a atualização do home page.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

1. Para confirmar que a alteração foi bem-sucedida, execute o comando a seguir da etapa 2 novamente.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   Para nosso exemplo, a saída agora deve aparecer da seguinte maneira:

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

1. Reinicie o aplicativo para confirmar se o home page aparece como a primeira tela, conforme esperado.

> [!NOTE]
> Todas as alterações que você fizer no aplicativo poderão redefinir a URL da home page. Se a URL da página inicial for redefinida, repita as etapas nesta seção para defini-la novamente.

## <a name="next-steps"></a>Próximas etapas

- [Habilitar acesso remoto ao SharePoint com o Proxy de Aplicativo do Azure AD](application-proxy-integrate-with-sharepoint-server.md)
- [Tutorial: Adicionar um aplicativo local para acesso remoto por meio do Proxy de Aplicativo no Azure Active Directory](application-proxy-add-on-premises-application.md)
