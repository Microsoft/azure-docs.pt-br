---
title: Configurar conectividade SSL no banco de dados do Azure para PostgreSQL - servidor único
description: Instruções e informações para configurar o banco de dados do Azure para PostgreSQL – único servidor e aplicativos associados ao uso de conexões SSL adequadamente.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 686adfb2998eff10ef4b9f378163b164ba970c56
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461835"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>Configurar conectividade SSL no banco de dados do Azure para PostgreSQL - servidor único
O Banco de dados do Azure para PostgreSQL prefere conectar-se seus aplicativos cliente ao serviço PostgreSQL usando o protocolo SSL. Impor conexões SSL entre seu servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques "man-in-the-middle" criptografando o fluxo de dados entre o servidor e seu aplicativo.

Por padrão, o serviço de banco de dados do PostgreSQL é configurado para exigir conexão SSL. Você pode optar por desabilitar a exigência de SSL se o aplicativo cliente não dá suporte a conectividade SSL. 

## <a name="enforcing-ssl-connections"></a>Impor conexões SSL
Para todos os Bancos de Dados do Azure para servidores PostgreSQL provisionados com o Portal e a CLI do Azure, a imposição de conexões SSL está habilitada por padrão. 

Da mesma forma, cadeias de conexão previamente definidas nas configurações de "Cadeias de Conexão" em seu servidor no Portal do Azure incluem os parâmetros necessários para linguagens comuns a fim de se conectar ao seu servidor de banco de dados usando SSL. O parâmetro SSL varia de acordo com o conector, por exemplo "ssl=true" ou "sslmode=require" ou "sslmode=required" e outras variações.

## <a name="configure-enforcement-of-ssl"></a>Configurar a imposição de SSL
Como opção, você pode desabilitar a imposição da conectividade SSL. O Microsoft Azure recomenda sempre habilitar a configuração **Impor conexão SSL** para melhorar a segurança.

### <a name="using-the-azure-portal"></a>Usando o portal do Azure
Visite seu servidor de Banco de Dados do Azure para PostgreSQL e clique em **Segurança de conexão**. Use o botão de alternância para habilitar ou desabilitar a configuração **Impor conexão SSL**. Em seguida, clique em **Salvar**. 

![Segurança de Conexão - Desabilitar a imposição de SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Você pode confirmar a configuração exibindo a página **Visão geral** para ver o indicador **Status de imposição de SSL**.

### <a name="using-azure-cli"></a>Usando a CLI do Azure
Você pode habilitar ou desabilitar o parâmetro **ssl-enforcement** usando os valores `Enabled` ou `Disabled` respectivamente na CLI do Azure.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Verificar se o seu aplicativo ou sua estrutura oferece suporte a conexões SSL
Algumas estruturas de aplicativo que usam PostgreSQL para seus serviços de banco de dados não habilitam o SSL por padrão durante a instalação. Se seu servidor PostgreSQL impõe conexões SSL, mas o aplicativo não está configurado para SSL, o aplicativo poderá falhar ao se conectar ao seu servidor de banco de dados. Confira a documentação de seu aplicativo para saber como habilitar conexões SSL.


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Aplicativos que exigem a verificação de certificado para conectividade SSL
Em alguns casos, os aplicativos exigem um arquivo de certificado local gerado de um arquivo de certificado (.cer) de uma Autoridade de Certificação (CA) confiável para se conectar com segurança. O certificado para se conectar a um banco de dados do Azure para servidor PostgreSQL está localizado em https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem. Baixe o arquivo de certificado e salve-o em seu local preferido. 

### <a name="connect-using-psql"></a>Conectar-se usando psql
O exemplo a seguir mostra como se conectar ao servidor PostgreSQL usando o utilitário de linha de comando do psql. Use o `sslmode=verify-full` configuração da cadeia de conexão para impor a verificação do certificado SSL. Passe o caminho do arquivo de certificado local para o `sslrootcert` parâmetro.

Abaixo está um exemplo da cadeia de caracteres de conexão psql:
```
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Confirme que o valor passado para `sslrootcert` corresponde ao caminho do arquivo para o certificado que você salvou.


## <a name="next-steps"></a>Próximas etapas
Analise as várias opções de conectividade do aplicativo no [bibliotecas de Conexão para o banco de dados do Azure para PostgreSQL](concepts-connection-libraries.md).
