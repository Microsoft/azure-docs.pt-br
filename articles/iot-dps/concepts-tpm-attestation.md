---
title: Serviço de Provisionamento de Dispositivos no Hub IoT do Azure – Atestado de TPM
description: Este artigo fornece uma visão geral conceitual do fluxo de atestado de TPM usando o Serviço de Provisionamento de Dispositivos  IoT.
author: nberdy
ms.author: nberdy
ms.date: 04/23/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 07c5dbce0b98d1c197164f4fc77682f78ede57f0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60746352"
---
# <a name="tpm-attestation"></a>Atestado de TPM

O Serviço de Provisionamento de Dispositivos no Hub IoT é um serviço auxiliar do Hub IoT que você usa para configurar o provisionamento de dispositivo sem interação para um Hub IoT especificado. Com o Serviço de Provisionamento de Dispositivos, é possível provisionar milhões de dispositivos de maneira segura.

Este artigo descreve um processo de atestado de identidade ao usar um [TPM](./concepts-device.md). TPM significa Trusted Platform Module e é um tipo de HSM (módulo de segurança de hardware). Este artigo pressupõe que você está usando um TPM discreto, de firmware ou integrado. Os TPMs emulados para software são adequados para fazer protótipos ou teste, mas não oferecem o mesmo nível de segurança que os TPMs discretos, de firmware ou integrados oferecem. Não recomendamos usar TPMs de software em produção. Para obter mais informações sobre os tipos de TPMs, consulte [Uma breve introdução ao TPM](https://trustedcomputinggroup.org/wp-content/uploads/TPM-2.0-A-Brief-Introduction.pdf).

Este artigo só é relevante para dispositivos que usam o TPM 2.0 com suporte à chave HMAC e suas chaves de endosso. Não é para dispositivos que usam certificados X.509 para autenticação. O TPM é um padrão ISO de todo o setor do Trusted Computing Group, e é possível ler mais sobre ele na [especificação do TPM 2.0 completa](https://trustedcomputinggroup.org/tpm-library-specification/) ou na [especificação ISO/IEC 11889](https://www.iso.org/standard/66510.html). Este artigo também pressupõe que você conhece os pares de chaves públicas e privadas e como elas são usadas para criptografia.

Os SDKs do dispositivo do Serviço de Provisionamento de Dispositivos lidam com tudo que foi descrito neste artigo para você. Não será necessário implementar mais nada se você estiver usando os SDKs em seus dispositivos. Este artigo ajuda a entender conceitualmente o que está acontecendo com seu chip de segurança do TPM quando seu dispositivo é provisionado e por que ele é tão seguro.

## <a name="overview"></a>Visão geral

Os TPMs usam uma coisa chamada EK (chave de endosso) como a raiz de confiança segura. A EK é exclusiva do TPM e sua alteração altera essencialmente o dispositivo para um novo.

Há outro tipo de chave que os TPMs têm, chamada SRK (chave raiz de armazenamento). Uma SRK pode ser gerada pelo proprietário do TPM após ele assumir a propriedade do TPM. Assumir a propriedade do TPM é uma maneira específica do TPM de dizer que "alguém define uma senha no HSM." Se um dispositivo for vendido para um novo proprietário, ele poderá assumir a propriedade do TPM para gerar uma nova SRK. A geração de uma nova SRK garante que o proprietário anterior não poderá usar o TPM. Como a SRK é exclusiva do proprietário do TPM, a SRK pode ser usada para selar dados para o próprio TPM para esse proprietário. A SRK oferecerá uma sandbox para o proprietário armazenar suas chaves e fornecerá revogabilidade de acesso se o dispositivo ou o TPM for vendido. É como se mudar para uma nova casa: assumir a propriedade é trocar as fechaduras das portas e destruir todos os móveis deixados pelos proprietários anteriores (SRK), mas não é possível alterar o endereço da casa (EK).

Depois que um dispositivo for configurado e estiver pronto para uso, ele terá uma EK e uma SRK disponíveis para uso.

![Assumindo a propriedade de um TPM](./media/concepts-tpm-attestation/tpm-ownership.png)

Uma observação sobre a propriedade do TPM: Apropriar-se de um TPM depende de muitos fatores, incluindo o fabricante do TPM, o conjunto de ferramentas do TPM que está sendo usado e o sistema operacional do dispositivo. Siga as instruções relevantes para seu sistema para assumir a propriedade.

O Serviço de Provisionamento de Dispositivos usa a parte pública da EK (EK_pub) para identificar e registrar dispositivos. O fornecedor do dispositivo poderá ler o EK_pub durante a fabricação ou testagem final e carregar o EK_pub para o serviço de provisionamento para que o dispositivo seja reconhecido quando se conectar para provisionar. O Serviço de Provisionamento de Dispositivos não verifica a SRK ou o proprietário. Portanto, "limpar" o TPM apagará os dados do cliente, mas a EK (e os outros dados do fornecedor) será preservada e o dispositivo ainda será reconhecido pelo Serviço de Provisionamento de Dispositivos quando ele se conectar para provisionar.

## <a name="detailed-attestation-process"></a>Processo de atestado detalhado

Quando um dispositivo com um TPM se conecta pela primeira vez ao Serviço de Provisionamento de Dispositivos, primeiro o serviço verifica o EK_pub fornecido com relação ao EK_pub armazenado na lista de registro. Se os EK_pubs não coincidirem, o dispositivo não terá permissão para provisionar. Se os EK_pubs não coincidirem, o serviço exigirá que o dispositivo prove a propriedade da parte privada da EK por meio de um desafio nonce, que é um desafio seguro usado para provar a identidade. O Serviço de Provisionamento de Dispositivos gera um nonce e, em seguida, o criptografa com a SRK e o EK_pub, fornecidos pelo dispositivo durante a chamada de registro inicial. O TPM sempre mantém a parte privada da EK segura. Isso impede a falsificação e garante que os tokens SAS são provisionados com segurança para dispositivos autorizados.

Vamos percorrer o processo de atestado com detalhes.

### <a name="device-requests-an-iot-hub-assignment"></a>O dispositivo solicita uma atribuição do Hub IoT

Primeiro, o dispositivo conecta-se ao Serviço de Provisionamento de Dispositivos e solicita o provisionamento. Ao fazer isso, o dispositivo fornece o serviço com sua ID de registro, um escopo da ID e o EK_pub e SRK_pub do TPM. O serviço passa o nonce criptografado de volta para o dispositivo e pede a ele para descriptografar o nonce e usar isso para assinar um token SAS para conectar-se novamente e concluir o provisionamento.

![O dispositivo solicita o provisionamento](./media/concepts-tpm-attestation/step-one-request-provisioning.png)

### <a name="nonce-challenge"></a>Desafio nonce

O dispositivo usa o nonce e as partes privadas da EK e da SRK para descriptografar o nonce para o TPM; a ordem da criptografia do nonce delega confiança da EK, que é imutável, para a SRK, que poderá ser alterada se um novo propriedade assumir a propriedade do TPM.

![Descriptografando o nonce](./media/concepts-tpm-attestation/step-two-nonce.png)

### <a name="validate-the-nonce-and-receive-credentials"></a>Validar o nonce e receber as credenciais

O dispositivo pode assinar um token SAS usando o nonce descriptografado e reestabelecer uma conexão com o Serviço de Provisionamento de Dispositivos usando o token SAS assinado. Com o desafio nonce concluído, o serviço permite que o dispositivo provisione.

![O dispositivo reestabelece a conexão com o Serviço de Provisionamento de Dispositivo para validar a propriedade da EK](./media/concepts-tpm-attestation/step-three-validation.png)

## <a name="next-steps"></a>Próximas etapas

Agora o dispositivo conecta-se ao Hub IoT e você tem a certeza de que as chaves dos seus dispositivos estão armazenadas com segurança. Agora que você sabe como o Serviço de Provisionamento de Dispositivos verifica com segurança a identidade de um dispositivo usando o TPM, confira os seguintes artigos para saber mais:

* [Conheça todos os conceitos no provisionamento automático](./concepts-auto-provisioning.md)
* [Comece a usar o provisionamento automático](./quick-setup-auto-provision.md) com os SDKs para cuidar do fluxo.
