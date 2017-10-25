---
title: Mapear um nome DNS personalizado existente para aplicativos Web do Azure | Documentos do Microsoft
description: "Saiba como adicionar um nome de domínio DNS personalizado existente (domínio intuitivo) a um aplicativo Web, back-end de aplicativo móvel ou aplicativo de API no Serviço de Aplicativo do Azure."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/23/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: ac105b82b9bc25e6cc9f5cdf21f34400a09b3934
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a>Mapear um nome DNS personalizado existente para aplicativos Web do Azure

Os [aplicativos Web do Azure](app-service-web-overview.md) fornecem um serviço de hospedagem na Web altamente escalonável,com aplicação automática de patches. Este tutorial mostra como mapear um nome DNS personalizado existente para os Aplicativos Web do Azure.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Mapear um subdomínio (por exemplo, `www.contoso.com`) usando um registro CNAME
> * Mapear um domínio raiz (por exemplo, `contoso.com`) usando um registro A
> * Mapear um domínio curinga (por exemplo, `*.contoso.com`) usando um registro CNAME
> * Automatizar o mapeamento de domínio com scripts

Você pode usar um **registro CNAME** ou um **registro A** para mapear um nome DNS personalizado para o serviço de aplicativo. 

> [!NOTE]
> É recomendável que você use um CNAME para todos os nomes DNS personalizados, exceto um domínio raiz (por exemplo, `contoso.com`).

Para migrar um site ativo e seu nome de domínio DNS para o Serviço de Aplicativo, consulte [Migrar um nome DNS ativo para o Serviço de Aplicativo do Azure](app-service-custom-domain-name-migrate.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* [Crie um aplicativo do Serviço de Aplicativo](/azure/app-service/) ou use um aplicativo que você criou para outro tutorial.
* Compre um nome de domínio e verifique se você tem acesso ao registro DNS do provedor de domínio (como o GoDaddy).

  Por exemplo, para adicionar entradas DNS a `contoso.com` e `www.contoso.com`, você deve poder definir as configurações de DNS do domínio raiz de `contoso.com`.

  > [!NOTE]
  > Caso não tenha um nome de domínio existente, considere a possibilidade de [comprar um domínio usando o portal do Azure](custom-dns-web-site-buydomains-web-app.md). 

## <a name="prepare-the-app"></a>Preparar o aplicativo

Para mapear um nome DNS personalizado para um aplicativo Web, o [plano do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/) do aplicativo Web deve ser uma camada paga (**Compartilhado**, **Básico**, **Standard** ou **Premium**). Nesta etapa, você verifica se o aplicativo do Serviço de Aplicativo está no tipo de preço com suporte.

### <a name="sign-in-to-azure"></a>Entrar no Azure

Abra o [portal do Azure](https://portal.azure.com) e entre com sua conta do Azure.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Navegar para o aplicativo no portal do Azure

No menu à esquerda, selecione **Serviços de Aplicativos** e, em seguida, selecione o nome do aplicativo.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

A página de gerenciamento do aplicativo do Serviço de Aplicativo é exibida.  

<a name="checkpricing"></a>

### <a name="check-the-pricing-tier"></a>Verifique o tipo de preço

No painel de navegação à esquerda da página do aplicativo, role até a seção **Configurações** e selecione **Escalar verticalmente (plano do Serviço de Aplicativo)**.

![Menu Escalar verticalmente](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

A camada atual do aplicativo é realçada por uma borda azul. Verifique se o aplicativo não está na camada **Gratuita**. Não há suporte para DNS personalizado no tipo **Gratuito**. 

![Verificar tipo de preço](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Se o plano do Serviço de Aplicativo não for **Gratuito**, feche a página **Escolher o tipo de preço** e vá para [Mapear um registro CNAME](#cname).

<a name="scaleup"></a>

### <a name="scale-up-the-app-service-plan"></a>Escalar verticalmente o plano do Serviço de Aplicativo

Selecione qualquer uma das camadas não estão livres (**compartilhado**, **básica**, **padrão**, ou **Premium**). 

Clique em **Selecionar**.

![Verificar tipo de preço](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Quando você receber a notificação a seguir, a operação de escala terá sido concluída.

![Confirmação da operação de escala](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname"></a>

## <a name="map-a-cname-record"></a>Criar um registro CNAME

No exemplo do tutorial, você adiciona um registro CNAME ao subdomínio `www` (por exemplo, `www.contoso.com`).

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-cname-record"></a>Criar um registro CNAME

Adicione um registro CNAME para mapear um subdomínio para o nome do host padrão do aplicativo (`<app_name>.azurewebsites.net`, no qual `<app_name>` é o nome do seu aplicativo).

Para o exemplo do domínio `www.contoso.com`, adicione um registro CNAME que mapeia o nome `www` para `<app_name>.azurewebsites.net`.

Depois de adicionar o CNAME, a página de registros DNS será parecida com o seguinte exemplo:

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/cname-record.png)

### <a name="enable-the-cname-record-mapping-in-azure"></a>Habilitar o mapeamento de registro CNAME no Azure

No painel de navegação à esquerda da página do aplicativo no portal do Azure, selecione **Domínios personalizados**. 

![Menu de domínio personalizado](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Na página **Domínios personalizados** do aplicativo, adicione o nome DNS personalizado totalmente qualificado (`www.contoso.com`) à lista.

Selecione o ícone **+** ao lado de **Adicionar nome do host**.

![Adicionar nome do host](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Digite o nome de domínio totalmente qualificado ao qual você adicionou um registro CNAME, como `www.contoso.com`. 

Selecione **Validar**.

O botão **Adicionar nome do host** é ativado. 

Verifique se **Tipo de registro de nome do host** está definido como **CNAME (www.example.com ou um subdomínio)**.

Selecione **Adicionar nome do host**.

![Adicionar nome DNS para o aplicativo](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Pode levar algum tempo para que o novo nome do host seja refletido na página **Domínios personalizados** do aplicativo. Tente atualizar o navegador para atualizar os dados.

![Registro CNAME adicionado](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Se você perdeu uma etapa ou cometeu um erro de digitação em algum lugar anteriormente, você verá um erro de verificação na parte inferior da página.

![Erro de verificação](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a"></a>

## <a name="map-an-a-record"></a>Mapear um registro A

No exemplo do tutorial, você adiciona um registro A ao domínio raiz (por exemplo, `contoso.com`). 

<a name="info"></a>

### <a name="copy-the-apps-ip-address"></a>Copiar o endereço IP do aplicativo

Para mapear um registro A, você precisa do endereço IP externo do aplicativo. Encontre esse endereço IP na página **Domínios personalizados** do aplicativo no portal do Azure.

No painel de navegação à esquerda da página do aplicativo no portal do Azure, selecione **Domínios personalizados**. 

![Menu de domínio personalizado](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Na página **domínios personalizados**, copie o endereço IP do aplicativo.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-a-record"></a>Criar um conjunto A de registros

Para mapear um registro A para um aplicativo, o Serviço de Aplicativo exige **dois** registros DNS:

- Um registro **A** a ser mapeado para o endereço IP do aplicativo.
- Um registro **TXT** a ser mapeado para o nome do host padrão do aplicativo `<app_name>.azurewebsites.net`. O Serviço de Aplicativo usa esse registro somente em tempo de configuração, para verificar se você possui o domínio personalizado. Depois que o domínio personalizado for validado e configurado no Serviço de Aplicativo, você poderá excluir esse registro TXT. 

Para o `contoso.com` exemplo de domínio, crie os registros TXT e acordo com a tabela a seguir (`@` normalmente representa o domínio raiz). 

| Tipo de registro | Host | Valor |
| - | - | - |
| O  | `@` | Endereço IP de [Copiar o endereço IP do aplicativo](#info) |
| TXT | `@` | `<app_name>.azurewebsites.net` |

Quando os registros são adicionados, a página de registros DNS fica parecida com o seguinte exemplo:

![Página de Registros DNS](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a"></a>

### <a name="enable-the-a-record-mapping-in-the-app"></a>Habilitar o mapeamento de registro A no aplicativo

De volta à página **Domínios personalizados** do aplicativo no portal do Azure, adicione o nome DNS personalizado totalmente qualificado (por exemplo, `contoso.com`) à lista.

Selecione o ícone **+** ao lado de **Adicionar nome do host**.

![Adicionar nome do host](./media/app-service-web-tutorial-custom-domain/add-host-name.png)

Digite o nome de domínio totalmente qualificado para o qual você configurou o registro A, como `contoso.com`.

Selecione **Validar**.

O botão **Adicionar nome do host** é ativado. 

Verifique se **Tipo de registro de nome de host** está definido como **Registro A (example.com)**.

Selecione **Adicionar nome do host**.

![Adicionar nome DNS para o aplicativo](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

Pode levar algum tempo para que o novo nome do host seja refletido na página **Domínios personalizados** do aplicativo. Tente atualizar o navegador para atualizar os dados.

![Registro A adicionado](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

Se você perdeu uma etapa ou cometeu um erro de digitação em algum lugar anteriormente, você verá um erro de verificação na parte inferior da página.

![Erro de verificação](./media/app-service-web-tutorial-custom-domain/verification-error.png)

<a name="wildcard"></a>

## <a name="map-a-wildcard-domain"></a>Mapear um domínio curinga

No exemplo do tutorial, você mapeia um [nome DNS curinga](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (por exemplo, `*.contoso.com`) para o aplicativo do Serviço de Aplicativo adicionando um registro CNAME. 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-cname-record"></a>Criar um registro CNAME

Adicione um registro CNAME para mapear um nome curinga para o nome do host padrão do aplicativo (`<app_name>.azurewebsites.net`).

Para o exemplo do domínio `*.contoso.com`, o registro CNAME mapeará o nome `*` para `<app_name>.azurewebsites.net`.

Quando o CNAME é adicionado, a página de registros DNS fica parecida com o seguinte exemplo:

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

### <a name="enable-the-cname-record-mapping-in-the-app"></a>Habilitar o mapeamento de registro CNAME no aplicativo

Agora você pode adicionar qualquer subdomínio que corresponde o nome curinga ao aplicativo (por exemplo, `sub1.contoso.com` e `sub2.contoso.com` correspondem a `*.contoso.com`). 

No painel de navegação à esquerda da página do aplicativo no portal do Azure, selecione **Domínios personalizados**. 

![Menu de domínio personalizado](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Selecione o ícone **+** ao lado de **Adicionar nome do host**.

![Adicionar nome do host](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Digite um nome de domínio totalmente qualificado que corresponde ao domínio curinga (por exemplo, `sub1.contoso.com`) e, em seguida, selecione **Validar**.

O botão **Adicionar nome do host** é ativado. 

Verifique se **Tipo de registro de nome do host** está definido como **Registro CNAME (www.example.com ou um subdomínio)**.

Selecione **Adicionar nome do host**.

![Adicionar nome DNS para o aplicativo](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

Pode levar algum tempo para que o novo nome do host seja refletido na página **Domínios personalizados** do aplicativo. Tente atualizar o navegador para atualizar os dados.

Selecione o ícone **+** novamente para adicionar outro nome de host que corresponde ao domínio curinga. Por exemplo, adicione `sub2.contoso.com`.

![Registro CNAME adicionado](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard2.png)

## <a name="test-in-browser"></a>Testar no navegador

Navegue para os nomes DNS que você configurou anteriormente (por exemplo, `contoso.com`, `www.contoso.com`, `sub1.contoso.com` e `sub2.contoso.com`).

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="automate-with-scripts"></a>Automatizar com scripts

Você pode automatizar o gerenciamento de domínios personalizados com scripts, usando o [CLI do Azure](/cli/azure/install-azure-cli) ou [PowerShell do Azure](/powershell/azure/overview). 

### <a name="azure-cli"></a>CLI do Azure 

O comando a seguir adiciona um nome DNS personalizado configurado para um aplicativo de serviço de aplicativo. 

```bash 
az appservice web config hostname add \
    --webapp <app_name> \
    --resource-group <resource_group_name> \ 
    --name <fully_qualified_domain_name> 
``` 

Para obter mais informações, consulte [Mapear um domínio personalizado para um aplicativo Web](scripts/app-service-cli-configure-custom-domain.md). 

### <a name="azure-powershell"></a>Azure PowerShell 

O comando a seguir adiciona um nome DNS personalizado configurado para um aplicativo de serviço de aplicativo. 

```PowerShell  
Set-AzureRmWebApp `
    -Name <app_name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app_name>.azurewebsites.net") 
```

Para obter mais informações, consulte [Atribuir um domínio personalizado para um aplicativo web](scripts/app-service-powershell-configure-custom-domain.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Mapear um subdomínio usando um registro CNAME
> * Mapear um domínio raiz usando um registro A
> * Mapear um domínio curinga usando um registro CNAME
> * Automatizar o mapeamento de domínio com scripts

Vá para o próximo tutorial para saber como associar um certificado SSL personalizado a um aplicativo Web.

> [!div class="nextstepaction"]
> [Associar um certificado SSL personalizado existente a Aplicativos Web do Azure](app-service-web-tutorial-custom-ssl.md)
