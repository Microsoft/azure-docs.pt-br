---
title: Proteger a comunicação de dispositivos OPC UA com o cofre OPC – Azure | Microsoft Docs
description: Como registrar aplicativos OPC UA e como emitir certificados de aplicativo assinados para seus dispositivos OPC UA com o cofre OPC.
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: a1b7564988c8a4d63a37b53d18ed3a7359e65d72
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105645970"
---
# <a name="use-the-opc-vault-certificate-management-service"></a>Usar o serviço de gerenciamento de certificados do cofre OPC

> [!IMPORTANT]
> Enquanto atualizamos este artigo, confira [IoT Industrial do Azure](https://azure.github.io/Industrial-IoT/) para obter o conteúdo mais atualizado.

Este artigo explica como registrar aplicativos e como emitir certificados de aplicativo assinados para seus dispositivos OPC UA.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="deploy-the-certificate-management-service"></a>Implantar o serviço de gerenciamento de certificados

Primeiro, implante o serviço na nuvem do Azure. Para obter detalhes, consulte [implantar o serviço de gerenciamento de certificados do cofre do OPC](howto-opc-vault-deploy.md).

### <a name="create-the-issuer-ca-certificate"></a>Criar o certificado de autoridade de certificação do emissor

Se você ainda não tiver feito isso, crie o certificado de autoridade de certificação do emissor. Para obter detalhes, consulte [criar e gerenciar o certificado do emissor para o cofre OPC](howto-opc-vault-manage.md).

## <a name="secure-opc-ua-applications"></a>Proteger aplicativos OPC UA

### <a name="step-1-register-your-opc-ua-application"></a>Etapa 1: registrar seu aplicativo OPC UA 

> [!IMPORTANT]
> A função de gravador é necessária para registrar um aplicativo.

1. Abra seu serviço de certificado em `https://myResourceGroup-app.azurewebsites.net` e entre.
2. Vá para **registrar novo**. Para um registro de aplicativo, um usuário precisa ter pelo menos a função de gravador atribuída.
2. O formulário de entrada segue as convenções de nomenclatura no OPC UA. Por exemplo, na captura de tela a seguir, as configurações para o exemplo de [servidor de referência do OPC UA](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/Applications/ReferenceServer) na pilha de .net standard do OPC UA são mostradas:

   ![Captura de tela do registro do servidor de referência do UA](media/howto-opc-vault-secure/reference-server-registration.png "Registro do servidor de referência do UA")

5. Selecione **registrar** para registrar o aplicativo no banco de dados do aplicativo do serviço de certificado. O fluxo de trabalho orienta diretamente o usuário para a próxima etapa para solicitar um certificado assinado para o aplicativo.

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>Etapa 2: proteger seu aplicativo com um certificado de aplicativo assinado pela AC

Proteja seu aplicativo OPC UA emitindo um certificado assinado com base em uma solicitação de assinatura de certificado (CSR). Como alternativa, você pode solicitar um novo par de chaves, que inclui uma nova chave privada no formato PFX ou PEM. Para obter informações sobre qual método tem suporte para seu aplicativo, consulte a documentação do seu dispositivo OPC UA. Em geral, o método CSR é recomendado, pois ele não requer que uma chave privada seja transferida por uma transmissão.

#### <a name="request-a-new-certificate-with-a-new-keypair"></a>Solicitar um novo certificado com um novo par de chaves

1. Vá para **aplicativos**.
3. Selecione **nova solicitação** para um aplicativo listado.

   ![Captura de tela de solicitação de novo certificado](media/howto-opc-vault-secure/request-new-certificate.png "Solicitar novo certificado")

3. Selecione **solicitar novo par de chaves e certificado** para solicitar uma chave privada e um novo certificado assinado com a chave pública para seu aplicativo.

   ![Captura de tela de gerar um novo par de chaves e certificado](media/howto-opc-vault-secure/generate-new-key-pair.png "Gerar novo par de chaves")

4. Preencha o formulário com um assunto e os nomes de domínio. Para a chave privada, escolha PEM ou PFX com senha. Selecione **gerar novo par de chaves** para criar a solicitação de certificado.

   ![Captura de tela que mostra as telas exibir detalhes da solicitação de certificado e o botão gerar novo par de chaves.](media/howto-opc-vault-secure/approve-reject.png "Aprovar certificado")

5. A aprovação exige um usuário com a função de aprovador e com permissões de assinatura no Azure Key Vault. No fluxo de trabalho típico, as funções aprovador e solicitante devem ser atribuídas a diferentes usuários. Selecione **aprovar** ou **rejeitar** para iniciar ou cancelar a criação real do par de chaves e a operação de assinatura. O novo par de chaves é criado e armazenado com segurança em Azure Key Vault, até ser baixado pelo solicitante do certificado. O certificado resultante com a chave pública é assinado pela autoridade de certificação. Essas operações podem levar alguns segundos para serem concluídas.

   ![Captura de tela de exibir detalhes da solicitação de certificado, com mensagem de aprovação na parte inferior](media/howto-opc-vault-secure/view-key-pair.png "Exibir par de chaves")

7. A chave privada resultante (PFX ou PEM) e o certificado (DER) podem ser baixados aqui no formato selecionado como download de arquivo binário. Uma versão codificada em base64 também está disponível, por exemplo, para copiar e colar o certificado em uma linha de comando ou entrada de texto. 
8. Depois que a chave privada for baixada e armazenada com segurança, você poderá selecionar **Excluir chave privada**. O certificado com a chave pública permanece disponível para uso futuro.
9. Devido ao uso de um certificado assinado por uma autoridade de certificação, o certificado de CA e a CRL (lista de certificados revogados) também devem ser baixados aqui.

Agora, ele depende do dispositivo OPC UA como aplicar o novo par de chaves. Normalmente, o certificado de autoridade de certificação e a CRL são copiados para uma `trusted` pasta, enquanto as chaves pública e privada do certificado de aplicativo são aplicadas a uma `own` pasta no repositório de certificados. Alguns dispositivos podem já dar suporte ao push do servidor para atualizações de certificado. Consulte a documentação do seu dispositivo OPC UA.

#### <a name="request-a-new-certificate-with-a-csr"></a>Solicitar um novo certificado com um CSR 

1. Vá para **aplicativos**.
3. Selecione **nova solicitação** para um aplicativo listado.

   ![Captura de tela de solicitação de novo certificado](media/howto-opc-vault-secure/request-new-certificate.png "Solicitar novo certificado")

3. Selecione **solicitar novo certificado com solicitação de assinatura** para solicitar um novo certificado assinado para seu aplicativo.

   ![Captura de tela de gerar um novo certificado](media/howto-opc-vault-secure/generate-new-certificate.png "Gerar novo certificado")

4. Carregue o CSR selecionando um arquivo local ou colando um CSR codificado em base64 no formulário. Selecione **gerar novo certificado**.

   ![Captura de tela de exibir detalhes da solicitação de certificado](media/howto-opc-vault-secure/approve-reject-csr.png "Aprovar CSR")

5. A aprovação exige um usuário com a função de aprovador e com permissões de assinatura no Azure Key Vault. Selecione **aprovar** ou **rejeitar** para iniciar ou cancelar a operação de assinatura real. O certificado resultante com a chave pública é assinado pela autoridade de certificação. Essa operação pode levar alguns segundos para ser concluída.

   ![Captura de tela que mostra a exibição dos detalhes da solicitação de certificado e inclui uma mensagem de aprovação na parte inferior.](media/howto-opc-vault-secure/view-cert-csr.png "Exibir Certificado")

6. O certificado resultante (DER) pode ser baixado aqui como arquivo binário. Uma versão codificada em base64 também está disponível, por exemplo, para copiar e colar o certificado em uma linha de comando ou entrada de texto. 
10. Depois que o certificado for baixado e armazenado com segurança, você poderá selecionar **excluir certificado**.
11. Devido ao uso de um certificado assinado por uma autoridade de certificação, o certificado de CA e a CRL devem ser baixados aqui também.

Agora, isso depende do dispositivo OPC UA como aplicar o novo certificado. Normalmente, o certificado de autoridade de certificação e a CRL são copiados para uma `trusted` pasta, ao passo que ele é aplicado a uma `own` pasta no repositório de certificados. Alguns dispositivos podem já dar suporte ao push do servidor para atualizações de certificado. Consulte a documentação do seu dispositivo OPC UA.

### <a name="step-3-device-secured"></a>Etapa 3: dispositivo protegido

O dispositivo OPC UA agora está pronto para se comunicar com outros dispositivos OPC UA protegidos por certificados assinados por CA, sem configuração adicional.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a proteger os dispositivos OPC UA, você pode:

> [!div class="nextstepaction"]
> [Executar um serviço de gerenciamento de certificados seguro](howto-opc-vault-secure-ca.md)
