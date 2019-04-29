---
title: Como desprovisionar dispositivos que foram provisionados com Provisionamento de Dispositivos no Hub IoT | Microsoft Docs
description: Como desprovisionar dispositivos que foram provisionados com Provisionamento de Dispositivos no Hub IoT
author: wesmc7777
ms.author: wesmc
ms.date: 05/11/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: ba62d8cff646ce5ef4f4b8a36fdad78ddc354227
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626496"
---
# <a name="how-to-deprovision-devices-that-were-previously-auto-provisioned"></a>Como desprovisionar dispositivos autoprovisionados anteriormente 

Talvez seja necessário desprovisionar dispositivos autoprovisionados anteriormente por meio do Serviço de Provisionamento de Dispositivos. Por exemplo, um dispositivo pode ser vendido ou movido para um Hub IoT diferente, ou ser perdido, roubado ou comprometido de qualquer outro modo. 

Em geral, o desprovisionamento de um dispositivo envolve duas etapas:

1. Cancele o registro do dispositivo do serviço de provisionamento para evitar autoprovisionamentos futuros. Dependendo se você deseja revogar o acesso temporária ou permanentemente, convém desabilitar ou excluir uma entrada de registro. Para dispositivos que usam o atestado X.509, convém desabilitar/excluir uma entrada na hierarquia dos grupos de registros existentes.  
 
   - Para saber como cancelar o registro de um dispositivo, consulte [Como cancelar o registro de um dispositivo no Provisionamento de Dispositivos no Hub IoT](how-to-revoke-device-access-portal.md).
   - Para saber como cancelar o registro de um dispositivo programaticamente usando uma das SDKs do serviço de provisionamento, consulte [Gerenciar registros de dispositivos com SDKs de serviço](how-to-manage-enrollments-sdks.md).

2. Cancele o registro do dispositivo do Hub IoT para evitar futuras comunicações e transferência de dados. Novamente, será possível desabilitar temporariamente ou excluir permanentemente a entrada do dispositivo no registro de identidade do Hub IoT em que foi provisionado. Consulte [Desabilitar dispositivos](/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices) para saber mais sobre a desabilitação. Consulte "Gerenciamento de Dispositivos/Dispositivos de IoT" para o recurso Hub IoT, no [Portal do Azure](https://portal.azure.com).

As etapas exatas que você adota para desprovisionar um dispositivo dependem do mecanismo de certificação e da entrada de registro aplicável ao serviço de provisionamento. As seções a seguir fornecem uma visão geral do processo, com base no tipo de registro e atestado.

## <a name="individual-enrollments"></a>Registros individuais
Dispositivos que utilizam atestado de TPM ou atestado X.509 com um certificado secundário são provisionados através de uma entrada de registro individual. 

Para desprovisionar um dispositivo que possui um registro individual: 

1. Cancele o registro do dispositivo no serviço de provisionamento:

   - Para dispositivos que usam o atestado de TPM, exclua a entrada de registro individual para revogar permanentemente o acesso do dispositivo ao serviço de provisionamento ou desabilite a entrada para revogar temporariamente o acesso. 
   - Para dispositivos que usam o atestado X.509, você pode excluir ou desabilitar a entrada. Lembre-se, no entanto, se você excluir um registro individual de um dispositivo que usa o X.509 e existir um grupo de registro habilitado para um certificado de autenticação na cadeia de certificados desse dispositivo, o dispositivo poderá registrar-se novamente. Para esses dispositivos, é mais seguro desabilitar a entrada de registro. Isso impedirá que o dispositivo registre-se novamente, independentemente da existência de um grupo de registro habilitado para um dos certificados de autenticação.

2. Desabilite ou exclua o dispositivo no registro de identidade do Hub IoT para o qual ele foi provisionado. 


## <a name="enrollment-groups"></a>Grupos de registro
Com o atestado X.509, os dispositivos também podem ser provisionados através de um grupo de registros. Os grupos de registro são configurados com um certificado de autenticação, um certificado de AC raiz ou intermediário e controlam o acesso ao serviço de provisionamento para dispositivos com esse certificado na cadeia de certificados. Para saber mais sobre grupos de registro e certificados X.509 com o serviço de provisionamento, consulte [Certificados X.509](concepts-security.md#x509-certificates). 

Para ver uma lista de dispositivos provisionados através de um grupo de registros, você pode visualizar os detalhes do grupo de registros. Essa é uma maneira fácil de entender qual Hub IoT para o qual cada dispositivo foi provisionado. Para visualizar a lista de dispositivos: 

1. Faça o logon no Portal do Azure e clique em **Todos os recursos** no menu à esquerda.
2. Clique no serviço de provisionamento na lista de recursos.
3. No seu serviço de provisionamento, clique em **Gerenciar registros** e, em seguida, selecione a guia **Grupos de Registro**.
4. Clique no grupo de registros para abri-lo.

   ![Exibir a entrada do grupo de registros no portal](./media/how-to-unprovision-devices/view-enrollment-group.png)

Com grupos de registro há dois cenários a considerar:

- Para desprovisionar todos os dispositivos que foram provisionados por meio de um grupo de registros:
  1. Desabilite o grupo de registro para adicionar o respectivo certificado de autenticação à lista de bloqueios. 
  2. Use a lista de dispositivos provisionados desse grupo de registros para desabilitar ou excluir cada dispositivo do registro de identidade do respectivo Hub IoT. 
  3. Após desabilitar ou excluir todos os dispositivos dos respectivos Hubs IoT, você poderá optar por excluir o grupo de registros. No entanto, esteja ciente de que se excluir o grupo de registros e houver um grupo de registros habilitado para um certificado de autenticação superior na cadeia de certificados de um ou mais dispositivos, esses dispositivos poderão registar novamente. 

- Para desprovisionar um único dispositivo de um grupo de registros:
  1. Crie um registro individual desabilitado para o seu certificado secundário (dispositivo). Isso revoga o acesso ao serviço de provisionamento para esse dispositivo enquanto ainda permite o acesso a outros dispositivos que possuem o certificado de autenticação do grupo de registros em sua cadeia. Não exclua o registro individual desabilitado para o dispositivo. Isso permitirá ao dispositivo registrar novamente através do grupo de registros. 
  2. Use a lista de dispositivos provisionados desse grupo de registros para encontrar o Hub IoT ao qual o dispositivo foi provisionado e desabilitá-lo ou excluí-lo do registro de identidade desse hub. 
  
  










