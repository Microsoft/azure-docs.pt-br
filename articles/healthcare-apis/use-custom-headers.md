---
title: Adicionar dados a logs de auditoria usando cabeçalhos personalizados – API do Azure para FHIR
description: Este artigo descreve como adicionar dados a logs de auditoria usando cabeçalhos HTTP personalizados na API do Azure para FHIR.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 10/13/2019
ms.openlocfilehash: 937be72bfec96119474e7effe9ba88a2cf253444
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86081836"
---
# <a name="add-data-to-audit-logs-by-using-custom-http-headers"></a>Adicionar dados a logs de auditoria usando cabeçalhos HTTP personalizados

Na API do Azure para FHIR (Fast Healthcare Interoperability Resources), um usuário pode querer incluir informações adicionais nos logs, provenientes do sistema de chamada.

Por exemplo, quando o usuário da API é autenticado por um sistema externo, esse sistema encaminha a chamada para a API do FHIR. Na camada de API do FHIR, as informações sobre o usuário original foram perdidas, pois a chamada foi encaminhada. Pode ser necessário registrar em log e reter essas informações de usuário para fins de auditoria ou gerenciamento. O sistema de chamada pode fornecer a identidade do usuário, o local do chamador ou outras informações necessárias nos cabeçalhos HTTP, que serão transmitidos à medida que a chamada for encaminhada.

Você pode ver esse fluxo de dados no seguinte diagrama:

:::image type="content" source="media/custom-headers/custom-headers-diagram.png" alt-text="Diagrama de cabeçalhos personalizados&quot;:::

Você pode usar cabeçalhos personalizados para capturar vários tipos de informações. Por exemplo:

* Informações de identidade ou autorização
* Origem do chamador
* Organização de origem
* Detalhes do sistema do cliente (registro de integridade eletrônico, portal do paciente)

> [!IMPORTANT]
> Lembre-se de que as informações enviadas em cabeçalhos personalizados são armazenadas em um sistema de registro interno da Microsoft por 30 dias depois de estarem disponíveis no Monitoramento de Log do Azure. É recomendável criptografar todas as informações antes de adicioná-las aos cabeçalhos personalizados. Você não deve passar nenhuma informação PHI por meio de cabeçalhos de cliente.

Você deve usar a seguinte convenção de nomenclatura para seus cabeçalhos HTTP: X-MS-AZUREFHIR-AUDIT-\<name>.

Esses cabeçalhos HTTP são incluídos em um recipiente de propriedades que é adicionado ao log. Por exemplo:

* X-MS-AZUREFHIR-AUDIT-USERID: 1234 
* X-MS-AZUREFHIR-AUDIT-USERLOCATION: XXXX
* X-MS-AZUREFHIR-AUDIT-XYZ: 1234

Essas informações são serializadas para JSON quando são adicionadas à coluna de propriedades no log. Por exemplo:

```json
{ &quot;X-MS-AZUREFHIR-AUDIT-USERID&quot; : &quot;1234&quot;,
&quot;X-MS-AZUREFHIR-AUDIT-USERLOCATION&quot; : &quot;XXXX&quot;,
&quot;X-MS-AZUREFHIR-AUDIT-XYZ&quot; : &quot;1234&quot; }
```
 
Assim como ocorre com qualquer cabeçalho HTTP, o mesmo nome de cabeçalho pode ser repetido com valores diferentes. Por exemplo:

* X-MS-AZUREFHIR-AUDIT-USERLOCATION: HospitalA
* X-MS-AZUREFHIR-AUDIT-USERLOCATION: Emergência

Quando adicionado ao log, os valores são combinados a uma lista delimitada por vírgulas. Por exemplo:

{ &quot;X-MS-AZUREFHIR-AUDIT-USERLOCATION&quot;: &quot;HospitalA, Emergency" }
 
Você pode adicionar um máximo de 10 cabeçalhos exclusivos (as repetições do mesmo cabeçalho com valores diferentes só são contadas uma vez). O comprimento máximo total do valor para qualquer cabeçalho é de 2.048 caracteres.

Se você está usando a biblioteca de APIs cliente de Firely C#, o código se parece com este:

```C#
FhirClient client;
client = new FhirClient(serverUrl);
client.OnBeforeRequest += (object sender, BeforeRequestEventArgs e) =>
{
    // Add custom headers to be added to the logs
    e.RawRequest.Headers.Add("X-MS-AZUREFHIR-AUDIT-UserLocation", "HospitalA");
};
client.Get("Patient");
```
## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a adicionar dados aos logs de auditoria usando cabeçalhos personalizados na API do Azure para FHIR. Em seguida, saiba mais sobre outras configurações adicionais que podem ser definidas na API do Azure para FHIR.
 
>[!div class="nextstepaction"]
>[Configurações adicionais](azure-api-for-fhir-additional-settings.md)
