---
title: "Lista de verificação de segurança do banco de dados do Azure | Microsoft Docs"
description: "Este artigo fornece um conjunto de lista de verificação de segurança do banco de dados do Azure."
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2017
ms.author: tomsh
ms.openlocfilehash: 5047635555a6b4592c0714677c2b942e50bad344
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-database-security-checklist"></a>Lista de verificação de segurança do banco de dados do Azure

Para ajudar a melhorar a segurança, o banco de dados do Azure inclui uma série de controles de segurança internos que você pode usar para limitar e controlar o acesso.

Estão incluídos:

-   Um firewall que permite que você crie [regras de firewall](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-firewall-configure) limitando a conectividade por endereço IP,
-   Firewall de nível de servidor acessível pelo portal do Azure
-   Regras de firewall no nível de banco de dados acessíveis pelo SSMS
-   Conectividade segura ao banco de dados usando cadeias de conexão segura
-   Usar gerenciamento de acesso
-   Criptografia de dados
-   Auditoria do Banco de Dados SQL
-   Detecção de ameaças do Banco de Dados SQL

## <a name="introduction"></a>Introdução
A computação na nuvem exige novos paradigmas de segurança que não são familiares para muitos usuários do aplicativo, administradores de banco de dados e programadores. Como resultado, algumas organizações hesitam implementar uma infraestrutura de nuvem para o gerenciamento de dados devido a supostos riscos de segurança. No entanto, grande parte dessa preocupação pode ser atenuada por meio de uma melhor compreensão dos recursos de segurança do Microsoft Azure e do Banco de Dados SQL do Microsoft Azure.

## <a name="checklist"></a>Lista de verificação
É recomendável que você leia o artigo [Práticas recomendadas de segurança do banco de dados do Azure](https://docs.microsoft.com/en-us/azure/security/azure-database-security-best-practices) antes de examinar esta lista de verificação. Você poderá tirar o máximo proveito desta lista de verificação depois de entender as práticas recomendadas. Você pode usar esta lista de verificação para certificar-se de que já resolveu os problemas importantes na segurança do banco de dados do Azure.


|Categoria da lista de verificação| Descrição|
| ------------ | -------- |
|**Proteger dados**||
| <br> Criptografia em movimento/trânsito| <ul><li>[Protocolo TLS](https://docs.microsoft.com/en-us/windows-server/security/tls/transport-layer-security-protocol), para criptografia de dados quando os dados se movem para as redes.</li><li>O banco de dados requer uma comunicação segura de clientes com base no protocolo [TDS (Tabular Data Stream)](https://msdn.microsoft.com/en-in/library/dd357628.aspx) em TLS (Transport Layer Security).</li></ul> |
|<br>Criptografia em repouso| <ul><li>[Transparent Data Encryption](http://go.microsoft.com/fwlink/?LinkId=526242), quando dados inativos são armazenados fisicamente em qualquer formato digital.</li></ul>|
|**Controlar acesso**||  
|<br> Acesso a banco de dados | <ul><li>[Autenticação](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-control-access) (Autenticação do Azure Active Directory), a autenticação do AD usa identidades gerenciadas pelo Azure Active Directory.</li><li>[Autorização](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-control-access) concede aos usuários os privilégios mínimos necessários.</li></ul> |
|<br>Acesso a aplicativos| <ul><li>[Segurança em nível de linha](https://msdn.microsoft.com/library/dn765131) (Usando política de segurança, ao mesmo tempo restringindo o acesso em nível de linha com base na identidade, função ou contexto de execução do usuário).</li><li>[Máscara de Dados Dinâmicos](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-dynamic-data-masking-get-started) (Usando permissão e política, limita a exposição de dados confidenciais através do mascaramento dos dados a usuários sem privilégios)</li></ul>|
|**Monitoramento proativo**||  
| <br>Acompanhamento e detecção| <ul><li>[Auditoria](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing) rastreia eventos do banco de dados e os grava em um log de auditoria/log de atividades em sua [conta de Armazenamento do Azure](https://docs.microsoft.com/en-us/azure/storage/storage-create-storage-account).</li><li>Acompanhe a integridade do banco de dados do Azure usando [Logs de atividades do Azure Monitor](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).</li><li>[Detecção de Ameaças](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-threat-detection) detecta as atividades anormais do banco de dados que indicam possíveis ameaças de segurança ao banco de dados. </li></ul> |
|<br>Central de Segurança do Azure| <ul><li>[Monitoramento de dados](https://docs.microsoft.com/en-us/azure/security-center/security-center-enable-auditing-on-sql-databases) Use a Central de Segurança do Azure como solução de monitoramento centralizado de segurança para SQL e outros serviços do Azure.</li></ul>|     

## <a name="conclusion"></a>Conclusão
O Banco de Dados do Azure é uma plataforma robusta de banco de dados, com uma gama completa de recursos de segurança que atendem aos vários requisitos de conformidade organizacional e regulamentar. Você pode proteger facilmente dados controlando o acesso físico aos seus dados e usando uma variedade de opções de segurança de dados no nível de arquivo, coluna ou linha com Transparent Data Encryption, criptografia no nível de célula ou segurança no nível de linha . Always Encrypted também permite operações em dados criptografados, simplificando o processo de atualizações do aplicativo. Por sua vez, o acesso a logs de auditoria da atividade do Banco de Dados SQL fornece a você as informações que você precisa, permitindo que você saiba como e quando os dados são acessados.

## <a name="next-steps"></a>Próximas etapas
Você pode melhorar a proteção do banco de dados contra usuários mal-intencionados ou acesso não autorizado com apenas algumas etapas simples. Neste tutorial, você aprenderá a:

- Configurar [regras de firewall](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-firewall-configure) para seu servidor e/ou banco de dados.
- Proteger seus dados com [criptografia](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/sql-server-encryption).
- Habilitar a [auditoria do Banco de Dados SQL](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing).

