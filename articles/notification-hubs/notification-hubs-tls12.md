---
title: Atualizações de TLS dos hubs de notificação
description: Saiba mais sobre o suporte para TLS nos hubs de notificação do Azure.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 04/29/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/28/2020
ms.openlocfilehash: 87a3627d7820f9f456ac08e2f20b70af961f817e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87084238"
---
# <a name="transport-layer-security-tls"></a>Protocolo TLS

Para garantir um nível mais alto de segurança, os hubs de notificação desabilitarão o suporte para as versões 1,0 e 1,1 de TLS em **31 de dezembro de 2020** (estendido de 30 de abril de 2020). Esses protocolos mais antigos fornecem criptografia fraca e estão vulneráveis a ataques fera e POODLE. Os aplicativos implantados em dispositivos que executam o Android versão 5 ou superior, ou iOS versão 5 ou superior, não são afetados por essa alteração, pois esses sistemas operacionais dão suporte a TLS 1,2 e o cliente e o servidor negociarão a versão mais recente com suporte mútuo do protocolo na conexão.

Recomendamos que você examine todos os seus aplicativos que usam os hubs de notificação do Azure para garantir que eles usem as bibliotecas mais aplicáveis e pilhas TLS que dão suporte a TLS 1,2.

## <a name="update-apps"></a>Atualizar aplicativos

Você pode garantir que seus aplicativos iOS estejam usando o TLS 1,2 usando o recurso de segurança de rede da Apple chamado segurança de transporte de aplicativo (ATS). O ATS não pode ser usado para SDKs com mais de iOS 9,0 ou macOS 10,11, e você pode ler mais sobre ele na [documentação da Apple](https://developer.apple.com/documentation/security/preventing_insecure_network_connections).

Para aplicativos Android usando instâncias de SSLSocket, defina protocolos habilitados em cada instância de SSLSocket, conforme observado em [setEnabledProtocols](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D)).

A tabela na página de suporte de [compatibilidade do protocolo TLS](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility) ajuda a mapear sistemas operacionais com versões compatíveis do TLS.

Para obter mais informações, consulte a visão geral do [suporte para protocolos TLS no Windows](/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows).

## <a name="next-steps"></a>Próximas etapas

- [Visão geral dos hubs de notificação](notification-hubs-push-notification-overview.md)
