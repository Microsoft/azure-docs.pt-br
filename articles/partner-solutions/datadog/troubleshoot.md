---
title: Solução de problemas do Datadog – soluções de parceiros do Azure
description: Este artigo fornece informações sobre a solução de problemas do Datadog no Azure.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 0e3c82f711de4cd9710c9aafe798a986e3403ed4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563701"
---
# <a name="troubleshooting-datadog-on-azure"></a>Solução de problemas do Datadog no Azure

Este documento contém informações sobre como solucionar problemas de suas soluções que usam o Datadog.

## <a name="purchase-errors"></a>Erros de compra

* A compra falha porque um cartão de crédito válido não está conectado à assinatura do Azure ou um método de pagamento não está associado à assinatura.

  Use uma assinatura do Azure diferente. Ou então, adicione ou atualize o cartão de crédito ou o método de pagamento da assinatura. Para obter mais informações, consulte [atualizando o método de crédito e pagamento](../../cost-management-billing/manage/change-credit-card.md).

* A assinatura do EA não permite compras do Marketplace.

  Use uma assinatura diferente. Ou então, verifique se sua assinatura do EA está habilitada para compra do Marketplace. Para obter mais informações, consulte [habilitar compras do Marketplace](../../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases). Se essas opções não resolverem o problema, entre em contato com o [suporte do Datadog](https://www.datadoghq.com/support).

## <a name="unable-to-create-datadog-resource"></a>Não é possível criar o recurso Datadog

Para configurar a integração do Datadog do Azure, você precisa ter acesso de **Proprietário** na assinatura do Azure. Verifique se você tem o acesso apropriado antes de iniciar a configuração.

## <a name="single-sign-on-errors"></a>Erros de logon único

**Não é possível salvar as configurações de logon único** -esse erro ocorre quando há outro aplicativo empresarial que está usando o identificador SAML Datadog. Para descobrir qual aplicativo está usando, selecione **Editar** na seção configuração básica do SAML.

Para resolver esse problema, desabilite o outro aplicativo ou use o outro aplicativo como o aplicativo empresarial para configurar o SSO do SAML com Datadog. Se você decidir usar o outro aplicativo, verifique se o aplicativo tem as [configurações necessárias](create.md#configure-single-sign-on).

O **aplicativo não está sendo exibido em uma configuração de logon único página** -primeiro, procure a ID do aplicativo. Se nenhum resultado for mostrado, verifique as configurações de SAML do aplicativo. A grade mostra apenas aplicativos com as configurações corretas de SAML. 

A URL do identificador deve ser `https://us3.datadoghq.com/account/saml/metadata.xml` .

A URL de resposta deve ser `https://us3.datadoghq.com/account/saml/assertion` .

A imagem a seguir mostra os valores corretos.
  
:::image type="content" source="media/troubleshoot/troubleshooting.png" alt-text="Verifique as configurações de SAML para o aplicativo Datadog no AAD." border="true":::

**Os usuários convidados convidados para o locatário não podem acessar o logon único** -alguns usuários têm dois endereços de email no portal do Azure. Normalmente, um email é o UPN (nome principal do usuário) e o outro email é um email alternativo.

Ao convidar o usuário convidado, use o UPN de locatário doméstico. Usando o UPN, você mantém o endereço de email em sincronia durante o processo de logon único. Você pode encontrar o UPN procurando o endereço de email no canto superior direito do portal do Azure do usuário.
  
## <a name="logs-not-being-emitted"></a>Logs não emitidos

Somente os recursos listados no Azure Monitor categorias de log de recursos emitem logs para Datadog. Para verificar se o recurso está emitindo logs para Datadog, navegue até configuração de diagnóstico do Azure para o recurso específico. Verifique se há uma configuração de diagnóstico Datadog.

:::image type="content" source="media/troubleshoot/diagnostic-setting.png" alt-text="Configuração de diagnóstico Datadog no recurso do Azure" border="true":::

## <a name="metrics-not-being-emitted"></a>Métricas não emitidas

O recurso Datadog é atribuído a uma função de **leitor de monitoramento** na assinatura do Azure apropriada. Essa função permite que o recurso Datadog colete métricas e envie essas métricas para Datadog.

Para verificar se o recurso tem a atribuição de função correta, abra o portal do Azure e selecione a assinatura. No painel esquerdo, selecione **controle de acesso (iam)**. Procure o nome do recurso Datadog. Confirme se o recurso Datadog tem a atribuição de função **leitor de monitoramento** , conforme mostrado abaixo.

:::image type="content" source="media/troubleshoot/datadog-role-assignment.png" alt-text="Atribuição de função Datadog na assinatura do Azure" border="true":::

## <a name="datadog-agent-installation-fails"></a>Falha na instalação do agente Datadog

A integração do Azure Datadog fornece a capacidade de instalar o agente do Datadog em uma máquina virtual ou serviço de aplicativo. Para configurar o agente Datadog, a chave de API selecionada como **chave padrão** na tela chaves de API é usada. Se uma chave padrão não estiver selecionada, a instalação do agente Datadog falhará.

Se o agente Datadog tiver sido configurado com uma chave incorreta, navegue até a tela chaves de API e altere a **chave padrão**. Você precisará desinstalar o agente do Datadog e reinstalá-lo para configurar a máquina virtual com as novas chaves de API.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como [gerenciar sua instância](manage.md) do Datadog.
