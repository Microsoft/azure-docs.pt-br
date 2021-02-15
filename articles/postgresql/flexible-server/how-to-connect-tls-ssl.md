---
title: Conectividade criptografada usando TLS/SSL no banco de dados do Azure para PostgreSQL-servidor flexível
description: Instruções e informações sobre como se conectar usando TLS/SSL no banco de dados do Azure para PostgreSQL – servidor flexível.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 0ba04ff14e62cd411515a765dc95ef7a3e72b709
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90933167"
---
# <a name="encrypted-connectivity-using-transport-layer-security-in-azure-database-for-postgresql---flexible-server"></a>Conectividade criptografada usando a segurança da camada de transporte no banco de dados do Azure para PostgreSQL-servidor flexível

> [!IMPORTANT]
> O Servidor Flexível do Banco de Dados do Azure para PostgreSQL está em versão prévia

Banco de dados do Azure para PostgreSQL-o servidor flexível dá suporte à conexão de aplicativos cliente ao serviço PostgreSQL usando TLS (segurança de camada de transporte), anteriormente conhecido como protocolo SSL (SSL). O TLS é um protocolo padrão do setor que garante conexões de rede criptografadas entre o servidor de banco de dados e os aplicativos cliente, permitindo que você atenda aos requisitos de conformidade.

Banco de dados do Azure para PostgreSQL-o servidor flexível dá suporte a conexões criptografadas usando TLS 1.2 +) e todas as conexões de entrada com TLS 1,0 e TLS 1,1 serão negadas. Para todos os servidores flexíveis, a imposição de conexões TLS está habilitada e você não pode desabilitar o TLS/SSL para se conectar ao servidor flexível.

## <a name="applications-that-require-certificate-verification-for-tlsssl-connectivity"></a>Aplicativos que exigem verificação de certificado para conectividade TLS/SSL
Em alguns casos, os aplicativos exigem um arquivo de certificado local gerado por meio de um arquivo de certificado de AC (autoridade de certificação) confiável para se conectar com segurança. Banco de dados do Azure para PostgreSQL-o servidor flexível usa a *autoridade de certificação raiz global do DigiCert*. Baixe esse certificado necessário para se comunicar por SSL da [AC raiz global do DigiCert](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) e salve o arquivo de certificado em seu local preferido. Por exemplo, este tutorial usa `c:\ssl`.


### <a name="connect-using-psql"></a>Conectar usando psql
Caso tenha criado um servidor flexível com *acesso privado (Integração VNet)* , será necessário se conectar ao servidor de um recurso na mesma VNet do servidor. Crie uma máquina virtual e adicione-a à VNET criada com o servidor flexível.

Caso tenha criado um servidor flexível com *acesso público (endereços IP permitidos)* , será possível adicionar seu endereço IP local à lista de regras de firewall no servidor.

O exemplo a seguir mostra como se conectar ao seu servidor usando a interface de linha de comando psql. Use a `sslmode=verify-full` configuração cadeia de conexão para impor a verificação de certificado TLS/SSL. Passe o caminho do arquivo de certificado local para o `sslrootcert` parâmetro.

```bash
 psql "sslmode=verify-full sslrootcert=c:\ssl\DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=postgres user=myadmin"
```
> [!Note]
> Confirme se o valor passado para `sslrootcert` corresponde ao caminho do arquivo para o certificado que você salvou.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Verifique se seu aplicativo ou estrutura dá suporte a conexões TLS

Algumas estruturas de aplicativo que usam o PostgreSQL para seus serviços de banco de dados não habilitam o TLS por padrão durante a instalação. O servidor PostgreSQL impõe conexões TLS, mas se o aplicativo não estiver configurado para TLS, o aplicativo poderá falhar ao se conectar ao seu servidor de banco de dados. Consulte a documentação do aplicativo para saber como habilitar conexões TLS.

## <a name="next-steps"></a>Próximas etapas
- [Crie e gerencie o banco de dados do Azure para PostgreSQL – rede virtual de servidor flexível usando o CLI do Azure](./how-to-manage-virtual-network-cli.md).
- Saiba mais sobre [rede no banco de dados do Azure para PostgreSQL – servidor flexível](./concepts-networking.md)
- Saiba mais sobre o [banco de dados do Azure para PostgreSQL – regras de firewall de servidor flexíveis](./concepts-networking.md#public-access-allowed-ip-addresses)
