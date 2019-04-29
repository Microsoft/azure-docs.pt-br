---
title: Usar as ferramentas fornecidas nos SDKs do Serviço de Provisionamento de Dispositivos Hub IoT do Azure para simplificar o desenvolvimento
description: Este documento examina as ferramentas fornecidas nos SDKs do Serviço de Provisionamento de Dispositivos Hub IoT do Azure para desenvolvimento
author: yzhong94
ms.author: yizhon
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: arjmands
ms.openlocfilehash: dc8c29b1c7d4e5056cb6aeee6335e32687fd547f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60627301"
---
# <a name="how-to-use-tools-provided-in-the-sdks-to-simplify-development-for-provisioning"></a>Como usar as ferramentas fornecidas nos SDKs para simplificar o desenvolvimento para provisionamento
O Serviço de Provisionamento de Dispositivos no Hub IoT simplifica o processo de [provisionamento automático](concepts-auto-provisioning.md) sem toque e Just-In-Time de uma maneira segura e escalonável.  Um atestado de segurança na forma de um certificado X.509 ou TPM (Trusted Platform Module) é necessário.  A Microsoft também faz parceria com [outros parceiros de hardware de segurança](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) para aumentar a confiança na proteção da implantação de IoT. Entender os requisitos de segurança de hardware pode ser um grande desafio para os desenvolvedores. Um conjunto de SDKs do Serviço de Provisionamento do IoT do Azure são fornecidos, de modo que os desenvolvedores possam usar uma camada de conveniência para escrever clientes que se comunicam com o serviço de provisionamento. Os SDKs também fornecem amostras para cenários comuns, bem como um conjunto de ferramentas para simplificar o atestado de segurança em desenvolvimento.

## <a name="trusted-platform-module-tpm-simulator"></a>Simulador TPM (Trusted Platform Module)
[TPM](https://docs.microsoft.com/azure/iot-dps/concepts-security) pode se referir a um padrão para armazenar chaves com segurança para autenticar a plataforma ou à interface de E/S usada para interagir com os módulos que implementam o padrão. Os TPMs podem existir como hardware discreto, hardware integrado, com base no firmware ou com base em software.  Em produção, o TPM está localizado no dispositivo, como hardware discreto, hardware integrado ou baseado em firmware. Na fase de teste, um simulador TPM baseado em software é fornecido para os desenvolvedores.  No momento, esse simulador está disponível apenas para o desenvolvimento na plataforma Windows.

As etapas para usar o simulador TPM são:
1. [Prepare o ambiente de desenvolvimento](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) e clone o repositório GitHub:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Navegue para a pasta do simulador TPM em ```azure-iot-sdk-java/provisioning/provisioning-tool/tpm-simulator/```.
3. Execute Simulator.exe antes de executar qualquer aplicativo cliente para o dispositivo de provisionamento.
4. Permita que o simulador seja executado em segundo plano durante o processo de provisionamento para obter a ID de registro e a Chave de Endosso.  Ambos os valores são válidos apenas para uma instância da execução.

## <a name="x509-certificate-generator"></a>Gerador de certificado X.509
Os [certificados X.509](https://docs.microsoft.com/azure/iot-dps/concepts-security#x509-certificates) podem ser usados como um mecanismo de atestado para escalar a produção e simplificar o provisionamento de dispositivos.  Há [várias maneiras](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) para obter um certificado X.509:
* Para o ambiente de produção, recomendamos a compra de um certificado de autoridade de certificação X.509 de uma autoridade de certificação raiz pública.
* Para o ambiente de teste, você pode gerar um certificado raiz X.509 ou uma cadeia de certificados X.509 usando:
    * OpenSSL: É possível usar scripts para a geração de certificado:
        * [Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)
        * [PowerShell ou Bash](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)
        
    * Emulador do DICE (mecanismo de composição de identidade de dispositivo): O DICE pode ser usado para a identidade do dispositivo de criptografia e o atestado com base no protocolo TLS e nos certificados de cliente X.509.  [Saiba](https://www.microsoft.com/research/publication/device-identity-dice-riot-keys-certificates/) mais sobre a identidade do dispositivo com o DICE.

### <a name="using-x509-certificate-generator-with-dice-emulator"></a>Usando o gerador de certificado X.509 com o emulador do DICE
Os SDKs fornecem um gerador de certificado X.509 com o emulador do DICE, localizado no [SDK do Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-tools/provisioning-x509-cert-generator).  Esse gerador funciona entre plataformas.  O certificado gerado pode ser usado para desenvolvimento em outras linguagens.

No momento, o emulador DICE gera um certificado raiz, um certificado intermediário, um certificado folha e a chave privada associada.  No entanto, o certificado raiz ou o certificado intermediário não pode ser usado para assinar um certificado folha separado.  Se você pretende testar cenários de registro de grupo em que um certificado de autenticação é usado para assinar os certificados folha de vários dispositivos, use o OpenSSL para produzir uma cadeia de certificados.

Para gerar um certificado X.509 usando esse gerador:
1. [Prepare o ambiente de desenvolvimento](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) e clone o repositório GitHub:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Altere a raiz para azure-iot-sdk-java.
3. Execute ```mvn install -DskipTests=true``` para baixar todos os pacotes necessários e compilar o SDK
4. Navegue para a raiz do Gerador de Certificado X.509 em ```azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator```.
5. Compile com ```mvn clean install```
6. Execute a ferramenta usando os seguintes comandos:
   ```
   cd target
   java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
   ```
7. Quando solicitado, você pode inserir um _Nome Comum_ para seus certificados.
8. A ferramenta gera localmente um **Certificado de Cliente**, a **Chave Privada do Certificado de Cliente**, o **Certificado Intermediário** e o **Certificado Raiz**.

**Certificado de Cliente** é o certificado folha no dispositivo.  **Certificado de Cliente** e a **Chave Privada do Certificado de Cliente** associada são necessários no cliente de dispositivo. Dependendo da linguagem escolhida, o mecanismo para colocar isso em um aplicativo cliente pode ser diferente.  Para obter mais informações, consulte os [Inícios Rápidos](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-x509) sobre como criar um dispositivo simulado usando o X.509.

O certificado raiz ou o intermediário podem ser usados para criar um grupo de registro ou um registro individual [de forma programática](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments-sdks) ou usando o [portal](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments).

## <a name="next-steps"></a>Próximas etapas
* Desenvolver usando o [SDK do IoT do Azure]( https://github.com/Azure/azure-iot-sdks) para o Hub IoT do Azure e o Serviço de Provisionamento de Dispositivos Hub IoT do Azure
