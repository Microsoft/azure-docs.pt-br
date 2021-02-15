---
title: Configurar o Azure defender para armazenamento
titleSuffix: Azure Storage
description: Configure o Azure defender para armazenamento para detectar anomalias na atividade de conta e ser notificado de tentativas potencialmente prejudiciais de acessar sua conta.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: tamram
ms.reviewer: ozgun
ms.openlocfilehash: 0bda32aaab301fe9ed685f0bfd6d4596fab4e5db
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92789005"
---
# <a name="configure-azure-defender-for-storage"></a>Configurar o Azure defender para armazenamento

O Azure defender para armazenamento fornece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de armazenamento. Essa camada de proteção permite que você resolva as ameaças sem ser um especialista em segurança ou gerenciar sistemas de monitoramento de segurança.

Os alertas de segurança são acionados quando ocorrem anomalias na atividade. Esses alertas de segurança são integrados à [central de segurança do Azure](https://azure.microsoft.com/services/security-center/)e também são enviados por email para administradores de assinatura, com detalhes de atividades suspeitas e recomendações sobre como investigar e corrigir ameaças.

O serviço ingere logs de recursos de solicitações de leitura, gravação e exclusão para o armazenamento de BLOBs e para os arquivos do Azure para detecção de ameaças. Para investigar os alertas do Azure defender, você pode exibir a atividade de armazenamento relacionada usando o log de Análise de Armazenamento. Para obter mais informações, consulte **Configurar o log** em [monitorar uma conta de armazenamento no portal do Azure](storage-monitor-storage-account.md#configure-logging).

## <a name="availability"></a>Disponibilidade

O Azure defender para armazenamento está disponível no momento para armazenamento de BLOBs, arquivos do Azure e Azure Data Lake Storage Gen2. Os tipos de conta que dão suporte ao Azure defender incluem contas de armazenamento de blob v2, BLOB de blocos e de uso geral. O Azure defender para armazenamento está disponível em todas as nuvens públicas e nuvens do governo dos EUA, mas não em outras regiões de nuvem soberanas ou do Azure governamental.

Contas com namespaces hierárquicos habilitados para Data Lake Storagem transações de suporte usando as APIs de armazenamento de BLOBs do Azure e as APIs de Data Lake Storage. Compartilhamentos de arquivos do Azure dão suporte a transações em SMB.

Para obter detalhes de preços, incluindo uma avaliação gratuita de 30 dias, consulte a [página de preços da central de segurança do Azure](https://azure.microsoft.com/pricing/details/security-center/).

A lista a seguir resume a disponibilidade do Azure defender para armazenamento:

- Estado da versão:
  - [Armazenamento de BLOBs](https://azure.microsoft.com/services/storage/blobs/) (disponibilidade geral)
  - [Arquivos do Azure](../files/storage-files-introduction.md) (disponibilidade geral)
  - Azure Data Lake Storage Gen2 (disponibilidade geral)
- Nuvens:<br>
    ✔ Nuvens comerciais<br>
    ✔ US Gov<br>
    ✘ China gov, outros gov

## <a name="set-up-azure-defender"></a>Configurar o Azure defender

Você pode configurar o Azure defender para armazenamento de várias maneiras, descritas nas seções a seguir.

### <a name="azure-security-center"></a>[Central de Segurança do Azure](#tab/azure-security-center)

Quando você assina a camada Standard na central de segurança do Azure, o Azure defender é automaticamente configurado em todas as suas contas de armazenamento. Você pode habilitar ou desabilitar o Azure defender para suas contas de armazenamento em uma assinatura específica da seguinte maneira:

1. Inicie a **central de segurança do Azure** no [portal do Azure](https://portal.azure.com).
1. No menu principal, em **Gerenciamento** , selecione **preços & configurações** .
1. Selecione a assinatura para a qual você deseja habilitar ou desabilitar o Azure defender.
1. Selecione **Azure defender ativado** para habilitar o Azure defender para a assinatura.
1. Em **selecionar plano do Azure defender por tipo de recurso** , localize a linha de **armazenamento** e selecione **habilitado** na coluna **plano** .
1. Salve suas alterações.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-security-center.png" alt-text="Captura de tela mostrando como habilitar o Azure defender para armazenamento na central de segurança":::

O Azure defender agora está habilitado para todas as contas de armazenamento nesta assinatura.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Inicie o [portal do Azure](https://portal.azure.com/).
1. Navegue para sua conta de armazenamento. Em **configurações** , selecione **segurança avançada** .
1. Selecione **habilitar Azure defender para armazenamento** .

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-portal.png" alt-text="Captura de tela mostrando como habilitar o Azure defender para armazenamento na central de segurança":::

O Azure defender agora está habilitado para esta conta de armazenamento.

### <a name="template"></a>[Modelo](#tab/template)

Use um modelo de Azure Resource Manager para implantar uma conta de armazenamento do Azure com o Azure defender habilitado. Para obter mais informações, consulte [conta de armazenamento com proteção avançada contra ameaças](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Use um Azure Policy para habilitar o Azure defender entre contas de armazenamento em uma assinatura ou grupo de recursos específico.

1. Inicie a página de **definições de política** do Azure.
1. Procure a política **implantar Azure defender em contas de armazenamento** .

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy-definitions.png" alt-text="Captura de tela mostrando como habilitar o Azure defender para armazenamento na central de segurança":::

1. Selecione uma assinatura do Azure ou um grupo de recursos.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy2.png" alt-text="Captura de tela mostrando como habilitar o Azure defender para armazenamento na central de segurança":::

1. Atribua a política.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy1.png" alt-text="Captura de tela mostrando como habilitar o Azure defender para armazenamento na central de segurança":::

### <a name="rest-api"></a>[REST API](#tab/rest-api)

Use comandos da API REST para criar, atualizar ou obter a configuração do Azure defender para uma conta de armazenamento específica.

- [Proteção avançada contra ameaças – criar](/rest/api/securitycenter/advancedthreatprotection/create)
- [Proteção avançada contra ameaças-obter](/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Use os seguintes cmdlets do PowerShell:

- [Habilitar a proteção avançada contra ameaças](/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
- [Obtenha proteção avançada contra ameaças](/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
- [Desabilitar a proteção avançada contra ameaças](/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>Explorar anomalias de segurança

Quando ocorrerem anomalias na atividade de armazenamento, você receberá uma notificação por email com informações sobre o evento de segurança suspeito. Os detalhes do evento incluem:

- A natureza da anomalia
- O nome da conta de armazenamento
- A hora do evento
- O tipo de armazenamento
- As possíveis causas
- As etapas de investigação
- As etapas de correção

O email também inclui detalhes sobre possíveis causas e ações recomendadas para investigar e atenuar a ameaça potencial.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert-email.png" alt-text="Captura de tela mostrando como habilitar o Azure defender para armazenamento na central de segurança":::

Você pode examinar e gerenciar seus alertas de segurança atuais no [bloco alertas de segurança](../../security-center/security-center-managing-and-responding-alerts.md)da central de segurança do Azure. Clicar em um alerta específico fornece detalhes e ações para investigar a ameaça atual e corrigir ameaças futuras.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert.png" alt-text="Captura de tela mostrando como habilitar o Azure defender para armazenamento na central de segurança":::

## <a name="security-alerts"></a>Alertas de segurança

Os alertas são gerados por tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de armazenamento. Para obter uma lista de alertas para o armazenamento do Azure, consulte [alertas para o armazenamento do Azure](../../security-center/alerts-reference.md#alerts-azurestorage).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [logs em contas de armazenamento do Azure](/rest/api/storageservices/About-Storage-Analytics-Logging)
- Saiba mais sobre a [Central de Segurança do Azure](../../security-center/security-center-introduction.md)