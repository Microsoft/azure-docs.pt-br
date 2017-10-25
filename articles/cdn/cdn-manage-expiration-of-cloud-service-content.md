---
title: "Gerenciar a expiração do conteúdo da Web na CDN do Azure | Microsoft Docs"
description: "Saiba como gerenciar a expiração de conteúdo de Serviços de Nuvem/Aplicativos Web do Azure, ASP.NET ou IIS na CDN do Azure."
services: cdn
documentationcenter: .NET
author: zhangmanling
manager: erikre
editor: 
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: c207d780857a61d4b1fc0f39e6185cae67abc955
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-expiration-of-azure-web-appscloud-services-aspnet-or-iis-content-in-azure-cdn"></a>Gerenciar a expiração de conteúdo de Serviços de Nuvem/Aplicativos Web do Azure, ASP.NET ou IIS na CDN do Azure
> [!div class="op_single_selector"]
> * [Serviços de Nuvem/Aplicativos Web do Azure, ASP.NET ou IIS](cdn-manage-expiration-of-cloud-service-content.md)
> * [Serviço Blob do Armazenamento do Azure](cdn-manage-expiration-of-blob-content.md)
> 
> 

Arquivos de qualquer servidor Web de origem publicamente acessível podem ser armazenados em cache no Azure CDN até que o TTL (tempo de vida) tenha decorrido.  O TTL é determinado pelo [cabeçalho *Controle de Cache*](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) na resposta HTTP do servidor de origem.  Este artigo descreve como definir cabeçalhos `Cache-Control` para Aplicativos Web do Azure, Serviços de Nuvem do Azure, aplicativos ASP.NET e sites de Internet Information Services, todos configurados de maneira semelhante.

> [!TIP]
> Você pode optar por não definir nenhum TTL em um arquivo.  Nesse caso, o Azure CDN aplica automaticamente um TTL padrão de sete dias.
> 
> Para saber mais sobre como o Azure CDN trabalha para acelerar o acesso a blobs e outros recursos, confira a [Visão geral do Azure CDN](cdn-overview.md).
> 
> 

## <a name="setting-cache-control-headers-in-configuration"></a>Definindo cabeçalhos Cache-Control na configuração
Para conteúdo estático, como imagens e folhas de estilo, você pode controlar a frequência de atualização modificando os arquivos **applicationHost.config** ou **Web.config** para seu aplicativo Web.  O elemento **system.webServer\staticContent\clientCache** no arquivo de configuração definirá o cabeçalho `Cache-Control` para seu conteúdo. Em **eb.config**, as configurações afetarão tudo na pasta e em todas as subpastas, a menos que sejam substituídas no nível da subpasta.  Por exemplo, você pode definir um tempo de vida padrão na raiz para que todo o conteúdo estático seja armazenado em cache por três dias, mas ter uma subpasta que tenha conteúdo mais variável com uma configuração de cache de seis horas.  Em **applicationHost.config**, todos os aplicativos no site serão afetados, mas pode ser substituídos nos arquivos **eb.config** dos aplicativos.

O seguinte XML mostra e exemplo de configuração de **clientCache** para especificar um tempo decorrido máximo de três dias:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

A especificação de **UseMaxAge** adiciona um `Cache-Control: max-age=<nnn>` à resposta com base no valor especificado no atributo **CacheControlMaxAge**. O formato do período de tempo para o atributo **cacheControlMaxAge** é <days>.<hours>:<min>:<sec>. Para obter mais informações sobre o nó **clientCache**, consulte [Cache de cliente <clientCache>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-in-code"></a>Definindo cabeçalhos Cache-Control no código
Para aplicativos ASP.NET, você pode definir o comportamento do caching de CDN programaticamente definindo a propriedade **HttpResponse** . Para obter mais informações sobre a propriedade **HttpResponse.Cache**, consulte [Propriedade HttpResponse.Cache](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) e [Classe HttpCachePolicy](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Se você quiser armazenar em cache programaticamente o conteúdo do aplicativo no ASP.NET, verifique se o conteúdo está marcado como armazenável em cache, definindo HttpCacheability como *Public*. Além disso, verifique se um validador de cache está definido. O validador de cache pode ser um carimbo de data/hora de Última Modificação definido chamando SetLastModified ou um valor de etag definido chamando SetETag. Opcionalmente, você também pode especificar um tempo de expiração de cache chamando SetExpires ou pode contar com a heurística de cache padrão descrita anteriormente neste documento.  

Por exemplo, para armazenar o conteúdo em cache por uma hora, adicione o seguinte:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="next-steps"></a>Próximas etapas
* [Leia os detalhes sobre o elemento **clientCache**](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Leia a documentação sobre a propriedade **HttpResponse.Cache**](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
* [Leia a documentação sobre a **classe HttpCachePolicy**](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

