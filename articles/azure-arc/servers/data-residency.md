---
title: Residência de dadosResidência de dados
description: Residência de dados e informações sobre servidores habilitados para Azure Arc.
ms.topic: reference
ms.date: 02/16/2021
ms.custom: references_regions
ms.openlocfilehash: d4764772473bbf2e5aafe2607a9462c9a6a15203
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100559504"
---
# <a name="azure-arc-enabled-servers-data-residency"></a>Servidores habilitados para Azure Arc: residência de dados

Este artigo explica o conceito de residência de dados e como ele se aplica aos servidores habilitados para Azure Arc.

Os servidores habilitados para Azure Arc estão **[disponíveis](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** nestas regiões: **Estados Unidos, Europa, Reino Unido, Austrália e Pacífico Asiático**.

## <a name="data-residency"></a>Residência de dadosResidência de dados

Os servidores habilitados para Azure Arc armazenam definições de configuração de [extensão de VM do Azure](manage-vm-extensions.md) (ou seja, valores de propriedade). A extensão requer a especificação antes de tentar habilitar no computador conectado. Por exemplo, quando você habilita a extensão de VM do Log Analytics, ela solicita a **ID do workspace** do Log Analytics e a **chave primária**.

As informações de metadados sobre o computador conectado também são coletadas. Especificamente:

* Nome, tipo e versão do sistema operacional
* Nome do computador
* FQDN (nome de domínio totalmente qualificado) do computador
* Versão do agente do Connected Machine
* Active Directory e FQDN (nome de domínio totalmente qualificado) do DNS
* UUID (ID DO BIOS)
* Pulsação do Connected Machine Agent
* Versão do agente do Connected Machine
* Chave pública para identidade gerenciada
* Detalhes e status de conformidade da política (se estiver usando políticas de configuração de convidado do Azure Policy)

Os servidores habilitados para Arc permitem que você especifique a região na qual os dados serão armazenados. A Microsoft pode replicar para outras regiões para fins de resiliência de dados, mas não replica nem move dados para fora da geografia. Esses dados são armazenados na região em que o recurso do computador do Azure Arc está configurada. Por exemplo, se o computador estiver registrado no Arc na região Leste dos EUA, esses dados serão armazenados na região dos EUA.

Para obter mais informações sobre a resiliência regional e o suporte de conformidade, confira [Geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como projetar para [resiliência do Azure](/azure/architecture/reliability/architect).
