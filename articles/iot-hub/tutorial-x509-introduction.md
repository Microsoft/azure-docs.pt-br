---
title: Tutorial – Noções básicas sobre a criptografia e os certificados X.509 para o Hub IoT do Azure | Microsoft Docs
description: Tutorial – Noções básicas sobre a criptografia e a PKI X.509 para o Hub IoT do Azure
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/25/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 2c375a02f534572826e1ebd6b8549e59f6e83640
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378373"
---
# <a name="tutorial-understanding-public-key-cryptography-and-x509-public-key-infrastructure"></a>Tutorial: Noções básicas sobre a criptografia por chave pública e a infraestrutura de chave pública X.509

Você pode usar certificados X.509 para autenticar dispositivos em um Hub IoT do Azure. Um certificado é um documento digital que contém a chave pública do dispositivo e pode ser usado para verificar se o dispositivo é o que alega ser. Os certificados X.509 e as CRLs (listas de certificados revogados) são documentados pelo [RFC 5280](https://tools.ietf.org/html/rfc5280). Os certificados são apenas uma parte de uma PKI (infraestrutura de chave pública) X.509. Para entender a PKI X.509, você precisa ter noções básicas sobre algoritmos de criptografia, chaves de criptografia, certificados e ACs (autoridades de certificação):

* Os **algoritmos** definem como os dados de texto não criptografado originais são transformados em texto cifrado e novamente em texto não criptografado.
* As **chaves** são cadeias de caracteres de dados aleatórios ou pseudoaleatórios usadas como entrada para um algoritmo.
* Os **certificados** são documentos digitais que contêm a chave pública de uma entidade e permitem que você determine se a entidade do certificado é quem ou o que alega ser.
* As **autoridades de certificação** atestam a autenticidade das entidades de certificado.

Você pode comprar um certificado de uma AC (autoridade de certificação). Você também pode, para teste e desenvolvimento, ou se está trabalhando em um ambiente independente, criar uma AC raiz autoassinada. Se, por exemplo, você tem um ou mais dispositivos e está testando a autenticação do hub IoT, assine automaticamente a AC raiz e use-a para emitir certificados de dispositivo. Você também pode emitir certificados de dispositivo autoassinados. Isso será discutido nos artigos seguintes.

Antes de abordar os certificados X.509 com mais detalhes e como usá-los para autenticar dispositivos em um Hub IoT, discutiremos a criptografia na qual os certificados se baseiam.

## <a name="cryptography"></a>Criptografia

A criptografia é usada para proteger informações e comunicações. Isso normalmente é feito com técnicas de criptografia para embaralhar o texto não criptografado (texto comum) em texto cifrado (texto codificado) e novamente em texto não criptografado. Esse processo de embaralhamento é chamado de criptografia. O processo reverso é chamado de descriptografia. A criptografia se preocupa com os seguintes objetivos:

* **Confidencialidade**: as informações podem ser compreendidas apenas pelo público-alvo.
* **Integridade**: as informações não podem ser alteradas no armazenamento ou em trânsito.
* **Não repúdio**: o criador das informações não pode negar mais tarde essa criação.
* **Autenticação**: o remetente e o destinatário podem confirmar a identidade um do outro.

## <a name="encryption"></a>Criptografia

O processo de criptografia exige um algoritmo e uma chave. O algoritmo define como os dados são transformados de texto não criptografado em texto cifrado e novamente em texto não criptografado. Uma chave é uma cadeia de caracteres de dados aleatórios usada como entrada para o algoritmo. Toda a segurança do processo está contida na chave. Portanto, a chave precisa ser armazenada com segurança. No entanto, os detalhes dos algoritmos mais populares estão disponíveis publicamente.

Existem dois tipos de criptografia. A criptografia simétrica usa a mesma chave para criptografia e descriptografia. A criptografia assimétrica usa chaves diferentes, mas matematicamente relacionadas para executar a criptografia e a descriptografia.

### <a name="symmetric-encryption"></a>Criptografia simétrica

A criptografia simétrica usa a mesma chave para criptografar o texto sem formatação em texto cifrado e descriptografar o texto cifrado novamente em texto sem formatação. O comprimento necessário da chave, expresso em número de bits, é determinado pelo algoritmo. Depois que a chave é usada para criptografar o texto sem formatação, a mensagem criptografada é enviada ao destinatário que descriptografa o texto cifrado. A chave simétrica precisa ser transmitida com segurança ao destinatário. O envio da chave é o maior risco de segurança do uso de um algoritmo simétrico.

![Exemplo de criptografia simétrica](media/tutorial-x509-introduction/symmetric-keys.png)

### <a name="asymmetric-encryption"></a>Criptografia assimétrica

Se apenas a criptografia simétrica é usada, o problema é que todas as partes da comunicação precisam ter a chave privada. No entanto, é possível que terceiros não autorizados capturem a chave durante a transmissão para usuários autorizados. Para resolver esse problema, use a criptografia assimétrica ou por chave pública.

Na criptografia assimétrica, cada usuário tem duas chaves matematicamente relacionadas, chamadas um par de chaves. Uma chave é pública e a outra é privada. O par de chaves garante que apenas o destinatário tenha acesso à chave privada necessária para descriptografar os dados. A ilustração a seguir resume o processo de criptografia assimétrica.

![Exemplo de criptografia assimétrica](media/tutorial-x509-introduction/asymmetric-keys.png)

1. O destinatário cria um par de chaves pública/privada e envia a chave pública para uma AC. A AC empacota a chave pública em um certificado X.509.

1. A parte remetente obtém a chave pública do destinatário da AC.

1. O remetente criptografa dados em texto sem formatação usando um algoritmo de criptografia. A chave pública do destinatário é usada para executar a criptografia.

1. O remetente transmite o texto cifrado para o destinatário. Não é necessário enviar a chave porque o destinatário já tem a chave privada necessária para descriptografar o texto cifrado.

1. O destinatário descriptografa o texto cifrado usando o algoritmo assimétrico especificado e a chave privada.

### <a name="combining-symmetric-and-asymmetric-encryption"></a>Como combinar as criptografias simétrica e assimétrica

As criptografias simétrica e assimétrica podem ser combinadas para aproveitar os respectivos pontos fortes relativos. A criptografia simétrica é muito mais rápida do que a assimétrica, mas, devido à necessidade de enviar chaves privadas para outras partes, não é tão segura. Para combinar os dois tipos, a criptografia simétrica pode ser usada para converter o texto não criptografado em texto cifrado. A criptografia assimétrica é usada para trocar a chave simétrica. Isso é demonstrado pelo diagrama a seguir.

![Criptografia simétrica e assimétrica](media/tutorial-x509-introduction/symmetric-asymmetric-encryption.png)

1. O remetente recupera a chave pública do destinatário.

1. O remetente gera uma chave simétrica e a usa para criptografar os dados originais.

1. Ele usa a chave pública do destinatário para criptografar a chave simétrica.

1. O remetente transmite a chave simétrica criptografada e o texto cifrado para o destinatário pretendido.

1. O destinatário usa a chave privada que corresponde à chave pública do destinatário para descriptografar a chave simétrica do remetente.

1. O destinatário usa a chave simétrica para descriptografar o texto cifrado.

### <a name="asymmetric-signing"></a>Assinatura assimétrica

Os algoritmos assimétricos podem ser usados para proteger os dados contra modificação e provar a identidade do criador dos dados. A ilustração a seguir mostra como a assinatura assimétrica ajuda a provar a identidade do remetente.

![Exemplo de assinatura assimétrica](media/tutorial-x509-introduction/asymmetric-signing.png)

1. O remetente transmite os dados em texto sem formatação por meio de um algoritmo de criptografia assimétrica, usando a chave privada para criptografia. Observe que esse cenário reverte o uso das chaves públicas e privadas descrito na seção anterior que explicou em detalhes a criptografia assimétrica.

1. O texto cifrado resultante é enviado ao destinatário.

1. O destinatário obtém a chave pública do originador de um diretório.

1. O destinatário descriptografa o texto cifrado usando a chave pública do originador. O texto não criptografado resultante comprova a identidade do originador, porque apenas o originador tem acesso à chave privada que criptografou inicialmente o texto original.

## <a name="signing"></a>Assinando

A assinatura digital pode ser usada para determinar se os dados foram modificados em trânsito ou quando inativos. Os dados são transmitidos por meio de um algoritmo de hash, uma função unidirecional que produz um resultado matemático com base na mensagem especificada. O resultado é chamado de *valor de hash*, *resumo da mensagem*, *resumo*, *assinatura* ou *impressão digital*. Um valor de hash não pode ser revertido para obter a mensagem original. Como uma pequena alteração na mensagem resulta em uma alteração significativa na *impressão digital*, o valor de hash pode ser usado para determinar se uma mensagem foi alterada. A ilustração a seguir mostra como algoritmos de criptografia assimétrica e de hash podem ser usados para verificar se uma mensagem não foi modificada.

![Exemplo de assinatura](media/tutorial-x509-introduction/signing.png)

1. O remetente cria uma mensagem de texto não criptografado.

1. Ele executa hash da mensagem de texto não criptografado para criar um resumo da mensagem.

1. O remetente criptografa o resumo usando uma chave privada.

1. Ele transmite a mensagem de texto sem formatação e o resumo criptografado para o destinatário pretendido.

1. O destinatário descriptografa o resumo usando a chave pública do remetente.

1. O destinatário executa o mesmo algoritmo de hash usado pelo remetente na mensagem.

1. O destinatário compara a assinatura resultante com a assinatura descriptografada. Se os resumos forem os mesmos, isso indicará que a mensagem não foi modificada durante a transmissão.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os campos que compõem um certificado, confira [Noções básicas sobre certificados de chave pública X.509](tutorial-x509-certificates.md).

Se você já sabe muito sobre os certificados X.509 e deseja gerar versões de teste que podem ser usadas para se autenticar no Hub IoT, confira os seguintes tópicos:

* [Como usar scripts fornecidos da Microsoft para criar certificados de teste](tutorial-x509-scripts.md)
* [Como usar o OpenSSL para criar certificados de teste](tutorial-x509-openssl.md)
* [Como usar o OpenSSL para criar certificados de teste autoassinados](tutorial-x509-self-sign.md)

Se você tem um Certificado de Autoridade de Certificação ou um certificado de AC subordinada e deseja carregá-lo no hub IoT e provar que é o proprietário dele, confira [Como comprovar a posse de um Certificado de Autoridade de Certificação](tutorial-x509-prove-possession.md).
