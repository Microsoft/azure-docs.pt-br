---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c0ce4e882f270f5e0c789a608aaada5c6c9cba92
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171781"
---
Se houver problemas para se conectar, verifique os seguintes itens:

- Se você tiver exportado um certificado do cliente com o **Assistente para Exportação de Certificados**, verifique se o exportou como um arquivo .pfx e selecionou **Incluir todos os certificados no caminho de certificação se possível**. Quando você exporta usando esse valor, as informações do certificado raiz também são exportadas. Após instalar o certificado no computador cliente, o certificado raiz no arquivo .pfx também será instalado. Para verificar se o certificado raiz está instalado, abra **Gerenciar certificados do usuário** e selecione **Autoridades de Certificação Confiáveis\Certificados**. Verifique se o certificado raiz está listado para que a autenticação funcione.

- Se você usou um certificado emitido com uma solução de AC corporativa e estiver com problemas na autenticação, verifique a ordem de autenticação no certificado do cliente. Verifique a ordem da lista de autenticação ao clicar duas vezes no certificado do cliente e selecionar a guia **Detalhes** e **Uso Avançado de Chave**. Verifique se a lista mostra *Autenticação de cliente* como o primeiro item. Caso contrário, é necessário emitir um certificado de cliente com base no modelo do usuário que tem a *Autenticação de Cliente* como o primeiro item na lista.

- Para saber mais sobre solução de problemas de P2S, confira [Solucionar problemas de conexões P2S](../articles/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).