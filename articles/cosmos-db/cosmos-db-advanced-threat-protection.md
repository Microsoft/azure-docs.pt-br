---
title: Proteção avançada contra ameaças para Azure Cosmos DB
description: Saiba como Azure Cosmos DB fornece criptografia de dados em repouso e como ele é implementado.
author: monhaber
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: v-mohabe
ms.custom: seodec18
ms.openlocfilehash: 02281a1cad9c7e6f9680441a699fa5d34558b890
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501488"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db"></a>Proteção avançada contra ameaças para Azure Cosmos DB

A proteção avançada contra ameaças para Azure Cosmos DB fornece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de Azure Cosmos DB. Essa camada de proteção permite que você resolva as ameaças, mesmo sem ser um especialista em segurança, e integre-as aos sistemas de monitoramento de segurança central.

Os alertas de segurança são disparados quando ocorrem anomalias na atividade. Esses alertas de segurança são integrados à  [central de segurança do Azure](https://azure.microsoft.com/services/security-center/)e também são enviados por email para administradores de assinatura, com detalhes da atividade suspeita e recomendações sobre como investigar e corrigir as ameaças.

> [!NOTE]
>
> * A proteção avançada contra ameaças para Azure Cosmos DB está disponível no momento apenas para a API do SQL.
> * A proteção avançada contra ameaças para Azure Cosmos DB está disponível atualmente nas regiões do Azure governamental e do soberanas Cloud.

Para uma experiência de investigação completa dos alertas de segurança, recomendamos habilitar o [log de diagnóstico no Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/logging), que registra as operações no próprio banco de dados, incluindo operações CRUD em todos os documentos, contêineres e bancos de dados.

## <a name="set-up-advanced-threat-protection"></a>Configurar a proteção avançada contra ameaças

### <a name="set-up-atp-using-the-portal"></a>Configurar ATP usando o portal

1. Inicie o portal do Azure em  [https://portal.azure.com](https://portal.azure.com/).

2. Na conta Azure Cosmos DB, no menu **configurações** , selecione **segurança avançada**.

    ![Configurar ATP](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png)

3. Na folha configuração de **segurança avançada** :

    * Clique na opção **proteção avançada contra ameaças** para defini- la como ativada.
    * Clique em **salvar** para salvar a política de proteção avançada contra ameaças nova ou atualizada.   

### <a name="set-up-atp-using-rest-api"></a>Configurar ATP usando a API REST

Use comandos da API REST para criar, atualizar ou obter a configuração de proteção avançada contra ameaças para uma conta de Azure Cosmos DB específica.

* [Proteção avançada contra ameaças – criar](https://go.microsoft.com/fwlink/?linkid=2099745)
* [Proteção avançada contra ameaças-obter](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="set-up-atp-using-azure-powershell"></a>Configurar ATP usando Azure PowerShell

Use os seguintes cmdlets do PowerShell:

* [Habilitar a proteção avançada contra ameaças](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [Obtenha proteção avançada contra ameaças](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [Desabilitar a proteção avançada contra ameaças](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

## <a name="manage-atp-security-alerts"></a>Gerenciar alertas de segurança ATP

Quando ocorre Azure Cosmos DB anomalias de atividade, um alerta de segurança é disparado com informações sobre o evento de segurança suspeito. 

 Na central de segurança do Azure, você pode examinar e gerenciar seus [alertas de segurança](../security-center/security-center-alerts-overview.md)atuais.  Clique em um alerta específico na [central de segurança](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) para exibir as possíveis causas e as ações recomendadas para investigar e atenuar a ameaça em potencial. A imagem a seguir mostra um exemplo de detalhes de alerta fornecidos na central de segurança.

 ![Detalhes de ameaças](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png)

Uma notificação por email também é enviada com os detalhes do alerta e as ações recomendadas. A imagem a seguir mostra um exemplo de um email de alerta.

 ![Detalhes do alerta](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png)

## <a name="cosmos-db-atp-alerts"></a>Cosmos DB alertas ATP

 Para ver uma lista dos alertas gerados durante o monitoramento de contas Azure Cosmos DB, consulte a seção [alertas de Cosmos DB](../security-center/security-center-alerts-data-services.md#cosmos-db) na documentação da central de segurança.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [log de diagnóstico no Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/logging#turn-on-logging-in-the-azure-portal)
* Saiba mais sobre a [central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)