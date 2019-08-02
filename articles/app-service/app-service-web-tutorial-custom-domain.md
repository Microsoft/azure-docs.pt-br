---
title: Mapear um nome DNS personalizado existente – Serviço de Aplicativo do Azure | Microsoft Docs
description: Saiba como adicionar um nome de domínio DNS personalizado existente (domínio intuitivo) a um aplicativo Web, back-end de aplicativo móvel ou aplicativo de API no Serviço de Aplicativo do Azure.
keywords: serviço do aplicativo, serviço do aplicativo do azure, mapeamento de domínio, nome de domínio, domínio existente, nome de host
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/06/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: bbb064c358eba2dd64ba9ae86540a30cb56adb66
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595025"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Tutorial: Mapear um nome DNS personalizado existente para o Serviço de Aplicativo do Azure

O [Serviço de Aplicativo do Azure](overview.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches. Este tutorial mostra como mapear um nome DNS personalizado existente para o Serviço de Aplicativo do Azure.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Mapear um subdomínio (por exemplo, `www.contoso.com`) usando um registro CNAME
> * Mapear um domínio raiz (por exemplo, `contoso.com`) usando um registro A
> * Mapear um domínio curinga (por exemplo, `*.contoso.com`) usando um registro CNAME
> * Redirecionar a URL padrão para um diretório personalizado
> * Automatizar o mapeamento de domínio com scripts

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* [Crie um aplicativo do Serviço de Aplicativo](/azure/app-service/) ou use um aplicativo que você criou para outro tutorial.
* Compre um nome de domínio e verifique se você tem acesso ao registro DNS do provedor de domínio (como o GoDaddy).

  Por exemplo, para adicionar entradas DNS a `contoso.com` e `www.contoso.com`, você deve poder definir as configurações de DNS do domínio raiz de `contoso.com`.

  > [!NOTE]
  > Caso não tenha um nome de domínio existente, considere a possibilidade de [comprar um domínio usando o portal do Azure](manage-custom-dns-buy-domain.md).

## <a name="prepare-the-app"></a>Preparar o aplicativo

Para mapear um nome DNS personalizado para um aplicativo Web, o [plano do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/) do aplicativo Web deve ser uma camada paga (**Compartilhado**, **Básico**, **Standard**, **Premium** ou **Consumo** para o Azure Functions). Nesta etapa, você verifica se o aplicativo do Serviço de Aplicativo está no tipo de preço com suporte.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="sign-in-to-azure"></a>Entrar no Azure

Abra o [portal do Azure](https://portal.azure.com) e entre com sua conta do Azure.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Navegar para o aplicativo no portal do Azure

No menu à esquerda, selecione **Serviços de Aplicativos** e, em seguida, selecione o nome do aplicativo.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

A página de gerenciamento do aplicativo do Serviço de Aplicativo é exibida.  

<a name="checkpricing" aria-hidden="true"></a>

### <a name="check-the-pricing-tier"></a>Verifique o tipo de preço

No painel de navegação à esquerda da página do aplicativo, role até a seção **Configurações** e selecione **Escalar verticalmente (plano do Serviço de Aplicativo)** .

![Menu Escalar verticalmente](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

A camada atual do aplicativo é realçada por uma borda azul. Verifique se o aplicativo não está na camada **F1**. Não há suporte para DNS personalizado na camada **F1**. 

![Verificar tipo de preço](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Se o plano do Serviço de Aplicativo não estiver na camada **F1**, feche a página **Escalar verticalmente** e vá para [Mapear um registro CNAME](#cname).

<a name="scaleup" aria-hidden="true"></a>

### <a name="scale-up-the-app-service-plan"></a>Escalar verticalmente o plano do Serviço de Aplicativo

Selecione qualquer uma das camadas não gratuitas (**D1**, **B1**, **B2**, **B3** ou qualquer camada na categoria **Produção**). Para obter opções adicionais, clique em **Ver opções adicionais**.

Clique em **Aplicar**.

![Verificar tipo de preço](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Quando você receber a notificação a seguir, a operação de escala terá sido concluída.

![Confirmação da operação de escala](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname" aria-hidden="true"></a>

## <a name="map-your-domain"></a>Mapear seu domínio

Você pode usar um **registro CNAME** ou um **registro A** para mapear um nome DNS personalizado para o serviço de aplicativo. Siga as respectivas etapas:

- [Mapear um registro CNAME](#map-a-cname-record)
- [Mapear um registro A](#map-an-a-record)
- [Mapear um domínio curinga (com um registro CNAME)](#map-a-wildcard-domain)

> [!NOTE]
> Você deve usar registros CNAME para todos os nomes DNS personalizados, exceto os domínios raiz (por exemplo, `contoso.com`). Para os domínios raiz, use registros A.

### <a name="map-a-cname-record"></a>Criar um registro CNAME

No exemplo do tutorial, você adiciona um registro CNAME ao subdomínio `www` (por exemplo, `www.contoso.com`).

#### <a name="access-dns-records-with-domain-provider"></a>Acessar registros DNS com o provedor de domínio

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>Criar um registro CNAME

Adicione um registro CNAME para mapear um subdomínio para o nome do domínio padrão do aplicativo (`<app_name>.azurewebsites.net`, em que `<app_name>` é o nome do aplicativo).

Para o exemplo do domínio `www.contoso.com`, adicione um registro CNAME que mapeia o nome `www` para `<app_name>.azurewebsites.net`.

Depois de adicionar o CNAME, a página de registros DNS será parecida com o seguinte exemplo:

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/cname-record.png)

#### <a name="enable-the-cname-record-mapping-in-azure"></a>Habilitar o mapeamento de registro CNAME no Azure

No painel de navegação à esquerda da página do aplicativo no portal do Azure, selecione **Domínios personalizados**.

![Menu de domínio personalizado](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Na página **Domínios personalizados** do aplicativo, adicione o nome DNS personalizado totalmente qualificado (`www.contoso.com`) à lista.

Selecione o ícone **+** ao lado de **Adicionar domínio personalizado**.

![Adicionar nome do host](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Digite o nome de domínio totalmente qualificado ao qual você adicionou um registro CNAME, como `www.contoso.com`.

Selecione **Validar**.

A página **Adicionar domínio personalizado** é exibida.

Certifique-se de que **Tipo de registro do nome do host** esteja definido como **CNAME (www\.exemplo.com ou qualquer subdomínio)** .

Selecione **Adicionar domínio personalizado**.

![Adicionar nome DNS para o aplicativo](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Pode levar algum tempo para que o novo domínio personalizado seja refletido na página **Domínios personalizados** do aplicativo. Tente atualizar o navegador para atualizar os dados.

![Registro CNAME adicionado](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

> [!NOTE]
> Um rótulo **Não seguro** para o domínio personalizado significa que ele ainda não está associado a um certificado SSL, e qualquer solicitação HTTPS em um navegador para o domínio personalizado receberá um erro ou um aviso, dependendo do navegador. Para adicionar uma associação SSL, confira [Associar um certificado SSL personalizado existente ao Serviço de Aplicativo do Azure](app-service-web-tutorial-custom-ssl.md).

Se você perdeu uma etapa ou cometeu um erro de digitação em algum lugar anteriormente, você verá um erro de verificação na parte inferior da página.

![Erro de verificação](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

### <a name="map-an-a-record"></a>Mapear um registro A

No exemplo do tutorial, você adiciona um registro A ao domínio raiz (por exemplo, `contoso.com`).

<a name="info"></a>

#### <a name="copy-the-apps-ip-address"></a>Copiar o endereço IP do aplicativo

Para mapear um registro A, você precisa do endereço IP externo do aplicativo. Encontre esse endereço IP na página **Domínios personalizados** do aplicativo no portal do Azure.

No painel de navegação à esquerda da página do aplicativo no portal do Azure, selecione **Domínios personalizados**.

![Menu de domínio personalizado](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Na página **domínios personalizados**, copie o endereço IP do aplicativo.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

#### <a name="access-dns-records-with-domain-provider"></a>Acessar registros DNS com o provedor de domínio

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-a-record"></a>Criar um conjunto A de registros

Para mapear um registro A para um aplicativo, o Serviço de Aplicativo exige **dois** registros DNS:

- Um registro **A** a ser mapeado para o endereço IP do aplicativo.
- Um registro **TXT** a ser mapeado para o nome do domínio padrão do aplicativo `<app_name>.azurewebsites.net`. O Serviço de Aplicativo usa esse registro somente em tempo de configuração, para verificar se você possui o domínio personalizado. Depois que o domínio personalizado for validado e configurado no Serviço de Aplicativo, você poderá excluir esse registro TXT.

Para o `contoso.com` exemplo de domínio, crie os registros TXT e acordo com a tabela a seguir (`@` normalmente representa o domínio raiz).

| Tipo de registro | Host | Valor |
| - | - | - |
| O | `@` | Endereço IP de [Copiar o endereço IP do aplicativo](#info) |
| TXT | `@` | `<app_name>.azurewebsites.net` |

> [!NOTE]
> Para adicionar um subdomínio (como `www.contoso.com`) usando um registro A em vez de um [registro CNAME](#map-a-cname-record) recomendado, seu registro A e o registro TXT devem se parecer com a tabela a seguir:
>
> | Tipo de registro | Host | Valor |
> | - | - | - |
> | O | `www` | Endereço IP de [Copiar o endereço IP do aplicativo](#info) |
> | TXT | `www` | `<app_name>.azurewebsites.net` |
>

Quando os registros são adicionados, a página de registros DNS fica parecida com o seguinte exemplo:

![Página de Registros DNS](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a" aria-hidden="true"></a>

#### <a name="enable-the-a-record-mapping-in-the-app"></a>Habilitar o mapeamento de registro A no aplicativo

De volta à página **Domínios personalizados** do aplicativo no portal do Azure, adicione o nome DNS personalizado totalmente qualificado (por exemplo, `contoso.com`) à lista.

Selecione o ícone **+** ao lado de **Adicionar domínio personalizado**.

![Adicionar nome do host](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Digite o nome de domínio totalmente qualificado para o qual você configurou o registro A, como `contoso.com`.

Selecione **Validar**.

A página **Adicionar domínio personalizado** é exibida.

Verifique se **Tipo de registro de nome de host** está definido como **Registro A (example.com)** .

Selecione **Adicionar domínio personalizado**.

![Adicionar nome DNS para o aplicativo](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

Pode levar algum tempo para que o novo domínio personalizado seja refletido na página **Domínios personalizados** do aplicativo. Tente atualizar o navegador para atualizar os dados.

![Registro A adicionado](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

> [!NOTE]
> Um rótulo **Não seguro** para o domínio personalizado significa que ele ainda não está associado a um certificado SSL, e qualquer solicitação HTTPS em um navegador para o domínio personalizado receberá um erro ou um aviso, dependendo do navegador. Para adicionar uma associação SSL, confira [Associar um certificado SSL personalizado existente ao Serviço de Aplicativo do Azure](app-service-web-tutorial-custom-ssl.md).

Se você perdeu uma etapa ou cometeu um erro de digitação em algum lugar anteriormente, você verá um erro de verificação na parte inferior da página.

![Erro de verificação](./media/app-service-web-tutorial-custom-domain/verification-error.png)

<a name="wildcard" aria-hidden="true"></a>

### <a name="map-a-wildcard-domain"></a>Mapear um domínio curinga

No exemplo do tutorial, você mapeia um [nome DNS curinga](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (por exemplo, `*.contoso.com`) para o aplicativo do Serviço de Aplicativo adicionando um registro CNAME.

#### <a name="access-dns-records-with-domain-provider"></a>Acessar registros DNS com o provedor de domínio

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>Criar um registro CNAME

Adicione um registro CNAME para mapear um nome curinga para o nome do domínio padrão do aplicativo (`<app_name>.azurewebsites.net`).

Para o exemplo do domínio `*.contoso.com`, o registro CNAME mapeará o nome `*` para `<app_name>.azurewebsites.net`.

Quando o CNAME é adicionado, a página de registros DNS fica parecida com o seguinte exemplo:

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

#### <a name="enable-the-cname-record-mapping-in-the-app"></a>Habilitar o mapeamento de registro CNAME no aplicativo

Agora você pode adicionar qualquer subdomínio que corresponde o nome curinga ao aplicativo (por exemplo, `sub1.contoso.com` e `sub2.contoso.com` correspondem a `*.contoso.com`).

No painel de navegação à esquerda da página do aplicativo no portal do Azure, selecione **Domínios personalizados**.

![Menu de domínio personalizado](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Selecione o ícone **+** ao lado de **Adicionar domínio personalizado**.

![Adicionar nome do host](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Digite um nome de domínio totalmente qualificado que corresponde ao domínio curinga (por exemplo, `sub1.contoso.com`) e, em seguida, selecione **Validar**.

O botão **Adicionar domínio personalizado** está ativado.

Certifique-se de que **Tipo de registro do nome do host** esteja definido como **registro CNAME (www\.exemplo.com ou qualquer subdomínio)** .

Selecione **Adicionar domínio personalizado**.

![Adicionar nome DNS para o aplicativo](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

Pode levar algum tempo para que o novo domínio personalizado seja refletido na página **Domínios personalizados** do aplicativo. Tente atualizar o navegador para atualizar os dados.

Selecione o ícone **+** novamente para adicionar outro domínio personalizado que corresponde ao domínio curinga. Por exemplo, adicione `sub2.contoso.com`.

![Registro CNAME adicionado](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard2.png)

> [!NOTE]
> Um rótulo **Anotação Segura** para o domínio personalizado significa que ele ainda não está associado a um certificado SSL, e qualquer solicitação HTTPS em um navegador para o domínio personalizado receberá um erro ou um aviso, dependendo do navegador. Para adicionar uma associação SSL, confira [Associar um certificado SSL personalizado existente ao Serviço de Aplicativo do Azure](app-service-web-tutorial-custom-ssl.md).

## <a name="test-in-browser"></a>Testar no navegador

Navegue para os nomes DNS que você configurou anteriormente (por exemplo, `contoso.com`, `www.contoso.com`, `sub1.contoso.com` e `sub2.contoso.com`).

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-not-found"></a>Resolver 404 "Não Encontrado"

Se você receber um erro HTTP 404 (não encontrado) ao navegar para a URL do seu domínio personalizado, verifique se seu domínio é resolvido para o endereço IP do seu aplicativo usando <a href="https://www.whatsmydns.net/" target="_blank">WhatsmyDNS.net</a>. Se não, isso pode acontecer devido a um dos seguintes motivos:

- O domínio personalizado configurado não tem um registro A e/ou um registro CNAME.
- O cliente do navegador armazenou em cache o endereço IP antigo do seu domínio. Desmarque a resolução DNS de teste e cache novamente. Em um computador Windows, limpe o cache com o `ipconfig /flushdns`.

<a name="virtualdir" aria-hidden="true"></a>

## <a name="migrate-an-active-domain"></a>Migrar um domínio ativo

Para migrar um site ativo e seu nome de domínio DNS para o Serviço de Aplicativo sem tempo de inatividade, veja [Migrar um nome DNS ativo para o Serviço de Aplicativo do Azure](manage-custom-dns-migrate-domain.md).

## <a name="redirect-to-a-custom-directory"></a>Redirecionar para um diretório personalizado

Por padrão, o Serviço de Aplicativo direciona solicitações da Web para o diretório raiz do seu código de aplicativo. No entanto, determinadas estruturas da Web não iniciam no diretório raiz. Por exemplo, [Laravel](https://laravel.com/) inicia no subdiretório `public`. Para continuar o exemplo de DNS do `contoso.com`, este aplicativo poderá estar acessível em `http://contoso.com/public`, mas você realmente deseja direcionar o `http://contoso.com` para o diretório `public` em vez disso. Essa etapa não envolve a resolução DNS, mas a personalização do diretório virtual.

Para fazer isso, selecione **Configurações de aplicativo** na navegação à esquerda da página do aplicativo Web. 

Na parte inferior da página, o diretório virtual raiz `/` aponta para `site\wwwroot` por padrão, que é o diretório raiz do seu código de aplicativo. Altere-o para apontar para o `site\wwwroot\public` em vez disso, por exemplo, e salve as alterações.

![Personalizar o diretório virtual](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

Após a conclusão da operação, o aplicativo deverá retornar para a página certa no caminho-raiz (por exemplo, http://contoso.com).

## <a name="automate-with-scripts"></a>Automatizar com scripts

Você pode automatizar o gerenciamento de domínios personalizados com scripts, usando o [CLI do Azure](/cli/azure/install-azure-cli) ou [PowerShell do Azure](/powershell/azure/overview). 

### <a name="azure-cli"></a>CLI do Azure 

O comando a seguir adiciona um nome DNS personalizado configurado para um aplicativo de serviço de aplicativo. 

```bash 
az webapp config hostname add \
    --webapp-name <app_name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

Para obter mais informações, consulte [Mapear um domínio personalizado para um aplicativo Web](scripts/cli-configure-custom-domain.md).

### <a name="azure-powershell"></a>Azure PowerShell 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O comando a seguir adiciona um nome DNS personalizado configurado para um aplicativo de serviço de aplicativo.

```powershell  
Set-AzWebApp `
    -Name <app_name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app_name>.azurewebsites.net")
```

Para obter mais informações, consulte [Atribuir um domínio personalizado para um aplicativo web](scripts/powershell-configure-custom-domain.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Mapear um subdomínio usando um registro CNAME
> * Mapear um domínio raiz usando um registro A
> * Mapear um domínio curinga usando um registro CNAME
> * Redirecionar a URL padrão para um diretório personalizado
> * Automatizar o mapeamento de domínio com scripts

Vá para o próximo tutorial para saber como associar um certificado SSL personalizado a um aplicativo Web.

> [!div class="nextstepaction"]
> [Associar um certificado SSL personalizado existente ao Serviço de Aplicativo do Azure](app-service-web-tutorial-custom-ssl.md)
