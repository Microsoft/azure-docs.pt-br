---
title: Recursos de solicitação de dados do cliente
author: dominicbetts
ms.author: dobett
manager: timlt
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 5dd027c886e8102e77ddefe93817daee0e1ec29b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626427"
---
# <a name="summary-of-customer-data-request-features"></a>Resumo dos recursos de solicitação de dados do cliente

O Serviço de Provisionamento de Dispositivos no Hub IoT do Azure é um serviço de nuvem baseado em REST API destinado a clientes empresariais que permite o provisionamento contínuo, automatizado e sem toque de dispositivos no Hub IoT do Azure com segurança, começando no dispositivo e terminando na nuvem.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Os dispositivos individuais recebem uma ID de registro e uma identificação do dispositivo de um administrador de locatários. Os dados desses dispositivos e sobre eles se baseiam nessas IDs. A Microsoft não mantém nenhuma informação e não tem acesso aos dados que permitem a correlação entre esses dispositivos e um indivíduo.

Muitos dos dispositivos gerenciados no Serviço de Provisionamento de Dispositivos não são dispositivos pessoais, por exemplo, um termostato de escritório ou um robô de fábrica. No entanto, os clientes podem considerar que alguns dispositivos contêm informações de identificação pessoal e, a seu critério, manter seus próprios métodos de acompanhamento de ativos ou inventário que vinculam dispositivos a indivíduos. O Serviço de Provisionamento de Dispositivos gerencia e armazena todos os dados associados a dispositivos como se fossem dados pessoais.

Os administradores de locatários podem usar o portal do Azure ou as APIs REST do serviço para atender às solicitações de informações pela exportação ou exclusão dos dados associados a uma identificação do dispositivo ou ID de registro.

> [!NOTE]
> Os dispositivos que foram provisionados no Hub IoT do Azure por meio do Serviço de Provisionamento de Dispositivos têm dados adicionais armazenados no serviço Hub IoT do Azure. Confira a [documentação de referência do Hub IoT do Azure](../iot-hub/iot-hub-customer-data-requests.md) para concluir uma solicitação completa de um dispositivo especificado.

## <a name="deleting-customer-data"></a>Excluindo os dados do cliente

O Serviço de Provisionamento de Dispositivos armazena dois tipos de registros. O primeiro contém informações sobre os dispositivos que podem ser provisionados e o segundo mostra quais dispositivos já passaram pelo processo de provisionamento.

Os administradores de locatários podem remover o primeiro tipo de registro do portal do Azure, e isso também remove o segundo tipo de registro associado.

Para obter mais informações, confira [Como gerenciar registros de dispositivos](how-to-manage-enrollments.md).

Também é possível executar operações de exclusão para ambos os tipos de registros usando APIs REST:

* Para excluir informações de registro referentes a um único dispositivo, use [Registro de Dispositivo – Excluir](/rest/api/iot-dps/deleteindividualenrollment/deleteindividualenrollment).
* Para excluir informações de registro referentes a um grupo de dispositivos, use [Grupo de Registros de Dispositivos – Excluir](/rest/api/iot-dps/deleteenrollmentgroup/deleteenrollmentgroup).
* Para excluir informações sobre os dispositivos provisionados, use [Estado do Registro – Excluir Estado do Registro](/rest/api/iot-dps/deletedeviceregistrationstate/deletedeviceregistrationstate).

## <a name="exporting-customer-data"></a>Exportando os dados do cliente

O Serviço de Provisionamento de Dispositivos armazena dois tipos de registros. O primeiro contém informações sobre os dispositivos que podem ser provisionados e o segundo mostra quais dispositivos já passaram pelo processo de provisionamento.

Os administradores de locatários podem exibir ambos os tipos de registros por meio do portal do Azure e exportá-los usando a operação "copiar e colar".

Para obter mais informações sobre como gerenciar registros, confira [Como gerenciar registros de dispositivos](how-to-manage-enrollments.md).

Também é possível executar operações de exportação para ambos os tipos de registros usando APIs REST:

* Para exportar informações de registro referentes a um único dispositivo, use [Registro de Dispositivo – Obter](/rest/api/iot-dps/getindividualenrollment/getindividualenrollment).
* Para exportar informações de registro referentes a um grupo de dispositivos, use [Registro de Dispositivo – Obter](/rest/api/iot-dps/getenrollmentgroup/getenrollmentgroup).
* Para exportar informações sobre os dispositivos já provisionados, use [Estado do Registro – Obter Estado do Registro](/rest/api/iot-dps/getdeviceregistrationstate/getdeviceregistrationstate).

> [!NOTE]
> Quando você usa os serviços empresariais da Microsoft, a Microsoft gera algumas informações, conhecidas como logs gerados pelo sistema. Alguns logs gerados pelo sistema do Serviço de Provisionamento de Dispositivos não são acessíveis nem exportáveis por administradores de locatários. Esses logs constituem ações reais conduzidas no serviço e dados de diagnóstico relacionados a dispositivos individuais.

## <a name="links-to-additional-documentation"></a>Links para documentação adicional

A documentação completa das APIs do Serviço de Provisionamento de Dispositivos está localizada em [https://docs.microsoft.com/rest/api/iot-dps](https://docs.microsoft.com/rest/api/iot-dps).

[Recursos de solicitação de dados do cliente](../iot-hub/iot-hub-customer-data-requests.md) do Hub IoT do Azure.