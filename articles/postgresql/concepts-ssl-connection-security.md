---
title: Configurar a conectividade SSL no Banco de Dados do Azure para PostgreSQL
description: Instruções e informações para configurar o Banco de Dados do Azure para PostgreSQL e aplicativos associados a fim de usar as conexões SSL adequadamente.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 5a0fc99052b18dc1fa837147aa914a473d27d832
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60871393"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql"></a>Configurar a conectividade SSL no Banco de Dados do Azure para PostgreSQL
O Banco de dados do Azure para PostgreSQL prefere conectar-se seus aplicativos cliente ao serviço PostgreSQL usando o protocolo SSL. Impor conexões SSL entre seu servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques de "intermediários" criptografando o fluxo de dados entre o servidor e seu aplicativo.

Por padrão, o serviço de banco de dados do PostgreSQL é configurado para exigir conexão SSL. Como opção, você poderá desabilitar a exigência de SSL para se conectar ao seu serviço de banco de dados, se seu aplicativo cliente não oferecer suporte à conectividade SSL. 

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
Muitas estruturas de aplicativo comuns que usam PostgreSQL para serviços de banco de dados, como Drupal e Django, não habilitam o SSL por padrão durante a instalação. A habilitação da conectividade SSL deve ser feita após a instalação ou por meio de comandos da CLI específicos ao aplicativo. Se o seu servidor PostgreSQL estiver impondo conexões SSL, e o aplicativo associado não estiver configurado corretamente, a conexão do aplicativo ao seu servidor de banco de dados poderá falhar. Confira a documentação de seu aplicativo para saber como habilitar conexões SSL.


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Aplicativos que exigem a verificação de certificado para conectividade SSL
Em alguns casos, os aplicativos exigem um arquivo de certificado local gerado de um arquivo de certificado (.cer) de uma Autoridade de Certificação (CA) confiável para se conectar com segurança. Veja as etapas a seguir para obter o arquivo .cer, decodificar o certificado e vinculá-lo ao seu aplicativo.

### <a name="download-the-certificate-file-from-the-certificate-authority-ca"></a>Baixar o arquivo de certificado da Autoridade de Certificação (CA) 
O certificado necessário para se comunicar por SSL com o servidor de Banco de Dados do Azure para PostgreSQL está [aqui](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt). Baixar o arquivo de certificado localmente.

### <a name="install-a-cert-decoder-on-your-machine"></a>Instalar um decodificador de certificado em seu computador 
Você pode usar [OpenSSL](https://github.com/openssl/openssl) para decodificar o arquivo de certificado necessário para seu aplicativo para se conectar com segurança ao seu servidor de banco de dados. Para saber como instalar o OpenSSL, consulte o [instruções de instalação do OpenSSL](https://github.com/openssl/openssl/blob/master/INSTALL). 


### <a name="decode-your-certificate-file"></a>Decodificar o arquivo de certificado
O arquivo de CA Raiz baixado está em formato criptografado. Use o OpenSSL para decodificar o arquivo de certificado. Para fazer isso, execute este comando OpenSSL:

```
openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
```

### <a name="connecting-to-azure-database-for-postgresql-with-ssl-certificate-authentication"></a>Conectar-se ao Banco de Dados do Azure para PostgreSQL com autenticação de certificado SSL
Agora que você decodificou o certificado, pode conectar-se ao seu servidor de banco de dados com segurança por SSL. Para permitir a verificação do certificado do servidor, o certificado deve ser colocado no arquivo ~/.postgresql/root.crt, no diretório base do usuário. (No Microsoft Windows, o arquivo é chamado % APPDATA%\postgresql\root.crt.). 

#### <a name="connect-using-psql"></a>Conectar-se usando psql
O exemplo a seguir mostra como conectar-se ao servidor PostgreSQL usando o utilitário de linha de comando psql. Use o arquivo `root.crt` criado e a opção `sslmode=verify-ca` ou `sslmode=verify-full`.

Usando a interface de linha de comando do PostgreSQL, execute o seguinte comando:
```bash
psql "sslmode=verify-ca sslrootcert=root.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=mylogin@mydemoserver"
```
Se tiver êxito, você receberá o seguinte resultado:
```bash
Password for user mylogin@mydemoserver:
psql (9.6.2)
WARNING: Console code page (437) differs from Windows code page (1252)
     8-bit characters might not work correctly. See psql reference
     page "Notes for Windows users" for details.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=>
```

## <a name="next-steps"></a>Próximas etapas
Analise as várias opções de conectividade do aplicativo em [Bibliotecas de conexão para o Banco de Dados do Azure para PostgreSQL](concepts-connection-libraries.md).
