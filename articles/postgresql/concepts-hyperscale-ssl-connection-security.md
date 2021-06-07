---
title: TLS (segurança de camada de transporte)-Citus (hiperescala)-banco de dados do Azure para PostgreSQL
description: Instruções e informações para configurar o banco de dados do Azure para PostgreSQL-Citus (hiperescala) e aplicativos associados para usar corretamente as conexões TLS.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: 659f86a107e4b08db4ec5195635ea32d2260d677
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87071466"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>Configurar o TLS no banco de dados do Azure para PostgreSQL-Citus (hiperescala)
O nó do coordenador de hiperescala (Citus) exige que os aplicativos cliente se conectem com o protocolo TLS. Impor o TLS entre o servidor de banco de dados e os aplicativos cliente ajuda a manter os dados confidenciais em trânsito. As configurações de verificação extra descritas abaixo também protegem contra ataques "Man-in-the-middle".

## <a name="enforcing-tls-connections"></a>Impondo conexões TLS
Os aplicativos usam uma "cadeia de conexão" para identificar o banco de dados de destino e as configurações de uma conexão. Clientes diferentes exigem configurações diferentes. Para ver uma lista de cadeias de conexão usadas por clientes comuns, consulte a seção **cadeias de conexão** do seu grupo de servidores na portal do Azure.

Os parâmetros TLS `ssl` e `sslmode` variam de acordo com os recursos do conector, por exemplo `ssl=true` ou `sslmode=require` ou `sslmode=required` .

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Verifique se seu aplicativo ou estrutura dá suporte a conexões TLS
Algumas estruturas de aplicativo não habilitam o TLS por padrão para conexões do PostgreSQL. No entanto, sem uma conexão segura, um aplicativo não pode se conectar a um nó de coordenador de hiperescala (Citus). Consulte a documentação do aplicativo para saber como habilitar conexões TLS.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Aplicativos que exigem verificação de certificado para conectividade TLS
Em alguns casos, os aplicativos exigem um arquivo de certificado local gerado de um arquivo de certificado (.cer) de uma Autoridade de Certificação (CA) confiável para se conectar com segurança. O certificado para se conectar a um banco de dados do Azure para PostgreSQL-Citus (hiperescala) está localizado em https://cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem . Baixe o arquivo de certificado e salve-o em seu local preferido.

> [!NOTE]
>
> Para verificar a autenticidade do certificado, você pode verificar sua impressão digital SHA-256 usando a ferramenta de linha de comando OpenSSL:
>
> ```sh
> openssl x509 -in DigiCertGlobalRootCA.crt.pem -noout -sha256 -fingerprint
>
> # should output:
> # 43:48:A0:E9:44:4C:78:CB:26:5E:05:8D:5E:89:44:B4:D8:4F:96:62:BD:26:DB:25:7F:89:34:A4:43:C7:01:61
> ```

### <a name="connect-using-psql"></a>Conectar usando psql
O exemplo a seguir mostra como se conectar ao seu nó de coordenador de hiperescala (Citus) usando o utilitário de linha de comando psql. Use a `sslmode=verify-full` configuração cadeia de conexão para impor a verificação de certificado TLS. Passe o caminho do arquivo de certificado local para o `sslrootcert` parâmetro.

Abaixo está um exemplo da cadeia de conexão do psql:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Confirme se o valor passado para `sslrootcert` corresponde ao caminho do arquivo para o certificado que você salvou.

## <a name="next-steps"></a>Próximas etapas
Aumente ainda mais a segurança com [as regras de firewall no banco de dados do Azure para PostgreSQL-Citus (hiperescala)](concepts-hyperscale-firewall-rules.md).
