---
title: "Conceitos de serviço no Serviço de Provisionamento de Dispositivos no Hub IoT do Azure | Microsoft Docs"
description: "Descreve conceitos de provisionamento de serviço específicos para dispositivos com DPS e Hub IoT"
services: iot-dps
keywords: 
author: nberdy
ms.author: nberdy
ms.date: 10/03/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 96c63e5d0379150ea619dbbe912a21e373f808af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>Conceitos do Serviço de Provisionamento de Dispositivos no Hub IoT

O Serviço de Provisionamento de Dispositivos no Hub IoT é um serviço auxiliar do Hub IoT que você usa para configurar o provisionamento de dispositivo sem interação para um Hub IoT especificado. Com o Serviço de Provisionamento de Dispositivos, você pode provisionar milhões de dispositivos de uma maneira segura e escalonável.

O provisionamento do dispositivo é um processo de duas partes. A primeira parte é estabelecer a conexão inicial entre o dispositivo e a solução de IoT, *registrando* o dispositivo. A segunda parte é aplicar a *configuração* apropriada ao dispositivo com base nos requisitos específicos da solução. Depois que ambas as etapas forem concluídas, poderemos dizer que o dispositivo foi completamente *provisionado*. O Serviço de Provisionamento de Dispositivos automatiza as etapas para fornecer uma experiência perfeita de provisionamento para o dispositivo.

Este artigo fornece uma visão geral dos conceitos de provisionamento mais aplicáveis para gerenciar o *serviço*. Este artigo é muito relevante para todas as personas envolvidas na [etapa de configuração de nuvem](about-iot-dps.md#cloud-setup-step) da preparação de um dispositivo para implantação.

## <a name="service-operations-endpoint"></a>Ponto de extremidade das operações de serviço

O ponto de extremidade das operações de serviço é o ponto de extremidade para gerenciar as configurações de serviço e manter a lista de registro. Esse ponto de extremidade é usado somente pelo administrador de serviços. Ele não é usado pelos dispositivos.

## <a name="device-provisioning-endpoint"></a>Ponto de extremidade de provisionamento do dispositivo

O ponto de extremidade de provisionamento do dispositivo é o ponto de extremidade central com o qual todos os dispositivos se comunicam para provisionamento. A URL é a mesma para todos os serviços de provisionamento para eliminar a necessidade de emitir novamente dispositivos com as novas informações de conexão em cenários de cadeia de fornecimento. O [escopo da ID](#id-scope) garante o isolamento de locatários.

## <a name="linked-iot-hubs"></a>Hubs IoT vinculados

O Serviço de Provisionamento de Dispositivos só pode provisionar dispositivos para Hubs IoT que foram vinculados a ele. Vincular um Hub IoT ao Serviço de Provisionamento de Dispositivos concede as permissões de leitura/gravação de serviço para registro de dispositivo do Hub IoT. Com o link, o Serviço de Provisionamento de Dispositivos pode registrar uma ID de dispositivo e definir a configuração inicial nos dispositivos gêmeos. Hubs de IoT vinculados podem estar em qualquer região do Azure. Você pode vincular hubs em outras assinaturas ao seu serviço de provisionamento.

## <a name="allocation-policy"></a>Política de alocação

A configuração de nível de serviço determina como o Serviço de Provisionamento de Dispositivos atribui dispositivos a um Hub IoT. Há três políticas de alocação com suporte:
* **Distribuição igualmente ponderada**: Hubs IoT vinculados têm probabilidades iguais de ter dispositivos provisionados a eles. A configuração padrão. Se estiver provisionando dispositivos a apenas um Hub IoT, você poderá manter essa configuração.
* **Menor latência**: dispositivos são provisionados para um Hub IoT com a menor latência para o dispositivo. Se vários Hubs IoT vinculados fornecerem a mesma menor latência, o serviço de provisionamento reciclará dispositivos entre esses hubs
* **Configuração estática por meio da lista de registro**: a especificação do Hub IoT desejado na lista de registro tem prioridade sobre a política de alocação no nível do serviço.

## <a name="enrollment"></a>Registro

Um registro é um relatório de dispositivos ou grupos de dispositivos que podem, em algum ponto, ser registrados. O registro de inscrições contém as informações sobre o dispositivo ou grupo de dispositivos, incluindo o método de atestado para os dispositivos e, opcionalmente, a configuração inicial desejada, o Hub IoT desejado e a ID do dispositivo desejada. Há dois tipos de inscrições com suporte pelo Serviço de Provisionamento de Dispositivos.

### <a name="enrollment-group"></a>Grupo de registro

Um grupo de registro é um grupo de dispositivos que compartilham um mecanismo de atestado específico. Todos os dispositivos do grupo de registro apresentam certificados X.509 que foram assinados pela mesma AC raiz. Grupos de registro só podem usar o mecanismo de atestado de X.509. O nome do grupo de registro e o nome do certificado devem ter caracteres alfanuméricos, letras minúsculas e podem conter hifens.

> [!TIP]
> É recomendável usar um grupo de registro para um grande número de dispositivos que compartilhem uma configuração inicial desejada ou para dispositivos que vão todos para o mesmo locatário.

### <a name="individual-enrollment"></a>Registro individual

Um registro individual é uma entrada para um único dispositivo que pode registrar. Inscrições individuais podem usar tokens de certificados X.509 ou tokens SAS (em um TPM real ou virtual) como mecanismos de atestado. A ID do registro em um registro individual é alfanumérica, com letras minúsculas e pode conter hifens. Registros individuais podem ter a ID de dispositivo de Hub IoT desejada especificada.

> [!TIP]
> É recomendável usar inscrições individuais para dispositivos que exigem configurações iniciais exclusivas ou para dispositivos que só podem usar tokens SAS por meio do TPM ou TPM virtual, como o mecanismo de atestado.

## <a name="registration"></a>Registro

Um registro é o registro de um dispositivo com registro/provisionamento bem-sucedido para um Hub IoT por meio do Serviço de Provisionamento de Dispositivos. Registros são criados automaticamente. Eles podem ser excluídos, mas não podem ser atualizados.

## <a name="operations"></a>Operações

As operações são a unidade de cobrança do Serviço de Provisionamento de Dispositivos. Uma operação é a conclusão bem-sucedida de uma instrução para o serviço. As operações incluem registros e repetições de registros de dispositivo, bem como alterações no lado do serviço, como adição e atualização das entradas da lista de registro.
