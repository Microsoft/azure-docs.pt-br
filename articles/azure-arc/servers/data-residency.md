---
title: Residência de dadosResidência de dados
description: Residência de dados e informações sobre servidores habilitados para Arc do Azure.
ms.topic: reference
ms.date: 02/16/2021
ms.custom: references_regions
ms.openlocfilehash: d4764772473bbf2e5aafe2607a9462c9a6a15203
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100559504"
---
# <a name="azure-arc-enabled-servers-data-residency"></a>Servidores habilitados para Arc do Azure: residência de dados

Este artigo explica o conceito de residência de dados e como ele se aplica aos servidores habilitados para Arc do Azure.

Os servidores habilitados para Arc do Azure estão **[disponíveis](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** na **Estados Unidos, na Europa, no Reino Unido, na austrália e no Pacífico Asiático**.

## <a name="data-residency"></a>Residência de dadosResidência de dados

Os servidores habilitados para Arc do Azure armazenam definições de configuração de [extensão de VM do Azure](manage-vm-extensions.md) (ou seja, valores de propriedade) a extensão requer a especificação antes de tentar habilitar no computador conectado. Por exemplo, quando você habilita a extensão de VM Log Analytics, ela solicita a **ID do espaço de trabalho** log Analytics e a **chave primária**.

As informações de metadados sobre o computador conectado também são coletadas. Especificamente:

* Nome, tipo e versão do sistema operacional
* Nome do computador
* FQDN (nome de domínio totalmente qualificado) do computador
* Versão do agente do Connected Machine
* Active Directory e FQDN (nome de domínio totalmente qualificado) do DNS
* UUID (ID DO BIOS)
* Pulsação do agente da máquina conectada
* Versão do agente do Connected Machine
* Chave pública para identidade gerenciada
* Detalhes e status de conformidade da política (se estiver usando Azure Policy políticas de configuração de convidado)

Os servidores habilitados para Arc permitem que você especifique a região onde os dados serão armazenados. A Microsoft pode replicar para outras regiões para resiliência de dados, mas a Microsoft não Replica nem move dados fora da geografia. Esses dados são armazenados na região em que o recurso de máquina do arco do Azure está configurado. Por exemplo, se o computador estiver registrado com Arc na região leste dos EUA, esses dados serão armazenados na região dos EUA.

Para obter mais informações sobre a resiliência regional e o suporte de conformidade, consulte [Geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como projetar para [resiliência do Azure](/azure/architecture/reliability/architect).
