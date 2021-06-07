---
title: Tutorial – Provar a propriedade de Certificados de Autoridade de Certificação no Hub IoT do Azure | Microsoft Docs
description: Tutorial – Provar sua propriedade de um Certificado de Autoridade de Certificação no Hub IoT do Azure
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
ms.openlocfilehash: b7740fa1f6a54dcfcc1181dddedcdd5fdb50402c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378220"
---
# <a name="tutorial-proving-possession-of-a-ca-certificate"></a>Tutorial: Como provar a posse de um Certificado de Autoridade de Certificação

Depois de carregar seu Certificado de Autoridade de Certificação (AC) raiz ou seu certificado de AC subordinada no Hub IoT, você precisará provar que é o proprietário dele:

1. No portal do Azure, navegue até o Hub IoT e selecione **Configurações > Certificados**.

2. Selecione **Adicionar** para adicionar um novo Certificado de Autoridade de Certificação.

3. Insira um nome de exibição no campo **Nome do Certificado** e selecione o certificado PEM a ser adicionado.

4. Clique em **Salvar**. Seu certificado será mostrado na lista de certificados com o status **Não verificado**. Esse processo de verificação provará que você tem a posse do certificado.

5. Selecione o certificado para ver a caixa de diálogo **Detalhes do Certificado**.

6. Escolha **Gerar Código de Verificação** na caixa de diálogo.

  :::image type="content" source="media/tutorial-x509-prove-possession/certificate-details.png" alt-text="{Caixa de diálogo de detalhes do certificado}":::

7. Copie o código de verificação para a área de transferência. É necessário definir o código de verificação como a entidade do certificado. Por exemplo, se o código de verificação for 75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3, adicione-o como a entidade do certificado, conforme mostrado na próxima etapa.

8. Há três maneiras de gerar um certificado de verificação:

    * Se você estiver usando o script do PowerShell fornecido pela Microsoft, execute `New-CACertsVerificationCert "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` para criar um certificado chamado `VerifyCert4.cer`. Para obter mais informações, confira [Como usar os scripts fornecidos pela Microsoft](tutorial-x509-scripts.md).

    * Se você estiver usando o script do Bash fornecido pela Microsoft, execute `./certGen.sh create_verification_certificate "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` para criar um certificado chamado `verification-code.cert.pem`. Para obter mais informações, confira [Como usar os scripts fornecidos pela Microsoft](tutorial-x509-scripts.md).

    * Se você estiver usando o OpenSSL para gerar os certificados, primeiro, precisará gerar uma chave privada e uma CSR (solicitação de assinatura de certificado):

      ```bash
      $ openssl genpkey -out pop.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048

      $ openssl req -new -key pop.key -out pop.csr

      -----
      Country Name (2 letter code) [XX]:.
      State or Province Name (full name) []:.
      Locality Name (eg, city) [Default City]:.
      Organization Name (eg, company) [Default Company Ltd]:.
      Organizational Unit Name (eg, section) []:.
      Common Name (eg, your name or your server hostname) []:75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3
      Email Address []:

      Please enter the following 'extra' attributes
      to be sent with your certificate request
      A challenge password []:
      An optional company name []:
 
      ```

      Em seguida, crie um certificado usando o arquivo de configuração da AC raiz (mostrado abaixo) ou o arquivo de configuração da AC subordinada e a CSR.

      ```bash
      openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

      ```

    Para obter mais informações, confira [Como usar o OpenSSL para criar certificados de teste](tutorial-x509-openssl.md).

10. Selecione o novo certificado na exibição **Detalhes do Certificado**.

11. Depois que o certificado for carregado, selecione **Verificar**. O status do Certificado de Autoridade de Certificação será alterado para **Verificado**.
