---
title: Comparar recursos do produto CDN (Rede de Distribuição de Conteúdo) do Azure| Microsoft Docs
description: Saiba mais sobre os recursos para os quais cada produto CDN do Azure oferece suporte.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: mdgattuso
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 06/05/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: e34fbe200420fef1653e21bb44ac2f7e0de90fb7
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593863"
---
# <a name="compare-azure-cdn-product-features"></a>Comparar recursos do produto CDN do Azure

A CDN do Azure (Rede de Distribuição de Conteúdo) inclui quatro produtos: **CDN Standard do Azure da Microsoft**, **CDN Standard do Azure da Akamai**, **CDN Standard do Azure da Verizon** e **CDN Premium do Azure da Verizon**. Para saber mais sobre como migrar um perfil do **Azure CDN Standard da Verizon** para **Azure CDN Premium da Verizon**, veja [Migrar um perfil do Azure CDN de Standard Verizon para Premium Verizon](cdn-migrate.md).

A tabela a seguir compara os recursos disponíveis em cada produto.

| **Recursos e otimizações de desempenho** | **Microsoft Standard** | **Akamai Standard** | **Verizon Standard** | **Verizon Premium** |
| --- | --- | --- | --- | --- |
| [Aceleração de site dinâmica](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  | Oferecido por meio do [Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Aceleração de site dinâmico – compactação de imagem adaptável](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Aceleração de site dinâmico – pré-busca de objeto](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [Otimização geral de entrega na Web](https://docs.microsoft.com/azure/cdn/cdn-optimization-overview#general-web-delivery)  | **&#x2713;** | **&#x2713;** , Selecione este tipo de otimização se o tamanho médio do arquivo for menor que 10 MB  | **&#x2713;** |  **&#x2713;** |
| [Otimização do streaming de Vídeo](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | via Entrega na Web geral | **&#x2713;**  | via Entrega na Web geral |  via Entrega na Web geral |
| [Otimização de arquivos grandes](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | via Entrega na Web geral | **&#x2713;** , Selecione este tipo de otimização se o tamanho médio do arquivo for maior que 10 MB   | via Entrega na Web geral |  via Entrega na Web geral |
| Alterar tipo de otimização | |**&#x2713;** | | |
| Porta de origem |Todas as portas TCP |[Portas de origem permitidas](https://docs.microsoft.com/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |Todas as portas TCP |Todas as portas TCP |
| [GSLB (Balanceamento de carga do servidor global)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Limpeza rápida](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;** , A limpeza de tudo e a limpeza de caractere curinga não foram suportadas corretamente na CDN do Azure do Akamai |**&#x2713;** |**&#x2713;** |
| [Pré-carregamento de ativos](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Configurações de cache/cabeçalho (usando [regras de cache](cdn-caching-rules.md))  |  |**&#x2713;** |**&#x2713;** | |
| Mecanismo de distribuição de conteúdo personalizável e baseado em regras (usando [mecanismo de regras](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| Configurações de cache/cabeçalho (usando o [mecanismo de regras](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| Redirecionamento/reconfiguração de URL (usando o [mecanismo de regras](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| Regras de dispositivo móvel (usando o [mecanismo de regras](cdn-rules-engine.md))  |  | | |**&#x2713;** |
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
| [Traga o seu próprio certificado](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) |**&#x2713;** |  | **&#x2713;** | **&#x2713;** |
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
| Fácil integração com serviços do Azure como [Armazenamento](cdn-create-a-storage-account-with-cdn.md), [Aplicativos Web](cdn-add-to-web-app.md) e [Serviços de Mídia](../media-services/media-services-portal-manage-streaming-endpoints.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Gerenciamento via [API REST](/rest/api/cdn/), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md) ou [PowerShell](cdn-manage-powershell.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Tipos de MIME de compactação](https://docs.microsoft.com/azure/cdn/cdn-improve-performance)  |Somente padrão |Configurável |Configurável  |Configurável  |
| Codificações de compactação  |gzip, brotli |gzip |gzip, deflate, bzip2, brotili  |gzip, deflate, bzip2, brotili  |






