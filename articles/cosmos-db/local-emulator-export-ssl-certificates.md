---
title: Exportar os certificados do Emulador do Azure Cosmos DB
description: Saiba como exportar o certificado do emulador de Azure Cosmos DB para uso com aplicativos Java, Python e Node.js. Os certificados devem ser exportados e usados para linguagens e ambientes de tempo de execução que não usam o repositório de certificados do Windows.
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/17/2020
author: deborahc
ms.author: dech
ms.custom: devx-track-python, devx-track-java, contperf-fy21q1
ms.openlocfilehash: 6c144f33f32422e27916e1987e0b2e8693f97945
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656437"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs-apps"></a>Exportar os certificados do emulador Azure Cosmos DB para uso com aplicativos Java, Python e Node.js
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O Emulador do Azure Cosmos DB fornece um ambiente local que emula o serviço Azure Cosmos DB para fins de desenvolvimento. O emulador de Azure Cosmos DB dá suporte apenas à comunicação segura por meio de conexões TLS.

Os certificados no emulador local Azure Cosmos DB são gerados na primeira vez em que você executa o emulador. Há dois certificados. Um deles é usado para se conectar ao emulador local e o outro é usado para gerenciar a criptografia padrão dos dados do emulador no emulador. O certificado que você quer exportar é o certificado de conexão com o nome amigável "DocumentDBEmulatorCertificate".

Ao usar o emulador para desenvolver aplicativos em diferentes idiomas, como Java, Python ou Node.js, você precisa exportar o certificado do emulador e importá-lo para o repositório de certificados necessário.

A linguagem e o tempo de execução do .NET usam o repositório de certificados do Windows para se conectar com segurança ao emulador local do Azure Cosmos DB quando o aplicativo é executado em um host do sistema operacional Windows. Outras linguagens têm seu próprio método de gerenciar e usar certificados. Por exemplo, o Java usa seu próprio [repositório de certificados](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), o Python usa os [invólucros de soquete](https://docs.python.org/2/library/ssl.html)e Node.js usa [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

Este artigo demonstra como exportar os certificados TLS/SSL para uso em diferentes idiomas e ambientes de tempo de execução que não se integram ao repositório de certificados do Windows. Leia mais sobre o emulador em [Usar o Emulador do Azure Cosmos DB para desenvolvimento e teste](./local-emulator.md).

## <a name="export-the-azure-cosmos-db-tlsssl-certificate"></a><a id="export-emulator-certificate"></a>Exportar o certificado Azure Cosmos DB TLS/SSL

Você precisa exportar o certificado do emulador para usar com êxito o ponto de extremidade do emulador de idiomas e ambientes de tempo de execução que não se integram ao repositório de certificados do Windows. Você pode exportar o certificado usando o Gerenciador de certificados do Windows. Use as instruções passo a passo a seguir para exportar o certificado "DocumentDBEmulatorCertificate" como um arquivo X. 509 (. cer) codificado em BASE 64:

1. Inicie o Gerenciador de certificados do Windows executando certlm. msc e navegue até a pasta certificados >pessoal e abra o certificado com o nome amigável **DocumentDbEmulatorCertificate**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png" alt-text="Etapa 1 de exportação do emulador local do Azure Cosmos DB":::

1. Clique em **Detalhes** e depois em **OK**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png" alt-text="Etapa 2 de exportação do emulador local do Azure Cosmos DB":::

1. Clique em **Copiar para arquivo...**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png" alt-text="Etapa 3 de exportação do emulador local do Azure Cosmos DB":::

1. Clique em **Avançar**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png" alt-text="Etapa 4 de exportação do emulador local do Azure Cosmos DB":::

1. Clique em **Não, não exportar a chave privada** e, em seguida, clique em **Avançar**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png" alt-text="Etapa 5 de exportação do emulador local do Azure Cosmos DB":::

1. Clique em **X.509 codificado em Base-64 (.CER)** e depois em **Avançar**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png" alt-text="Etapa 6 de exportação do emulador local do Azure Cosmos DB":::

1. Dê um nome ao certificado. Nesse caso, **documentdbemulatorcert** e, em seguida, clique em **Avançar**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png" alt-text="Etapa 7 de exportação do emulador local do Azure Cosmos DB":::

1. Clique em **Concluir**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png" alt-text="Etapa 8 de exportação do emulador local do Azure Cosmos DB":::

## <a name="use-the-certificate-with-java-apps"></a>Usar o certificado com aplicativos Java

Ao executar aplicativos Java ou aplicativos MongoDB que usam um cliente baseado em Java, é mais fácil instalar o certificado no repositório de certificados Java padrão do que passar os `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"` sinalizadores. Por exemplo, o aplicativo de demonstração Java incluído ( `https://localhost:8081/_explorer/index.html` ) depende do repositório de certificados padrão.

Siga as instruções em [adicionando um certificado ao repositório de certificados Java](/azure/developer/java/sdk/java-sdk-add-certificate-ca-store) para importar o certificado X. 509 para o repositório de certificados Java padrão. Tenha em mente que você trabalhará no diretório *% JAVA_HOME%* ao executar a ferramenta de keytool. Depois que o certificado for importado para o repositório de certificados, os clientes para SQL e a API do Azure Cosmos DB para MongoDB poderão se conectar ao emulador de Azure Cosmos DB.

Como alternativa, você pode executar o script bash a seguir para importar o certificado:

```bash
#!/bin/bash

# If emulator was started with /AllowNetworkAccess, replace the below with the actual IP address of it:
EMULATOR_HOST=localhost
EMULATOR_PORT=8081
EMULATOR_CERT_PATH=/tmp/cosmos_emulator.cert
openssl s_client -connect ${EMULATOR_HOST}:${EMULATOR_PORT} </dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > $EMULATOR_CERT_PATH
# delete the cert if already exists
sudo $JAVA_HOME/bin/keytool -cacerts -delete -alias cosmos_emulator
# import the cert
sudo $JAVA_HOME/bin/keytool -cacerts -importcert -alias cosmos_emulator -file $EMULATOR_CERT_PATH
```

Depois que o certificado TLS/SSL "CosmosDBEmulatorCertificate" estiver instalado, seu aplicativo deverá ser capaz de se conectar e usar o emulador de Azure Cosmos DB local. Se você tiver problemas, poderá seguir o artigo [depuração de conexões SSL/TLS](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) . Na maioria dos casos, o certificado pode não ser instalado no repositório *% JAVA_HOME%/jre/lib/security/cacerts* . Por exemplo, se você tiver várias versões instaladas do Java, seu aplicativo poderá estar usando um repositório cacerts diferente daquele que você atualizou.

## <a name="use-the-certificate-with-python-apps"></a>Usar o certificado com aplicativos Python

Ao se conectar ao emulador de aplicativos Python, a verificação de TLS é desabilitada. Por padrão, o [SDK do Python (versão 2.0.0 ou superior)](sql-api-sdk-python.md) para a API do SQL não tentará usar o certificado TLS/SSL ao se conectar ao emulador local. Se, no entanto, você quiser usar a validação de TLS, poderá seguir os exemplos na documentação de [wrappers de soquete Python](https://docs.python.org/2/library/ssl.html) .

## <a name="how-to-use-the-certificate-in-nodejs"></a>Como usar o certificado no Node.js

Ao se conectar ao emulador do Node.js SDKs, a verificação de TLS é desabilitada. Por padrão, o [ SDK doNode.js (versão 1.10.1 ou superior)](sql-api-sdk-node.md) para a API do SQL não tentará usar o certificado TLS/SSL ao se conectar ao emulador local. Se, no entanto, você quiser usar a validação de TLS, poderá seguir os exemplos na [ documentação doNode.js](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="rotate-emulator-certificates"></a>Girar certificados do emulador

Você pode forçar a regeneração dos certificados do emulador selecionando **Redefinir dados** do emulador Azure Cosmos DB em execução na bandeja do Windows. Observe que essa ação também apagará todos os dados armazenados localmente pelo emulador.

:::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png" alt-text="Redefinição de dados do emulador local do Azure Cosmos DB":::

Se você instalou o certificado no repositório de certificados Java ou os usou em outro lugar, será necessário importá-los novamente usando os certificados atuais. Seu aplicativo não pode se conectar ao emulador local até que você atualize os certificados.

## <a name="next-steps"></a>Próximas etapas

* [Usar parâmetros de linha de comando e comandos do PowerShell para controlar o emulador](emulator-command-line-parameters.md)
* [Problemas de depuração com o emulador](troubleshoot-local-emulator.md)
