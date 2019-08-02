---
title: 'Proxy de Aplicativo Azure AD: Histórico de lançamento de versão | Microsoft Docs'
description: Este artigo lista todas as versões do Proxy de aplicativo do Azure AD e descreve os novos recursos e correção de problemas
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97b54d57a13f0f2b47633a25f1a44efdfa17d11a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595052"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Proxy de Aplicativo Azure AD: Histórico de lançamento de versões
Este artigo lista as versões e recursos de Proxy de aplicativo do Azure Active Directory (Azure AD) que foram lançados. A equipe do AD do Azure atualiza regularmente o Proxy de aplicativo com novos recursos e funcionalidades. Conectores de Proxy de aplicativo são atualizadas automaticamente quando uma nova versão for lançada.

Aqui está uma lista de recursos relacionados:

Recurso |  Detalhes
--------- | --------- |
Como habilitar o Proxy de aplicativo | Pré-requisitos para habilitar o Proxy de aplicativo e instalar e registrar um conector são descritas neste [tutorial](application-proxy-add-on-premises-application.md).
Noções básicas sobre conectores de Proxy de Aplicativo Azure AD | Saiba mais sobre [gerenciamento de conector](application-proxy-connectors.md) e como conectores [atualização automática](application-proxy-connectors.md#automatic-updates).
Baixe o conector de Proxy de aplicativo do AD do Azure |  [Baixe o conector mais recente](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download).

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>Status de liberação

20 de setembro de 2018: Liberado para download

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

- Adicionado suporte de WebSocket para o aplicativo QlikSense. Para saber mais sobre como integrar o QlikSense com Proxy de aplicativo, consulte [instruções passo a passo](application-proxy-qlik.md). 
- Aprimorado o Assistente de instalação para torná-lo mais fácil de configurar um proxy de saída. 
- Definir o TLS 1.2 como o protocolo padrão para conectores. 
- Adicionado um novo contrato de licença de usuário final (EULA).  

### <a name="fixed-issues"></a>Problemas corrigidos

- Corrigido um bug que causou alguns vazamentos de memória no conector.
- Atualizada a versão do barramento de serviço do Azure, que inclui uma correção de bug para problemas de tempo limite do conector.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>Status de liberação

19 de janeiro de 2018: Liberado para download

### <a name="fixed-issues"></a>Problemas corrigidos

- Adicionado suporte para domínios personalizados que precisam de tradução de domínio do cookie.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>Status de liberação 

25 de maio de 2017: Liberado para download 

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos 

Controle aprimorado sobre limites de conexão de saída dos conectores. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>Status de liberação

15 de abril de 2017: Liberado para download

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

- Integração simplificada e gerenciamento com menos portas necessárias. Proxy de aplicativo agora requer a abertura de apenas duas portas de saída padrão: 443 e 80. O Proxy de aplicativo continua a usar as conexões de saída somente, portanto você não precisa todos os componentes em uma rede de Perímetro. Para obter detalhes, consulte nosso [documentação de configuração](application-proxy-add-on-premises-application.md).  
- Se compatível com o seu firewall ou proxy externos, agora você pode abrir sua rede pelo DNS, em vez de intervalo de IP. Serviços de Proxy de aplicativo exigem conexões com o *. msappproxy.net e *. servicebus.windows.net apenas.


## <a name="earlier-versions"></a>Versões anteriores

Se você estiver usando uma versão do conector de Proxy de aplicativo anterior ao 1.5.36.0, atualize para a versão mais recente para garantir que você tenha a versão mais recente totalmente recursos com suporte.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [de acesso remoto a aplicativos locais por meio do Proxy de aplicativo do Azure AD](application-proxy.md).
- Para começar a usar o Proxy de a Aplicativo, consulte [Tutorial: Adicionar um aplicativo local para acesso remoto por meio do Proxy de Aplicativo](application-proxy-add-on-premises-application.md).
