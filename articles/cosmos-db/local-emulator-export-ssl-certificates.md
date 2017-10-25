---
title: Exportar os certificados do Emulador do Azure Cosmos DB | Microsoft Docs
description: "Ao desenvolver em linguagens e em tempos de execução que não usam o Repositório de Certificados do Windows, você precisará exportar e gerenciar os certificados SSL. Esta postagem fornece instruções passo a passo."
services: cosmos-db
documentationcenter: 
keywords: Emulador do Azure Cosmos DB
author: voellm
manager: jhubbard
editor: 
ms.assetid: ef43deda-c2e9-4193-99e2-7f6a88a0319f
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2017
ms.author: tvoellm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bafbd43d65a0f46f87a13cea4a2a6299d3bf43ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs"></a>Exportar os certificados do Emulador do Azure Cosmos DB para uso com Java, Python e Node.js

[**Baixar o Emulador**](https://aka.ms/cosmosdb-emulator)

O Emulador do Azure Cosmos DB fornece um ambiente local que emula o serviço Azure Cosmos DB para fins de desenvolvimento, incluindo seu uso de conexões SSL. Esta postagem demonstra como exportar os certificados SSL para uso em linguagens e em tempos de execução que não são integrados ao Repositório de Certificados do Windows, por exemplo, Java, que usa seu próprio [repositório de certificados](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) e Python, que usa [wrappers de soquete](https://docs.python.org/2/library/ssl.html) e .Node.js que usa [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback). Leia mais sobre o emulador em [Usar o Emulador do Azure Cosmos DB para desenvolvimento e teste](./local-emulator.md).

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Rotação de certificados
> * Exportando o certificado SSL
> * Aprendendo a usar o certificado no Java, Python e Node.js

## <a name="certification-rotation"></a>Rotação de certificação

Os certificados no Emulador Local do Azure Cosmos DB são gerados na primeira vez que o emulador é executado. Há dois certificados. Um usado para conexão com o emulador do local, e outro para gerenciar segredos no emulador. O certificado que você quer exportar é o certificado de conexão com o nome amigável "DocumentDBEmulatorCertificate".

Os dois certificados podem ser gerados novamente clicando em **Redefinir Dados**, conforme mostrado abaixo no Emulador do Azure Cosmos DB em execução na Bandeja do Windows. Se você gerar novamente os certificados e instalá-los no repositório de certificados Java, ou usá-los em outro lugar, será necessário atualizá-los, caso contrário, seu aplicativo não conectará mais ao emulador local.

![Redefinição de dados do emulador local do Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png)

## <a name="how-to-export-the-azure-cosmos-db-ssl-certificate"></a>Como exportar o certificado SSL do Azure Cosmos DB

1. Inicie o gerenciador de Certificados do Windows executando certlm.msc, navegue até a pasta Pessoal-> Certificados e abra o certificado com o nome amigável **DocumentDbEmulatorCertificate**.

    ![Etapa 1 de exportação do emulador local do Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png)

2. Clique em **Detalhes** e depois em **OK**.

    ![Etapa 2 de exportação do emulador local do Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png)

3. Clique em **Copiar para arquivo...**.

    ![Etapa 3 de exportação do emulador local do Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png)

4. Clique em **Avançar**.

    ![Etapa 4 de exportação do emulador local do Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png)

5. Clique em **Não, não exportar a chave privada** e, em seguida, clique em **Avançar**.

    ![Etapa 5 de exportação do emulador local do Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png)

6. Clique em **X.509 codificado em Base-64 (.CER)** e depois em **Avançar**.

    ![Etapa 6 de exportação do emulador local do Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png)

7. Dê um nome ao certificado. Nesse caso, **documentdbemulatorcert** e, em seguida, clique em **Avançar**.

    ![Etapa 7 de exportação do emulador local do Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png)

8. Clique em **Concluir**.

    ![Etapa 8 de exportação do emulador local do Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png)

## <a name="how-to-use-the-certificate-in-java"></a>Como usar o certificado em Java

Durante a execução de aplicativos Java ou MongoDB que usam o cliente Java, é mais fácil instalar o certificado no repositório de certificados Java padrão do que passar os sinalizadores "-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>. Por exemplo, o [aplicativo de demonstração Java](https://localhost:8081/_explorer/index.html) incluído depende do repositório de certificados padrão.

Siga as instruções em [Adicionar um certificado ao Repositório de Certificados de AC Java](https://docs.microsoft.com/azure/java-add-certificate-ca-store) para importar o certificado X.509 no repositório de certificados Java padrão. Lembre-se de que você trabalhará no diretório %JAVA_HOME% durante a execução da keytool.

Após a instalação do certificado SSL “CosmosDBEmulatorCertificate”, seu aplicativo deverá conseguir se conectar e usar o Emulador local do Azure Cosmos DB. Se você continuar tendo problemas, siga o artigo [Depuração de conexões SSL/TLS](http://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html). É muito provável que o certificado não esteja instalado no repositório %JAVA_HOME%/jre/lib/security/cacerts. Por exemplo, se você tiver várias versões do Java instaladas, seu aplicativo poderá usar um repositório de cacerts diferente daquele que você atualizou.

## <a name="how-to-use-the-certificate-in-python"></a>Como usar o certificado em Python

Por padrão, o [SDK do Python (versão 2.0.0 ou superior)](documentdb-sdk-python.md) para a API do DocumentDB não tentará usar o certificado SSL ao se conectar ao emulador local. Se, no entanto, você quiser usar a validação de SSL, siga os exemplos na documentação [Wrappers de soquete Python](https://docs.python.org/2/library/ssl.html).

## <a name="how-to-use-the-certificate-in-nodejs"></a>Como usar o certificado no Node.js

Por padrão, o [SDK do Node.js (versão 1.10.1 ou superior)](documentdb-sdk-node.md) para a API do DocumentDB não tentará usar o certificado SSL ao se conectar ao emulador local. Se, no entanto, você quiser usar a validação de SSL, siga os exemplos na [Documentação do Node.js](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você fez o seguinte:

> [!div class="checklist"]
> * Trocou certificados
> * Exportou o certificado SSL
> * Aprendeu a usar o certificado no Java, Python e Node.js

Você pode continuar a criar um gatilho de HTTP de Azure Functions com um tutorial de associação de entrada do Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Criar uma função do Azure com entrada do Azure Cosmos DB](tutorial-functions-http-trigger.md) 
