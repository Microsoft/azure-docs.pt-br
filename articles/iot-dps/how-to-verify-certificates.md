---
title: Verificar certificados de autoridade de certificação X. 509 com o serviço de provisionamento de dispositivos no Hub IoT do Azure
description: Como fazer a prova de posse para certificados de AC X. 509 com o serviço de provisionamento de dispositivos do Hub IoT do Azure (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 02/26/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 2020205357c68e007a1a375c4853e8c04afd2019
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98789053"
---
# <a name="how-to-do-proof-of-possession-for-x509-ca-certificates-with-your-device-provisioning-service"></a>Como fazer uma prova de posse para certificados de autoridade de certificação X.509 com o Serviço de Provisionamento de Dispositivos

Um certificado de autoridade de certificação (CA) X.509 verificado é um certificado de autoridade de certificação que foi carregado e registrado para o serviço de provisionamento e passou por uma prova de posse com o serviço. 

A prova de posse envolve as seguintes etapas:
1. Obter um código de verificação exclusivo gerado pelo serviço de provisionamento para seu certificado de autoridade de certificação X.509. Você pode obter isso no Portal do Azure.
2. Criar um certificado de verificação X.509 com o código de verificação como seu assunto e assinar o certificado com a chave privada associada ao seu certificado de autoridade de certificação X.509.
3. Carregar o certificado de verificação assinado para o serviço. O serviço valida o certificado de verificação usando a parte pública do certificado de autoridade de certificação a ser verificado, assim, provando que você está em posse da chave privada do certificado de autoridade de certificação.

Certificados verificados desempenham um papel importante ao usar grupos de registro. Verificar a propriedade do certificado fornece uma camada adicional de segurança, garantindo que o carregador do certificado está em posse da chave privada do certificado. A verificação impede que um ator mal-intencionado espionando seu tráfego extraia de um certificado intermediário e use-o para criar um grupo de registro em seu próprio serviço de provisionamento, sequestrando efetivamente seus dispositivos. Fornecendo propriedade da raiz ou um certificado intermediário em uma cadeia de certificados, você está provando que tem permissão para gerar certificados de folha para os dispositivos que serão registrados como parte desse grupo de registro. Por esse motivo, a raiz ou certificado intermediário configurado em um grupo de registro deve ser um certificado verificado ou um dispositivo presente na autenticação com o serviço deve ser acumulado em certificado verificado na cadeia de certificados. Para saber mais sobre o atestado de certificado X. 509, consulte [certificados x. 509](concepts-x509-attestation.md) e [controlando o acesso do dispositivo ao serviço de provisionamento com certificados x. 509](concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

## <a name="register-the-public-part-of-an-x509-certificate-and-get-a-verification-code"></a>Registrar a parte pública de um certificado X.509 e obter um código de verificação

Para registrar um certificado de autoridade de certificação com o serviço de provisionamento e obter um código de verificação que pode ser usado durante a prova de posse, siga estas etapas. 

1. No portal do Azure, navegue até o serviço de provisionamento e abra **Certificados** no menu à esquerda. 
2. Clique em **Adicionar** para adicionar um novo certificado.
3. Insira um nome de exibição fácil para o certificado. Navegue até o arquivo .cer ou .pem que representa a parte pública do certificado X.509. Clique em **Carregar**.
4. Após receber uma notificação de que o certificado foi carregado com êxito, clique em **Salvar**.

    ![Carregar um certificado](./media/how-to-verify-certificates/add-new-cert.png)  

   Seu certificado será mostrado na lista **Explorador de Certificados**. Observe que o **STATUS** desse certificado é *Não verificado*.

5. Clique no certificado que você adicionou na etapa anterior.

6. Em **Detalhes do Certificado**, clique em **Gerar Código de Verificação**.

7. O serviço de provisionamento cria um **Código de verificação** que pode ser usado para validar a propriedade do certificado. Copie o código para a área de transferência. 

   ![Verificar certificado](./media/how-to-verify-certificates/verify-cert.png)  

## <a name="digitally-sign-the-verification-code-to-create-a-verification-certificate"></a>Assinar digitalmente o código de verificação para criar um certificado de verificação

Agora, você precisa assinar o *Código de verificação* com a chave privada associada ao certificado de autoridade de certificação X.509, que gera uma assinatura. Isso é conhecido como [Prova de posse](https://tools.ietf.org/html/rfc5280#section-3.1) e resulta em um certificado de verificação assinado.

A Microsoft fornece ferramentas e exemplos que podem ajudá-lo a criar um certificado de verificação assinado: 

- O **SDK C do Hub IoT do Azure** fornece scripts de PowerShell (Windows) e Bash (Linux) para ajudá-lo a criar certificados de autoridade de certificação e folha para desenvolver e executar uma prova de posse usando um código de verificação. Você pode fazer o download dos [arquivos](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) relevantes para seu sistema para uma pasta de trabalho e seguir as instruções no [leiame Gerenciar certificados de autoridade de certificação](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) para executar uma prova de posse de um certificado de autoridade de certificação. 
- O **SDK C# do Hub IoT do Azure** contém a [Amostra de Verificação de Certificado do Grupo](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample), que pode ser usado para realizar prova de posse.
 
> [!IMPORTANT]
> Além de realizar uma prova de posse, os scripts de PowerShell e Bash citados anteriormente também permitem que você crie certificados raiz, certificados intermediários e certificados de folha que podem ser usados para autenticar e provisionar dispositivos. Esses certificados devem ser usados somente para desenvolvimento. Eles nunca devem ser usados em um ambiente de produção. 

Os scripts de PowerShell e Bash fornecidos na documentação e SDKs dependem do [OpenSSL](https://www.openssl.org/). Você também pode usar o OpenSSL ou outras ferramentas de terceiros para ajudá-lo a fazer uma prova de posse. Para obter um exemplo usando ferramentas fornecidas com os SDKs, consulte [criar uma cadeia de certificados X. 509](tutorial-custom-hsm-enrollment-group-x509.md#create-an-x509-certificate-chain). 


## <a name="upload-the-signed-verification-certificate"></a>Carregar o certificado de verificação assinado

1. Carregue a assinatura resultante como um certificado de verificação para o seu serviço de provisionamento no portal. Em **Detalhes do certificado**, no portal do Azure, use o ícone _Explorador de arquivos_ próximo ao campo **arquivo .pem ou .cer do Certificado de verificação** para carregar o certificado de verificação assinado do sistema.

2. Depois que o certificado for carregado com êxito, clique em **Verificar**. O **STATUS** do seu certificado muda para **_Verificado_** na lista **Explorador de certificados**. Clique em **Atualizar** se ele não for atualizado automaticamente.

   ![Carregar verificação de certificado](./media/how-to-verify-certificates/upload-cert-verification.png)  

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como usar o portal para criar um grupo de registro, consulte [Gerenciando registros de dispositivos com o portal do Azure](how-to-manage-enrollments.md).
- Para saber mais sobre como usar os SDKs do serviço para criar um grupo de registro, consulte [Gerenciando registros de dispositivos com os SDKs do serviço](how-to-manage-enrollments-sdks.md).










