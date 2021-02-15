---
title: Executando a exportação invocando $export comando na API do Azure para FHIR
description: Este artigo descreve como exportar dados do FHIR usando o $export
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 1/21/2021
ms.author: cavoeg
ms.openlocfilehash: 3437c8bcf8ff508149abae2549d7c34521700840
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627256"
---
# <a name="how-to-export-fhir-data"></a>Como exportar dados do FHIR


O recurso de exportação em massa permite que os dados sejam exportados do servidor FHIR de acordo com a [especificação FHIR](https://hl7.org/fhir/uv/bulkdata/export/index.html). 

Antes de usar $export, você desejará certificar-se de que a API do Azure para FHIR está configurada para usá-la. Para definir configurações de exportação e criar conta de armazenamento do Azure, consulte [a página configurar dados de exportação](configure-export-data.md).

## <a name="using-export-command"></a>Usando o comando $export

Depois de configurar a API do Azure para FHIR para exportação, você pode usar o comando $export para exportar os dados para fora do serviço. Os dados serão armazenados na conta de armazenamento que você especificou ao configurar a exportação. Para saber como invocar o comando $export no FHIR Server, leia a documentação sobre a [especificação HL7 FHIR $Export](https://hl7.org/Fhir/uv/bulkdata/export/index.html). 

A API do Azure para FHIR dá suporte a $export nos seguintes níveis:
* [Sistema](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---system-level-export): `GET https://<<FHIR service base URL>>/$export>>`
* [Paciente](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---all-patients): `GET https://<<FHIR service base URL>>/Patient/$export>>`
* [Grupo de pacientes *](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---group-of-patients) – a API do Azure para FHIR exporta todos os recursos relacionados, mas não exporta as características do Grupo: `GET https://<<FHIR service base URL>>/Group/[ID]/$export>>`

Quando os dados são exportados, um arquivo separado é criado para cada tipo de recurso. Para garantir que os arquivos exportados não se tornem muito grandes, criamos um novo arquivo depois que o tamanho de um único arquivo exportado se torna maior que 64 MB. O resultado é que você pode obter vários arquivos para cada tipo de recurso, que será enumerado (ou seja, paciente-1. ndjson, paciente-2. ndjson). 


> [!Note] 
> `Patient/$export` e `Group/[ID]/$export` poderá exportar recursos duplicados se o recurso estiver em um compartimento de mais de um recurso ou em vários grupos.

Além disso, a verificação do status de exportação por meio da URL retornada pelo cabeçalho de local durante o enfileiramento tem suporte junto com o cancelamento do trabalho de exportação real.



## <a name="settings-and-parameters"></a>Configurações e parâmetros

### <a name="headers"></a>Cabeçalhos
Há dois parâmetros de cabeçalho necessários que devem ser definidos para trabalhos de $export. Os valores são definidos pela especificação de [$Export](https://hl7.org/Fhir/uv/bulkdata/export/index.html#headers)atual.
* **Accept** -Application/fhir + JSON
* **Preferir** -responder-Async

### <a name="query-parameters"></a>Parâmetros de consulta
A API do Azure para FHIR dá suporte aos seguintes parâmetros de consulta. Todos esses parâmetros são opcionais:

|Parâmetro de consulta        | Definido pela especificação de FHIR?    |  Descrição|
|------------------------|---|------------|
| \_outputFormat | Sim | Atualmente dá suporte a três valores para alinhar à especificação FHIR: Application/FHIR + ndjson, Application/ndjson ou apenas ndjson. Todos os trabalhos de exportação serão retornados `ndjson` e o valor passado não terá efeito sobre o comportamento do código. |
| \_since | Sim | Permite exportar apenas os recursos que foram modificados desde o momento fornecido |
| \_Escreva | Sim | Permite especificar quais tipos de recursos serão incluídos. Por exemplo, \_ digite = paciente retornaria apenas os recursos do paciente|
| \_typefilter | Sim | Para solicitar uma filtragem refinada, você pode usar \_ TypeFilter juntamente com o \_ parâmetro de tipo. O valor do parâmetro _typeFilter é uma lista separada por vírgulas de consultas FHIR que restringem ainda mais os resultados |
| \_Container | Não |  Especifica o contêiner dentro da conta de armazenamento configurada onde os dados devem ser exportados. Se um contêiner for especificado, os dados serão exportados para esse contêiner em uma nova pasta com o nome. Se o contêiner não for especificado, ele será exportado para um novo contêiner usando o carimbo de data/hora e a ID do trabalho. |


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a exportar recursos do FHIR usando o comando $export. Em seguida, saiba como exportar dados de identificação:
 
>[!div class="nextstepaction"]
>[Exportar dados de-identificados](de-identified-export.md)
