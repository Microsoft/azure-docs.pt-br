---
title: Aviso de migração de gateway para o banco de dados SQL de Gen2 para Gen3 | Microsoft Docs
description: Artigo fornece aviso aos usuários sobre a migração de endereços IP de Gateways de banco de dados de SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
manager: craigg
ms.date: 07/01/2019
ms.openlocfilehash: 5894579c62c524394c7fea044b96885f7c8e8204
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538374"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Migração de tráfego de banco de dados SQL do Azure para Gateways mais recentes

Como melhora a infraestrutura do Azure, a Microsoft atualizará periodicamente hardware para garantir que podemos fornecer a experiência melhor possível para o cliente. Nos próximos meses, planeje adicionar que gateways criados nas gerações de hardware mais recentes e encerrar Gateways criados no hardware mais antigo em algumas regiões.  

Os clientes serão notificados por email e no portal do Azure, bem com antecedência sobre qualquer alteração nos Gateways disponíveis em cada região. As informações mais atualizadas serão mantidas na [endereços IP de gateway do banco de dados SQL](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) tabela.

## <a name="impact-of-this-change"></a>Impacto dessa alteração

A primeira rodada de encerramento de Gateway está agendada para 1 de setembro de 2019 nas seguintes regiões:

- Oeste dos EUA
- Europa Ocidental
- East US
- Centro dos EUA
- Sudeste da Ásia
- Centro-Sul dos Estados Unidos
- Norte da Europa
- Centro-Norte dos EUA
- Oeste do Japão
- Leste do Japão
- Leste dos EUA 2
- Ásia Oriental

O endereço de IP encerrada deixará de aceitar tráfego e novas tentativas de conexão serão roteadas para um dos Gateways na região.

Em que você não verá o impacto dessa alteração:

- Clientes usando o redirecionamento de sua política de conexão não verá qualquer impacto.
- Conexões de banco de dados SQL de dentro do Azure e usando marcas de serviço não serão afetados.
- As conexões feitas usando as versões com suporte do Driver JDBC para SQL Server não verá nenhum impacto. Para versões com suporte do JDBC, consulte [baixar o Microsoft JDBC Driver para SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>O que fazer você fazer se você for afetado

É recomendável que você permita o tráfego de saída para os endereços IP para todos os [endereços IP de gateway do banco de dados SQL](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) na região em TCP porta 1433 e intervalo de 11000 a 11999 em seu dispositivo de firewall de porta. Para obter mais informações sobre intervalos de porta, consulte [política de Conexão](sql-database-connectivity-architecture.md#connection-policy).

Conexões feitas a partir de aplicativos usando o Microsoft JDBC Driver abaixo da versão 4.0 podem falhar a validação do certificado. Versões anteriores do Microsoft JDBC se baseiam em CN (nome comum) no campo assunto do certificado. A mitigação é para garantir que a propriedade hostNameInCertificate for definida como *. database.windows.net. Para obter mais informações sobre como definir a propriedade hostNameInCertificate, consulte [conectar-se com criptografia SSL](/sql/connect/jdbc/connecting-with-ssl-encryption).

Se a mitigação acima não funcionar, uma solicitação de suporte de arquivo para o banco de dados SQL usando a seguinte URL: https://aka.ms/getazuresupport

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [arquitetura de conectividade do SQL Azure](sql-database-connectivity-architecture.md)