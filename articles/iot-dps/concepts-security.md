---
title: "Conceitos de segurança no Serviço de Provisionamento de Dispositivos no Hub IoT do Azure | Microsoft Docs"
description: "Descreve conceitos de provisionamento de segurança específicos para dispositivos com o Serviço de Provisionamento de Dispositivos e o Hub IoT"
services: iot-dps
keywords: 
author: nberdy
ms.author: nberdy
ms.date: 09/05/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 3ccbaaf55d2bdfedffcdb5ca069798328e2d75fd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="iot-hub-device-provisioning-service-security-concepts"></a>Conceitos de segurança do Serviço de Provisionamento de Dispositivos no Hub IoT 

O Serviço de Provisionamento de Dispositivos no Hub IoT é um serviço auxiliar do Hub IoT que você usa para configurar o provisionamento de dispositivo sem interação para um Hub IoT especificado. Com o Serviço de Provisionamento de Dispositivos, você pode provisionar milhões de dispositivos de uma maneira segura e escalonável. Este artigo fornece uma visão geral dos conceitos de *segurança* envolvidos no provisionamento do dispositivo. Este artigo é relevante para todas as personas envolvidas na preparação de um dispositivo para implantação.

## <a name="attestation-mechanism"></a>Mecanismo de atestado

O mecanismo de atestado é o método usado para confirmar a identidade de um dispositivo. O mecanismo de atestado também é relevante para a lista de registro, que informa ao serviço de provisionamento qual método de atestado usar com um determinado dispositivo.

> [!NOTE]
> O Hub IoT usa o "esquema de autenticação" para um conceito semelhante nesse serviço.

O Serviço de Provisionamento de Dispositivos dá suporte a duas formas de atestado:
* **Certificados X.509** com base no fluxo de autenticação do certificado X.509 padrão.
* **Tokens SAS** com base em um desafio de nonce usando o padrão TPM para chaves. Isso não requer um TPM físico no dispositivo, mas o serviço espera atestar usando a chave de endosso de acordo com a [especificação TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).

## <a name="hardware-security-module"></a>Módulo de segurança de hardware

O módulo de segurança de hardware, ou HSM, é usado para armazenamento seguro, com base em hardware, de segredos do dispositivo e é a forma mais segura de armazenamento de segredo. Os certificados X.509 e os tokens SAS podem ser armazenados no HSM. Os HSMs podem ser usados com ambos os mecanismos de atestado que o provisionamento dá suporte.

> [!TIP]
> É altamente recomendável usar um HSM com dispositivos para armazenar segredos com segurança em seus dispositivos.

Os segredos de dispositivo também podem ser armazenados em software (memória), mas é uma forma menos segura de armazenamento que um HSM.

## <a name="trusted-platform-module-tpm"></a>TPM (Trusted Platform Module)

O TPM pode se referir a um padrão para armazenar com segurança as chaves usadas para autenticar a plataforma ou pode se referir à interface de E/S usada para interagir com os módulos que implementam o padrão. Os TPMs podem existir como hardware discreto, hardware integrado, com base no firmware ou com base em software. Saiba mais sobre [TPMs e atestado de TPM](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation). O Serviço de Provisionamento de Dispositivos somente dá suporte ao TPM 2.0.

## <a name="endorsement-key"></a>Chave de endosso

A chave de endosso é uma chave assimétrica contida dentro do TPM que foi inserido no tempo de fabricação e é exclusivo para cada TPM. A chave de endosso não pode ser alterada ou removida. A parte privada da chave de endosso nunca é liberada fora do TPM, enquanto que a parte pública da chave de endosso é usada para reconhecer um TPM genuíno. Saiba mais sobre a [chave de endosso](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx).

## <a name="storage-root-key"></a>Chave raiz de armazenamento

A chave raiz de armazenamento é armazenada no TPM e é usada para proteger as chaves do TPM criadas por aplicativos, para que essas chaves não possam ser usadas sem o TPM. A chave raiz de armazenamento é gerada quando você assume a propriedade do TPM. Quando você limpa o TPM para que um novo usuário possa assumir a propriedade, uma nova chave raiz de armazenamento é gerada. Saiba mais sobre a [chave raiz de armazenamento](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx).

## <a name="root-certificate"></a>Certificado raiz

Um certificado raiz é um tipo de certificado X.509 que representa uma autoridade de certificado e é autoassinado. É o terminal da cadeia de certificados.

## <a name="intermediate-certificate"></a>Certificado intermediário

Um certificado intermediário é um certificado X.509 que foi assinado pelo certificado raiz (ou por outro certificado com o certificado raiz em sua cadeia) e que é usado para assinar o certificado de folha.

## <a name="leaf-certificate"></a>Certificado de folha

Um certificado de folha ou o certificado de entidade final, é usado para identificar o proprietário do certificado e tem o certificado raiz em sua cadeia de certificados. O certificado de folha não é usado para assinar outros certificados.
