---
title: "Adicionar uma CDN a um Serviço de Aplicativo do Azure | Microsoft Docs"
description: "Adicione uma rede de distribuição de conteúdo (CDN) para um serviço de aplicativo do Azure para armazenar em cache e entregar os arquivos estáticos de servidores perto de seus clientes em todo o mundo."
services: app-service\web
author: syntaxc4
ms.author: cfowler
ms.date: 05/31/2017
ms.topic: tutorial
ms.service: app-service-web
manager: erikre
ms.workload: web
ms.custom: mvc
ms.openlocfilehash: 257b75d01f3904661c1a188a2d53ffcb74f48f06
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="add-a-content-delivery-network-cdn-to-an-azure-app-service"></a>Como adicionar uma Rede de distribuição de conteúdo (CDN) em um Serviço de Aplicativo do Azure

[A Rede de distribuição de conteúdo (CDN) do Azure](../cdn/cdn-overview.md) armazena em cache conteúdo Web estático em locais estrategicamente posicionados para fornecer uma taxa de transferência máxima para a distribuição de conteúdo aos usuários. O CDN também reduz a carga do servidor no seu aplicativo web. Este tutorial mostra como adicionar o CDN do Azure para um [aplicativo web no serviço de aplicativo do Azure](app-service-web-overview.md). 

Segue a home page do site de exemplo estático em HTML que você usará para trabalhar:

![Home page do aplicativo de exemplo](media/app-service-web-tutorial-content-delivery-network/sample-app-home-page.png)

O que você aprenderá:

> [!div class="checklist"]
> * Criar um novo ponto de extremidade do CDN.
> * Atualizar ativos em cache.
> * Usar cadeias de caracteres de consulta para controlar versões armazenadas em cache.
> * Usar um domínio personalizado para o ponto de extremidade do CDN.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- [Instalar o Git](https://git-scm.com/)
- [Instalar a CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-web-app"></a>Criar o aplicativo Web

Para criar o aplicativo Web com que você trabalhará, execute o [Início rápido HTML estático](app-service-web-get-started-html.md) na etapa **Navegar até o aplicativo**.

### <a name="have-a-custom-domain-ready"></a>Tenha um domínio personalizado pronto

Para concluir a etapa de domínio personalizado deste tutorial, você precisa possuir um domínio personalizado e ter acesso ao registro DNS do seu provedor de domínio (como GoDaddy). Por exemplo, para adicionar entradas DNS para `contoso.com` e `www.contoso.com`, você deve ter acesso para definir as configurações de DNS para o `contoso.com` domínio raiz.

Se você ainda não tiver um domínio de nome, considere concluir o [Tutorial de domínio do serviço de aplicativo](custom-dns-web-site-buydomains-web-app.md) para adquirir um domínio usando o portal do Azure. 

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure

Abra um navegador e navegue até o [portal do Azure](https://portal.azure.com).

## <a name="create-a-cdn-profile-and-endpoint"></a>Como criar um perfil do CDN e um ponto de extremidade

No painel de navegação esquerdo, selecione **Serviços de aplicativos** e, em seguida, selecione o aplicativo que você criou no [Início rápido do HTML estático](app-service-web-get-started-html.md).

![Selecione o aplicativo de serviço de aplicativo no portal](media/app-service-web-tutorial-content-delivery-network/portal-select-app-services.png)

Na página **Serviço de aplicativo**, na seção **Configurações**, selecione **Rede > Configurar CDN do Azure para seu aplicativo**.

![Selecione CDN no portal](media/app-service-web-tutorial-content-delivery-network/portal-select-cdn.png)

Na página da **Rede de distribuição de conteúdo do Azure**, forneça as configurações do **ponto de extremidade novo** conforme especificado na tabela.

![Como criar perfil e o ponto de extremidade no portal](media/app-service-web-tutorial-content-delivery-network/portal-new-endpoint.png)

| Configuração | Valor sugerido | Descrição |
| ------- | --------------- | ----------- |
| **Perfil do CDN** | myCDNProfile | Selecione **Criar novo** para criar um perfil de CDN. Um perfil do CDN é uma coleção de pontos de extremidade do CDN com o mesmo tipo de preços. |
| **Tipo de preços** | Akamai Standard | O [tipo de preços](../cdn/cdn-overview.md#azure-cdn-features) especifica o provedor e os recursos disponíveis. Neste tutorial, estamos usando o Akamai padrão. |
| **Nome do ponto de extremidade do CDN** | Qualquer nome que seja exclusivo no domínio azureedge.net | Acesse os recursos armazenados em cache no domínio  *\<endpointname >. azureedge.net*.

Selecione **Criar**.

O Azure cria o perfil e o ponto de extremidade. O ponto de extremidade novo aparece na lista de **pontos de extremidade** na mesma página e, quando ele tiver provisionado, o status será **Executando**.

![Ponto de extremidade novo na lista](media/app-service-web-tutorial-content-delivery-network/portal-new-endpoint-in-list.png)

### <a name="test-the-cdn-endpoint"></a>Testar o ponto de extremidade CDN

Se você selecionou o tipo de preços da Verizon, normalmente demora cerca de 90 minutos para a propagação de ponto de extremidade. Para Akamai, leva alguns minutos para ocorrer a propagação

O aplicativo de exemplo possui um arquivo `index.html` e as pastas *css*, *img* e *js* que contêm outros ativos estáticos. Os caminhos de conteúdo para todos esses arquivos são os mesmos no ponto de extremidade do CDN. Por exemplo, as duas URLs a seguir acessam o arquivo *bootstrap.css* na pasta *css*:

```
http://<appname>.azurewebsites.net/css/bootstrap.css
```

```
http://<endpointname>.azureedge.net/css/bootstrap.css
```

Navegue em um navegador até a URL abaixo:

```
http://<endpointname>.azureedge.net/index.html
```

![Home page do aplicativo de exemplo distribuídos a partir do CDN](media/app-service-web-tutorial-content-delivery-network/sample-app-home-page-cdn.png)

 Consulte a mesma página que você executou anteriormente em um aplicativo Web do Azure. A CDN do Azure recuperou os ativos do aplicativo web de origem e está servindo-os do ponto de extremidade da CDN

Para garantir que essa página está armazenada em cache no CDN, atualize a página. Duas solicitações para o mesmo ativo, às vezes, são necessárias para que o CDN armazene em cache o conteúdo solicitado.

Para obter mais informações sobre como criar perfis do CDN do Azure e pontos de extremidade, confira [Introdução ao CDN do Azure](../cdn/cdn-create-new-endpoint.md).

## <a name="purge-the-cdn"></a>Limpar o CDN

O CDN atualiza periodicamente seus recursos do aplicativo web de origem com base na configuração de tempo de vida (TTL). A TTL padrão é de sete dias.

Às vezes você precisará atualizar o CDN antes da expiração do TTL, por exemplo, quando você implanta o conteúdo atualizado para o aplicativo web. Para disparar uma atualização, você pode limpar manualmente os recursos do CDN. 

Nesta seção do tutorial, você implanta uma alteração para o aplicativo web e limpa o CDN para disparar o CDN para que ele atualize seu cache.

### <a name="deploy-a-change-to-the-web-app"></a>Implantar uma alteração no aplicativo web

Abra o arquivo `index.html` e adicione "-V2" para o título H1, conforme mostrado no exemplo a seguir: 

```
<h1>Azure App Service - Sample Static HTML Site - V2</h1>
```

Confirme as alterações para implantá-las no aplicativo web.

```bash
git commit -am "version 2"
git push azure master
```

Depois de concluir a implantação, navegue até a URL do aplicativo web e confira a alteração.

```
http://<appname>.azurewebsites.net/index.html
```

![V2 no título no aplicativo web](media/app-service-web-tutorial-content-delivery-network/v2-in-web-app-title.png)

Navegue até a URL de ponto de extremidade do CDN da home page, você não visualizará a alteração porque a versão armazenada em cache no CDN ainda não expirou. 

```
http://<endpointname>.azureedge.net/index.html
```

![Nenhum V2 no título no CDN](media/app-service-web-tutorial-content-delivery-network/no-v2-in-cdn-title.png)

### <a name="purge-the-cdn-in-the-portal"></a>Limpar o CDN no portal

Para disparar o CDN para atualizar sua versão armazenada em cache, limpe o CDN.

Na navegação à esquerda do portal, selecione **Grupos de recursos** e, em seguida, selecione o grupo de recursos que você criou para seu aplicativo web (myResourceGroup).

![Escolha o grupo de recursos](media/app-service-web-tutorial-content-delivery-network/portal-select-group.png)

Na lista de recursos, selecione o ponto de extremidade do CDN.

![Selecione o ponto de extremidade](media/app-service-web-tutorial-content-delivery-network/portal-select-endpoint.png)

Na parte superior da página **Ponto de extremidade**, clique em **Limpar**.

![Selecione Limpar](media/app-service-web-tutorial-content-delivery-network/portal-select-purge.png)

Digite os caminhos de conteúdo que você deseja limpar. Você pode passar um caminho de arquivo completo para limpar um arquivo específico ou um segmento de caminho para limpar e atualizar o conteúdo de uma pasta. Como você alterou `index.html`, certifique-se de que seja um dos caminhos.

Na parte inferior da página, selecione **Limpar**.

![Limpar página](media/app-service-web-tutorial-content-delivery-network/app-service-web-purge-cdn.png)

### <a name="verify-that-the-cdn-is-updated"></a>Verifique se o CDN está atualizado

Aguarde até que a solicitação de limpeza conclua o processamento, normalmente demora alguns minutos. Para conferir o status atual, selecione o ícone de sino na parte superior da página. 

![Limpar notificação](media/app-service-web-tutorial-content-delivery-network/portal-purge-notification.png)

Navegue até a URL de ponto de extremidade do CDN para `index.html`, agora você pode ver o V2 que você adicionou ao título da home page. Isso mostra que o cache do CDN foi atualizado.

```
http://<endpointname>.azureedge.net/index.html
```

![V2 no título no CDN](media/app-service-web-tutorial-content-delivery-network/v2-in-cdn-title.png)

Para obter mais informações, confira [Como limpar um ponto de extremidade do CDN do Azure](../cdn/cdn-purge-endpoint.md). 

## <a name="use-query-strings-to-version-content"></a>Use cadeias de caracteres de consulta para o conteúdo da versão

O CDN do Azure oferece as seguintes opções de comportamento do armazenamento em cache:

* Ignorar as cadeias de caracteres de consulta
* Ignorar o cache para cadeias de caracteres de consulta
* Armazenar em cache todas as URLs exclusivas 

A primeira delas é o padrão, o que significa que há apenas uma versão em cache de um ativo, independentemente da cadeia de caracteres de consulta na URL. 

Nesta seção do tutorial, você deve alterar o comportamento do armazenamento em cache para armazenar em cache todas as URLs exclusivas.

### <a name="change-the-cache-behavior"></a>Alterar o comportamento do armazenamento em cache

Na página do **ponto de extremidade do CDN** do portal do Azure, selecione **Cache**.

Selecione **Armazenar em cache todas as URLs exclusivas** na lista suspensa do **comportamento do armazenamento em cache da cadeia de caracteres de consulta**.

Selecione **Salvar**.

![Selecione o comportamento do armazenamento em cache da cadeia de caracteres de consulta](media/app-service-web-tutorial-content-delivery-network/portal-select-caching-behavior.png)

### <a name="verify-that-unique-urls-are-cached-separately"></a>Verifique se as URLs exclusivas são armazenadas em cache separadamente

Em um navegador, acesse a home page no ponto de extremidade do CDN, mas inclua uma cadeia de caracteres de consulta: 

```
http://<endpointname>.azureedge.net/index.html?q=1
```

O CDN retorna o conteúdo do aplicativo web atual, que inclui "V2" no título. 

Para garantir que essa página está armazenada em cache no CDN, atualize a página. 

Abra `index.html` e altere "V2" para "V3" e implante a alteração. 

```bash
git commit -am "version 3"
git push azure master
```

Em um navegador, acesse a URL de ponto de extremidade do CDN com uma cadeia de caracteres de consulta nova, como `q=2`. O CDN obtém o arquivo `index.html` atual e exibe "V3".  Mas se você navegar até o ponto de extremidade do CDN com a cadeia de caracteres de consulta `q=1`, confira "V2".

```
http://<endpointname>.azureedge.net/index.html?q=2
```

![V3 no título no CDN, cadeia de caracteres de consulta 2](media/app-service-web-tutorial-content-delivery-network/v3-in-cdn-title-qs2.png)

```
http://<endpointname>.azureedge.net/index.html?q=1
```

![V2 no título no CDN, cadeia de caracteres de consulta 1](media/app-service-web-tutorial-content-delivery-network/v2-in-cdn-title-qs1.png)

A saída mostra que cada cadeia de caracteres de consulta é tratada de maneira diferente:

* p = 1 foi usada antes e, portanto, o conteúdo em cache é retornado (V2).
* p = 2 é novo e, portanto, o conteúdo do aplicativo Web mais recente é recuperado e retornado (V3).

Para obter mais informações, confira [controle do comportamento do armazenamento em cache do CDN do Azure com cadeias de caracteres de consulta](../cdn/cdn-query-string.md).

## <a name="map-a-custom-domain-to-a-cdn-endpoint"></a>Como mapear um domínio personalizado para um ponto de extremidade do CDN

Você mapeará seu domínio personalizado com o ponto de extremidade do CDN criando um registro CNAME. Um Registro CNAME é um recurso do DNS que mapeia um domínio de origem a um domínio de destino. Por exemplo, você pode mapear `cdn.contoso.com` ou `static.contoso.com` para `contoso.azureedge.net`.

Se você não tiver um domínio personalizado, considere concluir o [Tutorial de domínio do serviço de aplicativo](custom-dns-web-site-buydomains-web-app.md) para adquirir um domínio usando o portal do Azure. 

### <a name="find-the-hostname-to-use-with-the-cname"></a>Localize o nome do host a ser usado com o CNAME

Na página de **Ponto de extremidade** do portal do Azure, certifique-se de que a **Visão geral** esteja selecionada na navegação esquerda e, em seguida, selecione o botão **+ domínio personalizado**, na parte superior da página.

![Selecione Adicionar domínio personalizado](media/app-service-web-tutorial-content-delivery-network/portal-select-add-domain.png)

Na página **adicionar um domínio personalizado**, você pode visualizar o nome de host do ponto de extremidade para usar na criação de um registro CNAME. O nome de host é derivado da sua URL de ponto de extremidade do CDN: **&lt;EndpointName>.azureedge.net**. 

![Adicionar página de domínio](media/app-service-web-tutorial-content-delivery-network/portal-add-domain.png)

### <a name="configure-the-cname-with-your-domain-registrar"></a>Como configurar o CNAME com seu registrador de domínio

Navegue até o site do registrador do domínio e localize a seção para criar registros DNS. Você pode encontrá-lo em uma seção como **Nome de Domínio**, **DNS** ou **Gerenciamento de Servidor de Nomes**.

Localize a seção de gerenciamento de CNAMEs. Talvez você precise acessar uma página de configurações avançadas e procurar as palavras CNAME, Alias ou Subdomínios.

Crie um registro CNAME que mapeia o subdomínio escolhido (por exemplo, **estático** ou **cdn**) para o **nome de host do ponto de extremidade** mostrado anteriormente no portal. 

### <a name="enter-the-custom-domain-in-azure"></a>Digite o domínio personalizado no Azure

Retorne à página **Adicionar domínios personalizados** e insira seu domínio personalizado, incluindo o subdomínio, na caixa de diálogo. Por exemplo, insira: `cdn.contoso.com`.   
   
O Azure verificará se o registro do CNAME existe para o nome de domínio que você digitou. Se o CNAME estiver correto, seu domínio personalizado será validado.

Observe que, em alguns casos, pode levar algum tempo para que o registro CNAME se propague para servidores de nomes na Internet. Se o domínio não for validado imediatamente, aguarde alguns minutos e tente novamente.

### <a name="test-the-custom-domain"></a>Teste do domínio personalizado

Em um navegador, navegue até o arquivo `index.html` usando o seu domínio personalizado (por exemplo, `cdn.contoso.com/index.html`) para verificar se o resultado é o mesmo de quando você acessa `<endpointname>azureedge.net/index.html` diretamente.

![Home page do aplicativo de exemplo usando a URL do domínio personalizado](media/app-service-web-tutorial-content-delivery-network/home-page-custom-domain.png)

Para obter mais informações, confira [Mapeamento do conteúdo do CDN do Azure para um domínio personalizado](../cdn/cdn-map-content-to-custom-domain.md).

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Próximas etapas

O que você aprendeu:

> [!div class="checklist"]
> * Criar um novo ponto de extremidade do CDN.
> * Atualizar ativos em cache.
> * Usar cadeias de caracteres de consulta para controlar versões armazenadas em cache.
> * Usar um domínio personalizado para o ponto de extremidade do CDN.

Aprenda a otimizar o desempenho da CDN nos seguintes artigos:

> [!div class="nextstepaction"]
> [Melhorar o desempenho compactando os arquivos na CDN do Azure](../cdn/cdn-improve-performance.md)

> [!div class="nextstepaction"]
> [Pré-carregar ativos em um ponto de extremidade da CDN do Azure](../cdn/cdn-preload-endpoint.md)
