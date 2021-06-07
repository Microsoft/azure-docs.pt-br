---
title: Tutorial – Usar o OpenSSL para criar certificados autoassinados para o Hub IoT do Azure | Microsoft Docs
description: Tutorial – Usar o OpenSSL para criar certificados X.509 autoassinados para o Hub IoT do Azure
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
ms.openlocfilehash: 982e402946cbd71d946bc1e316cef99621c536a3
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378186"
---
# <a name="tutorial-using-openssl-to-create-self-signed-certificates"></a>Tutorial: Como usar o OpenSSL para criar certificados autoassinados

Você pode autenticar um dispositivo no Hub IoT usando dois certificados de dispositivo autoassinados. Às vezes, isso é chamado de autenticação de impressão digital, porque os certificados contêm impressões digitais (valores de hash) que você envia para o hub IoT. As etapas a seguir descrevem como criar dois certificados autoassinados.

## <a name="step-1---create-a-key-for-the-first-certificate"></a>Etapa 1 – Criar uma chave para o primeiro certificado

```bash
openssl genpkey -out device1.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

## <a name="step-2---create-a-csr-for-the-first-certificate"></a>Etapa 2 – Criar uma CSR para o primeiro certificado

Lembre-se de especificar a identificação do dispositivo quando solicitado.

```bash
openssl req -new -key device1.key -out device1.csr

Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server hostname) []:{your-device-id}
Email Address []:

```

## <a name="step-3---check-the-csr"></a>Etapa 3 – Verificar a CSR

```bash
openssl req -text -in device1.csr -noout
```

## <a name="step-4---self-sign-certificate-1"></a>Etapa 4 – Autoassinar o certificado 1

```bash
openssl x509 -req -days 365 -in device1.csr -signkey device1.key -out device.crt
```

## <a name="step-5---create-a-key-for-certificate-2"></a>Etapa 5 – Criar uma chave para o certificado 2

Quando solicitado, especifique a mesma identificação do dispositivo que você usou para o certificado 1.

```bash
openssl req -new -key device2.key -out device2.csr

Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server hostname) []:{your-device-id}
Email Address []:

```

## <a name="step-6---self-sign-certificate-2"></a>Etapa 6 – Autoassinar o certificado 2

```bash
openssl x509 -req -days 365 -in device2.csr -signkey device2.key -out device2.crt
```

## <a name="step-7---retrieve-the-thumbprint-for-certificate-1"></a>Etapa 7 – Recuperar a impressão digital do certificado 1

```bash
openssl x509 -in device.crt -text -fingerprint
```

## <a name="step-8---retrieve-the-thumbprint-for-certificate-2"></a>Etapa 8 – Recuperar a impressão digital do certificado 2

```bash
openssl x509 -in device2.crt -text -fingerprint
```

## <a name="step-9---create-a-new-iot-device"></a>Etapa 9 – Criar um dispositivo IoT

Navegue até o Hub IoT no portal do Azure e crie uma identidade de dispositivo IoT com as seguintes características:

* Forneça a **Identificação do Dispositivo** que corresponda ao nome da entidade dos dois certificados.
* Selecione **X.509 Autoassinado** como o tipo de autenticação.
* Cole as impressões digitais de cadeias de caracteres hexa que você copiou dos certificados primários e secundários do dispositivo. Verifique se as cadeias de caracteres hexa não têm delimitadores de dois-pontos.

## <a name="next-steps"></a>Próximas etapas

Acesse [Como testar a autenticação de certificado](tutorial-x509-test-certificate.md) para determinar se o certificado pode autenticar seu dispositivo no Hub IoT.
