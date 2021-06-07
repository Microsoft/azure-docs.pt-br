---
title: Exportando dados de identificação (versão prévia) para a API do Azure para FHIR
description: Este artigo descreve como configurar e usar a exportação de-identificada
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 9/28/2020
ms.author: matjazl
ms.openlocfilehash: 60a2a41a8005e8bd0fbc313c9a177d54df6dac5e
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017654"
---
# <a name="exporting-de-identified-data-preview"></a>Exportando dados de identificação (versão prévia)

> [!Note] 
> Os resultados ao usar a exportação desidentificada variam de acordo com fatores como dados indeterminados e funções selecionadas pelo cliente. A Microsoft não pode avaliar as saídas de exportação desidentificadas ou determinar a aceitação dos casos de uso e das necessidades de conformidade do cliente. A exportação desidentificada não é garantida para atender a quaisquer requisitos legais, regulatórios ou de conformidade específicos.

O comando de $export também pode ser usado para exportar dados desidentificados do servidor FHIR. Ele usa o mecanismo de anonimato de [ferramentas FHIR para anonimato](https://github.com/microsoft/FHIR-Tools-for-Anonymization)e obtém detalhes de configuração de anonimato em parâmetros de consulta. Você pode criar seu próprio arquivo de configuração de anonimato ou usar o [arquivo de configuração de exemplo](https://github.com/microsoft/FHIR-Tools-for-Anonymization#sample-configuration-file-for-hipaa-safe-harbor-method) para o método Safe Harbor da HIPAA como um ponto de partida. 

 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

> [!Note] 
> Agora, a API do Azure para FHIR dá suporte apenas à exportação desidentificada no nível do sistema ($export).

|Parâmetro de consulta            | Exemplo |Optionality| Descrição|
|---------------------------|---------|-----------|------------|
| _\_anonymizationConfig_   |DemoConfig.jsem|Necessário para exportação de-identificado |Nome do arquivo de configuração. Consulte o formato do arquivo de configuração [aqui](https://github.com/microsoft/FHIR-Tools-for-Anonymization#configuration-file-format). Esse arquivo deve ser **mantido dentro de um contêiner chamado** anonimion dentro da mesma conta de armazenamento do Azure configurada como o local de exportação. |
| _\_anonymizationConfigEtag_|"0x8D8494A069489EC"|Opcional para exportação de-identificado|Essa é a eTag do arquivo de configuração. Você pode obter a ETag usando o Gerenciador de armazenamento do Azure da propriedade blob|

> [!IMPORTANT]
> A exportação bruta, bem como as gravações de exportação desidentificadas para a mesma conta de armazenamento do Azure especificada como parte da configuração de exportação. É recomendável que você use contêineres diferentes correspondentes a uma configuração diferente de identificada e gerencie o acesso do usuário no nível do contêiner.