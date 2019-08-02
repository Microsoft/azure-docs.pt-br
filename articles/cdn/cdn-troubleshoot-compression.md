---
title: Solucionando problemas de compactação de arquivo na CDN do Azure | Microsoft Docs
description: Solucione problemas com a compactação de arquivo da CDN do Azure.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: a6624e65-1a77-4486-b473-8d720ce28f8b
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 5195dc3c47d2a4377147b2ef49b23bab6b3fee77
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593320"
---
# <a name="troubleshooting-cdn-file-compression"></a>Solucionando problemas de compactação de arquivo CDN
Este artigo ajuda você a solucionar problemas com a [compactação de arquivo CDN](cdn-improve-performance.md).

Se você precisar de mais ajuda em qualquer momento neste artigo, você pode contatar os especialistas do Azure nos [fóruns do Azure MSDN e Excedente de Pilha](https://azure.microsoft.com/support/forums/). Como alternativa, você também pode registrar um incidente de suporte do Azure. Vá para o [site de Suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **Obter Suporte**.

## <a name="symptom"></a>Sintoma
A compactação do ponto de extremidade está habilitada, mas os arquivos estão sendo retornados descompactados.

> [!TIP]
> Para verificar se os arquivos estão sendo retornados compactados, você precisará usar uma ferramenta como [Fiddler](https://www.telerik.com/fiddler) ou as [ferramentas de desenvolvedor](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) do seu navegador.  Verifique os cabeçalhos de resposta HTTP retornados com o conteúdo CDN armazenado em cache.  Se houver um cabeçalho chamado `Content-Encoding` com um valor **gzip**, **bzip2** ou **deflate**, seu conteúdo será compactado.
> 
> ![Cabeçalho Content-Encoding](./media/cdn-troubleshoot-compression/cdn-content-header.png)
> 
> 

## <a name="cause"></a>Causa
Há várias causas possíveis, incluindo:

* O conteúdo solicitado não está qualificado para compactação.
* A compactação não está habilitada para o tipo de arquivo solicitado.
* A solicitação HTTP não incluía um cabeçalho solicitando um tipo de compactação válido.

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas
> [!TIP]
> Assim como ocorre com a implantação de novos pontos de extremidade, alterações na configuração da CDN demoram um pouco para serem propagadas pela rede.  Normalmente, as alterações são aplicadas dentro de 90 minutos.  Se esta for a primeira vez que você configura a compactação do ponto de extremidade CDN, será necessário considerar uma espera de 1 a 2 horas para garantir que as configurações de compactação são propagadas para os POPs. 
> 
> 

### <a name="verify-the-request"></a>Verificar a solicitação
Primeiro, devemos fazer uma verificação de integridade rápida na solicitação.  É possível usar as [ferramentas de desenvolvedor](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) do navegador para exibir as solicitações feitas no momento.

* Verifique se a solicitação está sendo enviada para a URL do ponto de extremidade, `<endpointname>.azureedge.net`, e não para sua origem.
* Verifique se a solicitação contém um cabeçalho **Accept-Encoding** e se o valor desse cabeçalho contém **gzip**, **deflate** ou **bzip2**.

> [!NOTE]
> Os perfis da **CDN do Azure da Akamai** suportam somente a codificação **gzip**.
> 
> 

![Cabeçalhos da solicitação CDN](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profiles"></a>Verificar as configurações de compactação (perfis CDN Standard)
> [!NOTE]
> Essa etapa se aplica somente se o seu perfil de CDN é um perfil da **CDN Standard do Azure da Microsoft**, **CDN Standard do Azure da Verizon** ou **CDN Standard do Azure do Akamai**. 
> 
> 

Navegue até seu ponto de extremidade no [Portal do Azure](https://portal.azure.com) e clique no botão **Configurar** .

* Verifique se a compactação está habilitada.
* Verifique se o tipo MIME do conteúdo a ser compactado está incluído na lista de formatos compactados.

![Configurações de compactação CDN](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profiles"></a>Verificar as configurações de compactação (perfis CDN Premium)
> [!NOTE]
> Essa etapa se aplica somente se o seu perfil de CDN é um perfil da **CDN Premium do Azure da Verizon**.
> 
> 

Navegue até seu ponto de extremidade no [Portal do Azure](https://portal.azure.com) e clique no botão **Gerenciar** .  O portal suplementar será aberto.  Passe o mouse sobre a guia **HTTP Grande**, em seguida, sobre o submenu **Configurações do Cache**.  Clique em **Compactação**. 

* Verifique se a compactação está habilitada.
* Verifique se a lista **Tipos de Arquivo** contém uma lista separada por vírgula (sem espaços) de tipos MIME.
* Verifique se o tipo MIME do conteúdo a ser compactado está incluído na lista de formatos compactados.

![Configurações de compactação premium CDN](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached-verizon-cdn-profiles"></a>Verifique se o conteúdo está armazenado em cache (perfis CDN da Verizon)
> [!NOTE]
> Essa etapa se aplica somente se o seu perfil de CDN é um perfil da **CDN Standard do Azure da Verizon** ou **CDN Premium do Azure da Verizon**.
> 
> 

Usando as ferramentas de desenvolvedor do navegador, verifique os cabeçalhos de resposta para garantir que o arquivo está armazenado em cache na região em que está sendo solicitado.

* Verifique o cabeçalho de resposta **Server** .  O cabeçalho deve ter o formato **Plataforma (POP/ID do Servidor)** , como mostrado no exemplo a seguir.
* Verifique o cabeçalho de resposta **X-Cache** .  No cabeçalho, deve-se ler **HIT**.  

![Cabeçalhos de resposta CDN](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements-verizon-cdn-profiles"></a>Verificar se o arquivo atende aos requisitos de tamanho (perfis da CDN da Verizon)
> [!NOTE]
> Essa etapa se aplica somente se o seu perfil de CDN é um perfil da **CDN Standard do Azure da Verizon** ou **CDN Premium do Azure da Verizon**.
> 
> 

Para ser elegível para compactação, um arquivo deve atender aos seguintes requisitos de tamanho:

* Maior que 128 bytes.
* Menor que 1 MB.

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Verifique a solicitação no servidor de origem por um cabeçalho **Via**
O cabeçalho HTTP **Via** indica ao servidor Web que a solicitação está sendo passada por um servidor proxy.  Por padrão, os servidores Web do Microsoft IIS não compactam as respostas quando a solicitação contém um cabeçalho **Via** .  Para substituir esse comportamento, execute o seguinte procedimento:

* **IIS 6**: [Defina HcNoCompressionForProxies = "FALSE" nas propriedades de Metabase do IIS](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90))
* **IIS 7 e superior**: [Defina as **noCompressionForHttp10** e **noCompressionForProxies** como False na configuração do servidor](https://www.iis.net/configreference/system.webserver/httpcompression)

