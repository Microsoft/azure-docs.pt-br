---
title: SSL/TLS-banco de dados do Azure para PostgreSQL-servidor único
description: Instruções e informações sobre como configurar a conectividade TLS para o banco de dados do Azure para PostgreSQL-servidor único.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: c98ee8f747975d4237c2906be2060eddbc7b9990
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000939"
---
# <a name="configure-tls-connectivity-in-azure-database-for-postgresql---single-server"></a>Configurar a conectividade TLS no banco de dados do Azure para PostgreSQL-servidor único

O banco de dados do Azure para PostgreSQL prefere conectar seus aplicativos cliente ao serviço PostgreSQL usando TLS (segurança de camada de transporte), anteriormente conhecido como protocolo SSL (SSL). A imposição de conexões TLS entre o servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques "Man-in-the-middle" criptografando o fluxo de dados entre o servidor e seu aplicativo.

Por padrão, o serviço de banco de dados PostgreSQL está configurado para exigir conexão TLS. Você pode optar por desabilitar a exigência de TLS se o aplicativo cliente não oferecer suporte à conectividade TLS.

>[!NOTE]
> Com base nos comentários dos clientes, estendemos a substituição do certificado raiz para nossa CA raiz Baltimore existente até 15 de fevereiro de 2021 (02/15/2021).

> [!IMPORTANT] 
> O certificado raiz SSL está definido para expirar a partir de 15 de fevereiro de 2021 (02/15/2021). Atualize seu aplicativo para usar o [novo certificado](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem). Para saber mais, confira [atualizações de certificado planejadas](concepts-certificate-rotation.md)


## <a name="enforcing-tls-connections"></a>Impondo conexões TLS

Para todos os servidores do banco de dados do Azure para PostgreSQL provisionados por meio do portal do Azure e da CLI, a imposição de conexões TLS é habilitada por padrão. 

Da mesma forma, cadeias de conexão predefinidas nas configurações de "cadeias de conexão" em seu servidor no portal do Azure incluem os parâmetros necessários para linguagens comuns para se conectar ao servidor de banco de dados usando TLS. O parâmetro TLS varia de acordo com o conector, por exemplo, "SSL = true" ou "sslmode = require" ou "sslmode = required" e outras variações.

## <a name="configure-enforcement-of-tls"></a>Configurar a imposição de TLS

Opcionalmente, você pode desabilitar a imposição de conectividade TLS. O Microsoft Azure recomenda sempre habilitar a configuração **Impor conexão SSL** para melhorar a segurança.

### <a name="using-the-azure-portal"></a>Usando o portal do Azure

Visite seu servidor de Banco de Dados do Azure para PostgreSQL e clique em **Segurança de conexão**. Use o botão de alternância para habilitar ou desabilitar a configuração **Impor conexão SSL**. Em seguida, clique em **Salvar**.

:::image type="content" source="./media/concepts-ssl-connection-security/1-disable-ssl.png" alt-text="Segurança de conexão-desabilitar impor TLS/SSL":::

Você pode confirmar a configuração exibindo a página **Visão geral** para ver o indicador **Status de imposição de SSL**.

### <a name="using-azure-cli"></a>Usando a CLI do Azure

Você pode habilitar ou desabilitar o parâmetro **ssl-enforcement** usando os valores `Enabled` ou `Disabled` respectivamente na CLI do Azure.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Verifique se seu aplicativo ou estrutura dá suporte a conexões TLS

Algumas estruturas de aplicativo que usam o PostgreSQL para seus serviços de banco de dados não habilitam o TLS por padrão durante a instalação. Se o seu servidor PostgreSQL impõe conexões TLS, mas o aplicativo não está configurado para TLS, o aplicativo pode falhar ao se conectar ao seu servidor de banco de dados. Consulte a documentação do aplicativo para saber como habilitar conexões TLS.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Aplicativos que exigem verificação de certificado para conectividade TLS

Em alguns casos, os aplicativos exigem um arquivo de certificado local gerado por meio de um arquivo de certificado de AC (autoridade de certificação) confiável para se conectar com segurança. O certificado para se conectar a um servidor de banco de dados do Azure para PostgreSQL está localizado em https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem . Baixe o arquivo de certificado e salve-o em seu local preferido. 

Consulte os links a seguir para obter certificados para servidores em nuvens soberanas: [Azure governamental](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem), [Azure China](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)e [Azure Alemanha](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt).

### <a name="connect-using-psql"></a>Conectar usando psql

O exemplo a seguir mostra como se conectar ao seu servidor PostgreSQL usando o utilitário de linha de comando psql. Use a `sslmode=verify-full` configuração cadeia de conexão para impor a verificação de certificado TLS/SSL. Passe o caminho do arquivo de certificado local para o `sslrootcert` parâmetro.

O comando a seguir é um exemplo da cadeia de conexão psql:

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Confirme se o valor passado para `sslrootcert` corresponde ao caminho do arquivo para o certificado que você salvou.

## <a name="tls-enforcement-in-azure-database-for-postgresql-single-server"></a>Imposição de TLS no banco de dados do Azure para PostgreSQL servidor único

Banco de dados do Azure para PostgreSQL-o servidor único dá suporte à criptografia para clientes que se conectam ao seu servidor de banco de dados usando o protocolo TLS. O TLS é um protocolo padrão do setor que garante conexões de rede seguras entre o servidor de banco de dados e os aplicativos cliente, permitindo que você atenda aos requisitos de conformidade.

### <a name="tls-settings"></a>Configurações de protocolo TLS

O banco de dados do Azure para PostgreSQL servidor único fornece a capacidade de impor a versão de TLS para as conexões de cliente. Para impor a versão de TLS, use a configuração de opção de **versão mínima do TLS** . Os valores a seguir são permitidos para esta configuração de opção:

|  Configuração de TLS mínima             | Versão do TLS do cliente com suporte                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (padrão) | Não é necessário TLS                      |
| TLS1_0                           | TLS 1,0, TLS 1,1, TLS 1,2 e superior |
| TLS1_1                           | TLS 1,1, TLS 1,2 e superior          |
| TLS1_2                           | TLS versão 1,2 e superior           |


Por exemplo, definir essa versão de configuração de TLS mínima como TLS 1,0 significa que o servidor permitirá conexões de clientes usando TLS 1,0, 1,1 e 1.2 +. Como alternativa, definir isso como 1,2 significa que você só permite conexões de clientes que usam o TLS 1.2 + e todas as conexões com TLS 1,0 e TLS 1,1 serão rejeitadas.

> [!Note] 
> Por padrão, o banco de dados do Azure para PostgreSQL não impõe uma versão mínima do TLS (a configuração `TLSEnforcementDisabled` ).
>
> Depois de aplicar uma versão mínima do TLS, não será possível desabilitar mais tarde a imposição mínima da versão.

Para saber como definir a configuração de TLS para o servidor único do banco de dados do Azure para PostgreSQL, consulte [como definir a configuração de TLS](howto-tls-configurations.md).

## <a name="cipher-support-by-azure-database-for-postgresql-single-server"></a>Suporte de codificação pelo banco de dados do Azure para PostgreSQL servidor único

Como parte da comunicação SSL/TLS, os conjuntos de codificação são validados e apenas suporte para os naipes de codificação têm permissão para se comunicar com o banco de dados Server. A validação do conjunto de codificação é controlada na [camada do gateway](concepts-connectivity-architecture.md#connectivity-architecture) e não explicitamente no próprio nó. Se os conjuntos de codificação não corresponderem a um dos conjuntos listados abaixo, as conexões de entrada do cliente serão rejeitadas.

### <a name="cipher-suite-supported"></a>Conjunto de codificação com suporte

*   TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
*   TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="next-steps"></a>Próximas etapas

Examine várias opções de conectividade de aplicativo em [bibliotecas de conexões para o banco de dados do Azure para PostgreSQL](concepts-connection-libraries.md).

- Saiba como [Configurar o TLS](howto-tls-configurations.md)