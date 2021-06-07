---
title: Solucionando problemas de compactação de arquivo no Azure front door Standard/Premium
description: Saiba como solucionar problemas com a compactação de arquivo na porta de recepção do Azure. Este artigo aborda várias causas possíveis.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2020
ms.author: qixwang
ms.openlocfilehash: c912ebde5499ec2f9f68e5f5762af60823faefe1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101098542"
---
# <a name="troubleshooting-azure-front-door-standardpremium-file-compression"></a>Solucionando problemas de compactação de arquivo Standard/Premium da porta do Azure

> [!Note]
> Esta documentação é específica para o Azure Front Door Standard/Premium (Versão Prévia). Está procurando informações sobre o Azure Front Door? Veja [aqui](../front-door-overview.md).

Este artigo ajuda você a solucionar problemas de compactação de arquivo Standard/Premium da porta do Azure.

> [!IMPORTANT]
> O Azure Front Door Standard/Premium (versão prévia) está na fase de versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="symptom"></a>Sintoma

A compactação para a rota está habilitada, mas os arquivos estão sendo retornados descompactados.

> [!TIP]
> Para verificar se os arquivos estão sendo retornados compactados, você precisará usar uma ferramenta como [Fiddler](https://www.telerik.com/fiddler) ou as [ferramentas de desenvolvedor](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) do seu navegador.  Verifique os cabeçalhos de resposta HTTP retornados com o conteúdo CDN armazenado em cache.  Se houver um cabeçalho chamado `Content-Encoding` com um valor **gzip**, **bzip2** ou **deflate**, seu conteúdo será compactado.
> 
> ![Cabeçalho Content-Encoding](../media/troubleshoot-compression/content-header.png)
> 

## <a name="cause"></a>Causa

Há várias causas possíveis, incluindo:

* O conteúdo solicitado não está qualificado para compactação.
* A compactação não está habilitada para o tipo de arquivo solicitado.
* A solicitação HTTP não incluiu um cabeçalho solicitando um tipo de compactação válido.
* A origem está enviando conteúdo em bloco.

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

> [!TIP]
> Assim como na implantação de novos pontos de extremidade, o AFD das alterações de configuração leva algum tempo para se propagar pela rede.  Normalmente, as alterações são aplicadas dentro de 90 minutos.  Se esta for a primeira vez que você configura a compactação do ponto de extremidade CDN, será necessário considerar uma espera de 1 a 2 horas para garantir que as configurações de compactação são propagadas para os POPs. 
> 

### <a name="verify-the-request"></a>Verificar a solicitação

Primeiro, devemos fazer uma verificação dupla da solicitação. Você pode usar as ferramentas de **[desenvolvedor](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)** do navegador para exibir as solicitações que estão sendo feitas.

* Verifique se a solicitação está sendo enviada para a URL do ponto de extremidade, `<endpointname>.z01.azurefd.net` , e não sua origem.
* Verifique se a solicitação contém um cabeçalho **Accept-Encoding** e se o valor desse cabeçalho contém **gzip**, **deflate** ou **bzip2**.

![Cabeçalhos da solicitação CDN](../media/troubleshoot-compression/request-headers.png)

### <a name="verify-compression-settings"></a>Verificar configurações de compactação

Navegue até o ponto de extremidade na [portal do Azure](https://portal.azure.com) e selecione o botão **Configurar** no painel rotas. Verifique se a compactação está **habilitada**.

![Configurações de compactação CDN](../media/troubleshoot-compression/compression-settings.png)

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Verifique a solicitação no servidor de origem por um cabeçalho **Via**

O cabeçalho HTTP **Via** indica ao servidor Web que a solicitação está sendo passada por um servidor proxy.  Por padrão, os servidores Web do Microsoft IIS não compactam as respostas quando a solicitação contém um cabeçalho **via** .  Para substituir esse comportamento, faça o seguinte:

* **IIS 6**: defina HcNoCompressionForProxies = "false" nas propriedades da metabase do IIS. Para obter informações, consulte [compactação do IIS 6](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90)).
* **IIS 7 e superior**: defina **noCompressionForHttp10** e **noCompressionForProxies** como *false* na configuração do servidor. Para obter mais informações, consulte [compactação http](https://www.iis.net/configreference/system.webserver/httpcompression).
