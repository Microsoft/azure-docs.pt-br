---
title: Configurar chaves gerenciadas pelo cliente no Azure Sentinel | Microsoft Docs
description: Saiba como configurar chaves gerenciadas pelo cliente (CMK) no Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2020
ms.author: yelevin
ms.openlocfilehash: a1c2754fcae5768c6b87d6280fc882acd46d9a0a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100585339"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>Configurar chaves gerenciadas pelo cliente no Azure Sentinel

Este artigo fornece informações básicas e etapas para configurar uma chave gerenciada pelo cliente (CMK) para o Azure Sentinel. O CMK permite que todos os dados salvos ou enviados ao Azure Sentinel sejam criptografados em todos os recursos de armazenamento relevantes com uma chave do Azure Key Vault criada ou de sua propriedade.

> [!NOTE]
> - O recurso Azure Sentinel CMK é fornecido apenas para **novos clientes**.
>
> - O acesso a esse recurso é controlado pelo registro de recursos do Azure. Você pode solicitar acesso entrando em contato azuresentinelCMK@microsoft.com . As solicitações pendentes serão aprovadas de acordo com a capacidade disponível.
>
> - A capacidade de CMK só está disponível para clientes que enviam 1 TB por dia ou mais. Você receberá informações sobre preços adicionais quando solicitar à Microsoft para provisionar o CMK na sua assinatura do Azure. Saiba mais sobre [Preços do Log Analytics](../azure-monitor/logs/manage-cost-storage.md#log-analytics-dedicated-clusters).

## <a name="how-cmk-works"></a>Como o CMK funciona 

A solução do Azure Sentinel usa vários recursos de armazenamento para a coleção e os recursos de log, incluindo o Log Analytics e outros. Como parte da configuração do CMK do Azure Sentinel, você precisará definir as configurações do CMK também nos recursos de armazenamento relacionados. Os dados salvos em recursos de armazenamento diferentes do Log Analytics também serão criptografados.

Saiba mais sobre [CMK](../azure-monitor/logs/customer-managed-keys.md#customer-managed-key-overview).

> [!NOTE]
> Se você habilitar o CMK no Azure Sentinel, qualquer recurso de Visualização Pública que não ofereça suporte ao CMK não será habilitado.

## <a name="enable-cmk"></a>Habilitar CMK 

Para provisionar o CMK, siga estas etapas: 

1.  Criar um Azure Key Vault e armazenar a chave.

2.  Habilitar o CMK no seu workspace do Log Analytics.

3.  Registrar no Cosmos DB.

4.  Adicionar uma política de acesso à sua instância do Azure Key Vault.

5.  Habilitar o CMK no Azure Sentinel.

6.  Habilitar o Azure Sentinel.

### <a name="step-1-create-an-azure-key-vault-and-storing-key"></a>ETAPA 1: Criar um Azure Key Vault e armazenar a chave

1.  [Crie o recurso do Azure Key Vault](/azure-stack/user/azure-stack-key-vault-manage-portal) e gere ou importe uma chave a ser usada para criptografia de dados.
    > [!NOTE]
    >  O Azure Key Vault deve ser configurado como recuperável para proteger sua chave e o acesso.

1.  [Ative as opções de recuperação:](../key-vault/general/key-vault-recovery.md)

    -   Verifique se [Exclusão temporária](../key-vault/general/soft-delete-overview.md) está ativado.

    -   Ative a [Proteção contra limpeza](../key-vault/general/soft-delete-overview.md#purge-protection) para proteger contra a exclusão forçada do segredo/cofre mesmo após a exclusão temporária.

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>ETAPA 2: Habilitar o CMK no seu workspace do Log Analytics

Siga as instruções em [Configuração de chave gerenciada pelo cliente do Azure Monitor](../azure-monitor/logs/customer-managed-keys.md) para criar um workspace do CMK que será usado como o workspace do Azure Sentinel nas etapas a seguir.

### <a name="step-3-register-for-cosmos-db"></a>ETAPA 3: Registrar no Cosmos DB

O Azure Sentinel funciona com o Cosmos DB como um recurso de armazenamento adicional. Certifique-se de registrar-se no Cosmos DB.

Siga as instruções do Cosmos DB para [Registrar o provedor de recursos do Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) para sua assinatura do Azure.

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>ETAPA 4: Adicionar uma política de acesso à sua instância do Azure Key Vault

Certifique-se de adicionar o acesso do Cosmos DB à sua instância do Azure Key Vault. Siga a instrução do Cosmos DB para [adicionar uma política de acesso à sua instância do Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance) com a entidade de segurança do Azure Cosmos DB.

### <a name="step-5-enable-cmk-in-azure-sentinel"></a>ETAPA 5: Habilitar o CMK no Azure Sentinel

A capacidade do CMK do Azure Sentinel é fornecido para novos clientes somente após receber acesso diretamente do grupo de produtos do Azure. Use seus contatos na Microsoft para receber aprovação da equipe do Azure Sentinel para habilitar o CMK na sua solução.

Após obter aprovação, será pedido que você forneça as seguintes informações para habilitar o recurso do CMK.

-  ID do workspace na qual você deseja habilitar o CMK

-  URL de Key Vault: Copie o "identificador de chave" da chave até a última barra:  
    

    ![identificador da chave](./media/customer-managed-keys/key-identifier.png)

    A equipe do Azure Sentinel habilitará o recurso do CMK do Azure Sentinel para seu workspace fornecido.

-  Verificação pela equipe de produtos do Azure Sentinel de que você foi aprovado para usar esse recurso. Você deve ter isso antes de continuar.

### <a name="step-6-enable-azure-sentinel"></a>ETAPA 6: Habilitar o Azure Sentinel


Vá para a portal do Azure e habilite o Azure Sentinel no workspace no qual você configurou o CMK. Para obter mais informações, consulte [Integração do Azure Sentinel](quickstart-onboard.md).

## <a name="key-encryption-key-revocation-or-deletion"></a>Revogação ou exclusão da chave de criptografia da chave


No caso de um usuário revogar a chave de criptografia da chave, excluindo-a ou removendo o acesso ao Azure Sentinel, em uma hora, o Azure Sentinel honrará a alteração e se comportará como se os dados não estivessem mais disponíveis. Neste ponto, qualquer operação executada que use recursos de armazenamento persistente, como ingestão de dados, alterações de configuração persistente e criação de incidentes, será impedida. Os dados armazenados anteriormente não serão excluídos, mas permanecerão inacessíveis. Os dados inacessíveis são administrados pela política de retenção de dados e serão limpos de acordo com essa política.

A única operação possível após a revogação ou exclusão da chave de criptografia é a exclusão da conta.

Se o acesso for restaurado após a revogação, o Azure Sentinel irá restaurar o acesso aos dados dentro de uma hora.

Para entender mais sobre como isso funciona no Azure Monitor, consulte [Revogação do CMK do Azure Monitor](../azure-monitor/logs/customer-managed-keys.md#key-revocation).

## <a name="key-encryption-key-rotation"></a>Rotação da chave de criptografia da chave


O Azure Sentinel e o Log Analytics são compatíveis com a rotação de chaves. Quando um usuário executa a rotação de chaves no Key Vault, o Azure Sentinel torna-se compatível com a nova chave em uma hora.

No Key Vault, você pode executar a rotação de chaves criando uma nova versão da chave:

![rotação de chaves](./media/customer-managed-keys/key-rotation.png)

Você pode desativar a versão anterior da chave após 24 horas ou depois que os logs de auditoria do Azure Key Vault não mostrarem mais nenhuma atividade que use a versão anterior.

Se você usar a mesma chave no Azure Sentinel e no Log Analytics, será necessário executar a rotação de chaves para atualizar explicitamente o recurso de cluster no Log Analytics com a nova versão da chave do Azure Key Vault. Para obter mais informações, consulte [Rotação do CMK do Azure Monitor](../azure-monitor/logs/customer-managed-keys.md#key-rotation).

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como configurar uma chave gerenciada pelo cliente no Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](./tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.