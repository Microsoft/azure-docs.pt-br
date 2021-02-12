---
title: Solução de problemas Apache Kafka para nuvem confluente-soluções de parceiros do Azure
description: Este artigo fornece informações sobre solução de problemas e perguntas frequentes (FAQ) para a nuvem confluente no Azure.
ms.service: partner-services
ms.topic: conceptual
ms.date: 01/15/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: cbf166086a489165e8100dafd7c212ab6c298b41
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253321"
---
# <a name="troubleshooting-apache-kafka-for-confluent-cloud-solutions"></a>Solução de problemas Apache Kafka para soluções de nuvem confluentes

Este documento contém informações sobre como solucionar problemas de suas soluções que usam Apache Kafka para nuvem confluente.

Se você não encontrar uma resposta ou não puder resolver um problema, [crie uma solicitação por meio do portal do Azure](manage.md#get-support) ou contate o [suporte do Fluent](https://support.confluent.io).

## <a name="cant-find-offer-in-the-marketplace"></a>Não é possível localizar a oferta no Marketplace

Para localizar a oferta no Azure Marketplace, use as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com), selecione **criar um recurso**.
1. Pesquise _Apache Kafka na nuvem confluente_.
1. Selecione o bloco do aplicativo.

Se a oferta não for exibida, entre em contato com o [suporte do Fluent](https://support.confluent.io). Sua ID de locatário Azure Active Directory deve estar na lista de locatários permitidos. Para saber como localizar sua ID de locatário, consulte [como localizar sua ID de locatário Azure Active Directory](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md).

## <a name="conflict-error"></a>Erro de conflito

Se você já se registrou anteriormente para nuvem confluente, você deve usar um novo endereço de email para criar outro recurso de organização de nuvem confluente. Ao usar um endereço de email previamente registrado, você receberá um erro de **conflito** . Registre-se novamente, mas desta vez com um novo endereço de email.

## <a name="deploymentfailed-error"></a>Erro de DeploymentFailed

Se você receber um erro **DeploymentFailed** , verifique o status da sua assinatura do Azure. Verifique se ele não está suspenso e se não tem nenhum problema de cobrança.

## <a name="resource-isnt-displayed"></a>O recurso não é exibido

Se as folhas de **visão geral** ou **excluir** folhas para a nuvem confluente não forem exibidas no portal, tente atualizar a página. Esse erro pode ser um problema intermitente com o Portal. Se isso não funcionar, entre em contato com o [suporte do Fluent](https://support.confluent.io).

Se o recurso de nuvem confluente não for encontrado na lista **todos os recursos** do Azure, entre em contato com o [suporte do Fluent](https://support.confluent.io).

## <a name="resource-creation-takes-long-time"></a>A criação de recursos leva muito tempo

Se o processo de implantação levar mais de três horas para ser concluído, entre em contato com o suporte.

Se a implantação falhar e o recurso de nuvem confluente tiver um status de `Failed` , exclua o recurso. Após a exclusão, tente criar o recurso novamente.

## <a name="offer-plan-doesnt-load"></a>O plano de oferta não é carregado

Esse erro pode ser um problema intermitente com o portal do Azure. Tente implantar a oferta novamente.

## <a name="unable-to-delete"></a>Não é possível excluir

Se não for possível excluir recursos confluentes, verifique se você tem permissões para excluir o recurso. Você deve ter permissão para executar ações Microsoft. confluente/*/Delete. Para obter informações sobre como exibir permissões, consulte [listar atribuições de função do Azure usando o portal do Azure](../../role-based-access-control/role-assignments-list-portal.md).

Se você tiver as permissões corretas, mas ainda não puder excluir o recurso, entre em contato com o [suporte do Fluent](https://support.confluent.io). Essa condição pode estar relacionada à política de retenção do influente. O suporte do influente pode excluir a organização e o endereço de email para você.

## <a name="unable-to-use-single-sign-on"></a>Não é possível usar o logon único

Se o SSO não estiver funcionando para o portal de SaaS de nuvem confluente, verifique se você está usando o email de Azure Active Directory correto. Você também deve ter consentido para permitir o acesso ao portal de SaaS (software como serviço) de nuvem confluente. Para obter mais informações, consulte as [diretrizes de logon único](manage.md#single-sign-on).

Se o problema persistir, entre em contato com o [suporte do Fluent](https://support.confluent.io).

## <a name="next-steps"></a>Próximas etapas

Saiba como [gerenciar sua instância](manage.md) do Apache Kafka para nuvem confluente.
