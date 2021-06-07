---
title: Tutorial – Usar scripts da Microsoft para criar certificados de teste X.509 para o Hub IoT do Azure | Microsoft Docs
description: Tutorial – Usar scripts personalizados para criar Certificados de Autoridades de Certificação e de dispositivo para o Hub IoT do Azure
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
ms.openlocfilehash: ff4b63f49a87dd9ca6b0ef458bdcf1c285a34a18
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378203"
---
# <a name="tutorial-using-microsoft-supplied-scripts-to-create-test-certificates"></a>Tutorial: Como usar scripts fornecidos pela Microsoft para criar certificados de teste

A Microsoft fornece scripts do PowerShell e do Bash para ajudar você a entender como criar seus certificados X.509 e autenticá-los em um Hub IoT. Os scripts estão localizados em um [repositório](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) GitHub. Eles são fornecidos apenas para fins de demonstração. Os certificados criados por eles não devem ser usados para produção. Eles contêm senhas embutidas em código (“1234”) e expiram após 30 dias. Para um ambiente de produção, você precisará usar suas melhores práticas para a criação de certificados e o gerenciamento do tempo de vida.

## <a name="powershell-scripts"></a>Scripts do PowerShell

### <a name="step-1---setup"></a>Etapa 1 – Instalação

Obter OpenSSL para Windows. Confira <https://www.openssl.org/docs/faq.html#MISC4> para obter locais para baixá-lo ou <https://www.openssl.org/source/> para compilá-lo do código-fonte. Em seguida, execute os scripts preliminares:

1. Copie os scripts deste [repositório](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) GitHub para o diretório local no qual deseja trabalhar. Todos os arquivos serão criados como filhos desse diretório.

1. Inicie o PowerShell como administrador.

1. Altere para o diretório em que carregou os scripts.

1. Na linha de comando, defina a variável de ambiente `$ENV:OPENSSL_CONF` para o diretório no qual o arquivo de configuração do OpenSSL (openssl.cnf) está localizado.

1. Execute `Set-ExecutionPolicy -ExecutionPolicy Unrestricted` para que o PowerShell possa executar os scripts.

1. Execute `. .\ca-certs.ps1`. Isso levará as funções do script para o namespace global do PowerShell.

1. Execute `Test-CACertsPrerequisites`. O PowerShell usa o Repositório de Certificados do Windows para gerenciar os certificados. Esse comando verifica se não haverá colisões de nome posteriormente com os certificados existentes e se o OpenSSL está configurado corretamente.

### <a name="step-2---create-certificates"></a>Etapa 2 – Criar certificados

Execute `New-CACertsCertChain [ecc|rsa]`. O ECC é recomendado para Certificados de Autoridade de Certificação, mas não é necessário. Esse script atualiza o diretório e o Repositório de Certificados do Windows com os seguintes certificados intermediários e da AC:

* intermediate1.pem
* intermediate2.pem
* intermediate3.pem
* RootCA.cer
* RootCA.pem

Depois de executar o script, adicione o novo Certificado de Autoridade de Certificação (RootCA.pem) ao seu Hub IoT:

1. Acesse o Hub IoT e procure Certificados.

1. Selecione **Adicionar**.

1. Insira um nome de exibição para o Certificado de Autoridade de Certificação.

1. Carregue o Certificado de Autoridade de Certificação.

1. Clique em **Salvar**.

### <a name="step-3---prove-possession"></a>Etapa 3 – Provar a posse

Agora que você carregou o Certificado de Autoridade de Certificação no Hub IoT, é necessário provar que você realmente é o proprietário dele:

1. Selecione o novo Certificado de Autoridade de Certificação.

1. Escolha **Gerar Código de Verificação** na caixa de diálogo **Detalhes do Certificado**. Para obter mais informações, confira [Provar a posse de um Certificado de Autoridade de Certificação](tutorial-x509-prove-possession.md).

1. Crie um certificado que contenha o código de verificação. Por exemplo, se o código de verificação for `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"`, execute o comando a seguir para criar um certificado no diretório de trabalho que contém a entidade `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288`. O script cria um certificado chamado `VerifyCert4.cer`.

    `New-CACertsVerificationCert "106A5SD242AF512B3498BD609C4941E66R34H268DDB3288"`

1. Carregue `VerifyCert4.cer` no Hub IoT na caixa de diálogo **Detalhes do Certificado**.

1. Selecione **Verificar**.

### <a name="step-4---create-a-new-device"></a>Etapa 4 – Criar um dispositivo

Crie um dispositivo para o Hub IoT:

1. No Hub IoT, procure a seção **Dispositivos IoT**.

1. Adicione um novo dispositivo com a ID `mydevice`.

1. Para autenticação, escolha **AC de X.509 Assinado**.

1. Execute `New-CACertsDevice mydevice` para criar um certificado de dispositivo. Isso criará os seguintes arquivos no diretório de trabalho:

   * `mydevice.pfx`
   * `mydevice-all.pem`
   * `mydevice-private.pem`
   * `mydevice-public.pem`

### <a name="step-5---test-your-device-certificate"></a>Etapa 5 – Testar o certificado de dispositivo

Acesse [Como testar a autenticação de certificado](tutorial-x509-test-certificate.md) para determinar se o certificado de dispositivo pode se autenticar no Hub IoT. Você precisará da versão PFX do certificado, `mydevice.pfx`.

### <a name="step-6---cleanup"></a>Etapa 6 – Limpeza

No menu Iniciar, abra **Gerenciar Certificados de Computador** e procure **Certificados – Computador Local > pessoal**. Remova os certificados emitidos pela "AC somente de teste da IoT do Azure*". Da mesma forma, remova os certificados apropriados de **> Autoridade de Certificação Raiz Confiável > Certificados e > Autoridades de Certificação Intermediárias > Certificados**.

## <a name="bash-scripts"></a>Scripts Bash

### <a name="step-1---setup"></a>Etapa 1 – Instalação

1. Inicie o Bash.

1. Altere para o diretório no qual deseja trabalhar. Todos os arquivos serão criados neste diretório.

1. Copie `*.cnf` e `*.sh` para o diretório de trabalho.

### <a name="step-2---create-certificates"></a>Etapa 2 – Criar certificados

1. Execute `./certGen.sh create_root_and_intermediate`. Isso criará os seguintes arquivos no diretório **certs**:

    * azure-iot-test-only.chain.ca.cert.pem
    * azure-iot-test-only.intermediate.cert.pem
    * azure-iot-test-only.root.ca.cert.pem

1. Acesse o Hub IoT e procure **Certificados**.

1. Selecione **Adicionar**.

1. Insira um nome de exibição para o Certificado de Autoridade de Certificação.

1. Carregue somente o Certificado de Autoridade de Certificação no Hub IoT. O nome do certificado é `./certs/azure-iot-test-only.root.ca.cert.pem.`

1. Clique em **Salvar**.

### <a name="step-3---prove-possession"></a>Etapa 3 – Provar a posse

1. Selecione o novo Certificado de Autoridade de Certificação criado na etapa anterior.

1. Escolha **Gerar Código de Verificação** na caixa de diálogo **Detalhes do Certificado**. Para obter mais informações, confira [Provar a posse de um Certificado de Autoridade de Certificação](tutorial-x509-prove-possession.md).

1. Crie um certificado que contenha o código de verificação. Por exemplo, se o código de verificação for `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"`, execute o comando a seguir para criar um certificado no diretório de trabalho chamado `verification-code.cert.pem` que contém a entidade `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288`.

    `./certGen.sh create_verification_certificate "106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"`

1. Carregue o certificado no hub IoT na caixa de diálogo **Detalhes do Certificado**.

1. Selecione **Verificar**.

### <a name="step-4---create-a-new-device"></a>Etapa 4 – Criar um dispositivo

Crie um dispositivo para o hub IoT:

1. No Hub IoT, procure a seção Dispositivos IoT.

1. Adicione um novo dispositivo com a ID `mydevice`.

1. Para autenticação, escolha **AC de X.509 Assinado**.

1. Execute `./certGen.sh create_device_certificate mydevice` para criar um certificado de dispositivo. Isso criará dois arquivos chamados `new-device.cert.pem` e `new-device.cert.pfx` no diretório de trabalho.

### <a name="step-5---test-your-device-certificate"></a>Etapa 5 – Testar o certificado de dispositivo

Acesse [Como testar a autenticação de certificado](tutorial-x509-test-certificate.md) para determinar se o certificado de dispositivo pode se autenticar no Hub IoT. Você precisará da versão PFX do certificado, `new-device.cert.pfx`.

### <a name="step-6---cleanup"></a>Etapa 6 – Limpeza

Como o script Bash simplesmente cria certificados no diretório de trabalho, basta excluí-los quando você terminar o teste.

## <a name="next-steps"></a>Próximas etapas

Para testar seu certificado, Acesse [Como testar a autenticação de certificado](tutorial-x509-test-certificate.md) para determinar se o certificado pode autenticar seu dispositivo no Hub IoT.
