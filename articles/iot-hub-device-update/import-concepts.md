---
title: Entender a atualização do dispositivo para importação do Hub IoT | Microsoft Docs
description: Conceitos principais para importar uma nova atualização para a atualização de dispositivo para o Hub IoT.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/10/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 4cd5e0c016b98a3dc9336237a5c1b14e6b0f5789
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102040576"
---
# <a name="importing-updates-into-device-update-for-iot-hub"></a>Importando atualizações na atualização do dispositivo para o Hub IoT
Para implantar uma atualização em dispositivos da atualização de dispositivo para o Hub IoT, primeiro você precisará _importar_ essa atualização para o serviço de atualização de dispositivo. Aqui está uma visão geral de alguns conceitos importantes para entender quando se trata de importar atualizações.

## <a name="import-manifest"></a>Importar manifesto

Um manifesto de importação é um arquivo JSON que define informações importantes sobre a atualização que você está importando. Você enviará o manifesto de importação e os arquivos de atualização associados (como um pacote de atualização de firmware) como parte do processo de importação. Os metadados definidos no manifesto de importação são usados para ingerir a atualização. Alguns dos metadados também são usados no momento da implantação, por exemplo, para validar se uma atualização foi instalada corretamente.

O manifesto de importação contém vários itens que representam a atualização de dispositivo importante para os conceitos do Hub IoT. Esses conceitos são descritos abaixo.

### <a name="update-identity-update-id"></a>Atualizar identidade (ID da atualização)

A identidade de atualização representa o identificador exclusivo de uma atualização. Ele define propriedades importantes sobre uma atualização que está sendo importada. A identidade de atualização é composta por três partes:
* Provedor: essa é a entidade que está criando ou diretamente responsável pela atualização. Em geral, ele será um nome de empresa.
* Nome: um identificador para uma classe de atualizações. A classe pode ser qualquer coisa que você escolher. Em geral, ele será um nome de dispositivo ou modelo.
* Versão: é um número de versão que distingue essa atualização de outras que têm o mesmo nome e provedor. Essa versão é usada pela atualização do dispositivo para o serviço do Hub IoT e pode ou não corresponder a uma versão de um componente de software individual no dispositivo. 

### <a name="compatibility"></a>Compatibilidade

Para simplificar as implantações de atualização, a atualização de dispositivo para o Hub IoT compara as propriedades de compatibilidade para uma atualização, que são definidas no manifesto de importação, com as propriedades de dispositivo correspondentes. Somente as atualizações que têm propriedades correspondentes serão retornadas e estarão disponíveis para implantação.

### <a name="installedcriteria"></a>InstalledCriteria

O InstalledCriteria é usado pelo agente de atualização em um dispositivo para determinar se uma atualização foi instalada com êxito.


## <a name="next-steps"></a>Próximas etapas

Se você estiver pronto, experimente o [Guia de importação How-To](./import-update.md), que o orientará durante o processo de importação passo a passo.


