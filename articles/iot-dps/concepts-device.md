---
title: Conceitos de dispositivo no provisionamento do dispositivo do Azure | Microsoft Docs
description: Descreve conceitos de provisionamento de dispositivo específicos para dispositivos com o Serviço de Provisionamento de Dispositivos e o Hub IoT
author: nberdy
ms.author: nberdy
ms.date: 09/05/2017
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 2904da863707c5f653d774b0a480cc48c95c8d1c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60745971"
---
# <a name="iot-hub-device-provisioning-service-device-concepts"></a>Conceitos de dispositivo do Serviço de Provisionamento de Dispositivos no Hub IoT

O Serviço de Provisionamento de Dispositivos no Hub IoT é um serviço auxiliar do Hub IoT que você usa para configurar o provisionamento de dispositivo sem interação para um Hub IoT especificado. Com o Serviço de Provisionamento de Dispositivos, você pode provisionar milhões de dispositivos de uma maneira segura e escalonável.

Este artigo fornece uma visão geral dos conceitos de *dispositivo* envolvidos no provisionamento do dispositivo. Este artigo é muito relevante para todas as personas envolvidas na [etapa de fabricação](about-iot-dps.md#manufacturing-step) da preparação de um dispositivo para implantação.

## <a name="attestation-mechanism"></a>Mecanismo de atestado

O mecanismo de atestado é o método usado para confirmar a identidade de um dispositivo. O mecanismo de atestado também é relevante para a lista de registro, que informa ao serviço de provisionamento qual método de atestado usar com um determinado dispositivo.

> [!NOTE]
> O Hub IoT usa o "esquema de autenticação" para um conceito semelhante nesse serviço.

O Serviço de Provisionamento de Dispositivos dá suporte às seguintes formas de atestado:
* **Certificados X.509** com base no fluxo de autenticação do certificado X.509 padrão.
* **Trusted Platform Module (TPM)** com base em um desafio nonce, usando o padrão TPM para chaves para apresentar um token de Assinatura de Acesso Compartilhado (SAS) assinado. Isso não requer um TPM físico no dispositivo, mas o serviço espera atestar usando a chave de endosso de acordo com a [especificação TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).
* **Chave simétrica** com base em [tokens de segurança](../iot-hub/iot-hub-devguide-security.md#security-tokens) de SAS (assinatura de acesso compartilhado), que incluem uma assinatura de hash e uma expiração inserida. Para obter mais informações, veja [Atestado de chave simétrica](concepts-symmetric-key-attestation.md).

## <a name="hardware-security-module"></a>Módulo de segurança de hardware

O módulo de segurança de hardware, ou HSM, é usado para armazenamento seguro, com base em hardware, de segredos do dispositivo e é a forma mais segura de armazenamento de segredo. Os certificados X.509 e os tokens SAS podem ser armazenados no HSM. Os HSMs podem ser usados com ambos os mecanismos de atestado que o serviço de provisionamento dá suporte.

> [!TIP]
> É altamente recomendável usar um HSM com dispositivos para armazenar segredos com segurança em seus dispositivos.

Os segredos de dispositivo também podem ser armazenados em software (memória), mas é uma forma menos segura de armazenamento que um HSM.

## <a name="registration-id"></a>ID de registro

A ID do registro é usada para identificar exclusivamente um dispositivo no Serviço de Provisionamento de Dispositivos. A ID do dispositivo deve ser exclusiva no [escopo da ID](#id-scope) do serviço de provisionamento. Cada dispositivo deve ter uma ID de registro. A ID do registro é alfanumérica, com letras minúsculas e pode conter hifens.

* No caso do TPM, a ID de registro é fornecida pelo próprio TPM.
* No caso de atestado com base em X.509, a ID do registro é fornecida como o nome da entidade do certificado.

## <a name="device-id"></a>Id do Dispositivo

A ID do dispositivo é a ID como ela aparece no Hub IoT. A ID do dispositivo desejada pode ser definida na entrada do registro, mas sua definição não é necessária. Se nenhuma ID de dispositivo desejada for especificada na lista de registro, a ID do registro será usada como a ID do dispositivo ao registrar o dispositivo. Saiba mais sobre as [IDs de dispositivo do Hub IoT](../iot-hub/iot-hub-devguide-identity-registry.md).

## <a name="id-scope"></a>Escopo da ID

O escopo da ID é atribuído a um Serviço de Provisionamento de Dispositivos quando ele é criado pelo usuário e é usado para identificar exclusivamente o serviço de provisionamento específico por meio do qual o dispositivo será registrado. O escopo da ID é gerado pelo serviço e é imutável, o que assegura a exclusividade.

> [!NOTE]
> A exclusividade é importante para operações de implantação de execução longa e cenários de mesclagem e aquisição.
