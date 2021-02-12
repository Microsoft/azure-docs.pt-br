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
ms.openlocfilehash: d5de16c8156762a229d6c707080bc197dc206a7c
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475583"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Futuras alterações importantes na Central de Segurança do Azure

> [!IMPORTANT]
> As informações nesta página estão relacionadas aos produtos ou recursos de pré-lançamento e podem ser substancialmente modificadas antes de serem lançadas comercialmente, caso sejam. A Microsoft não assume nenhum compromisso nem faz garantias, expressas nem implícitas, quanto às informações fornecidas daqui em diante.

Nesta página, você aprenderá sobre as alterações planejadas para a Central de Segurança. Ela descreve as modificações planejadas para o produto que podem afetar coisas como a classificação de segurança ou os fluxos de trabalho.

Se você estiver procurando as notas sobre a versão mais recentes, poderá encontrá-las no artigo [Novidades na Central de Segurança do Azure](release-notes.md).


## <a name="planned-changes"></a>Alterações planejadas

- [As recomendações de proteção de cargas de trabalho do Kubernetes em breve serão lançadas em GA (disponibilidade geral)](#kubernetes-workload-protection-recommendations-will-soon-be-released-for-general-availability-ga)
- [Duas recomendações do controle de segurança "Aplicar atualizações do sistema" que está sendo preterido](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Aprimoramentos na recomendação de classificação de dados SQL](#enhancements-to-sql-data-classification-recommendation)


### <a name="kubernetes-workload-protection-recommendations-will-soon-be-released-for-general-availability-ga"></a>As recomendações de proteção de cargas de trabalho do Kubernetes em breve serão lançadas em GA (disponibilidade geral)

**Data estimada da alteração:** Fevereiro de 2021

As recomendações de proteção de cargas de trabalho do Kubernetes descritas em [Proteger suas cargas de trabalho do Kubernetes](kubernetes-workload-protections.md) estão atualmente em versão prévia. Embora uma recomendação esteja em versão prévia, ela não torna um recurso não íntegro e é incluída nos cálculos da sua classificação de segurança.

Essas recomendações serão lançadas em breve em GA (disponibilidade geral) e, portanto, *serão* incluídas no cálculo da classificação. Se você ainda não corrigiu, isso pode resultar em um pequeno impacto em sua classificação de segurança.

Corrija-os sempre que possível (saiba como em [Corrigir recomendações na Central de Segurança do Azure](security-center-remediate-recommendations.md)).

As recomendações da proteção de cargas de trabalho do Kubernetes são:

- Um complemento do Azure Policy para Kubernetes deverá estar instalado e habilitado nos clusters
- Deverão ser aplicados limites de memória e CPU ao contêiner
- Os contêineres com privilégios deverão ser evitados
- Um sistema de arquivos raiz imutável (somente leitura) deverá ser aplicado aos contêineres
- Os contêineres com elevação de privilégio deverão ser evitados
- Executar contêineres como usuário raiz deverá ser evitado
- Os contêineres que compartilham namespaces de host confidenciais deverão ser evitados
- Deverão ser aplicadas aos contêineres funcionalidades do Linux com privilégios mínimos
- Usar montagens de volumes de HostPath do pod deverá ser restrito a uma lista conhecida
- Os contêineres deverão escutar somente em portas permitidas
- Os serviços deverão escutar somente em portas permitidas
- Usar redes e portas do host deverá ser restrito
- As ações para substituir ou desabilitar o perfil do AppArmor de contêineres deverão ser restritas
- As imagens de contêiner devem ser implantadas apenas de registros confiáveis             

Saiba mais sobre essas recomendações em [Proteger suas cargas de trabalho do Kubernetes](kubernetes-workload-protections.md).

### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Duas recomendações do controle de segurança "Aplicar atualizações do sistema" que está sendo preterido 

**Data estimada da alteração:** Fevereiro de 2021

As duas seguintes recomendações estão agendadas para serem preteridas em fevereiro de 2021:

- **Seus computadores devem ser reiniciados para que as atualizações do sistema sejam aplicadas**. Isso pode resultar em um impacto sutil na sua classificação de segurança.
- **O agente de monitoramento deve ser instalado em seus computadores**. Essa recomendação está relacionada somente a computadores locais e um pouco da lógica dela será transferido para outra recomendação, **Problemas de integridade do agente do Log Analytics devem ser resolvidos em seus computadores**. Isso pode resultar em um impacto sutil na sua classificação de segurança.

É recomendável verificar suas configurações de exportação contínua e automação de fluxo de trabalho para ver se essas recomendações estão incluídas nelas. Além disso, painéis ou outras ferramentas de monitoramento que possam estar as usando devem ser atualizados de acordo.

Saiba mais sobre cada uma dessas recomendações na [página de referência de recomendações de segurança](recommendations-reference.md).


### <a name="enhancements-to-sql-data-classification-recommendation"></a>Aprimoramentos na recomendação de classificação de dados SQL

**Data estimada da alteração:** T2 de 2021

A versão atual da recomendação **Os dados confidenciais nos seus bancos de dados SQL devem ser classificados** no controle de segurança **Aplicar classificação de dados** será substituída por uma nova versão que está mais bem alinhada com a estratégia de classificação de dados da Microsoft. Como resultado:

- A recomendação não afetará mais sua classificação de segurança
- O controle de segurança ("Aplicar classificação de dados") não afetará mais sua classificação de segurança
- A ID da recomendação também será alterada (atualmente b0df6f56-862d-4730-8597-38c0fd4ebd59)



## <a name="next-steps"></a>Próximas etapas

Para ver todas as alterações recentes feitas no produto, confira as [Novidades na Central de Segurança do Azure](release-notes.md).
