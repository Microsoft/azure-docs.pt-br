---
title: Terminologia usada com o serviço de provisionamento de dispositivos no Hub IoT do Azure | Microsoft Docs
description: Descreve a terminologia comum usada com o DPS (serviço de provisionamento de dispositivos) e o Hub IoT
author: wesmc7777
ms.author: wesmc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotga
ms.openlocfilehash: b9fc37c6589cdd0bc6a5cdce7b7ebebe2c6e9a85
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019439"
---
# <a name="iot-hub-device-provisioning-service-dps-terminology"></a>Terminologia do DPS (serviço de provisionamento de dispositivos) do Hub IoT

O Serviço de Provisionamento de Dispositivos no Hub IoT é um serviço auxiliar do Hub IoT que você usa para configurar o provisionamento de dispositivo sem interação para um Hub IoT especificado. Com o serviço de provisionamento de dispositivos, você pode [provisionar](about-iot-dps.md#provisioning-process) milhões de dispositivos de maneira segura e escalonável.

O provisionamento de dispositivos é um processo de duas partes. A primeira parte é estabelecer a conexão inicial entre o dispositivo e a solução de IoT *registrando* o dispositivo. A segunda parte é aplicar a *configuração* apropriada ao dispositivo com base nos requisitos específicos da solução. Quando as duas etapas forem concluídas, o dispositivo será totalmente *provisionado*. O Serviço de Provisionamento de Dispositivos automatiza as etapas para fornecer uma experiência perfeita de provisionamento para o dispositivo.

Este artigo fornece uma visão geral dos conceitos de provisionamento mais aplicáveis para gerenciar o *serviço*. Este artigo é muito relevante para todas as personas envolvidas na [etapa de configuração de nuvem](about-iot-dps.md#cloud-setup-step) da preparação de um dispositivo para implantação.

## <a name="service-operations-endpoint"></a>Ponto de extremidade das operações de serviço

O ponto de extremidade das operações de serviço é o ponto de extremidade para gerenciar as configurações de serviço e manter a lista de registro. Esse ponto de extremidade é usado somente pelo administrador de serviços. Ele não é usado pelos dispositivos.

## <a name="device-provisioning-endpoint"></a>Ponto de extremidade de provisionamento do dispositivo

O ponto de extremidade de provisionamento de dispositivos é o ponto de extremidade único que todos os dispositivos usam para provisionamento automático. A URL é a mesma para todas as instâncias de serviços de provisionamento, para eliminar a necessidade de atualizar novamente os dispositivos com novas informações de conexão nos cenários da cadeia de fornecedores. O escopo da ID garante o isolamento de locatários.

## <a name="linked-iot-hubs"></a>Hubs IoT vinculados

O Serviço de Provisionamento de Dispositivos somente pode provisionar dispositivos para hubs de IoT que tenham sido vinculados a ele. Vincular um Hub IoT a uma instância do serviço de Provisionamento de Dispositivo fornece as permissões de leitura/gravação de serviço ao registro de dispositivo do Hub IoT e, com o vínculo, um serviço de Provisionamento de Dispositivos pode registrar uma ID do dispositivo e definir a configuração inicial no dispositivo gêmeo. Hubs de IoT vinculados podem estar em qualquer região do Azure. Você pode vincular hubs em outras assinaturas ao seu serviço de provisionamento.


## <a name="allocation-policy"></a>Política de alocação

A configuração de nível de serviço determina como o Serviço de Provisionamento de Dispositivos atribui dispositivos a um Hub IoT. Há três políticas de alocação com suporte:

* **Distribuição igualmente ponderada**: Hubs IoT vinculados têm probabilidades iguais de ter dispositivos provisionados a eles. A configuração padrão. Se estiver provisionando dispositivos a apenas um Hub IoT, você poderá manter essa configuração.

* **Menor latência**: dispositivos são provisionados para um Hub IoT com a menor latência para o dispositivo. Se vários Hubs IoT vinculados fornecerem a mesma menor latência, o serviço de provisionamento reciclará dispositivos entre esses hubs

* **Configuração estática por meio da lista de registro**: a especificação do Hub IoT desejado na lista de registro tem prioridade sobre a política de alocação no nível do serviço.

* **Personalizado (use A função do Azure)**: uma política de alocação personalizada oferece mais controle sobre como os dispositivos são atribuídos a um hub IOT. Isso é feito usando código personalizado em uma função do Azure para atribuir dispositivos a um Hub IoT. O serviço de provisionamento de dispositivos chama o código de função do Azure, fornecendo todas as informações relevantes sobre o dispositivo e o registro para seu código. O código de função é executado e retorna as informações do Hub IoT para provisionar o dispositivo.

## <a name="enrollment"></a>Registro

Um registro é o registro de dispositivos ou grupos de dispositivos que podem ser registrados por meio de provisionamento automático. O registro de registro contém informações sobre o dispositivo ou grupo de dispositivos, incluindo:
- o [mecanismo de certificação](#attestation-mechanism) usado pelo dispositivo
- a configuração inicial desejada opcional
- hub IoT desejado
- a ID do dispositivo desejado

Há dois tipos de registros com suporte pelo Serviço de Provisionamento de Dispositivos:

### <a name="enrollment-group"></a>Grupo de registro

Um grupo de registro é um grupo de dispositivos que compartilham um mecanismo de atestado específico. Os grupos de registro dão suporte a X. 509, bem como ao Symmetric. Todos os dispositivos no grupo de registro X. 509 apresentam certificados X. 509 que foram assinados pela mesma raiz ou AC (autoridade de certificação) intermediária. Cada dispositivo no grupo de registro de chave simétrica apresenta tokens SAS derivados da chave simétrica do grupo. O nome do grupo de registro e o nome do certificado devem ter caracteres alfanuméricos, letras minúsculas e podem conter hifens.

> [!TIP]
> É recomendável usar um grupo de registro para um grande número de dispositivos que compartilhem uma configuração inicial desejada ou para dispositivos que vão todos para o mesmo locatário.

### <a name="individual-enrollment"></a>Registro individual

Um registro individual é uma entrada para um único dispositivo que pode registrar. Os registros individuais podem usar certificados folha X.509 ou tokens de SAS (de um TPM físico ou virtual) como mecanismos de certificação. A ID do registro em um registro individual é alfanumérica, com letras minúsculas e pode conter hifens. Registros individuais podem ter a ID de dispositivo de Hub IoT desejada especificada.

> [!TIP]
> É recomendável o uso de registros individuais para dispositivos que exigem configurações iniciais exclusivas ou para dispositivos que somente podem autenticar usando tokens de SAS via atestado de TPM.


## <a name="attestation-mechanism"></a>Mecanismo de atestado

Um mecanismo de atestado é o método usado para confirmar a identidade de um dispositivo. O mecanismo de atestado é configurado em uma entrada de registro e informa ao serviço de provisionamento qual método usar ao verificar a identidade de um dispositivo durante o registro.

> [!NOTE]
> O Hub IoT usa o "esquema de autenticação" para um conceito semelhante nesse serviço.

O Serviço de Provisionamento de Dispositivos dá suporte às seguintes formas de atestado:
* **Certificados X.509** com base no fluxo de autenticação do certificado X.509 padrão. Para obter mais informações, consulte [atestado X. 509](concepts-x509-attestation.md).
* **Trusted Platform Module (TPM)** com base em um desafio nonce, usando o padrão TPM para chaves para apresentar um token de Assinatura de Acesso Compartilhado (SAS) assinado. Isso não requer um TPM físico no dispositivo, mas o serviço espera atestar usando a chave de endosso de acordo com a [especificação do TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/). Para obter mais informações, consulte [atestado de TPM](concepts-tpm-attestation.md).
* **Chave simétrica** com base em [tokens de segurança](../iot-hub/iot-hub-devguide-security.md#security-tokens) de SAS (assinatura de acesso compartilhado), que incluem uma assinatura de hash e uma expiração inserida. Para obter mais informações, veja [Atestado de chave simétrica](concepts-symmetric-key-attestation.md).


## <a name="hardware-security-module"></a>Módulo de segurança de hardware

O módulo de segurança de hardware, ou HSM, é usado para armazenamento seguro, com base em hardware, de segredos do dispositivo e é a forma mais segura de armazenamento de segredo. Os certificados X.509 e os tokens SAS podem ser armazenados no HSM. Os HSMs podem ser usados com ambos os mecanismos de atestado que o serviço de provisionamento dá suporte.

> [!TIP]
> É altamente recomendável usar um HSM com dispositivos para armazenar segredos com segurança em seus dispositivos.

Os segredos de dispositivo também podem ser armazenados em software (memória), mas é uma forma menos segura de armazenamento que um HSM.



## <a name="id-scope"></a>Escopo da ID

O escopo da ID é atribuído a um Serviço de Provisionamento de Dispositivos quando ele é criado pelo usuário e é usado para identificar exclusivamente o serviço de provisionamento específico por meio do qual o dispositivo será registrado. O escopo da ID é gerado pelo serviço e é imutável, o que assegura a exclusividade.

> [!NOTE]
> A exclusividade é importante para operações de implantação de execução longa e cenários de mesclagem e aquisição.


## <a name="registration"></a>Registro

Um registro é o registro de um dispositivo com registro/provisionamento com êxito para um Hub IoT por meio do Serviço de Provisionamento de Dispositivos. Registros são criados automaticamente. Eles podem ser excluídos, mas não podem ser atualizados.


## <a name="registration-id"></a>ID de registro

A ID de registro é usada para identificar exclusivamente um registro de dispositivo com o serviço de provisionamento de dispositivos. A ID do dispositivo deve ser exclusiva no [escopo da ID](#id-scope) do serviço de provisionamento. Cada dispositivo deve ter uma ID de registro. A ID de registro é alfanumérica, não diferencia maiúsculas de minúsculas e pode conter caracteres especiais, incluindo dois pontos, ponto, sublinhado e hífen.

* No caso do TPM, a ID de registro é fornecida pelo próprio TPM.
* No caso de atestado com base em X.509, a ID do registro é fornecida como o nome da entidade do certificado.

## <a name="device-id"></a>ID do Dispositivo

A ID do dispositivo é a ID como ela aparece no Hub IoT. A ID do dispositivo desejada pode ser definida na entrada do registro, mas sua definição não é necessária. A configuração da ID de dispositivo desejada só tem suporte em registros individuais. Se nenhuma ID de dispositivo desejada for especificada na lista de registro, a ID do registro será usada como a ID do dispositivo ao registrar o dispositivo. Saiba mais sobre as [IDs de dispositivo do Hub IoT](../iot-hub/iot-hub-devguide-identity-registry.md).



## <a name="operations"></a>Operations

As operações são a unidade de cobrança do Serviço de Provisionamento de Dispositivos. Uma operação é a conclusão bem-sucedida de uma instrução para o serviço. As operações incluem registros e repetições de registros de dispositivo, bem como alterações no lado do serviço, como adição e atualização das entradas da lista de registro.
