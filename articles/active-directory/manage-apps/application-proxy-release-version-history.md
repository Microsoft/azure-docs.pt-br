---
title: 'Proxy de Aplicativo do Azure Active Directory: histórico de lançamento de versão'
description: Este artigo lista todas as versões do Proxy de Aplicativo do Azure Active Directory e descreve os novos recursos e os problemas corrigidos.
services: active-directory
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 02/17/2021
ms.subservice: app-mgmt
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 0244e0ca66f452649d3136eb122d1c2666182ae2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200979"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Proxy de Aplicativo do AD do Azure: histórico de lançamento de versão
Este artigo lista as versões e os recursos do proxy de aplicativo Azure Active Directory (AD do Azure) que foram lançados. A equipe do Azure AD atualiza regularmente o proxy de aplicativo com novos recursos e funcionalidades. Os conectores de proxy de aplicativo são atualizados automaticamente quando uma nova versão é liberada. 

É recomendável verificar se as atualizações automáticas estão habilitadas para seus conectores para garantir que você tenha os recursos e correções de bug mais recentes. A Microsoft fornece suporte direto para a versão mais recente do conector e uma versão anterior.

Aqui está uma lista de recursos relacionados:

| Recurso                                         | Detalhes                                                      |
| ------------------------------------------------ | ------------------------------------------------------------ |
| Como habilitar o proxy de aplicativo                  | Os pré-requisitos para habilitar o proxy de aplicativo e instalar e registrar um conector são descritos neste [tutorial](application-proxy-add-on-premises-application.md). |
| Noções básicas sobre conectores de Proxy de Aplicativo Azure AD | Saiba mais sobre o [Gerenciamento de conectores](application-proxy-connectors.md) e como os conectores são [atualizados automaticamente](application-proxy-connectors.md#automatic-updates). |
| Download do conector do Azure Proxy de Aplicativo do AD    | [Baixe o conector mais recente](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download). |

## <a name="1519750"></a>1.5.1975.0

### <a name="release-status"></a>Status de liberação

22 de julho de 2020: liberado para download esta versão só está disponível para instalação por meio da página de download. Um lançamento de atualização automática desta versão será lançado posteriormente.

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos
-   Suporte aprimorado para ambientes de nuvem do Azure governamental. Para obter as etapas sobre como instalar corretamente o conector para a nuvem do Azure governamental, examine os [pré-requisitos](../hybrid/reference-connect-government-cloud.md#allow-access-to-urls) e [as etapas de instalação](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud).
- Suporte para usar o cliente Web Serviços de Área de Trabalho Remota com o proxy de aplicativo. Consulte [publicar área de trabalho remota com o proxy de aplicativo do AD do Azure](application-proxy-integrate-with-remote-desktop-services.md) para obter mais detalhes.
- Negociações de extensão de WebSocket aprimoradas. 
- Suporte para roteamento otimizado entre grupos de conectores e serviços de nuvem de proxy de aplicativo baseados na região. Consulte [otimizar o fluxo de tráfego com proxy de aplicativo do Azure Active Directory](application-proxy-network-topology.md) para obter mais detalhes. 

### <a name="fixed-issues"></a>Problemas corrigidos
- Corrigido um problema de WebSocket que forçou cadeias de caracteres em minúsculas.
- Correção de um problema que fazia com que os conectores não respondessem ocasionalmente.

## <a name="1516260"></a>1.5.1626.0

### <a name="release-status"></a>Status de liberação

17 de julho de 2020: liberado para download. Esta versão só está disponível para instalação por meio da página de download. Um lançamento de atualização automática desta versão será lançado posteriormente.

### <a name="fixed-issues"></a>Problemas corrigidos
- Problema de vazamento de memória resolvido presente na versão anterior
- Aprimoramentos gerais para o suporte ao WebSocket

## <a name="1515260"></a>1.5.1526.0

### <a name="release-status"></a>Status de liberação

07 de abril de 2020: lançamento para download esta versão só está disponível para instalação por meio da página de download. Um lançamento de atualização automática desta versão será lançado posteriormente.

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos
-   Os conectores usam apenas o TLS 1,2 para todas as conexões. Consulte [pré-requisitos do conector](application-proxy-add-on-premises-application.md#prerequisites) para obter mais detalhes.
- Sinalização aprimorado entre o conector e os serviços do Azure. Isso inclui o suporte a sessões confiáveis para comunicação do WCF entre o conector e os serviços do Azure e os aprimoramentos de cache do DNS para comunicações WebSocket.
- Suporte para configurar um proxy entre o conector e o aplicativo de back-end. Para obter mais informações, consulte [trabalhar com servidores proxy locais existentes](application-proxy-configure-connectors-with-proxy-servers.md).

### <a name="fixed-issues"></a>Problemas corrigidos
- Removido o fallback para a porta 8080 para comunicações do conector para os serviços do Azure.
- Foram adicionados rastreamentos de depuração para comunicações WebSocket. 
- Resolvido o preservação do atributo SameSite quando definido em cookies de aplicativo de back-end.

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>Status de liberação

20 de setembro de 2018: lançamento para download

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

- Adicionado suporte WebSocket para o aplicativo QlikSense. Para saber mais sobre como integrar o QlikSense com o proxy de aplicativo, consulte este [passo a passos](application-proxy-qlik.md). 
- Assistente de instalação aprimorado para facilitar a configuração de um proxy de saída. 
- Defina o TLS 1,2 como o protocolo padrão para conectores. 
- Um novo contrato de licença do End-User (EULA) foi adicionado.  

### <a name="fixed-issues"></a>Problemas corrigidos

- Corrigido um bug que causou vazamentos de memória no conector.
- Atualização da versão do barramento de serviço do Azure, que inclui uma correção de bug para problemas de tempo limite do conector.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>Status de liberação

19 de janeiro de 2018: liberado para download

### <a name="fixed-issues"></a>Problemas corrigidos

- Suporte adicionado para domínios personalizados que precisam de tradução de domínio no cookie.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>Status de liberação 

25 de maio de 2017: liberado para download 

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos 

Controle aprimorado dos limites de conexão de saída dos conectores. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>Status de liberação

15 de abril de 2017: liberado para download

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

- Integração e gerenciamento simplificados com menos portas necessárias. O proxy de aplicativo agora requer a abertura de apenas duas portas de saída padrão: 443 e 80. O proxy de aplicativo continua a usar apenas conexões de saída, portanto, você ainda não precisa de nenhum componente em uma DMZ. Para obter detalhes, consulte nossa [documentação de configuração](application-proxy-add-on-premises-application.md).  
- Se houver suporte pelo seu proxy ou firewall externo, agora você poderá abrir sua rede pelo DNS em vez do intervalo de IP. Os serviços de proxy de aplicativo exigem conexões somente com *. msappproxy.net e *. servicebus.windows.net.


## <a name="earlier-versions"></a>Versões anteriores

Se você estiver usando uma versão do conector de proxy de aplicativo anterior à 1.5.36.0, atualize para a versão mais recente para garantir que você tenha os recursos com suporte total mais recentes.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre o [acesso remoto a aplicativos locais por meio do Azure proxy de aplicativo do AD](application-proxy.md).
- Para começar a usar o proxy de aplicativo, consulte [tutorial: adicionar um aplicativo local para acesso remoto por meio do proxy de aplicativo](application-proxy-add-on-premises-application.md).
