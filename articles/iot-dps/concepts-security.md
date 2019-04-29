---
title: Conceitos de segurança no Serviço de Provisionamento de Dispositivos no Hub IoT do Azure | Microsoft Docs
description: Descreve conceitos de provisionamento de segurança específicos para dispositivos com o Serviço de Provisionamento de Dispositivos e o Hub IoT
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: e35330874c647eba2cddde694563c8a1d9e83df5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60775110"
---
# <a name="iot-hub-device-provisioning-service-security-concepts"></a>Conceitos de segurança do Serviço de Provisionamento de Dispositivos no Hub IoT 

O Serviço de Provisionamento de Dispositivos no Hub IoT é um serviço auxiliar do Hub IoT que você usa para configurar o provisionamento de dispositivo sem interação para um Hub IoT especificado. Com o Serviço de Provisionamento de Dispositivos, é possível [provisionar automaticamente](concepts-auto-provisioning.md) milhões de dispositivos de maneira segura e escalonável. Este artigo fornece uma visão geral dos conceitos de *segurança* envolvidos no provisionamento do dispositivo. Este artigo é relevante para todas as personas envolvidas na preparação de um dispositivo para implantação.

## <a name="attestation-mechanism"></a>Mecanismo de atestado

O mecanismo de atestado é o método usado para confirmar a identidade de um dispositivo. O mecanismo de atestado também é relevante para a lista de registro, que informa ao serviço de provisionamento qual método de atestado usar com um determinado dispositivo.

> [!NOTE]
> O Hub IoT usa o "esquema de autenticação" para um conceito semelhante nesse serviço.

O Serviço de Provisionamento de Dispositivos dá suporte às seguintes formas de atestado:
* **Certificados X.509** com base no fluxo de autenticação do certificado X.509 padrão.
* **Trusted Platform Module (TPM)** com base em um desafio nonce, usando o padrão TPM para chaves para apresentar um token de Assinatura de Acesso Compartilhado (SAS) assinado. Esta forma de atestado não requer um TPM físico no dispositivo, mas o serviço espera atestar usando a chave de endosso de acordo com a [especificação TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).
* **Chave simétrica** com base em [tokens de segurança](../iot-hub/iot-hub-devguide-security.md#security-tokens) de SAS (assinatura de acesso compartilhado), que incluem uma assinatura de hash e uma expiração inserida. Para obter mais informações, veja [Atestado de chave simétrica](concepts-symmetric-key-attestation.md).


## <a name="hardware-security-module"></a>Módulo de segurança de hardware

O módulo de segurança de hardware, ou HSM, é usado para armazenamento seguro, com base em hardware, de segredos do dispositivo e é a forma mais segura de armazenamento de segredo. Os certificados X.509 e os tokens SAS podem ser armazenados no HSM. Os HSMs podem ser usados com ambos os mecanismos de atestado que o provisionamento dá suporte.

> [!TIP]
> É altamente recomendável usar um HSM com dispositivos para armazenar segredos com segurança em seus dispositivos.

Os segredos de dispositivo também podem ser armazenados em software (memória), mas é uma forma menos segura de armazenamento que um HSM.

## <a name="trusted-platform-module"></a>Trusted Platform Module

O TPM pode se referir a um padrão para armazenar com segurança as chaves usadas para autenticar a plataforma ou pode se referir à interface de E/S usada para interagir com os módulos que implementam o padrão. Os TPMs podem existir como hardware discreto, hardware integrado, com base no firmware ou com base em software. Saiba mais sobre [TPMs e atestado de TPM](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation). O Serviço de Provisionamento de Dispositivos somente dá suporte ao TPM 2.0.

O atestado de TPM é baseado em um desafio de nonce, o qual usa as chaves raiz de armazenamento e de endosso para apresentar um token de SAS (Assinatura de Acesso Compartilhado) assinado.

### <a name="endorsement-key"></a>Chave de endosso

A chave de endosso é uma chave assimétrica contida dentro do TPM que foi gerada internamente ou inserida no tempo de fabricação e é exclusiva para cada TPM. A chave de endosso não pode ser alterada ou removida. A parte privada da chave de endosso nunca é liberada fora do TPM, enquanto que a parte pública da chave de endosso é usada para reconhecer um TPM genuíno. Saiba mais sobre a [chave de endosso](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx).

### <a name="storage-root-key"></a>Chave raiz de armazenamento

A chave raiz de armazenamento é armazenada no TPM e é usada para proteger as chaves do TPM criadas por aplicativos, para que essas chaves não possam ser usadas sem o TPM. A chave raiz de armazenamento é gerada quando você assume a propriedade do TPM. Quando você limpa o TPM para que um novo usuário possa assumir a propriedade, uma nova chave raiz de armazenamento é gerada. Saiba mais sobre a [chave raiz de armazenamento](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx).

## <a name="x509-certificates"></a>Certificados X.509

Usar certificados X.509 como um mecanismo de atestado é uma maneira excelente de escalar a produção e simplificar o provisionamento do dispositivo. Os certificados X.509 normalmente são organizados em uma cadeia de certificados de confiança na qual cada certificado na cadeia é assinado pela chave privada do próximo certificado mais alto e assim por diante, terminando em um certificado raiz autoassinado. Essa estruturação estabelece uma cadeia de confiança delegada a partir do certificado raiz gerado por uma AC (autoridade de certificação) raiz confiável através de cada AC intermediária para o certificado "folha" de entidade final instalado em um dispositivo. Para saber mais, consulte [Autenticação de dispositivo usando certificados de AC X.509](/azure/iot-hub/iot-hub-x509ca-overview). 

Geralmente a cadeia de certificados representa alguma hierarquia física ou lógica associada aos dispositivos. Por exemplo, um fabricante pode:
- emitir um Certificado de Autoridade de Certificação raiz autoassinado
- usar o certificado raiz para gerar um Certificado de Autoridade de Certificação intermediária exclusivo para cada alocador
- usar o certificado de cada alocador para gerar um Certificado de Autoridade de Certificação intermediária exclusivo para cada linha de produção na planta
- e, finalmente, usar o certificado de linha de produção para gerar um certificado de dispositivo único (entidade final) para cada dispositivo fabricado na linha. 

Para saber mais, consulte [Entendimento conceitual de certificados de AC X.509 no setor de IoT](/azure/iot-hub/iot-hub-x509ca-concept). 

### <a name="root-certificate"></a>Certificado raiz

Um certificado raiz é um certificado X.509 autoassinado que representa uma AC (autoridade de certificação). É o terminal, ou âncora de confiança, da cadeia de certificados. Certificados raiz podem ser autoemitidos por uma organização ou adquiridos de uma autoridade de certificação raiz. Para saber mais, consulte [Obter certificados de AC X.509](/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates). O certificado raiz também pode ser referenciado como um certificado de AC raiz.

### <a name="intermediate-certificate"></a>Certificado intermediário

Um certificado intermediário é um certificado X.509 que foi assinado pelo certificado raiz (ou por outro certificado intermediário com o certificado raiz em sua cadeia). O último certificado intermediário em uma cadeia é usado para assinar o certificado de folha. Um certificado intermediário também pode ser referenciado como um certificado de AC intermediário.

### <a name="end-entity-leaf-certificate"></a>Certificado "secundário" de entidade final

O certificado de folha, ou certificado de entidade final, identifica o proprietário do certificado. Ele tem o certificado raiz em sua cadeia de certificados, bem como zero ou mais certificados intermediários. O certificado de folha não é usado para assinar outros certificados. Ele identifica exclusivamente o dispositivo para o serviço de provisionamento e, às vezes, são referenciados como um certificado de dispositivo. Durante a autenticação, o dispositivo usa a chave privada associada ao certificado para responder a um desafio de comprovação de posse do serviço.

Folha certificados usados com um [registro Individual](./concepts-service.md#individual-enrollment) entrada tem um requisito de que o **nome da entidade** deve ser definida como a ID do registro da entrada de registro Individual. Folha certificados usados com um [grupo de registros](./concepts-service.md#enrollment-group) entrada deve ter o **nome da entidade** definido como a ID do dispositivo desejada que será mostrada no **registros** para o dispositivo autenticado no grupo de registro.

Para saber mais, consulte [Autenticação de dispositivos assinados com certificados de AC X.509](/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates).

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Controlando o acesso de dispositivo para o serviço de provisionamento com certificados X.509

O serviço de provisionamento expõe dois tipos de entrada de registro que você pode usar para controlar o acesso de dispositivos que usam o mecanismo de atestado X.509:  

- As entradas de [registro individual](./concepts-service.md#individual-enrollment) são configuradas com o certificado do dispositivo associado a um dispositivo específico. Essas entradas controlam os registros de dispositivos específicos.
- As entradas de [grupo de registros](./concepts-service.md#enrollment-group) são associadas a um certificado de AC intermediário ou raiz. Essas entradas controlam os registros de todos os dispositivos que têm esse certificado raiz ou intermediário em sua cadeia de certificados. 

Quando um dispositivo se conecta ao serviço de provisionamento, o serviço prioriza entradas de registro mais específicas em relação a entradas de registro menos específicas. Ou seja, se existir um registro individual para o dispositivo, o serviço de provisionamento aplicará essa entrada. Se não houver nenhum registro individual para o dispositivo e existir um grupo de registro para o primeiro certificado intermediário na cadeia de certificados do dispositivo, o serviço aplicará essa entrada, e assim por diante para cima na cadeia até a raiz. O serviço aplica a primeira entrada aplicável que encontrar, como:

- Se a primeira entrada de registro encontrada estiver habilitada, o serviço provisionará o dispositivo.
- Se a primeira entrada de registro encontrada estiver desabilitada, o serviço não provisionará o dispositivo.  
- Se não for encontrada nenhuma entrada de registro para qualquer um dos certificados na cadeia de certificados do dispositivo, o serviço não provisionará o dispositivo. 

Esse mecanismo e a estrutura hierárquica de cadeias de certificados oferece flexibilidade eficiente em como você pode controlar o acesso a dispositivos individuais, bem como a grupos de dispositivos. Por exemplo, imagine cinco dispositivos com as seguintes cadeias de certificados: 

- *Dispositivo 1*: certificado raiz -> certificado A -> certificado do dispositivo 1
- *Dispositivo 2*: certificado raiz -> certificado A -> certificado do dispositivo 2
- *Dispositivo 3*: certificado raiz -> certificado A -> certificado do dispositivo 3
- *Dispositivo 4*: certificado raiz -> certificado B -> certificado do dispositivo 4
- *Dispositivo 5*: certificado raiz -> certificado B -> certificado do dispositivo 5

Inicialmente, você pode criar uma única entrada de registro de grupo habilitado para o certificado raiz para habilitar o acesso para todos os cinco dispositivos. Se o certificado B posteriormente for comprometido, você poderá criar uma entrada de grupo de registros desabilitada para o certificado B para impedir que o *Dispositivo 4* e o *Dispositivo 5* se registrem. Se depois ainda o *Dispositivo 3* for comprometido, você poderá criar uma entrada de registro individual desabilitada para este certificado. Isso revoga o acesso para o *Dispositivo 3*, mas ainda permite que o *Dispositivo 1* e o *Dispositivo 2* se registrem.