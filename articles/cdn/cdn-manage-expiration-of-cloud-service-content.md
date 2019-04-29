---
title: Gerenciar a expiração do conteúdo da Web na CDN do Azure | Microsoft Docs
description: Saiba como gerenciar a expiração de conteúdo de Serviços de Nuvem/Aplicativos Web do Azure, ASP.NET ou IIS na CDN do Azure.
services: cdn
documentationcenter: .NET
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2018
ms.author: magattus
ms.openlocfilehash: c21ae227d74442be5701dd906180392b1e0fdf8b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60636635"
---
# <a name="manage-expiration-of-web-content-in-azure-cdn"></a>Gerenciar a expiração do conteúdo da Web na CDN do Azure
> [!div class="op_single_selector"]
> * [Conteúdo da Web do Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [Armazenamento de Blobs do Azure](cdn-manage-expiration-of-blob-content.md)
> 

Arquivos de servidores Web de origem acessíveis publicamente podem ser armazenados em cache na CDN (Rede de Distribuição de Conteúdo) até que a TTL (vida útil) tenha decorrida. A vida útil é determinada pelo cabeçalho `Cache-Control` na resposta HTTP do servidor de origem. Este artigo descreve como definir cabeçalhos `Cache-Control` para recurso de Aplicativos Web do Serviço de Aplicativo do Microsoft Azure, Serviços de Nuvem do Microsoft Azure, aplicativos ASP.NET e sites do IIS (Serviços de Informações da Internet), todos configurados de forma semelhante. Você pode definir o cabeçalho `Cache-Control` usando arquivos de configuração ou programaticamente. 

Também é possível controlar as configurações de cache do portal do Azure, definindo as [regras de cache da CDN](cdn-caching-rules.md). Se você criar uma ou mais regras de cache e definir o comportamento do cache para **Substituir** ou **Ignorar cache**, as configurações de cache fornecidas pela origem discutidas neste artigo serão ignoradas. Para obter informações sobre conceitos gerais de cache, consulte [Como funciona o cache](cdn-how-caching-works.md).

> [!TIP]
> Você pode optar por não definir nenhuma vida útil em um arquivo. Nesse caso, a CDN do Azure aplica automaticamente um TTL padrão de sete dias, exceto se você tiver configurado as regras de cache no Portal do Azure. Esse padrão TTL aplica-se somente para otimizações de entrega da web gerais. Para otimizações de arquivo grande, o TTL padrão é de um dia e para otimizações de streaming de mídia, o TTL padrão é um ano.
> 
> Para obter mais informações sobre como a CDN do Azure trabalha para acelerar o acesso a arquivos e outros recursos, consulte [Visão geral da Rede de Distribuição de Conteúdo do Azure](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Configurar cabeçalhos de Controle de Cache usando regras de cache da CDN
O método preferencial para configurar um cabeçalho `Cache-Control` do servidor Web é usar regras de cache no Portal do Azure. Para obter mais informações sobre as regras de cache da CDN, consulte [Controlar o comportamento de cache da CDN do Azure com regras de cache](cdn-caching-rules.md).

> [!NOTE] 
> As regras de cache estão disponíveis apenas para perfis de **CDN Standard do Azure do Verizon** e **CDN Standard do Azure da Akamai**. Para perfis de **CDN Premium do Azure do Verizon**, é necessário usar o [mecanismo de regras da CDN do Azure](cdn-rules-engine.md) no portal **Gerenciar** para uma funcionalidade semelhante.

**Para navegar até a página de regras de cache da CDN**:

1. No Portal do Azure, selecione um perfil CDN e selecione o ponto de extremidade para o servidor Web.

1. No painel esquerdo em Configurações, selecione **Regras de cache**.

   ![Botão de Regras de cache da CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-rules-btn.png)

   A página **Regras de cache** é exibida.

   ![Página de Cache da CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-page.png)


**Para definir os cabeçalhos de Controle de Cache do servidor Web usando regras de cache globais:**

1. Em **Regras de cache globais**, defina **Comportamento de cache da cadeia de caracteres de consulta** para **Ignorar cadeias de consulta** e defina **Comportamento do cache** para **Substituição**.
      
1. Para **Duração da expiração do cache**, insira 3600 na caixa **Segundos**ou 1 na caixa **Horas**. 

   ![Exemplo de regras de cache globais da CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-global-caching-rules-example.png)

   Essa regra de cache global define uma duração de cache de uma hora e afeta todas as solicitações para o ponto de extremidade. Ela substitui todos os cabeçalhos HTTP `Cache-Control` ou `Expires` que são enviados pelo servidor de origem especificado pelo ponto de extremidade.   

1. Clique em **Salvar**.

**Para definir os cabeçalhos de Controle de Cache do servidor Web usando regras de cache personalizadas:**

1. Em **Personalizar regras de cache**, crie duas condições de combinação:

      a. Para a primeira condição de correspondência, ajuste **Condição de correspondência** para **Path** e insira `/webfolder1/*` para o **Valor de correspondência**. Defina o **Comportamento de cache**  para **Substituição** e insira 4 na caixa **Horas**.

     b. Para a segunda condição de correspondência, ajuste **Condição de correspondência** para **Path** e insira `/webfolder1/file1.txt` para o **Valor de correspondência**. Defina o **Comportamento de cache**  para **Substituição** e insira 2 na caixa **Horas**.

    ![Exemplo de regras de cache personalizadas da CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-custom-caching-rules-example.png)

    A primeira regra de cache personalizada estabelece uma duração de cache de quatro horas para qualquer arquivo na pasta `/webfolder1` no servidor de origem especificado pelo ponto de extremidade. A segunda regra substitui a primeira regra somente para o arquivo `file1.txt` e define uma duração de cache de duas horas para isso.

1. Clique em **Salvar**.


## <a name="setting-cache-control-headers-by-using-configuration-files"></a>Configurando cabeçalhos de Cache-Control usando arquivos de configuração
Para conteúdo estático, como imagens e folhas de estilo, você pode controlar a frequência de atualização modificando os arquivos de configuração **applicationHost.config** ou **Web.config** para seu aplicativo Web. Para definir `Cache-Control` para o seu conteúdo, use o elemento `<system.webServer>/<staticContent>/<clientCache>` em qualquer arquivo.

### <a name="using-applicationhostconfig-files"></a>Usando arquivos ApplicationHost.config
O arquivo **ApplicationHost.config** é o arquivo raiz do sistema de configuração do IIS. As definições de configuração em um arquivo **ApplicationHost.config** afetam todos os aplicativos no site, mas são substituídas pelas configurações dos arquivos **Web.config** que existem para um aplicativo Web.

### <a name="using-webconfig-files"></a>Usando arquivos Web.config
Com um arquivo **Web.config**, você pode personalizar a maneira como seu aplicativo Web ou um diretório específico dele se comporta. Normalmente, você tem pelo menos um arquivo **Web.config** na pasta raiz do seu aplicativo Web. Para cada arquivo **Web.config** em uma pasta específica, os conjuntos de configuração afetam tudo nessa pasta e suas subpastas, a menos que sejam substituídas no nível da subpasta por outro arquivo **Web.config**. 

Por exemplo, você pode definir um elemento `<clientCache>` em um arquivo **Web.config** na pasta raiz do seu aplicativo Web para armazenar em cache todo o conteúdo estático nele por três dias. Você também pode adicionar um arquivo **Web.config** com conteúdo mais variável (por exemplo, `\frequent`) e definir seu elemento `<clientCache>` para armazenar em cache o conteúdo da subpasta por seis horas. O resultado final é que o conteúdo em todo o site é armazenado em cache por três dias, exceto pelo o conteúdo no diretório `\frequent`, que é armazenado em cache por apenas seis horas.  

O exemplo do arquivo de configuração XML a seguir mostra como configurar o elemento `<clientCache>` para especificar a duração máxima de três dias:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Para usar o atributo **cacheControlMaxAge**, você deve definir o valor do atributo **cacheControlMode** como `UseMaxAge`. Essa configuração fez o cabeçalho HTTP e a diretiva, `Cache-Control: max-age=<nnn>`, serem adicionados à resposta. O formato do valor do intervalo de tempo para o atributo **cacheControlMaxAge** é `<days>.<hours>:<min>:<sec>`. Seu valor é convertido em segundos e é usado como o valor da diretiva `Cache-Control` `max-age`. Para obter mais informações sobre o `<clientCache>` elemento, consulte [Cache do cliente \<clientCache >](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-programmatically"></a>Configurando cabeçalhos Cache-Control programaticamente
Para aplicativos ASP.NET, você controla o comportamento de cache de CDN programaticamente configurando a propriedade **HttpResponse.Cache** da API do .NET. Para obter informações sobre a propriedade **HttpResponse.Cache**, consulte [Propriedade HttpResponse.Cache](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) e [Classe HttpCachePolicy](/dotnet/api/system.web.httpcachepolicy).  

Para armazenar em cache o conteúdo do aplicativo programaticamente no ASP.NET, siga essas etapas:
   1. Verifique se o conteúdo está marcado como armazenável em cache, configurando `HttpCacheability` para `Public`. 
   1. Defina um validador de cache chamando um dos seguintes métodos `HttpCachePolicy`:
      - Chame `SetLastModified` para definir um valor de carimbo de data/hora para o cabeçalho `Last-Modified`.
      - Chame `SetETag` para definir um valor para o cabeçalho `ETag`.
   1. Opcionalmente, especifique um tempo de expiração de cache, chamando `SetExpires` para definir um valor para o cabeçalho `Expires`. Caso contrário, as heurísticas de cache padrão descritas anteriormente neste documento se aplicam.

Por exemplo, para armazenar em cache o conteúdo por uma hora, adicione o seguinte código C#:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>Testando o cabeçalho de Controle de Cache
Você pode facilmente verificar as configurações TTL do seu conteúdo da Web. Com as [ferramentas para desenvolvedores](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) do seu navegador, teste se o conteúdo da Web inclui cabeçalho de resposta `Cache-Control`. Você também pode usar uma ferramenta como a **wget**, [Postman](https://www.getpostman.com/) ou [Fiddler](https://www.telerik.com/fiddler) para examinar os cabeçalhos de resposta.

## <a name="next-steps"></a>Próximas etapas
* [Leia os detalhes sobre o elemento **clientCache**](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Leia a documentação sobre a propriedade **HttpResponse.Cache**](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) 
* [Leia a documentação da **Classe HttpCachePolicy**](/dotnet/api/system.web.httpcachepolicy)  
* [Saiba mais sobre conceitos de cache](cdn-how-caching-works.md)
