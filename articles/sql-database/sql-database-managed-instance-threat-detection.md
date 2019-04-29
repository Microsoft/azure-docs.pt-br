---
title: Configurar a detecção de ameaças – Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: A detecção de ameaças detecta atividades anormais do banco de dados que indicam possíveis ameaças de segurança contra o banco de dados em uma instância gerenciada.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: a8e9dfe70e300e6b1d0d50aae60660644f2ab31d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61313875"
---
# <a name="configure-threat-detection-preview-in-azure-sql-database-managed-instance"></a>Configurar a detecção de ameaças (versão prévia) na instância gerenciada do Banco de Dados SQL do Azure

A [detecção de ameaças](sql-database-threat-detection-overview.md) para uma [instância gerenciada](sql-database-managed-instance-index.yml) detecta atividades anormais que indicam tentativas potencialmente perigosas e incomuns de acessar ou explorar bancos de dados. A detecção de ameaças pode identificar **Possíveis injeções de SQL**, **Acesso de um data center ou localização incomum**, **Acesso de uma entidade de segurança desconhecida ou de um aplicativo potencialmente prejudicial** e **Credenciais SQL de força bruta**, veja mais detalhes em [Alertas da detecção de ameaças](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Você pode receber notificações sobre as ameaças detectadas por meio de [notificações por email](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) ou do [Portal do Azure](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

A [detecção de ameaças](sql-database-threat-detection-overview.md) faz parte da oferta de [segurança de dados avançada](sql-database-advanced-data-security.md) (ADS), um pacote unificado de funcionalidades avançadas de segurança do SQL. A detecção de ameaças pode ser acessada e gerenciada por meio do portal central da ADS do SQL. O serviço de detecção de ameaças é cobrado a US$ 15/mês por instância gerenciada, com os primeiros 30 dias gratuitos.

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Configurar a detecção de ameaças para a instância gerenciada no portal do Azure

1. Inicie o portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Navegue até a página de configuração da instância gerenciada que você quer proteger. Na página **Configurações**, selecione **Detecção de Ameaças**.
3. Na página de configurações de Detecção de Ameaças
   - **ATIVE** a Detecção de ameaças.
   - Configure a **lista de emails** para receber os alertas de segurança após a detecção das atividades de banco de dados anormais.
   - Selecione a **Conta de Armazenamento do Azure** onde os registros de auditoria de ameaças anormais são salvos.
4. Clique em **Salvar** para salvar a política de detecção de ameaças atualizada ou nova.

   ![detecção de ameaças](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [detecção de ameaças](sql-database-threat-detection-overview.md).
- Para saber mais sobre instâncias gerenciadas, confira [O que é uma instância gerenciada](sql-database-managed-instance.md).
- Saiba mais sobre a [detecção de ameaças para banco de dados individual](sql-database-threat-detection.md).
- Saiba mais sobre a [auditoria de instância gerenciada](https://go.microsoft.com/fwlink/?linkid=869430).
- Saiba mais sobre a [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro).
