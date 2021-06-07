---
title: Tutorial – Noções básicas sobre os certificados de chave pública X.509 para o Hub IoT do Azure | Microsoft Docs
description: Tutorial – Noções básicas sobre os certificados de chave pública X.509 para o Hub IoT do Azure
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 5503f9ad57180146c25a01c133a27b34e643496c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378339"
---
# <a name="tutorial-understanding-x509-public-key-certificates"></a>Tutorial: Noções básicas sobre os certificados de chave pública X.509

Os certificados X.509 são documentos digitais que representam um usuário, um computador, um serviço ou um dispositivo. Eles são emitidos por uma AC (autoridade de certificação), uma AC subordinada ou uma autoridade de registro e contêm a chave pública da entidade do certificado. Eles não contêm a chave privada da entidade que precisa ser armazenada com segurança. Os certificados de chave pública são documentados pelo [RFC 5280](https://tools.ietf.org/html/rfc5280). Eles são assinados digitalmente e, em geral, contêm as seguintes informações:

* Informações sobre a entidade do certificado
* A chave pública que corresponde à chave privada da entidade
* Informações sobre a AC emissora
* Os algoritmos de criptografia e/ou assinatura digital com suporte
* Informações para determinar a revogação e o status de validade do certificado

## <a name="certificate-fields"></a>Campos do certificado

Com o tempo, houve três versões de certificado. Cada versão adiciona campos à anterior. A versão 3 é atual e contém os campos das versões 1 e 2, além dos campos da versão 3. A versão 1 definiu os seguintes campos:

* **Versão**: um valor (1, 2 ou 3) que identifica o número de versão do certificado
* **Número de Série**: um número exclusivo para cada certificado emitido por uma AC
* **Algoritmo de Assinatura de AC**: nome do algoritmo usado pela AC para assinar o conteúdo do certificado
* **Nome do Emissor**: o DN (nome diferenciado) da AC emissora do certificado
* **Período de Validade**: o período durante o qual o certificado é considerado válido
* **Nome da Entidade**: nome da entidade representada pelo certificado
* **Informações de Chave Pública da Entidade**: chave pública pertencente à entidade do certificado

A versão 2 adicionou os campos a seguir que contêm informações sobre o emissor do certificado. No entanto, esses campos são raramente usados.

* **ID Exclusiva do Emissor**: um identificador exclusivo para a AC emissora, conforme definido pela AC
* **ID Exclusiva da Entidade**: um identificador exclusivo para a entidade do certificado, conforme definido pela AC emissora

Os certificados da versão 3 adicionaram as seguintes extensões:

* **Identificador da Chave da Autoridade**: pode ter um destes dois valores:
  * A entidade da AC e o número de série do Certificado de Autoridade de Certificação que emitiu este certificado
  * Um hash da chave pública da AC que emitiu este certificado
* **Identificador de Chave da Entidade**: hash da chave pública do certificado atual
* **Uso da Chave** Define o serviço para o qual um certificado pode ser usado. Esse valor pode ser um ou mais dos seguintes:
  * **Assinatura digital**
  * **Não recusa**
  * **Codificação de chave**
  * **Codificação de Dados**
  * **Acordo de Chaves**
  * **Sinal de Certificado da Chave**
  * **Sinal de CRL**
  * **Somente Criptografia**
  * **Somente Descriptografia**
* **Período de Uso da Chave Privada**: período de validade da parte da chave privada de um par de chaves
* **Políticas de Certificado**: políticas usadas para validar a entidade do certificado
* **Mapeamentos de Política**: mapeia uma política em uma organização para a política em outra
* **Nome Alternativo da Entidade**: lista de nomes alternativos para a entidade
* **Nome Alternativo do Emissor**: lista de nomes alternativos para a AC emissora
* **Atributo Dir da Entidade**: atributos de um diretório X.500 ou LDAP
* **Restrições Básicas**: permite que o certificado designe se ele é emitido para uma AC ou para um usuário, um computador, um dispositivo ou um serviço. Essa extensão também inclui uma restrição de comprimento de caminho que limita o número de ACs subordinadas que podem existir.
* **Restrições de Nome**: designa quais namespaces são permitidos em um certificado emitido por uma AC
* **Restrições de Política**: podem ser usadas para proibir mapeamentos de política entre ACs
* **Uso Estendido de Chave**: indica como a chave pública de um certificado pode ser usada além das finalidades identificadas na extensão de **Uso de Chave**
* **Pontos de Distribuição de CRL**: contém uma ou mais URLs em que a CRL (lista de certificados revogados) base é publicada
* **Inibir anyPolicy**: inibe o uso do OID de **Todas as Políticas de Emissão** (2.5.29.32.0) em certificados de AC subordinada
* **CRL mais Recente**: contém uma ou mais URLs em que a CRL delta da AC emissora é publicada
* **Acesso às Informações da Autoridade**: contém uma ou mais URLs em que o Certificado de AC emissora é publicado
* **Acesso a Informações da Entidade**: contém informações sobre como recuperar detalhes adicionais de uma entidade de certificado

## <a name="certificate-formats"></a>Formatos de certificado

Os certificados podem ser salvos em vários formatos. A autenticação do Hub IoT do Azure normalmente usa os formatos PEM e PFX.

### <a name="binary-certificate"></a>Certificado binário

Contém um certificado binário de formato bruto usando a Codificação DER ASN.1.

### <a name="ascii-pem-format"></a>Formato ASCII PEM

Um certificado PEM (extensão .pem) contém um certificado codificado em Base64 que começa com -----BEGIN CERTIFICATE----- e termina com -----END CERTIFICATE-----. O formato PEM é muito comum e é exigido pelo Hub IoT o no upload de alguns certificados.

### <a name="ascii-pem-key"></a>Chave ASCII (PEM)

Contém uma chave DER codificada em Base64 com possivelmente metadados adicionais sobre o algoritmo usado para proteção de senha.

### <a name="pkcs7-certificate"></a>Certificado PKCS nº 7

Um formato projetado para o transporte de dados assinados ou criptografados. Ele é definido pelo [RFC 2315](https://tools.ietf.org/html/rfc2315). Pode incluir toda a cadeia de certificados.

### <a name="pkcs8-key"></a>Chave PKCS nº 8

O formato de um repositório de chaves privadas definido pelo [RFC 5208](https://tools.ietf.org/html/rfc5208).

### <a name="pkcs12-key-and-certificate"></a>Chave e certificado PKCS nº 12

Um formato complexo que pode armazenar e proteger uma chave e toda a cadeia de certificados. Normalmente, é usado com uma extensão .pfx. O PKCS nº 12 é sinônimo do formato PFX.

## <a name="for-more-information"></a>Para obter mais informações

Para mais informações, consulte os seguintes tópicos:

* [O guia do leigo para o jargão do certificado X.509](https://techcommunity.microsoft.com/t5/internet-of-things/the-layman-s-guide-to-x-509-certificate-jargon/ba-p/2203540)
* [Entendimento conceitual de certificados de AC X.509 no setor de IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-concept)

## <a name="next-steps"></a>Próximas etapas

Caso deseje gerar certificados de teste que possam ser usados para autenticar dispositivos no Hub IoT, confira os seguintes tópicos:

* [Como usar scripts fornecidos da Microsoft para criar certificados de teste](tutorial-x509-scripts.md)
* [Como usar o OpenSSL para criar certificados de teste](tutorial-x509-openssl.md)
* [Como usar o OpenSSL para criar certificados de teste autoassinados](tutorial-x509-self-sign.md)

Se você tem um Certificado de Autoridade de Certificação ou um certificado de AC subordinada e deseja carregá-lo no hub IoT e provar que é o proprietário dele, confira [Como comprovar a posse de um Certificado de Autoridade de Certificação](tutorial-x509-prove-possession.md).
