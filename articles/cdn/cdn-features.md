---
title: Comparação dos recursos de produto da CDN (Rede de Distribuição de Conteúdo) do Azure
description: Saiba mais sobre os recursos para os quais cada produto CDN do Azure oferece suporte.
services: cdn
documentationcenter: ''
author: asudbring
ms.service: azure-cdn
ms.topic: overview
ms.date: 11/15/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 9aa394cda245bd3a457a16c19660bfe08553d14d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058877"
---
# <a name="what-are-the-comparisons-between-azure-cdn-product-features"></a>Quais são as comparações entre os recursos de produto da CDN do Azure?

A CDN do Azure (Rede de Distribuição de Conteúdo) inclui quatro produtos: 

* **CDN Standard do Azure da Microsoft**
* **CDN Standard do Azure da Akamai**
* **CDN Standard do Azure da Verizon**
* **CDN Premium do Azure da Verizon**. 

A tabela a seguir compara os recursos disponíveis em cada produto.

| **Recursos e otimizações de desempenho** | **Microsoft Standard** | **Akamai Standard** | **Verizon Standard** | **Verizon Premium** |
| --- | --- | --- | --- | --- |
| [Aceleração de site dinâmica](./cdn-dynamic-site-acceleration.md)  | Oferecido por meio do [Azure Front Door Service](../frontdoor/front-door-overview.md) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Aceleração de site dinâmico – compactação de imagem adaptável](./cdn-dynamic-site-acceleration.md#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Aceleração de site dinâmico – pré-busca de objeto](./cdn-dynamic-site-acceleration.md#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [Otimização geral de entrega na Web](./cdn-optimization-overview.md#general-web-delivery)  | **&#x2713;** | **&#x2713;** , Selecione este tipo de otimização se o tamanho médio do arquivo for menor que 10 MB  | **&#x2713;** |  **&#x2713;** |
| [Otimização do streaming de Vídeo](./cdn-media-streaming-optimization.md)  | via Entrega na Web geral | **&#x2713;**  | via Entrega na Web geral |  via Entrega na Web geral |
| [Otimização de arquivos grandes](./cdn-large-file-optimization.md)  | via Entrega na Web geral | **&#x2713;** , Selecione este tipo de otimização se o tamanho médio do arquivo for maior que 10 MB   | via Entrega na Web geral |  via Entrega na Web geral |
| Alterar tipo de otimização | |**&#x2713;** | | |
| Porta de origem |Todas as portas TCP |[Portas de origem permitidas](/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |Todas as portas TCP |Todas as portas TCP |
| [GSLB (Balanceamento de carga do servidor global)](../traffic-manager/traffic-manager-load-balancing-azure.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Limpeza rápida](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;** , A limpeza de tudo e a limpeza de caractere curinga não foram suportadas corretamente na CDN do Azure do Akamai |**&#x2713;** |**&#x2713;** |
| [Pré-carregamento de ativos](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Configurações de cache/cabeçalho (usando [regras de cache](cdn-caching-rules.md))  |**&#x2713;** usando o [mecanismo de regras Standard](cdn-standard-rules-engine.md)  |**&#x2713;** |**&#x2713;** | |
| Mecanismo de distribuição de conteúdo personalizável e baseado em regras |**&#x2713;** usando o [mecanismo de regras Standard](cdn-standard-rules-engine.md)  | | |**&#x2713;** usando o [mecanismo de regras](./cdn-verizon-premium-rules-engine.md) |
| Configurações de cache/cabeçalho  |**&#x2713;** usando o [mecanismo de regras Standard](cdn-standard-rules-engine.md) | | |**&#x2713;** usando o [mecanismo de regras Premium](./cdn-verizon-premium-rules-engine.md) |
| Redirecionamento/reescrita de URL |**&#x2713;** usando o [mecanismo de regras Standard](cdn-standard-rules-engine.md)  | | |**&#x2713;** usando o [mecanismo de regras Premium](./cdn-verizon-premium-rules-engine.md) |
| Regras de dispositivo móvel  |**&#x2713;** usando o [mecanismo de regras Standard](cdn-standard-rules-engine.md) | | |**&#x2713;** usando o [mecanismo de regras Premium](./cdn-verizon-premium-rules-engine.md) |
| [Cache de cadeia de caracteres de consulta](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Pilha dupla IPv4/IPv6 | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Suporte do HTTP/2](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Segurança** | **Microsoft Standard** | **Akamai Standard** | **Verizon Standard** | **Verizon Premium** | 
| Suporte ao protocolo HTTPS com o ponto de extremidade CDN | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTPS de domínio personalizado](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;** , exige o CNAME direto para habilitar |**&#x2713;** |**&#x2713;** |
| [Suporte a nome de domínio personalizado](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Filtragem geográfica](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Autenticação de token](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [Proteção DDOS](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Traga o seu próprio certificado](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) |**&#x2713;** |  | **&#x2713;** | **&#x2713;** |
| Versões do TLS com suporte | TLS 1.2, TLS 1.0/1.1 – [Configurável](/rest/api/cdn/cdn/customdomains/enablecustomhttps#usermanagedhttpsparameters) | TLS 1.2 | TLS 1.2 | TLS 1.2 |
||||
| **Análise e relatórios** | **Microsoft Standard** | **Akamai Standard** | **Verizon Standard** | **Verizon Premium** | 
| [Logs de diagnóstico do Azure](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Relatórios de núcleo da Verizon](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Relatórios personalizados da Verizon](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Relatórios avançados de HTTP](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [Estatísticas em tempo real](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [Desempenho do nó de borda](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [Alertas em tempo real](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **Facilidade de uso** | **Microsoft Standard** | **Akamai Standard** | **Verizon Standard** | **Verizon Premium** | 
| Fácil integração com serviços do Azure como [Armazenamento](cdn-create-a-storage-account-with-cdn.md), [Aplicativos Web](cdn-add-to-web-app.md) e [Serviços de Mídia](../media-services/previous/media-services-portal-manage-streaming-endpoints.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Gerenciamento via [API REST](/rest/api/cdn/), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md) ou [PowerShell](cdn-manage-powershell.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Tipos de MIME de compactação](./cdn-improve-performance.md)  |Configurável |Configurável |Configurável  |Configurável  |
| Codificações de compactação  |gzip, brotli |gzip |gzip, deflate, bzip2, brotli  |gzip, deflate, bzip2, brotli  |

## <a name="migration"></a>Migração

Para saber mais sobre como migrar um perfil do **Azure CDN Standard da Verizon** para **Azure CDN Premium da Verizon**, veja [Migrar um perfil do Azure CDN de Standard Verizon para Premium Verizon](cdn-migrate.md). 

> [!NOTE]
> Há um caminho de atualização do Standard Verizon para o Premium Verizon. No momento, não há nenhum mecanismo de conversão entre outros produtos.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [CDN do Azure](cdn-overview.md).
