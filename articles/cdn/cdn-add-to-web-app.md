---
title: Tutorial - Adicionar o CDN do Azure para um aplicativo web do Serviço de Aplicativo do Azure | Microsoft Docs
description: Neste tutorial, uma Rede de Distribuição de Conteúdo (CDN) do Azure é adicionada a um aplicativo web do Serviço de Aplicativo do Azure para armazenar em cache e entregar os arquivos estáticos de servidores perto de seus clientes em todo o mundo.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/14/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 1b67522834497a264d95fc9b80246b16841d6026
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594218"
---
# <a name="tutorial-add-azure-cdn-to-an-azure-app-service-web-app"></a>Tutorial: Adicionar o CDN do Azure para um aplicativo Web do Serviço de Aplicativo do Azure

Este tutorial mostra como adicionar a [Rede de Distribuição de Conteúdo (CDN)](cdn-overview.md) do Azure para um [aplicativo Web no Serviço de Aplicativo do Azure](../app-service/overview.md). Aplicativos Web é um serviço de hospedagem de aplicativos Web, APIs REST e back-ends móveis. 

Segue a home page do site de exemplo estático em HTML que você usará para trabalhar:

![Home page do aplicativo de exemplo](media/cdn-add-to-web-app/sample-app-home-page.png)

O que você aprenderá:

> [!div class="checklist"]
> * Criar um novo ponto de extremidade do CDN.
> * Atualizar ativos em cache.
> * Usar cadeias de caracteres de consulta para controlar versões armazenadas em cache.


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- [Instalar o Git](https://git-scm.com/)
- [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-web-app"></a>Criar o aplicativo Web

Para criar o aplicativo Web com que você trabalhará, execute o [Início rápido HTML estático](../app-service/app-service-web-get-started-html.md) na etapa **Navegar até o aplicativo**.

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure

Abra um navegador e navegue até o [portal do Azure](https://portal.azure.com).

### <a name="dynamic-site-acceleration-optimization"></a>Otimização de aceleração de site dinâmico
Se você quiser otimizar seu ponto de extremidade de CDN para aceleração de site dinâmico (DSA), use o [portal de CDN](cdn-create-new-endpoint.md) para criar sei perfil e ponto de extremidade. Com a [otimização de DSA](cdn-dynamic-site-acceleration.md) da CDN do Azure, o desempenho de páginas da Web com conteúdo dinâmico é melhorado de maneira significativa. Para obter instruções sobre como otimizar um ponto de extremidade de CDN para DSA no portal de CDN, consulte [Configuração do ponto de extremidade de CDN para acelerar a entrega de arquivos dinâmicos](cdn-dynamic-site-acceleration.md#cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files). Caso contrário, se você não quiser otimizar seu novo ponto de extremidade, use o portal da Web do aplicativo para criá-lo usando as etapas da próxima seção. Observe que para perfis **CDN do Azure da Verizon**, não é possível alterar a otimização de um ponto de extremidade CDN depois que ele foi criado.

## <a name="create-a-cdn-profile-and-endpoint"></a>Como criar um perfil do CDN e um ponto de extremidade

No painel de navegação esquerdo, selecione **Serviços de aplicativos** e, em seguida, selecione o aplicativo que você criou no [Início rápido do HTML estático](../app-service/app-service-web-get-started-html.md).

![Selecione o aplicativo de serviço de aplicativo no portal](media/cdn-add-to-web-app/portal-select-app-services.png)

Na página **Serviço de aplicativo**, na seção **Configurações**, selecione **Rede > Configurar CDN do Azure para seu aplicativo**.

![Selecione CDN no portal](media/cdn-add-to-web-app/portal-select-cdn.png)

Na página da **Rede de distribuição de conteúdo do Azure**, forneça as configurações do **ponto de extremidade novo** conforme especificado na tabela.

![Como criar perfil e o ponto de extremidade no portal](media/cdn-add-to-web-app/portal-new-endpoint.png)

| Configuração | Valor sugerido | DESCRIÇÃO |
| ------- | --------------- | ----------- |
| **Perfil do CDN** | myCDNProfile | Um perfil do CDN é uma coleção de pontos de extremidade do CDN com o mesmo tipo de preços. |
| **Tipo de preços** | Akamai Standard | O [tipo de preços](cdn-features.md) especifica o provedor e os recursos disponíveis. Este tutorial usa *Akamai padrão*. |
| **Nome do ponto de extremidade do CDN** | Qualquer nome que seja exclusivo no domínio azureedge.net | Acesse os recursos armazenados em cache no domínio *&lt;endpointname&gt;* .azureedge.net.

Selecione **Criar** para criar um perfil de CDN.

O Azure cria o perfil e o ponto de extremidade. O novo ponto de extremidade aparece na lista de **pontos de extremidade** e, quando ele for provisionado, o status será **Executando**.

![Ponto de extremidade novo na lista](media/cdn-add-to-web-app/portal-new-endpoint-in-list.png)

### <a name="test-the-cdn-endpoint"></a>Testar o ponto de extremidade CDN

 Como o registro demora um pouco para se propagar, o ponto de extremidade não fica imediatamente disponível para uso: 
   - Para perfis da **CDN Standard do Azure da Microsoft**, a propagação geralmente é concluída em dez minutos. 
   - Para perfis da **CDN Standard do Azure da Akamai**, a propagação normalmente é concluída em um minuto. 
   - Para perfis da **CDN Standard do Azure da Verizon** e **CDN Premium do Azure da Verizon**, a propagação geralmente conclui em 90 minutos. 

O aplicativo de exemplo possui um arquivo *index.html* e as pastas *css*, *img* e *js* que contêm outros ativos estáticos. Os caminhos de conteúdo para todos esses arquivos são os mesmos no ponto de extremidade do CDN. Por exemplo, as duas URLs a seguir acessam o arquivo *bootstrap.css* na pasta *css*:

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

![Home page do aplicativo de exemplo distribuídos a partir do CDN](media/cdn-add-to-web-app/sample-app-home-page-cdn.png)

 Consulte a mesma página que você executou anteriormente em um aplicativo Web do Azure. A CDN do Azure recuperou os ativos do aplicativo web de origem e está servindo-os do ponto de extremidade da CDN

Para garantir que essa página está armazenada em cache no CDN, atualize a página. Duas solicitações para o mesmo ativo, às vezes, são necessárias para que o CDN armazene em cache o conteúdo solicitado.

Para obter mais informações sobre como criar perfis do CDN do Azure e pontos de extremidade, confira [Introdução ao CDN do Azure](cdn-create-new-endpoint.md).

## <a name="purge-the-cdn"></a>Limpar o CDN

O CDN atualiza periodicamente seus recursos do aplicativo web de origem com base na configuração de tempo de vida (TTL). A TTL padrão é de sete dias.

Às vezes você precisará atualizar o CDN antes da expiração do TTL; por exemplo, quando você implanta o conteúdo atualizado para o aplicativo web. Para disparar uma atualização, limpe manualmente os recursos do CDN. 

Nesta seção do tutorial, você implanta uma alteração para o aplicativo web e limpa o CDN para disparar o CDN para que ele atualize seu cache.

### <a name="deploy-a-change-to-the-web-app"></a>Implantar uma alteração no aplicativo web

Abra o arquivo *index.html* e adicione *- V2* para o título H1, conforme mostrado no exemplo a seguir: 

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

![V2 no título no aplicativo web](media/cdn-add-to-web-app/v2-in-web-app-title.png)

Se você navegar até a URL de ponto de extremidade do CDN para a home page, você não visualizará a alteração porque a versão armazenada em cache no CDN ainda não expirou. 

```
http://<endpointname>.azureedge.net/index.html
```

![Nenhum V2 no título no CDN](media/cdn-add-to-web-app/no-v2-in-cdn-title.png)

### <a name="purge-the-cdn-in-the-portal"></a>Limpar o CDN no portal

Para disparar o CDN para atualizar sua versão armazenada em cache, limpe o CDN.

Na navegação à esquerda do portal, selecione **Grupos de recursos** e, em seguida, selecione o grupo de recursos que você criou para seu aplicativo web (myResourceGroup).

![Escolha o grupo de recursos](media/cdn-add-to-web-app/portal-select-group.png)

Na lista de recursos, selecione o ponto de extremidade do CDN.

![Selecione o ponto de extremidade](media/cdn-add-to-web-app/portal-select-endpoint.png)

Na parte superior da página **Ponto de extremidade**, selecione **Limpar**.

![Selecione Limpar](media/cdn-add-to-web-app/portal-select-purge.png)

Digite os caminhos de conteúdo que você deseja limpar. Você pode passar um caminho de arquivo completo para limpar um arquivo específico ou um segmento de caminho para limpar e atualizar o conteúdo de uma pasta. Como você alterou *index.html*, certifique-se de que está em um dos caminhos.

Na parte inferior da página, selecione **Limpar**.

![Limpar página](media/cdn-add-to-web-app/app-service-web-purge-cdn.png)

### <a name="verify-that-the-cdn-is-updated"></a>Verifique se o CDN está atualizado

Aguarde até que a solicitação de limpeza conclua o processamento, que normalmente é de alguns minutos. Para conferir o status atual, selecione o ícone de sino na parte superior da página. 

![Limpar notificação](media/cdn-add-to-web-app/portal-purge-notification.png)

Quando você navega para a URL de ponto de extremidade CDN para *index.html*, você verá o *V2* que você adicionou ao título na home page, que indica que o cache CDN foi atualizado.

```
http://<endpointname>.azureedge.net/index.html
```

![V2 no título no CDN](media/cdn-add-to-web-app/v2-in-cdn-title.png)

Para obter mais informações, confira [Como limpar um ponto de extremidade do CDN do Azure](../cdn/cdn-purge-endpoint.md). 

## <a name="use-query-strings-to-version-content"></a>Use cadeias de caracteres de consulta para o conteúdo da versão

O CDN do Azure oferece as seguintes opções de comportamento do armazenamento em cache:

* Ignorar as cadeias de caracteres de consulta
* Ignorar o cache para cadeias de caracteres de consulta
* Armazenar em cache todas as URLs exclusivas 

A primeira opção é a padrão, o que significa que há apenas uma versão em cache de um ativo, independentemente da cadeia de caracteres de consulta na URL. 

Nesta seção do tutorial, você deve alterar o comportamento do armazenamento em cache para armazenar em cache todas as URLs exclusivas.

### <a name="change-the-cache-behavior"></a>Alterar o comportamento do armazenamento em cache

Na página do **ponto de extremidade do CDN** do portal do Azure, selecione **Cache**.

Selecione **Armazenar em cache todas as URLs exclusivas** na lista suspensa do **comportamento do armazenamento em cache da cadeia de caracteres de consulta**.

Clique em **Salvar**.

![Selecione o comportamento do armazenamento em cache da cadeia de caracteres de consulta](media/cdn-add-to-web-app/portal-select-caching-behavior.png)

### <a name="verify-that-unique-urls-are-cached-separately"></a>Verifique se as URLs exclusivas são armazenadas em cache separadamente

Em um navegador, acesse a home page no ponto de extremidade do CDN, e inclua uma cadeia de caracteres de consulta: 

```
http://<endpointname>.azureedge.net/index.html?q=1
```

O CDN do Azure retorna o conteúdo do aplicativo web atual, que inclui *V2* no título. 

Para garantir que essa página está armazenada em cache no CDN, atualize a página. 

Abra *index.html*, altere *V2* para *V3*, em seguida, implante a alteração. 

```bash
git commit -am "version 3"
git push azure master
```

Em um navegador, acesse a URL de ponto de extremidade do CDN com uma cadeia de caracteres de consulta nova, como `q=2`. O CDN do Azure obtém o arquivo *index.html* atual e exibe *V3*. Contudo, se você navegar até o ponto de extremidade do CDN com a cadeia de caracteres de consulta `q=1`, você verá *V2*.

```
http://<endpointname>.azureedge.net/index.html?q=2
```

![V3 no título no CDN, cadeia de caracteres de consulta 2](media/cdn-add-to-web-app/v3-in-cdn-title-qs2.png)

```
http://<endpointname>.azureedge.net/index.html?q=1
```

![V2 no título no CDN, cadeia de caracteres de consulta 1](media/cdn-add-to-web-app/v2-in-cdn-title-qs1.png)

A saída mostra que cada cadeia de caracteres de consulta é tratada de maneira diferente:

* p = 1 foi usada antes e, portanto, o conteúdo em cache é retornado (V2).
* p = 2 é novo e, portanto, o conteúdo do aplicativo Web mais recente é recuperado e retornado (V3).

Para obter mais informações, confira [controle do comportamento do armazenamento em cache do CDN do Azure com cadeias de caracteres de consulta](../cdn/cdn-query-string.md).

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Próximas etapas

O que você aprendeu:

> [!div class="checklist"]
> * Criar um novo ponto de extremidade do CDN.
> * Atualizar ativos em cache.
> * Usar cadeias de caracteres de consulta para controlar versões armazenadas em cache.

Aprenda a otimizar o desempenho da CDN nos seguintes artigos:

> [!div class="nextstepaction"]
> [Tutorial: Adicionar um domínio personalizado ao seu ponto de extremidade da CDN do Azure](cdn-map-content-to-custom-domain.md)


