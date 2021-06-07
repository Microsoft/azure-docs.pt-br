---
title: Configurar o servidor MFA-Azure Active Directory
description: Saiba como definir as configurações para o servidor Azure MFA no portal do Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb28f415b7d08b4c4430ed90a7ccbfe3a333416d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101689038"
---
# <a name="configure-mfa-server-settings"></a>Definir configurações do servidor MFA

Este artigo ajuda você a gerenciar as configurações do servidor MFA do Azure no portal do Azure.

> [!IMPORTANT]
> A partir de 1º de julho de 2019, a Microsoft não oferecerá mais o servidor MFA para novas implantações. Os novos clientes que desejarem exigir a autenticação multifator de seus usuários devem usar a autenticação multifator do Azure AD baseada em nuvem. Os clientes existentes que ativaram o servidor MFA antes de 1º de julho poderão baixar a versão mais recente, atualizações futuras e gerar credenciais de ativação como de costume.

As seguintes configurações do servidor MFA estão disponíveis:

| Recurso | Descrição |
| ------- | ----------- |
| Configurações do servidor | Faça o download do Servidor MFA e gere credenciais de ativação para inicializar seu ambiente |
| [Desvio único](#one-time-bypass) | Permitir que um usuário autentique sem executar a autenticação multifator por um período limitado. |
| [Regras de cache](#caching-rules) |  O cache é usado principalmente quando os sistemas locais, como VPN, enviam várias solicitações de verificação enquanto a primeira solicitação ainda está em andamento. Esse recurso permite que as próximas solicitações ocorram automaticamente depois que o usuário conclui a primeira verificação em andamento. |
| Status do servidor | Veja o status dos seus servidores MFA locais incluindo versão, status, IP e última hora e data de comunicação. |

## <a name="one-time-bypass"></a>Desvio único

O recurso de bypass único permite que um usuário autentique uma única vez sem executar a autenticação multifator. O bypass é temporário e expira após um número de segundos especificado. Quando o aplicativo móvel ou o telefone não estiver recebendo notificações ou chamadas telefônicas, você poderá permitir um bypass avulso para que o usuário possa acessar o recurso desejado.

Para criar um bypass único, conclua as seguintes etapas:

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador.
1. Procure e selecione **Azure Active Directory** e, em seguida, navegue até **segurança**  >  **MFA** de  >  **passagem única**.
1. Selecione **Adicionar**.
1. Se necessário, selecione o grupo de replicação para o bypass.
1. Insira o nome de usuário como `username@domain.com`. Insira o número de segundos que o bypass deve durar e o motivo do bypass.
1. Selecione **Adicionar**. O tempo limite entra em vigor imediatamente. O usuário precisa entrar antes que o bypass avulso se expire.

Você também pode exibir o relatório de bypass único nessa mesma janela.

## <a name="caching-rules"></a>Regras de cache

Você pode definir um período de tempo para permitir tentativas de autenticação depois que um usuário é autenticado usando o recurso _cache_. As tentativas de autenticação subsequentes do usuário, dentro do período de tempo especificado, ocorrerão automaticamente.

O cache é usado principalmente quando os sistemas locais, como VPN, enviam várias solicitações de verificação enquanto a primeira solicitação ainda está em andamento. Esse recurso permite que as próximas solicitações ocorram automaticamente depois que o usuário conclui a primeira verificação em andamento.

>[!NOTE]
> O recurso de cache não se destina a ser usado para entradas no Azure AD (Azure Active Directory).

Para configurar o cache, conclua as seguintes etapas:

1. Navegue para **Azure Active Directory** > **Segurança** > **MFA** > **Regras de cache**.
1. Selecione **Adicionar**.
1. Selecione o **tipo de cache** na lista suspensa. Insira o número máximo de **segundos de cache**.
1. Se necessário, selecione um tipo de autenticação e especifique um aplicativo.
1. Selecione **Adicionar**.

## <a name="next-steps"></a>Próximas etapas

As opções adicionais de configuração do servidor MFA estão disponíveis no console Web do servidor MFA. Você também pode [Configurar o servidor MFA do Azure para alta disponibilidade](howto-mfaserver-deploy-ha.md).
