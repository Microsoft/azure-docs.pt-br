---
title: Visão geral da segurança de AC X.509 do Hub IoT do Azure | Microsoft Docs
description: Visão geral – como autenticar dispositivos no Hub IoT usando autoridades de certificação X.509.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: b7464e5cc052ecade4a10102de947d37a63c962a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615027"
---
# <a name="device-authentication-using-x509-ca-certificates"></a>Autenticação de dispositivo usando certificados de AC X.509

Este artigo descreve como usar certificados de autoridade de certificação (AC) X.509 para autenticar dispositivos conectados ao Hub IoT.  Neste artigo, você aprenderá:

* Como adquirir um certificado de autoridade de certificação X.509
* Como registrar seu certificado de autoridade de certificação X.509 no Hub IoT
* Como assinar dispositivos usando certificados de AC X.509
* Como são autenticados os dispositivos assinados com a AC X.509

## <a name="overview"></a>Visão geral

O recurso de AC X.509 permite a autenticação de dispositivo no Hub IoT usando uma autoridade de certificação (AC). Ele simplifica muito o processo de registro de dispositivo inicial e logística de cadeia de fornecimento durante a produção do dispositivo. [Saiba mais neste artigo de cenário sobre o valor do uso de certificados de AC X.509](iot-hub-x509ca-concept.md) para autenticação de dispositivo.  Recomendamos que você leia este artigo de cenário antes de prosseguir, pois ele explica porque as etapas a seguir existem.

## <a name="prerequisite"></a>Pré-requisito

Usar o recurso de AC X.509 exige que você tenha uma conta de Hub IoT.  [Saiba como criar uma instância de Hub IoT](quickstart-send-telemetry-dotnet.md) caso já não tenha uma.

## <a name="how-to-get-an-x509-ca-certificate"></a>Como adquirir um certificado de autoridade de certificação X.509

O certificado de autoridade de certificação X.509 está no topo da cadeia de certificados para cada um de seus dispositivos.  Você pode adquirir ou criar um dependendo de como pretende usá-lo.

Para o ambiente de produção, recomendamos que você adquira um certificado de autoridade de certificação X.509 de uma autoridade de certificação raiz pública. Adquirir um Certificado de Autoridade de Certificação tem a vantagem de ter uma AC raiz atuando como um terceiro confiável para garantir a legitimidade de seus dispositivos. Considere esta opção se quiser que seus dispositivos sejam parte de uma rede de IoT aberta na qual devem interagir com produtos ou serviços de terceiros.

Você também pode criar uma AC X.509 autoassinada para experimentação ou para uso em redes de IoT fechadas.

Independentemente de como você obtém o certificado de autoridade de certificação X.509, certifique-se de manter sua chave privada correspondente secreta e protegida o tempo todo.  Isso é necessário para a criação de relação de confiança na autenticação da AC X.509.

Saiba como [criar um certificado de autoridade de certificação autoassinado](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md), que você pode usar para experimentação em toda esta descrição do recurso.

## <a name="sign-devices-into-the-certificate-chain-of-trust"></a>Inscrever dispositivos em uma cadeia de certificados de confiança

O proprietário de um certificado de autoridade de certificação X.509 pode assinar criptograficamente uma AC intermediária, que, por sua vez, pode assinar outra AC intermediária e assim por diante, até a última autoridade de AC encerrar esse processo assinando um dispositivo. O resultado é uma cadeia em cascata de certificados conhecida como uma cadeia de certificados de confiança. Na vida real, isso funciona como a delegação de confiança para assinar dispositivos. Essa delegação é importante porque estabelece uma cadeia de custódia criptograficamente variável e evita o compartilhamento de chaves de assinatura.

![img-generic-cert-chain-of-trust](./media/generic-cert-chain-of-trust.png)

O certificado do dispositivo (também chamado de um certificado de folha) deve ter o *nome da entidade* definido como o **ID do dispositivo** que foi usada ao registrar o dispositivo de IoT no IoT Hub do Azure. Essa configuração é necessária para autenticação.

Saiba mais aqui como [criar uma cadeia de certificados](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) como feito durante a assinatura de dispositivos.

## <a name="how-to-register-the-x509-ca-certificate-to-iot-hub"></a>Como registrar seu certificado de autoridade de certificação X.509 no Hub IoT

Registre seu certificado de autoridade de certificação X. 509 no Hub IoT no qual ele será usado para autenticar seus dispositivos durante o registro e a conexão.  Registrar o Certificado de Autoridade de Certificação X.509 é um processo de duas etapas composto pelo upload do arquivo do certificado e prova de posse.

O processo de upload implica o carregamento de um arquivo que contém o certificado.  Esse arquivo nunca deve conter nenhuma chave privada.

A etapa de prova de posse envolve um processo de desafio criptográfico e processo de resposta entre você e o Hub IoT.  Considerando que o conteúdo de certificados digitais é público e, portanto, suscetível a espionagem, o Hub IoT verifica se você realmente tem o certificado de autoridade de certificação.  Ele faz isso gerando um desafio aleatório que você deve assinar com a chave privada correspondente do certificado de autoridade de certificação.  Se você manteve a chave privada protegida e secreta como recomendado anteriormente, então, somente você terá o conhecimento para concluir esta etapa. A confidencialidade de chaves privadas é a fonte de confiança nesse método.  Depois de assinar o desafio, conclua esta etapa carregando um arquivo que contém os resultados.

Saiba aqui como [registrar seu certificado de autoridade de certificação](iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub)

## <a name="how-to-create-a-device-on-iot-hub"></a>Como criar um dispositivo no Hub IoT

Para impedir a representação do dispositivo, o Hub IoT exige que você o informe quais dispositivos esperar.  Você pode fazer isso criando uma entrada de dispositivo no registro de dispositivos do Hub IoT.  Este processo é automatizado ao usar o [Serviço de provisionamento do dispositivo](https://azure.microsoft.com/blog/azure-iot-hub-device-provisioning-service-preview-automates-device-connection-configuration/) do Hub IoT. 

Saiba mais aqui como [criar manualmente um dispositivo no Hub IoT](iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub).

Criar um dispositivo X.509 para o Hub IoT

## <a name="authenticating-devices-signed-with-x509-ca-certificates"></a>Autenticação de dispositivos assinados com certificados de AC X.509

Com o certificado de autoridade de certificação X.509 registrado e os dispositivos assinados em uma cadeia de certificados de confiança, o que sobra é a autenticação do dispositivo quando o dispositivo se conectar, até mesmo pela primeira vez.  Quando um dispositivo assinado pela AC X.509 se conecta, ele carrega sua cadeia de certificados para validação. A cadeia inclui todos os certificados de AC e de dispositivo intermediários.  Com essas informações, o Hub IoT autentica o dispositivo em um processo de duas etapas.  O Hub IoT valida criptograficamente a cadeia de certificados para consistência interna e, em seguida, emite um desafio de prova de posse para o dispositivo.  O Hub IoT declara o dispositivo como autêntico em uma resposta bem-sucedida de prova de posse do dispositivo.  Esta declaração pressupõe que a chave privada do dispositivo é protegida e que somente o dispositivo pode responder a esse desafio.  Recomendamos o uso de chips seguros como HSM (Módulos Seguros de Hardware) em dispositivos para proteger as chaves privadas.

Uma conexão bem-sucedida do dispositivo com o Hub IoT conclui o processo de autenticação e também é uma indicação de uma configuração apropriada.

Saiba mais aqui como [concluir essa etapa de conexão do dispositivo](iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [o valor da autenticação da AC X.509](iot-hub-x509ca-concept.md) em IoT.

Introdução ao [Serviço de Provisionamento de Dispositivos](https://docs.microsoft.com/azure/iot-dps/) no Hub IoT.
