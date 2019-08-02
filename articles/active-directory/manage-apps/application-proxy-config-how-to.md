---
title: Como configurar um aplicativo de Application Proxy| Microsoft Docs
description: Saiba como criar e configurar um aplicativo de application Proxy em poucas etapas simples
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7aaf2eb282bc3fd0b9f3853ce493c479a3d3c3a9
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807848"
---
# <a name="how-to-configure-an-application-proxy-application"></a>Como configurar um aplicativo de Application Proxy

Este artigo ajuda você a entender como configurar um aplicativo de Proxy de aplicativo no Azure AD para expor seus aplicativos locais para a nuvem.

## <a name="recommended-documents"></a>Documentos recomendados

Para saber mais sobre a criação de um aplicativo de Application Proxy por meio do Portal de administração e configurações iniciais, siga o [publicar aplicativos usando o Application Proxy do Azure AD](application-proxy-add-on-premises-application.md).

Para obter detalhes sobre como configurar conectores, consulte [habilitar Application Proxy no portal do Azure](application-proxy-add-on-premises-application.md).

Para obter informações sobre como carregar certificados e usar domínios personalizados, consulte [trabalhando com domínios personalizados no Application Proxy do Azure AD](application-proxy-configure-custom-domain.md).

## <a name="create-the-applicationsetting-the-urls"></a>Criar o aplicativo/Definindo as URLs

Se você estiver seguindo as etapas na documentação [publicar aplicativos usando o Application Proxy do Azure AD](application-proxy-add-on-premises-application.md) e está recebendo um erro ao criar o aplicativo, consulte os detalhes do erro para obter informações e sugestões para corrigir o aplicativo. A maioria das mensagens de erro incluem uma correção sugerida. Para evitar erros comuns, verifique se:

- Você é um administrador com permissão para criar um aplicativo de Application Proxy
- A URL interna é exclusiva
- A URL externa é exclusiva
- As URLs começam com http ou https e terminam com um "/"
- A URL deve ser um nome de domínio, não um endereço IP

A mensagem de erro deve exibir no canto superior direito, quando você cria o aplicativo. Você também pode selecionar o ícone de notificação para ver as mensagens de erro.

![Mostra onde encontrar o prompt de notificação no portal do Azure](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>Configurar grupos de conectores/conectores

Se você tiver dificuldades para configurar seu aplicativo por causa de aviso sobre conectores e grupos de conector, consulte as instruções sobre como habilitar o Application Proxy para obter detalhes sobre como baixar conectores. Se você quiser saber mais sobre conectores, consulte a [documentação de conectores](application-proxy-connectors.md).

Se os conectores estão inativos, isso significa que eles são incapazes de alcançar o serviço. Isso geralmente é porque todas as portas necessárias não estão abertas. Para ver uma lista de portas necessárias, consulte a seção de pré-requisitos da documentação para habilitação do Application Proxy.

## <a name="upload-certificates-for-custom-domains"></a>Carregar certificados para domínios personalizados

Domínios personalizados permitem que você especifique o domínio de suas URLs externas. Para usar domínios personalizados, você precisa carregar o certificado para esse domínio. Para obter informações sobre como usar domínios personalizados e certificados, consulte [trabalhando com domínios personalizados no Application Proxy do Azure AD](application-proxy-configure-custom-domain.md).

Se você estiver encontrando problemas ao carregar o certificado, procure as mensagens de erro no portal para obter informações adicionais sobre o problema com o certificado. Problemas de certificado comuns incluem:

- Certificado expirado
- O certificado é auto-assinado
- Certificado está sem a chave privada

A mensagem de erro exibida no canto superior direito ao tentar carregar o certificado. Você também pode selecionar o ícone de notificação para ver as mensagens de erro.

## <a name="next-steps"></a>Próximas etapas

[Publicar aplicativos usando o Proxy de Aplicativo do AD do Azure](application-proxy-add-on-premises-application.md)
