---
title: Configurar a conectividade SSL no Banco de Dados do Azure para PostgreSQL | Microsoft Docs
description: "Instruções e informações para configurar o Banco de Dados do Azure para PostgreSQL e aplicativos associados a fim de usar as conexões SSL adequadamente."
services: postgresql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: jhubbard
ms.service: postgresql
ms.custom: 
ms.topic: article
ms.date: 05/15/2017
ms.openlocfilehash: 685aa4c2f75b7c3260ca737f7c786157480b2d90
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql"></a>Configurar a conectividade SSL no Banco de Dados do Azure para PostgreSQL
O Banco de dados do Azure para PostgreSQL prefere conectar-se seus aplicativos cliente ao serviço PostgreSQL usando o protocolo SSL. Impor conexões SSL entre seu servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques de "intermediários" criptografando o fluxo de dados entre o servidor e seu aplicativo.

Por padrão, o serviço de banco de dados do PostgreSQL é configurado para exigir conexão SSL. Como opção, você pode desabilitar a exigência de SSL para conectar ao seu serviço de banco de dados, se seu aplicativo cliente não oferecer suporte à conectividade SSL. 

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
az postgres server update --resource-group myresourcegroup --name mypgserver-20170401 --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Verificar se o seu aplicativo ou sua estrutura oferece suporte a conexões SSL
Muitas estruturas de aplicativo comuns que usam PostgreSQL para serviços de banco de dados, como Drupal e Django, não habilitam o SSL por padrão durante a instalação. A habilitação da conectividade SSL deve ser feita após a instalação ou por meio de comandos da CLI específicos ao aplicativo. Se o seu servidor PostgreSQL estiver impondo conexões SSL, e o aplicativo associado não estiver configurado corretamente, a conexão do aplicativo ao seu servidor de banco de dados poderá falhar. Confira a documentação de seu aplicativo para saber como habilitar conexões SSL.


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Aplicativos que exigem a verificação de certificado para conectividade SSL
Em alguns casos, os aplicativos exigem um arquivo de certificado local gerado de um arquivo de certificado (.cer) de uma Autoridade de Certificação (CA) confiável para se conectar com segurança. Veja as etapas a seguir para obter o arquivo .cer, decodificar o certificado e vinculá-lo ao seu aplicativo.

### <a name="download-the-certificate-file-from-the-certificate-authority-ca"></a>Baixar o arquivo de certificado da Autoridade de Certificação (CA) 
O certificado necessário para se comunicar por SSL com o servidor de Banco de Dados do Azure para PostgreSQL está [aqui](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt). Baixar o arquivo de certificado localmente.

### <a name="download-and-install-openssl-on-your-machine"></a>Baixar e instalar o OpenSSL em seu computador 
Para decodificar o arquivo de certificado exigido para conectar seu aplicativo com segurança ao servidor de banco de dados, instale o OpenSSL em seu computador local.

#### <a name="for-linux-os-x-or-unix"></a>Para Linux, OS X ou Unix
As bibliotecas OpenSSL são fornecidas diretamente no código-fonte a partir da [OpenSSL Software Foundation](http://www.openssl.org). As instruções a seguir orientarão você pelas etapas necessárias de instalação do OpenSSL no computador com Linux. Este artigo usa comandos comprovadamente funcionais no Ubuntu 12.04 e superior.

Abra uma sessão de terminal e instale o OpenSSL
```bash
wget http://www.openssl.org/source/openssl-1.1.0e.tar.gz
``` 
Extraia os arquivos do pacote baixado
```bash
tar -xvzf openssl-1.1.0e.tar.gz
```
Entre no diretório onde os arquivos foram extraídos. Por padrão, deve ser da seguinte maneira.

```bash
cd openssl-1.1.0e
```
Configure o OpenSSL executando o comando a seguir. Se você quiser os arquivos em uma pasta diferente de /usr/local/openssl, altere o seguinte conforme for apropriado.

```bash
./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl
```
Agora que o OpenSSL está configurado corretamente, você precisa compilá-lo para converter seu certificado. Para compilar, execute o seguinte comando:

```bash
make
```
Após a conclusão da compilação, você estará pronto para instalar o OpenSSL como um executável, executando o seguinte comando:
```bash
make install
```
Para confirmar a instalação bem-sucedida do OpenSSL em seu sistema, execute o comando a seguir e verifique se você obtém o mesmo resultado.

```bash
/usr/local/openssl/bin/openssl version
```
Se for bem-sucedida, você verá a seguinte mensagem.
```bash
OpenSSL 1.1.0e 7 Apr 2014
```

#### <a name="for-windows"></a>Para Windows
A instalação do OpenSSL em um PC com Windows pode ser feita destas maneiras:
1. **(Recomendado)** Usando a funcionalidade interna Bash para Windows no Windows 10 e superior, o OpenSSL é instalado por padrão. As instruções sobre como habilitar a funcionalidade Bash para Windows no Windows 10 podem ser encontradas [aqui](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide).
2. Por meio do download de um aplicativo Win32/64 fornecido pela comunidade. Embora a OpenSSL Software Foundation não forneça, nem endosse, qualquer instalador específico do Windows, ela fornece uma lista dos instaladores disponíveis [aqui](https://wiki.openssl.org/index.php/Binaries)

### <a name="decode-your-certificate-file"></a>Decodificar o arquivo de certificado
O arquivo de CA Raiz baixado está em formato criptografado. Use o OpenSSL para decodificar o arquivo de certificado. Para fazer isso, execute este comando OpenSSL:

```dos
OpenSSL>x509 -inform DER -in BaltimoreCyberTrustRoot.cer -text -out root.crt
```

### <a name="connecting-to-azure-database-for-postgresql-with-ssl-certificate-authentication"></a>Conectar-se ao Banco de Dados do Azure para PostgreSQL com autenticação de certificado SSL
Agora que você decodificou o certificado, pode conectar-se ao seu servidor de banco de dados com segurança por SSL. Para permitir a verificação do certificado do servidor, o certificado deve ser colocado no arquivo ~/.postgresql/root.crt, no diretório base do usuário. (No Microsoft Windows, o arquivo é chamado % APPDATA%\postgresql\root.crt.). Veja a seguir instruções para se conectar ao Banco de Dados do Azure para PostgreSQL.

> [!NOTE]
> Atualmente, há um problema conhecido em que, se você usar "sslmode=verify-full" em sua conexão ao serviço, a conexão falhará com o seguinte erro: _o certificado do servidor para "&lt;region&gt;.control.database.windows.net" (e outros 7 nomes) não corresponde ao nome de host "&lt;servername&gt;.postgres.database.azure.com"._
> Se "sslmode=verify-full" for obrigatório, use a convenção de nomenclatura do servidor **&lt;servername&gt;.database.windows.net** como nome de host da cadeia de conexão. Planejamos remover esta limitação no futuro. Conexões que usam outros [modos SSL](https://www.postgresql.org/docs/9.6/static/libpq-ssl.html#LIBPQ-SSL-SSLMODE-STATEMENTS) devem continuar usando a convenção de nomenclatura de host preferenciais  **&lt;servername&gt;.postgres.database.azure.com**.

#### <a name="using-psql-command-line-utility"></a>Usar o utilitário de linha de comando psql
O exemplo a seguir mostra como conectar-se ao servidor PostgreSQL usando o utilitário de linha de comando psql. Use o arquivo `root.crt` criado e a opção `sslmode=verify-ca` ou `sslmode=verify-full`.

Usando a interface de linha de comando do PostgreSQL, execute o seguinte comando:
```bash
psql "sslmode=verify-ca sslrootcert=root.crt host=mypgserver-20170401.postgres.database.azure.com dbname=postgres user=mylogin@mypgserver-20170401"
```
Se tiver êxito, você receberá o seguinte resultado:
```bash
Password for user mylogin@mypgserver-20170401:
psql (9.6.2)
WARNING: Console code page (437) differs from Windows code page (1252)
     8-bit characters might not work correctly. See psql reference
     page "Notes for Windows users" for details.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=>
```

#### <a name="using-pgadmin-gui-tool"></a>Usando a ferramenta GUI de pgAdmin
A configuração de pgAdmin 4 para se conectar com segurança via SSL exige que você defina `SSL mode = Verify-CA` ou `SSL mode = Verify-Full` da seguinte maneira:

![Captura de tela de pgAdmin - conexão - modo SSL Require](./media/concepts-ssl-connection-security/2-pgadmin-ssl.png)

## <a name="next-steps"></a>Próximas etapas
Analise as várias opções de conectividade do aplicativo em [Bibliotecas de conexão para o Banco de Dados do Azure para PostgreSQL](concepts-connection-libraries.md)
