---
title: Usar um mecanismo de regras para impor o HTTPS na CDN do Azure Padrão | Microsoft Docs
description: Use o mecanismo de regras da CDN do Azure (Rede de Distribuição de Conteúdo Padrão do Microsoft Azure) para personalizar como a CDN do Azure processa solicitações HTTP, incluindo o bloqueio da entrega de determinados tipos de conteúdo, a definição de uma política de cache e a modificação de cabeçalhos HTTP. Neste artigo, saiba como criar uma regra para redirecionar os usuários para HTTPS.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: b94798580b2d4ba746c152486c0de753cf64d2c9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564893"
---
# <a name="set-up-the-standard-rules-engine-for-azure-cdn"></a>Configurar o mecanismo de regras Standard para a CDN do Azure

Este artigo descreve como configurar e usar o mecanismo de regras Standard da CDN do Azure (Rede de Distribuição de Conteúdo do Azure).

## <a name="standard-rules-engine"></a>mecanismo de regras Standard

Use o mecanismo de regras Standard da CDN do Azure para personalizar como as solicitações HTTP são processadas. Por exemplo, você pode usar o mecanismo de regras para impor o uso de protocolos específicos na distribuição de conteúdo, definir uma política de cache ou modificar um cabeçalho HTTP. Este artigo demonstra como criar uma regra que redireciona automaticamente os usuários para HTTPS. 

> [!NOTE]
> O mecanismo de regras descrito neste artigo está disponível apenas para a CDN do Azure Padrão da Microsoft. 

## <a name="redirect-users-to-https"></a>Redirecionar os usuários para HTTPS

1. Nos seus perfis da Microsoft, acesse a Rede de Distribuição de Conteúdo do Azure.

1. Na página **Perfil da CDN**, selecione o ponto de extremidade para o qual deseja criar regras.
  
1. Selecione a guia **Mecanismo de Regras**.
   
    O painel **Mecanismo de Regras** será aberto e exibirá a lista das regras globais disponíveis. 
   
    [![Página Novas regras da CDN do Azure](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > A ordem na qual são listadas as várias regras afeta como elas são processadas. As ações especificadas em uma regra podem ser substituídas por uma regra seguinte.
   >

1. Selecione **Adicionar regra** e insira um nome da regra. Os nomes da regra precisam começar com uma letra e só podem conter números e letras.

1. Para identificar o tipo de solicitação à qual a regra se aplica, crie uma condição de correspondência:
    1. Selecione **Adicionar condição** e selecione a condição de correspondência **Protocolo de solicitação**.
    1. Em **Operador**, selecione **É igual a**.
    1. Em **Valor**, selecione **HTTP**.
   
   [![Condição de correspondência de regra da CDN do Azure](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > Selecione uma entre várias condições de correspondência na lista suspensa **Adicionar condição**. Para obter uma lista detalhada das condições de correspondência, confira [Condições de correspondência do mecanismo de regras Standard](cdn-standard-rules-engine-match-conditions.md).
   
1. Selecione a ação a ser aplicada às solicitações que atendem à condição de correspondência:
   1. Selecione **Adicionar ação** e **Redirecionamento de URL**.
   1. Em **Tipo**, selecione **Encontrado (302)** .
   1. Para **Protocolo**, selecione **HTTPS**.
   1. Mantenha todos os outros campos em branco para usar os valores de entrada.
   
   [![Ação de regra da CDN do Azure](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > Selecione uma entre várias ações na lista suspensa **Adicionar ação**. Para obter uma lista detalhada das ações, confira [Ações do mecanismo de regras Standard](cdn-standard-rules-engine-actions.md).

6. Selecione **Salvar** para salvar a nova regra. A regra agora está disponível para uso.
   
   > [!IMPORTANT]
   > As alterações nas regras podem levar até 15 minutos para serem propagadas pela CDN do Azure.
   >
   

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da CDN do Azure](cdn-overview.md)
- [Referência do mecanismo de regras Standard](cdn-standard-rules-engine-reference.md)
- [Condições de correspondência do mecanismo de regras Standard](cdn-standard-rules-engine-match-conditions.md)
- [Ações no mecanismo de regras Standard](cdn-standard-rules-engine-actions.md)
