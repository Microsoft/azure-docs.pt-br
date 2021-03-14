---
title: Alterações importantes chegando à Central de Segurança do Azure
description: Futuras alterações na Central de Segurança do Azure às quais você talvez precise estar atento e que podem exigir algum planejamento
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/25/2021
ms.author: memildin
ms.openlocfilehash: a2c29049decc056f0d3c8083d21574456610c124
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99555145"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Futuras alterações importantes na Central de Segurança do Azure

> [!IMPORTANT]
> As informações nesta página estão relacionadas aos produtos ou recursos de pré-lançamento e podem ser substancialmente modificadas antes de serem lançadas comercialmente, caso sejam. A Microsoft não assume nenhum compromisso nem faz garantias, expressas nem implícitas, quanto às informações fornecidas daqui em diante.

Nesta página, você aprenderá sobre as alterações planejadas para a Central de Segurança. Ela descreve as modificações planejadas para o produto que podem afetar coisas como a classificação de segurança ou os fluxos de trabalho.

Se você estiver procurando as notas sobre a versão mais recentes, poderá encontrá-las no artigo [Novidades na Central de Segurança do Azure](release-notes.md).


## <a name="planned-changes"></a>Alterações planejadas

- [Duas recomendações do controle de segurança "Aplicar atualizações do sistema" que está sendo preterido](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Aprimoramentos na recomendação de classificação de dados SQL](#enhancements-to-sql-data-classification-recommendation)
- [Reprovação de 11 alertas do Azure Defender](#deprecation-of-11-azure-defender-alerts)

### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Duas recomendações do controle de segurança "Aplicar atualizações do sistema" que está sendo preterido 

**Data estimada da alteração:** Fevereiro de 2021

As duas seguintes recomendações estão agendadas para serem preteridas em fevereiro de 2021:

- **Seus computadores devem ser reiniciados para que as atualizações do sistema sejam aplicadas**. Isso pode resultar em um impacto sutil na sua classificação de segurança.
- **O agente de monitoramento deve ser instalado em seus computadores**. Essa recomendação está relacionada somente a computadores locais e um pouco da lógica dela será transferido para outra recomendação, **Problemas de integridade do agente do Log Analytics devem ser resolvidos em seus computadores**. Isso pode resultar em um impacto sutil na sua classificação de segurança.

É recomendável verificar suas configurações de exportação contínua e automação de fluxo de trabalho para ver se essas recomendações estão incluídas nelas. Além disso, painéis ou outras ferramentas de monitoramento que possam estar as usando devem ser atualizados de acordo.

Saiba mais sobre cada uma dessas recomendações na [página de referência de recomendações de segurança](recommendations-reference.md).


### <a name="enhancements-to-sql-data-classification-recommendation"></a>Aprimoramentos na recomendação de classificação de dados SQL

**Data estimada da alteração:** T2 de 2021

A recomendação **Os dados confidenciais nos seus bancos de dados SQL devem ser classificados** no controle de segurança **Aplicar classificação de dados** será substituída por uma nova versão que está mais bem alinhada com a estratégia de classificação de dados da Microsoft. Como resultado, a ID da recomendação também será alterada (atualmente, b0df6f56-862d-4730-8597-38c0fd4ebd59).


### <a name="deprecation-of-11-azure-defender-alerts"></a>Reprovação de 11 alertas do Azure Defender

**Data estimada da alteração:** Março de 2021

No mês que vem, os onze alertas do Azure Defender listados abaixo serão preteridos.

- Novos alertas substituirão estes dois alertas e fornecerão uma cobertura melhor:

    | AlertType                | AlertDisplayName                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | VERSÃO PRÉVIA – Execução da função "Get-AzureDomainInfo" do kit de ferramentas do MicroBurst detectada |
    | ARM_MicroBurstRunbook    | VERSÃO PRÉVIA – Execução da função "Get-AzurePasswords" do kit de ferramentas do MicroBurst detectada  |
    |                          |                                                                          |

- Estes nove alertas estão relacionados a um conector do Azure Active Directory Identity Protection que já foi preterido:

    | AlertType           | AlertDisplayName              |
    |---------------------|-------------------------------|
    | UnfamiliarLocation  | Propriedades de entrada desconhecidas |
    | AnonymousLogin      | Endereço IP anônimo          |
    | InfectedDeviceLogin | Endereço IP vinculado a malware     |
    | ImpossibleTravel    | Viagem atípica               |
    | MaliciousIP         | Endereço IP mal-intencionado          |
    | LeakedCredentials   | Credenciais vazadas            |
    | PasswordSpray       | Pulverização de senha                |
    | LeakedCredentials   | Inteligência contra ameaças do Azure AD  |
    | AADAI               | IA do Azure AD                   |
    |                     |                               |
 



## <a name="next-steps"></a>Próximas etapas

Para ver todas as alterações recentes feitas no produto, confira as [Novidades na Central de Segurança do Azure](release-notes.md).
