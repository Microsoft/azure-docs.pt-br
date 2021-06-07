---
title: O que são as notificações de integridade do serviço do Azure?
description: As notificações de integridade do serviço permitem exibir mensagens de integridade do serviço publicadas pelo Microsoft Azure.
ms.topic: conceptual
ms.date: 4/12/2018
ms.openlocfilehash: 4f2b4a0779d775ed5be5bfa28831cccc68c33caf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86529005"
---
# <a name="use-the-azure-portal-to-view-service-health-notifications"></a>Usar o portal do Azure para exibir notificações de integridade do serviço

As notificações de integridade do serviço são publicadas pelo Azure e contêm informações sobre os recursos em sua assinatura. Essas notificações são uma subclasse dos eventos do log de atividades e também podem ser encontradas no log de atividades. As notificações de integridade do serviço podem ser informativas ou acionáveis, dependendo da classe.

Há diversas classes de notificações de integridade do serviço:  

- **Ação necessária:** o Azure poderá notar algo incomum acontecer em sua conta e trabalhar com você para corrigir isso. O Azure lhe envia uma notificação detalhando as ações que você precisa tomar ou sobre como contatar o suporte ou a engenharia do Azure.  
- **Incidente:** um evento que causa impacto em um serviço atualmente está afetando um ou mais recursos em sua assinatura.  
- **Manutenção:** uma atividade de manutenção planejada que pode afetar um ou mais recursos em sua assinatura.  
- **Informações:** possíveis otimizações que podem ajudar a melhorar o uso de recursos. 
- **Segurança:** informações urgentes relacionadas à segurança sobre suas soluções em execução no Azure.

Cada notificação de integridade do serviço inclui detalhes sobre o escopo e o impacto em seus recursos. Os detalhes incluem:

Nome da propriedade | Descrição
-------- | -----------
canais | Um dos seguintes valores: **Admin** ou **Operação**.
correlationId | Geralmente, um GUID no formato de cadeia de caracteres. Eventos que pertencem à mesma ação geralmente compartilham a mesma correlationId.
eventDataId | O identificador exclusivo de um evento.
eventName | O título de um evento.
nível | O nível de um evento
resourceProviderName | O nome do provedor de recursos do recurso afetado.
resourceType| O tipo de recurso do recurso afetado.
subStatus | Geralmente, o código de status HTTP da chamada REST correspondente, mas também pode incluir outras cadeias de caracteres que descrevam um substatus. Por exemplo: OK (Código de Status HTTP: 200), Criado (Código de Status HTTP: 201), Aceito (Código de Status HTTP: 202), Sem Conteúdo (Código de Status HTTP: 204), Solicitação Incorreta (Código de Status HTTP: 400), Não Encontrado (Código de Status HTTP: 404), Conflito (Código de Status HTTP: 409), Erro Interno do Servidor (Código de Status HTTP: 500), Serviço Não Disponível (Código de Status HTTP: 503), Tempo Limite do Gateway (Código de Status HTTP: 504).
eventTimestamp | Carimbo de data/hora quando o evento foi gerado pelo serviço do Azure que está processando a solicitação correspondente ao evento.
submissionTimestamp | Carimbo de hora quando o evento tornou-se disponível para consulta.
subscriptionId | A assinatura do Azure na qual esse evento foi registrado.
status | Cadeia de caracteres que descreve o status da operação. Alguns valores comuns são: **Iniciado**, **Em Andamento**, **Êxito**, **Falha**, **Ativo** e **Resolvido**.
operationName | O nome da operação.
category | Esta propriedade é sempre **ServiceHealth**.
resourceId | ID do recurso afetado.
Properties.title | O título localizado dessa comunicação. Inglês é o padrão.
Properties.communication | Os detalhes localizados da comunicação com marcação HTML. Inglês é o padrão.
Properties.incidentType | Um dos seguintes valores: **ActionRequired**, **informativo**, **incidente**, **manutenção** ou **segurança**.
Properties.trackingId | O incidente ao qual esse evento está associado. Use-a para correlacionar os eventos relacionados a um incidente.
Properties.impactedServices | Um blob JSON com escape que descreve as regiões e os serviços afetados pelo incidente. Uma lista de Services, que, individualmente, tem um **ServiceName** e uma lista de ImpactedRegions, que têm um **RegionName**.
Properties.defaultLanguageTitle | A comunicação em inglês.
Properties.defaultLanguageContent | A comunicação em inglês como marcação HTML ou texto sem formatação.
Properties.stage | Os valores possíveis para **Incidente** e **Segurança** são **Ativo,** **Resolvido** ou **RCA**. Para **ActionRequired** ou **Informational**, o único valor é **Ativo**. Para **manutenção** , eles são: **ativo**, **planejado**, em **andamento**, **cancelado**, **reagendado**, **resolvido** ou **concluído**.
Properties.communicationId | A comunicação à qual esse evento está associado.

### <a name="details-on-service-health-level-information"></a>Detalhes sobre informações de nível de integridade de serviço

**Ação Necessária** (properties.incidentType == ActionRequired)
- Informativo-a ação do administrador é necessária para evitar o impacto nos serviços existentes.
    
**Manutenção** (properties.incidentType == Maintenance)
- Aviso-manutenção de emergência
- Informativo-manutenção planejada padrão

**Informações** (properties.incidentType == Information)
- Informativo-o administrador pode ser solicitado a evitar o impacto nos serviços existentes.

**Segurança** (properties.incidentType == Security)
- Aviso-consultoria de segurança que afeta os serviços existentes e pode exigir ação do administrador.
- Informativo-consultoria de segurança que afeta os serviços existentes.

**Problemas de Serviço** (properties.incidentType == Incident)
- Erro – problemas amplamente difundidos ao acessar vários serviços em várias regiões estão impactando um amplo conjunto de clientes.
- Aviso – problemas ao acessar serviços específicos e/ou regiões específicas estão impactando um subconjunto de clientes.
- Informativo – problemas que afetam as operações de gerenciamento e/ou latência, não impactando a disponibilidade do serviço.
