---
title: 'Tutorial: Mapear um nome DNS personalizado existente'
description: Saiba como adicionar um nome de domínio DNS personalizado existente (domínio intuitivo) a um aplicativo Web, um back-end de aplicativo móvel ou um aplicativo de API no Serviço de Aplicativo do Azure.
keywords: serviço do aplicativo, serviço do aplicativo do azure, mapeamento de domínio, nome de domínio, domínio existente, nome de host
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/25/2020
ms.custom: mvc, seodec18
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./app-service-web-tutorial-custom-domain-uiex
ms.openlocfilehash: 216c96579944661ecf248e4e8676ac45021c53e6
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2021
ms.locfileid: "106293950"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Tutorial: Mapear um nome DNS personalizado existente para o Serviço de Aplicativo do Azure

O [Serviço de Aplicativo do Azure](overview.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches. Este tutorial mostra como mapear um nome DNS (Sistema de Nomes de Domínio) personalizado existente para o Serviço de Aplicativo.

![Captura de tela que mostra a navegação no portal do Azure para um aplicativo do Azure.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Mapear um subdomínio (por exemplo, `www.contoso.com`) usando um registro CNAME.
> * Mapear um domínio raiz (por exemplo, `contoso.com`) usando um registro A.
> * Mapear um domínio curinga (por exemplo, `*.contoso.com`) usando um registro CNAME.
> * Redirecionar a URL padrão para um diretório personalizado.
> * Automatizar o mapeamento de domínio com scripts.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* [Crie um aplicativo do Serviço de Aplicativo](./index.yml) ou use um aplicativo que você criou para outro tutorial.
* Compre um nome de domínio e verifique se você tem acesso ao registro DNS do provedor de domínio (como o GoDaddy).

  Por exemplo, para adicionar entradas DNS a `contoso.com` e `www.contoso.com`, você deve poder definir as configurações de DNS do domínio raiz de `contoso.com`.

  > [!NOTE]
  > Caso você não tenha um nome de domínio, considere a possibilidade de [comprar um domínio usando o portal do Azure](manage-custom-dns-buy-domain.md).

## <a name="prepare-the-app"></a>Preparar o aplicativo

Para mapear um nome DNS personalizado para um aplicativo Web, o [Plano do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/) do aplicativo Web precisa ser uma camada paga (Compartilhado, Básico, Standard, Premium ou Consumo para o Azure Functions). Nesta etapa, você verifica se o aplicativo do Serviço de Aplicativo está no tipo de preço com suporte.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

  > [!NOTE]
  > A remoção ou a adição de um domínio personalizado ao seu aplicativo Web fará com que o seu site seja reiniciado.
### <a name="sign-in-to-azure"></a>Entrar no Azure

Abra o [portal do Azure](https://portal.azure.com) e entre com a sua conta do Azure.

### <a name="select-the-app-in-the-azure-portal"></a>Selecionar o aplicativo no Portal do Microsoft Azure

1. Pesquise e selecione **Serviços de Aplicativos**.

   ![Captura de tela que mostra a seleção de Serviços de Aplicativos.](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. Na página **Serviços de Aplicativos**, selecione o nome do seu aplicativo do Azure.

   ![Captura de tela mostrando a navegação do portal para um aplicativo do Azure.](./media/app-service-web-tutorial-custom-domain/select-app.png)

A página de gerenciamento do aplicativo do Serviço de Aplicativo é exibida.

<a name="checkpricing" aria-hidden="true"></a>

### <a name="check-the-pricing-tier"></a>Verifique o tipo de preço

1. No painel esquerdo da página do aplicativo, role a página até a seção **Configurações** e selecione **Aumentar (Plano do Serviço de Aplicativo)** .

   ![Captura de tela que mostra o menu Aumentar (Plano do Serviço de Aplicativo).](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. A camada atual do aplicativo é realçada por uma borda azul. Verifique se o aplicativo não está na camada **F1**. Não há suporte para o DNS personalizado na camada **F1**.

   ![Captura de tela que mostra tipos de preço recomendados.](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. Se o Plano do Serviço de Aplicativo não estiver na camada **F1**, feche a página **Aumentar** e acesse [Mapear um registro CNAME](#map-a-cname-record).

<a name="scaleup" aria-hidden="true"></a>

### <a name="scale-up-the-app-service-plan"></a>Escalar verticalmente o plano do Serviço de Aplicativo

1. Selecione qualquer uma das camadas não gratuitas (**D1**, **B1**, **B2**, **B3** ou qualquer camada na categoria **Produção**). Para obter mais opções, selecione **Ver opções adicionais**.

1. Selecione **Aplicar**.

   ![Captura de tela que mostra como verificar o tipo de preço.](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   Quando você receber a notificação a seguir, a operação de escala terá sido concluída.

   ![Captura de tela que mostra a confirmação da operação de escala.](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname" aria-hidden="true"></a>

## <a name="get-a-domain-verification-id"></a>Obter a ID de verificação de domínio

Para adicionar um domínio personalizado ao seu aplicativo, você precisa verificar sua propriedade do domínio adicionando uma ID de verificação como um registro TXT com seu provedor de domínio. No painel esquerdo da página do aplicativo, selecione **Domínios personalizados**. Copie a ID da caixa **ID de Verificação de Domínio Personalizado** na página **Domínios Personalizados** para a próxima etapa.

![Captura de tela que mostra a ID na caixa ID de Verificação de Domínio Personalizado.](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

> [!WARNING]
> A adição de IDs de verificação de domínio ao domínio personalizado pode impedir entradas DNS pendentes e ajudar a evitar a tomada de controle de subdomínio. Para os domínios personalizados configurados anteriormente sem essa ID de verificação, você deverá protegê-los do mesmo risco adicionando a ID de verificação ao seu registro DNS. Para obter mais informações sobre essa ameaça comum de alta severidade, confira [Tomada de controle de subdomínio](../security/fundamentals/subdomain-takeover.md).

## <a name="map-your-domain"></a>Mapear seu domínio

Você pode usar um registro CNAME ou um registro A para mapear um nome DNS personalizado para o serviço de aplicativo. Siga as respectivas etapas:

- [Mapear um registro CNAME](#map-a-cname-record)
- [Mapear um registro A](#map-an-a-record)
- [Mapear um domínio curinga (com um registro CNAME)](#map-a-wildcard-domain)

> [!NOTE]
> Você deve usar registros CNAME para todos os nomes DNS personalizados, exceto os domínios raiz (por exemplo, `contoso.com`). Para os domínios raiz, use registros A.

### <a name="map-a-cname-record"></a>Criar um registro CNAME

No exemplo do tutorial, você adiciona um registro CNAME ao subdomínio `www` (por exemplo, `www.contoso.com`).

Se você tiver um subdomínio diferente de `www`, substitua `www` pelo subdomínio (por exemplo, por `sub` se o seu domínio personalizado for `sub.constoso.com`).

#### <a name="access-dns-records-with-a-domain-provider"></a>Acessar registros DNS com o provedor de domínio

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>Criar um registro CNAME

Mapeie um subdomínio para o nome do domínio padrão do aplicativo (`<app-name>.azurewebsites.net`, em que `<app-name>` é o nome do aplicativo). Para criar um mapeamento CNAME para o subdomínio `www`, crie dois registros:

| Tipo de registro | Host | Valor | Comentários |
| - | - | - |
| CNAME | `www` | `<app-name>.azurewebsites.net` | O mapeamento de domínio em si. |
| TXT | `asuid.www` | [A ID de verificação que você obteve anteriormente](#get-a-domain-verification-id) | O Serviço de Aplicativo acessa o registro TXT `asuid.<subdomain>` para verificar sua propriedade do domínio personalizado. |

Depois de adicionar os registros CNAME e TXT, a página de registros DNS será parecida com o seguinte exemplo:

![Captura de tela que mostra a navegação no portal para um aplicativo do Azure.](./media/app-service-web-tutorial-custom-domain/cname-record.png)

#### <a name="enable-the-cname-record-mapping-in-azure"></a>Habilitar o mapeamento de registro CNAME no Azure

1. No painel esquerdo da página do aplicativo no portal do Azure, selecione **Domínios personalizados**.

    ![Captura de tela que mostra o menu Domínios personalizados.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Na página **Domínios personalizados** do aplicativo, adicione o nome DNS personalizado totalmente qualificado (`www.contoso.com`) à lista.

1. Selecione **Adicionar domínio personalizado**.

    ![Captura de tela que mostra o item Adicionar nome do host.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Digite o nome de domínio totalmente qualificado ao qual você adicionou um registro CNAME, como `www.contoso.com`.

1. Selecione **Validar**. A página **Adicionar domínio personalizado** será exibida.

1. Certifique-se de que **Tipo de registro do nome do host** esteja definido como **CNAME (www\.exemplo.com ou qualquer subdomínio)** . Selecione **Adicionar domínio personalizado**.

    ![Captura de tela que mostra o botão Adicionar domínio personalizado.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    Pode levar algum tempo para que o novo domínio personalizado seja refletido na página **Domínios Personalizados** do aplicativo. Atualize o navegador para atualizar os dados.

    ![Captura de tela que mostra como adicionar o registro CNAME.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > Um rótulo de aviso do domínio personalizado significa que ele ainda não está associado a um certificado TLS/SSL. As solicitações HTTPS de um navegador para o seu domínio personalizado receberão um erro ou um aviso, dependendo do navegador. Para adicionar uma associação de TLS, confira [Proteger um nome DNS personalizado com uma associação TLS/SSL no Serviço de Aplicativo do Azure](configure-ssl-bindings.md).

    Se você perdeu uma etapa ou cometeu um erro de digitação em algum lugar anteriormente, verá um erro de verificação na parte inferior da página.

    ![Captura de tela que mostra um erro de verificação.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

### <a name="map-an-a-record"></a>Mapear um registro A

No exemplo do tutorial, você adiciona um registro A ao domínio raiz (por exemplo, `contoso.com`).

<a name="info"></a>

#### <a name="copy-the-apps-ip-address"></a>Copiar o endereço IP do aplicativo

Para mapear um registro A, você precisa do endereço IP externo do aplicativo. Encontre esse endereço IP na página **Domínios Personalizados** do aplicativo no portal do Azure.

1. No painel esquerdo da página do aplicativo no portal do Azure, selecione **Domínios personalizados**.

   ![Captura de tela que mostra o menu Domínios personalizados.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Na página **Domínios Personalizados**, copie o endereço IP do aplicativo.

   ![Captura de tela que mostra a navegação do portal para um aplicativo do Azure.](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

#### <a name="access-dns-records-with-a-domain-provider"></a>Acessar registros DNS com o provedor de domínio

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-a-record"></a>Criar um conjunto A de registros

Para mapear um registro A para um aplicativo, geralmente para o domínio raiz, crie dois registros:

| Tipo de registro | Host | Valor | Comentários |
| - | - | - |
| Um | `@` | Endereço IP de [Copiar o endereço IP do aplicativo](#info) | O mapeamento de domínio em si (`@` normalmente representa o domínio raiz). |
| TXT | `asuid` | [A ID de verificação que você obteve anteriormente](#get-a-domain-verification-id) | O Serviço de Aplicativo acessa o registro TXT `asuid.<subdomain>` para verificar sua propriedade do domínio personalizado. Para o domínio raiz, use `asuid`. |

> [!NOTE]
> Para adicionar um subdomínio (como `www.contoso.com`) usando um registro A em vez de um [registro CNAME](#map-a-cname-record) recomendado, o registro A e o registro TXT devem ser parecidos com a seguinte tabela:
>
> | Tipo de registro | Host | Valor |
> | - | - | - |
> | Um | `www` | Endereço IP de [Copiar o endereço IP do aplicativo](#info) |
> | TXT | `asuid.www` | [A ID de verificação que você obteve anteriormente](#get-a-domain-verification-id) |
>

Quando os registros são adicionados, a página de registros DNS é parecida com o seguinte exemplo:

![Captura de tela que mostra uma página de registros DNS.](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a" aria-hidden="true"></a>

#### <a name="enable-the-a-record-mapping-in-the-app"></a>Habilitar o mapeamento de registro A no aplicativo

De volta à página **Domínios Personalizados** do aplicativo no portal do Azure, adicione o nome DNS personalizado totalmente qualificado (por exemplo, `contoso.com`) à lista.

1. Selecione **Adicionar domínio personalizado**.

    ![Captura de tela que mostra como adicionar um nome do host.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Digite o nome de domínio totalmente qualificado para o qual você configurou o registro A, como `contoso.com`. 

1. Selecione **Validar**. A página **Adicionar domínio personalizado** é exibida.

1. Verifique se **Tipo de registro de nome de host** está definido como **Registro A (example.com)** . Selecione **Adicionar domínio personalizado**.

    ![Captura de tela que mostra como adicionar um nome DNS ao aplicativo.](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    Pode levar algum tempo para que o novo domínio personalizado seja refletido na página **Domínios Personalizados** do aplicativo. Atualize o navegador para atualizar os dados.

    ![Captura de tela que mostra como adicionar um registro A.](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    > [!NOTE]
    > Um rótulo de aviso do domínio personalizado significa que ele ainda não está associado a um certificado TLS/SSL. As solicitações HTTPS de um navegador para o seu domínio personalizado receberão um erro ou um aviso, dependendo do navegador. Para adicionar uma associação de TLS, confira [Proteger um nome DNS personalizado com uma associação TLS/SSL no Serviço de Aplicativo do Azure](configure-ssl-bindings.md).
    
    Se você perdeu uma etapa ou cometeu um erro de digitação em algum lugar anteriormente, verá um erro de verificação na parte inferior da página.
    
    ![Captura de tela mostrando um erro de verificação.](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
<a name="wildcard" aria-hidden="true"></a>

### <a name="map-a-wildcard-domain"></a>Mapear um domínio curinga

No exemplo do tutorial, você mapeia um [nome DNS curinga](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (por exemplo, `*.contoso.com`) para o aplicativo do Serviço de Aplicativo adicionando um registro CNAME.

#### <a name="access-dns-records-with-a-domain-provider"></a>Acessar registros DNS com o provedor de domínio

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>Criar um registro CNAME

Mapeie um nome de domínio curinga `*` para o nome do domínio padrão do aplicativo (`<app-name>.azurewebsites.net`, em que `<app-name>` é o nome do aplicativo). Para mapear o nome curinga, crie dois registros:

| Tipo de registro | Host | Valor | Comentários |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | O mapeamento de domínio em si. |
| TXT | `asuid` | [A ID de verificação que você obteve anteriormente](#get-a-domain-verification-id) | O Serviço de Aplicativo acessa o registro TXT `asuid` para verificar sua propriedade do domínio personalizado. |

Para o exemplo do domínio `*.contoso.com`, o registro CNAME mapeará o nome `*` para `<app-name>.azurewebsites.net`.

Quando o CNAME é adicionado, a página de registros DNS fica parecida com o seguinte exemplo:

![Captura de tela que mostra a navegação para um aplicativo do Azure.](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

#### <a name="enable-the-cname-record-mapping-in-the-app"></a>Habilitar o mapeamento de registro CNAME no aplicativo

Agora você pode adicionar qualquer subdomínio que corresponda o nome curinga ao aplicativo (por exemplo, `sub1.contoso.com`, `sub2.contoso.com` e `*.contoso.com` correspondem a `*.contoso.com`).

1. No painel esquerdo da página do aplicativo no portal do Azure, selecione **Domínios personalizados**.

    ![Captura de tela que mostra o menu Domínios personalizados.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Selecione **Adicionar domínio personalizado**.

    ![Captura de tela que mostra como adicionar um nome do host.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Digite um nome de domínio totalmente qualificado que corresponde ao domínio curinga (por exemplo, `sub1.contoso.com`) e, em seguida, selecione **Validar**.

    O botão **Adicionar domínio personalizado** está ativado.

1. Certifique-se de que **Tipo de registro do nome do host** esteja definido como **registro CNAME (www\.exemplo.com ou qualquer subdomínio)** . Selecione **Adicionar domínio personalizado**.

    ![Captura de tela que mostra como adicionar um nome DNS ao aplicativo.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    Pode levar algum tempo para que o novo domínio personalizado seja refletido na página **Domínios Personalizados** do aplicativo. Atualize o navegador para atualizar os dados.

1. Selecione o ícone **+** novamente para adicionar outro domínio personalizado que corresponde ao domínio curinga. Por exemplo, adicione `sub2.contoso.com`.

    ![Captura de tela que mostra como adicionar um registro CNAME.](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard-2.png)

    > [!NOTE]
    > Um rótulo de aviso do domínio personalizado significa que ele ainda não está associado a um certificado TLS/SSL. As solicitações HTTPS de um navegador para o seu domínio personalizado receberão um erro ou um aviso, dependendo do navegador. Para adicionar uma associação de TLS, confira [Proteger um nome DNS personalizado com uma associação TLS/SSL no Serviço de Aplicativo do Azure](configure-ssl-bindings.md).
    
## <a name="test-in-a-browser"></a>Testar em um navegador

Procure os nomes DNS configurados anteriormente (por exemplo, `contoso.com`, `www.contoso.com`, `sub1.contoso.com` e `sub2.contoso.com`).

![Captura de tela que mostra a navegação para um aplicativo do Azure.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-not-found"></a>Resolver 404 "Não Encontrado"

Se você receber um erro HTTP 404 (Não Encontrado) ao navegar para a URL do seu domínio personalizado, verifique se o domínio é resolvido para o endereço IP do aplicativo usando <a href="https://www.nslookup.io/" target="_blank">nslookup.io</a>. Caso contrário, verifique se os registros A e CNAME estão configurados corretamente usando o mesmo site. Se resolver o IP corretamente, mas você ainda estiver recebendo um 404, o navegador poderá ter armazenado em cache o endereço IP antigo do seu domínio. Desmarque o cache e teste a resolução DNS novamente. Em um computador Windows, limpe o cache com o `ipconfig /flushdns`.

## <a name="migrate-an-active-domain"></a>Migrar um domínio ativo

Para migrar um site ativo e seu nome de domínio DNS para o Serviço de Aplicativo sem tempo de inatividade, veja [Migrar um nome DNS ativo para o Serviço de Aplicativo do Azure](manage-custom-dns-migrate-domain.md).

<a name="virtualdir" aria-hidden="true"></a>

## <a name="redirect-to-a-custom-directory"></a>Redirecionar para um diretório personalizado

Por padrão, o Serviço de Aplicativo direciona solicitações da Web para o diretório raiz do seu código de aplicativo. No entanto, algumas estruturas da Web não são iniciadas no diretório raiz. Por exemplo, [Laravel](https://laravel.com/) inicia no subdiretório `public`. Para continuar o exemplo de DNS `contoso.com`, este aplicativo poderá estar acessível em `http://contoso.com/public`, mas o ideal é direcionar `http://contoso.com` para o diretório `public`. Essa etapa não envolve a resolução DNS, mas trata da personalização do diretório virtual.

Para personalizar um diretório virtual para aplicativos do Windows, selecione **Configurações de aplicativo** no painel esquerdo da página do aplicativo Web. 

> [!NOTE]
> Os aplicativos do Linux não têm essa página. Para alterar a raiz do site para aplicativos do Linux, confira os guias de configuração específicos à linguagem de programação ([PHP](configure-language-php.md?pivots=platform-linux#change-site-root), por exemplo).

Na parte inferior da página, o diretório virtual raiz `/` aponta para `site\wwwroot` por padrão, que é o diretório raiz do seu código de aplicativo. Altere-o para apontar para o `site\wwwroot\public` em vez disso, por exemplo, e salve as alterações.

![Captura de tela que mostra como personalizar um diretório virtual.](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

Após a conclusão da operação, o aplicativo deverá retornar para a página certa no caminho raiz (por exemplo, `http://contoso.com`).

## <a name="automate-with-scripts"></a>Automatizar com scripts

Você pode automatizar o gerenciamento de domínios personalizados com scripts usando a [CLI do Azure](/cli/azure/install-azure-cli) ou o [Azure PowerShell](/powershell/azure/).

### <a name="azure-cli"></a>CLI do Azure

O comando a seguir adiciona um nome DNS personalizado configurado para um aplicativo de serviço de aplicativo.

```bash 
az webapp config hostname add \
    --webapp-name <app-name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

Para obter mais informações, consulte [Mapear um domínio personalizado para um aplicativo Web](scripts/cli-configure-custom-domain.md).

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O comando a seguir adiciona um nome DNS personalizado configurado para um aplicativo de serviço de aplicativo.

```powershell  
Set-AzWebApp `
    -Name <app-name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app-name>.azurewebsites.net")
```

Para obter mais informações, consulte [Atribuir um domínio personalizado para um aplicativo web](scripts/powershell-configure-custom-domain.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Mapear um subdomínio usando um registro CNAME.
> * Mapear um domínio raiz usando um registro A.
> * Mapear um domínio curinga usando um registro CNAME.
> * Redirecionar a URL padrão para um diretório personalizado.
> * Automatizar o mapeamento de domínio com scripts.

Prossiga para o próximo tutorial e saiba como associar um certificado TLS/SSL personalizado a um aplicativo Web.

> [!div class="nextstepaction"]
> [Proteger um nome DNS personalizado com uma associação TLS/SSL no Serviço de Aplicativo do Azure](configure-ssl-bindings.md)
