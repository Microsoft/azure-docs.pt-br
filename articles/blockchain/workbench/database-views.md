---
title: Exibições de banco de dados no Azure Blockchain Workbench
description: Visão geral do banco de dados Azure Blockchain Workbench SQL DB.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 02/21/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 06b7fb678bc79203589cfa75e8afb457d6ed344f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60866506"
---
# <a name="database-views-in-azure-blockchain-workbench"></a>Exibições de banco de dados no Azure Blockchain Workbench

Azure Workbench de Blockchain fornece dados de razão distribuído para um *banco de dados* SQL DB em cadeia. O banco de dados fora da cadeia torna possível usar o SQL e as ferramentas existentes, tais como [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017), para interagir com os dados do blockchain.

O Azure Blockchain Workbench fornece um conjunto de exibições de banco de dados que fornecem acesso a dados que poderão ser úteis ao executar as consultas. Essas exibições são extremamente desnormalizadas para tornar mais fácil de obter rapidamente iniciadas criando relatórios, análise e caso contrário consumir dados blockchain com as ferramentas existentes e sem a necessidade de treinar novamente a equipe de banco de dados.

Esta seção inclui uma visão geral das exibições de banco de dados e os dados que eles contêm.

> [!NOTE]
> Qualquer uso direto de tabelas de banco de dados encontrado no banco de dados fora dessas exibições, ao mesmo tempo possível, não é compatível.
>

## <a name="vwapplication"></a>vwApplication

Essa exibição fornece detalhes sobre **Aplicativos** que tenham sido carregados no Azure Blockchain Workbench.

| NOME                             | Type          | Pode ser nulo | DESCRIÇÃO                                                                                                                                                                                                                                                   |
|----------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                    | int           | Não           | Um identificador exclusivo para o aplicativo |
| ApplicationName                  | nvarchar (50)  | Não           | O nome do aplicativo |
| ApplicationDescription           | nvarchar (255) | Sim         | Descrição do aplicativo |
| ApplicationDisplayName           | nvarchar (255) | Não           | O nome a ser exibido em uma interface do usuário |
| ApplicationEnabled               | bit           | Não           | Identifica se o aplicativo está atualmente habilitado<br /> **Observação:** Mesmo que um aplicativo possa ser refletido como desabilitado no banco de dados, os contratos associados permanecem no blockchain e os dados sobre esses contratos permanecem no banco de dados. |
| UploadedDtTm                     | Datetime2 (7)  | Não           | A data e hora que um contrato foi carregado |
| UploadedByUserId                 | int           | Não           | A ID do usuário que carregou o aplicativo |
| UploadedByUserExternalId         | nvarchar (255) | Não           | O identificador externo para o usuário que carregou o aplicativo. Por padrão, essa ID é o usuário do Azure Active Directory para o consortium.                                                                                                |
| UploadedByUserProvisioningStatus | int           | Não           | Identifica o status atual do processo de provisionamento para o usuário. Os valores possíveis são: <br />0 – o usuário foi criado pela API<br />1 – uma chave foi sido associada ao usuário no banco de dados<br />2 – o usuário está totalmente provisionado                         |
| UploadedByUserFirstName          | nvarchar (50)  | Sim         | O nome do usuário que carregou o contrato |
| UploadedByUserLastName           | nvarchar (50)  | Sim         | O sobrenome do usuário que carregou o contrato |
| UploadedByUserEmailAddress       | nvarchar (255) | Sim         | O endereço de email do usuário que atualizou o contrato |

## <a name="vwapplicationrole"></a>vwApplicationRole

Essa exibição fornece detalhes sobre as funções que foram definidas nos aplicativos Azure Blockchain Workbench.

No aplicativo *Transferir Ativo*, por exemplo, funções como *Comprador* e *Vendedor* podem ser definidas.

| NOME                   | Type             | Pode ser nulo | DESCRIÇÃO                                       |
|------------------------|------------------|-------------|---------------------------------------------------|
| ApplicationId          | int              | Não           | Um identificador exclusivo para o aplicativo           |
| ApplicationName        | nvarchar (50)     | Não           | O nome do aplicativo                       |
| ApplicationDescription | nvarchar (255)    | Sim         | Descrição do aplicativo                  |
| ApplicationDisplayName | nvarchar (255)    | Não           | O nome a ser exibido em uma interface do usuário      |
| RoleId                 | int              | Não           | Um identificador exclusivo para a função no aplicativo |
| RoleName               | nvarchar50)      | Não           | O nome da função                              |
| RoleDescription        | descrição(255) | Sim         | Uma descrição da função                         |

## <a name="vwapplicationroleuser"></a>vwApplicationRoleUser

Essa exibição fornece detalhes sobre as funções que foram definidas nos aplicativos Azure Blockchain Workbench e os usuários associados a eles.

Em um aplicativo *Transferir ativo*, por exemplo, *John Smith* pode estar associado a função *Comprador*.

| NOME                       | Type          | Pode ser nulo | DESCRIÇÃO                                                                                                                                                                                                                           |
|----------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId              | int           | Não           | Um identificador exclusivo para o aplicativo                                                                                                                                                                                               |
| ApplicationName            | nvarchar (50)  | Não           | O nome do aplicativo                                                                                                                                                                                                           |
| ApplicationDescription     | nvarchar (255) | Sim         | Descrição do aplicativo                                                                                                                                                                                                      |
| ApplicationDisplayName     | nvarchar (255) | Não           | O nome a ser exibido em uma interface do usuário                                                                                                                                                                                          |
| ApplicationRoleId          | int           | Não           | Um identificador exclusivo para a função no aplicativo                                                                                                                                                                                     |
| ApplicationRoleName        | nvarchar50)   | Não           | O nome da função                                                                                                                                                                                                                  |
| ApplicationRoleDescription | nvarchar (255) | Sim         | Uma descrição da função                                                                                                                                                                                                             |
| UserId                     | int           | Não           | A ID do usuário associado à função |
| UserExternalId             | nvarchar (255) | Não           | O identificador externo para o usuário que está associado à função. Por padrão, essa ID é o usuário do Azure Active Directory para o consortium.                                                                     |
| UserProvisioningStatus     | int           | Não           | Identifica o status atual do processo de provisionamento para o usuário. Os valores possíveis são: <br />0 – o usuário foi criado pela API<br />1 – uma chave foi sido associada ao usuário no banco de dados<br />2 – o usuário está totalmente provisionado |
| UserFirstName              | nvarchar (50)  | Sim         | O primeiro nome do usuário que está associado à função |
| UserLastName               | nvarchar (255) | Sim         | O sobrenome do usuário que está associado à função |
| UserEmailAddress           | nvarchar (255) | Sim         | O endereço de email do usuário que está associado à função |

## <a name="vwconnectionuser"></a>vwConnectionUser

Esta exibição fornece detalhes sobre as conexões que foram definidas no Azure Blockchain Workbench e os usuários associados a elas. Para cada conexão, essa exibição contém os seguintes dados:

-   Detalhes do razão associado
-   Informações do usuário associado

| NOME                     | Type          | Pode ser nulo | DESCRIÇÃO                                                                                                                                                                                                                           |
|--------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId             | int           | Não           | O identificador exclusivo para uma conexão no Azure Blockchain Workbench |
| ConnectionEndpointUrl    | nvarchar (50)  | Não           | A URL do ponto de extremidade para uma conexão |
| ConnectionFundingAccount | nvarchar (255) | Sim         | A conta de financiamento associada à uma conexão, se aplicável |
| LedgerId                 | int           | Não           | O identificador exclusivo de um razão |
| LedgerName               | nvarchar (50)  | Não           | O nome do razão |
| LedgerDisplayName        | nvarchar (255) | Não           | Nome do razão para exibir na interface do usuário |
| UserId                   | int           | Não           | A ID do usuário associado à conexão |
| UserExternalId           | nvarchar (255) | Não           | O identificador externo para o usuário que está associado à conexão. Por padrão, essa ID é o usuário do Azure Active Directory para o consortium. |
| UserProvisioningStatus   | int           | Não           |Identifica o status atual do processo de provisionamento para o usuário. Os valores possíveis são: <br />0 – o usuário foi criado pela API<br />1 – uma chave foi sido associada ao usuário no banco de dados<br />2 – o usuário está totalmente provisionado |
| UserFirstName            | nvarchar (50)  | Sim         | O primeiro nome do usuário associado à conexão |
| UserLastName             | nvarchar (255) | Sim         | O sobrenome do usuário associado à conexão |
| UserEmailAddress         | nvarchar (255) | Sim         | O endereço de email do usuário associado à conexão |

## <a name="vwcontract"></a>vwContract

Esta exibição fornece detalhes sobre os contratos implantados. Para cada contrato, essa exibição contém os seguintes dados:

-   Definição de aplicativo associado
-   Definição de fluxo de trabalho associado
-   Implementação de razão associado à função
-   Detalhes do usuário que iniciou a ação
-   Detalhes relacionados ao bloco de blockchain e transações

| NOME                                     | Type           | Pode ser nulo | DESCRIÇÃO                                                                                                                                                                                                                                                   |
|------------------------------------------|----------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId                             | int            | Não           | O identificador exclusivo para uma conexão no Azure Blockchain Workbench.                                                                                                                                                                                         |
| ConnectionEndpointUrl                    | nvarchar (50)   | Não           | A URL do ponto de extremidade para uma conexão |
| ConnectionFundingAccount                 | nvarchar (255)  | Sim         | A conta de financiamento associada à uma conexão, se aplicável |
| LedgerId                                 | int            | Não           | O identificador exclusivo de um razão |
| LedgerName                               | nvarchar (50)   | Não           | O nome do razão |
| LedgerDisplayName                        | nvarchar (255)  | Não           | Nome do razão para exibir na interface do usuário |
| ApplicationId                            | int            | Não           | Um identificador exclusivo para o aplicativo |
| ApplicationName                          | nvarchar (50)  | Não           | O nome do aplicativo |
| ApplicationDisplayName                   | nvarchar (255) | Não           | O nome a ser exibido em uma interface do usuário |
| ApplicationEnabled                       | bit            | Não           | Identifica se o aplicativo está habilitado no momento.<br /> **Observação:** Mesmo que um aplicativo possa ser refletido como desabilitado no banco de dados, os contratos associados permanecem no blockchain e os dados sobre esses contratos permanecem no banco de dados.  |
| WorkflowId                               | int            | Não           | Um identificador exclusivo para o fluxo de trabalho associado a um contrato |
| WorkflowName                             | nvarchar (50)   | Não           | O nome do fluxo de trabalho associado a um contrato |
| WorkflowDisplayName                      | nvarchar (255)  | Não           | O nome do fluxo de trabalho associado ao contrato exibido na interface do usuário |
| WorkflowDescription                      | nvarchar (255)  | Sim         | A descrição do fluxo de trabalho associado ao contrato |
| ContractCodeId                           | int            | Não           | Um identificador exclusivo para o código de contrato associado ao contrato |
| ContractFileName                         | int            | Não           | O nome do arquivo que contém o código de contrato inteligente para este fluxo de trabalho. |
| ContractUploadedDtTm                     | int            | Não           | A data e hora que um contrato foi carregado |
| ContractId                               | int            | Não           | O identificador exclusivo para o contrato |
| ContractProvisioningStatus               | int            | Não           | Identifica o status atual do processo de provisionamento para o contrato. Os valores possíveis são: <br />0 – o contrato foi criado pela API no banco de dados<br />1 – o contrato foi enviado para o razão<br />2 – o contrato foi implantado com êxito para o razão<br />3 ou 4 - O contrato falhou ao ser implantado na razão<br />5 - O contrato foi implantado com êxito na razão <br /><br />A partir da versão 1.5, os valores de 0 a 5 têm suporte. Para compatibilidade com versões anteriores na versão atual, a exibição **vwContractV0** está disponível e dá suporte apenas a valores de 0 a 2. |
| ContractLedgerIdentifier                 | nvarchar (255) |             | O endereço de email do usuário que implantou o contrato |
| ContractDeployedByUserId                 | int            | Não           | Um identificador externo para o usuário que implantou o contrato. Por padrão, essa ID é o guid que representa a ID do Azure Active Directory para o usuário.                                                                                                          |
| ContractDeployedByUserExternalId         | nvarchar (255)  | Não           | Um identificador externo para o usuário que implantou o contrato. Por padrão, essa ID é o guid que representa a ID do Azure Active Directory para o usuário.                                                                                                         |
| ContractDeployedByUserProvisioningStatus | int            | Não           | Identifica o status atual do processo de provisionamento para o usuário. Os valores possíveis são: <br />0 – o usuário foi criado pela API<br />1 – uma chave foi sido associada ao usuário no banco de dados <br />2 – o usuário está totalmente provisionado                     |
| ContractDeployedByUserFirstName          | nvarchar (50)   | Sim         | O primeiro nome do usuário que implantou o contrato |
| ContractDeployedByUserLastName           | nvarchar (255)  | Sim         | O sobrenome do usuário que implantou o contrato |
| ContractDeployedByUserEmailAddress       | nvarchar (255)  | Sim         | O endereço de email do usuário que implantou o contrato |

## <a name="vwcontractaction"></a>vwContractAction

Esta exibição representa a maioria das informações relacionadas às ações tomadas sobre contratos e foi projetada para facilitar prontamente cenários comuns de relatórios. Para cada ação tomada, essa exibição contém os seguintes dados:

-   Definição de aplicativo associado
-   Definição de fluxo de trabalho associado
-   Função de contrato inteligente associado e definição de parâmetro
-   Implementação de razão associado à função
-   Valores de instância específica para parâmetros
-   Detalhes do usuário que iniciou a ação
-   Detalhes relacionados ao bloco de blockchain e transações

| NOME                                     | Type          | Pode ser nulo | DESCRIÇÃO                                                                                                                                                                                                                                                                                                    |
|------------------------------------------|---------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                            | int           | Não           | Um identificador exclusivo para o aplicativo |
| ApplicationName                          | nvarchar (50)  | Não           | O nome do aplicativo |
| ApplicationDisplayName                   | nvarchar (255) | Não           | O nome a ser exibido em uma interface do usuário |
| ApplicationEnabled                       | bit           | Não           | Este campo identifica se o aplicativo está habilitado no momento. Observação - mesmo que um aplicativo possa ser refletido como desabilitado no banco de dados, os contratos associados permanecem no blockchain e dados sobre esses contratos permanecem no banco de dados.                                                  |
| WorkflowId                               | int           | Não           | Um identificador exclusivo para um fluxo de trabalho |
| WorkflowName                             | nvarchar (50)  | Não           | Nome do fluxo de trabalho |
| WorkflowDisplayName                      | nvarchar (255) | Não           | O nome do fluxo de trabalho para exibir em uma interface do usuário |
| WorkflowDescription                      | nvarchar (255) | Sim         | A descrição do fluxo de trabalho |
| ContractId                               | int           | Não           | Um identificador exclusivo para o contrato |
| ContractProvisioningStatus               | int           | Não           | Identifica o status atual do processo de provisionamento para o contrato. Os valores possíveis são: <br />0 – o contrato foi criado pela API no banco de dados<br />1 – o contrato foi enviado para o razão<br />2 – o contrato foi implantado com êxito para o razão<br />3 ou 4 - O contrato falhou ao ser implantado na razão<br />5 - O contrato foi implantado com êxito na razão <br /><br />A partir da versão 1.5, os valores de 0 a 5 têm suporte. Para compatibilidade com versões anteriores na versão atual, a exibição **vwContractActionV0** está disponível e dá suporte apenas valores de 0 a 2. |
| ContractCodeId                           | int           | Não           | Um identificador exclusivo para a implementação do código do contrato |
| ContractLedgerIdentifier                 | nvarchar (255) | Sim         | Um identificador exclusivo associado à versão implantada de um contrato inteligente para um razão distribuído específico. Por exemplo, Ethereum. |
| ContractDeployedByUserId                 | int           | Não           | O identificador exclusivo do usuário que implantou o contrato |
| ContractDeployedByUserFirstName          | nvarchar (50)  | Sim         | O primeiro nome do usuário que implantou o contrato |
| ContractDeployedByUserLastName           | nvarchar (255) | Sim         | O sobrenome do usuário que implantou o contrato |
| ContractDeployedByUserExternalId         | nvarchar (255) | Não           | O identificador externo do usuário que implantou o contrato. Por padrão, essa ID é o guid que representa sua identidade no consórcio do Active Directory do Azure.                                                                                                                                                |
| ContractDeployedByUserEmailAddress       | nvarchar (255) | Sim         | O endereço de email do usuário que implantou o contrato |
| WorkflowFunctionId                       | int           | Não           | Um identificador exclusivo de uma função de fluxo de trabalho |
| WorkflowFunctionName                     | nvarchar (50)  | Não           | O nome da função |
| WorkflowFunctionName              | nvarchar (255) | Não           | O nome de uma função a ser exibido na interface do usuário |
| WorkflowFunctionDescription              | nvarchar (255) | Não           | A descrição da função |
| ContractActionId                         | int           | Não           | O identificador exclusivo para a ação do contrato |
| ContractActionProvisioningStatus         | int           | Não           | Identifica o status atual do processo de provisionamento para a ação do contrato. Os valores possíveis são: <br />0 – o contrato de ação foi criado pela API no banco de dados<br />1 – o contrato de ação foi enviado para o razão<br />2 – o contrato de ação foi implantado com êxito ao razão<br />3 ou 4 - O contrato falhou ao ser implantado na razão<br />5 - O contrato foi implantado com êxito na razão <br /><br />A partir da versão 1.5, os valores de 0 a 5 têm suporte. Para compatibilidade com versões anteriores na versão atual, a exibição **vwContractActionV0** está disponível e dá suporte apenas valores de 0 a 2. |
| ContractActionTimestamp                  | datetime(2,7) | Não           | O carimbo de hora/data da ação de contrato |
| ContractActionExecutedByUserId           | int           | Não           | Identificador exclusivo do usuário que executou a ação do contrato |
| ContractActionExecutedByUserFirstName    | int           | Sim         | Primeiro nome do usuário que executou a ação do contrato |
| ContractActionExecutedByUserLastName     | nvarchar (50)  | Sim         | Sobrenome do usuário que executou a ação do contrato |
| ContractActionExecutedByUserExternalId   | nvarchar (255) | Sim         | Identificador externo do usuário que executou a ação do contrato. Por padrão, essa ID é o guid que representa sua identidade no consórcio do Active Directory do Azure. |
| ContractActionExecutedByUserEmailAddress | nvarchar (255) | Sim         | O endereço de email do usuário que executou a ação do contrato |
| WorkflowFunctionParameterId              | int           | Não           | Um identificador exclusivo para um parâmetro da função |
| WorkflowFunctionParameterName            | nvarchar (50)  | Não           | O nome de um parâmetro da função |
| WorkflowFunctionParameterDisplayName     | nvarchar (255) | Não           | O nome de um parâmetro de função a ser exibido na interface do usuário |
| WorkflowFunctionParameterDataTypeId      | int           | Não           | O identificador exclusivo para o tipo de dados associado a um parâmetro da função de fluxo de trabalho |
| WorkflowParameterDataTypeName            | nvarchar (50)  | Não           | O nome de um tipo de dados associado a um parâmetro de função de fluxo de trabalho |
| ContractActionParameterValue             | nvarchar (255) | Não           | O valor para o parâmetro armazenado no contrato inteligente |
| BlockHash                                | nvarchar (255) | Sim         | O resumo da mensagem do bloco |
| BlockNumber                              | int           | Sim         | O número do bloco na razão |
| BlockTimestamp                           | datetime(2,7) | Sim         | O carimbo de data/hora do bloco |
| TransactionId                            | int           | Não           | Um identificador exclusivo para a transação |
| TransactionFrom                          | nvarchar (255) | Sim         | A parte que originou a transação |
| TransactionTo                            | nvarchar (255) | Sim         | A parte que foi transacionada com |
| TransactionHash                          | nvarchar (255) | Sim         | O hash de uma transação |
| TransactionIsWorkbenchTransaction        | bit           | Sim         | Um bit que identifica se a transação é uma transação do Azure Blockchain Workbench |
| TransactionProvisioningStatus            | int           | Sim         | Identifica o status atual do processo de provisionamento para a transação. Os valores possíveis são: <br />0 – a transação foi criada pela API no banco de dados<br />1 – a transação foi enviada para o razão<br />2 – a transação foi implantada com êxito ao razão                 |
| TransactionValue                         | decimal(32,2) | Sim         | O valor da transação |

## <a name="vwcontractproperty"></a>vwContractProperty

Esta exibição representa a maioria das informações relacionadas às propriedades associadas sobre um contrato e foi projetada para facilitar prontamente cenários comuns de relatórios. Para cada ação tomada, essa exibição contém os seguintes dados:

-   Definição de aplicativo associado
-   Definição de fluxo de trabalho associado
-   Detalhes do usuário que implantaram o fluxo de trabalho
-   Definição de propriedade de contrato inteligente associado
-   Valores de instância específica de propriedades
-   Detalhes da propriedade estado do contrato

| NOME                               | Type          | Pode ser nulo | DESCRIÇÃO                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | Não           | Um identificador exclusivo para o aplicativo |
| ApplicationName                    | nvarchar (50)  | Não           | O nome do aplicativo |
| ApplicationDisplayName             | nvarchar (255) | Não           | O nome a ser exibido em uma interface do usuário |
| ApplicationEnabled                 | bit           | Não           | Identifica se o aplicativo está habilitado no momento.<br />**Observação:** Mesmo que um aplicativo possa ser refletido como desabilitado no banco de dados, os contratos associados permanecem no blockchain e os dados sobre esses contratos permanecem no banco de dados.                      |
| WorkflowId                         | int           | Não           | O identificador exclusivo para o fluxo de trabalho |
| WorkflowName                       | nvarchar (50)  | Não           | Nome do fluxo de trabalho |
| WorkflowDisplayName                | nvarchar (255) | Não           | O nome do fluxo de trabalho exibido na interface do usuário |
| WorkflowDescription                | nvarchar (255) | Sim         | A descrição do fluxo de trabalho |
| ContractId                         | int           | Não           | O identificador exclusivo para o contrato |
| ContractProvisioningStatus         | int           | Não           | Identifica o status atual do processo de provisionamento para o contrato. Os valores possíveis são: <br />0 – o contrato foi criado pela API no banco de dados<br />1 – o contrato foi enviado para o razão<br />2 – o contrato foi implantado com êxito para o razão<br />3 ou 4 - O contrato falhou ao ser implantado na razão<br />5 - O contrato foi implantado com êxito na razão <br /><br />A partir da versão 1.5, os valores de 0 a 5 têm suporte. Para compatibilidade com versões anteriores na versão atual, a exibição **vwContractPropertyV0** está disponível e dá suporte apenas a valores de 0 a 2. |
| ContractCodeId                     | int           | Não           | Um identificador exclusivo para a implementação do código do contrato |
| ContractLedgerIdentifier           | nvarchar (255) | Sim         | Um identificador exclusivo associado à versão implantada de um contrato inteligente para um razão distribuído específico. Por exemplo, Ethereum. |
| ContractDeployedByUserId           | int           | Não           | O identificador exclusivo do usuário que implantou o contrato |
| ContractDeployedByUserFirstName    | nvarchar (50)  | Sim         | O primeiro nome do usuário que implantou o contrato |
| ContractDeployedByUserLastName     | nvarchar (255) | Sim         | O sobrenome do usuário que implantou o contrato |
| ContractDeployedByUserExternalId   | nvarchar (255) | Não           | O identificador externo do usuário que implantou o contrato. Por padrão, essa ID é o guid que representa sua identidade no consórcio do Active Directory do Azure |
| ContractDeployedByUserEmailAddress | nvarchar (255) | Sim         | O endereço de email do usuário que implantou o contrato |
| WorkflowPropertyId                 | int           |             | Um identificador exclusivo para uma propriedade de um fluxo de trabalho |
| WorkflowPropertyDataTypeId         | int           | Não           | A ID do tipo de dados da propriedade |
| WorkflowPropertyDataTypeName       | nvarchar (50)  | Não           | O nome do tipo de dados da propriedade |
| WorkflowPropertyName               | nvarchar (50)  | Não           | O nome da propriedade de fluxo de trabalho |
| WorkflowPropertyDisplayName        | nvarchar (255) | Não           | O nome de exibição da propriedade do fluxo de trabalho |
| WorkflowPropertyDescription        | nvarchar (255) | Sim         | Uma descrição da propriedade |
| ContractPropertyValue              | nvarchar (255) | Não           | O valor de uma propriedade no contrato |
| StateName                          | nvarchar (50)  | Sim         | Se essa propriedade contém o estado do contrato, é o nome para o estado de exibição. Se não estiver associado ao estado, o valor será nulo. |
| StateDisplayName                   | nvarchar (255) | Não           | Se essa propriedade contém o estado, ele é o nome para o estado de exibição. Se não estiver associado ao estado, o valor será nulo. |
| StateValue                         | nvarchar (255) | Sim         | Se essa propriedade contém o estado, é o valor de estado. Se não estiver associado ao estado, o valor será nulo. |

## <a name="vwcontractstate"></a>vwContractState

Esta exibição representa a maioria das informações relacionadas ao estado de um contrato específico e é projetado para facilitar prontamente os cenários de relatório comuns. Cada registro nessa exibição contém os dados a seguir:

-   Definição de aplicativo associado
-   Definição de fluxo de trabalho associado
-   Detalhes do usuário que implantaram o fluxo de trabalho
-   Definição de propriedade de contrato inteligente associado
-   Detalhes da propriedade estado do contrato

| NOME                               | Type          | Pode ser nulo | DESCRIÇÃO                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | Não           | Um identificador exclusivo para o aplicativo |
| ApplicationName                    | nvarchar (50)  | Não           | O nome do aplicativo |
| ApplicationDisplayName             | nvarchar (255) | Não           | O nome a ser exibido em uma interface do usuário |
| ApplicationEnabled                 | bit           | Não           | Identifica se o aplicativo está habilitado no momento.<br />**Observação:** Mesmo que um aplicativo possa ser refletido como desabilitado no banco de dados, os contratos associados permanecem no blockchain e os dados sobre esses contratos permanecem no banco de dados. |
| WorkflowId                         | int           | Não           | Um identificador exclusivo para o fluxo de trabalho |
| WorkflowName                       | nvarchar (50)  | Não           | Nome do fluxo de trabalho |
| WorkflowDisplayName                | nvarchar (255) | Não           | O nome exibido na interface do usuário |
| WorkflowDescription                | nvarchar (255) | Sim         | A descrição do fluxo de trabalho |
| ContractLedgerImplementationId     | nvarchar (255) | Sim         | Um identificador exclusivo associado à versão implantada de um contrato inteligente para um razão distribuído específico. Por exemplo, Ethereum. |
| ContractId                         | int           | Não           | Um identificador exclusivo para o contrato |
| ContractProvisioningStatus         | int           | Não           |Identifica o status atual do processo de provisionamento para o contrato. Os valores possíveis são: <br />0 – o contrato foi criado pela API no banco de dados<br />1 – o contrato foi enviado para o razão<br />2 – o contrato foi implantado com êxito para o razão<br />3 ou 4 - O contrato falhou ao ser implantado na razão<br />5 - O contrato foi implantado com êxito na razão <br /><br />A partir da versão 1.5, os valores de 0 a 5 têm suporte. Para compatibilidade com versões anteriores na versão atual, a exibição **vwContractStateV0** está disponível e dá suporte apenas a valores de 0 a 2. |
| ConnectionId                       | int           | Não           | Um identificador exclusivo para a instância de blockchain que o fluxo de trabalho é implantado |
| ContractCodeId                     | int           | Não           | Um identificador exclusivo para a implementação do código do contrato |
| ContractDeployedByUserId           | int           | Não           | Identificador exclusivo do usuário que implantou o contrato |
| ContractDeployedByUserExternalId   | nvarchar (255) | Não           | O identificador externo do usuário que implantou o contrato. Por padrão, essa ID é o guid que representa sua identidade no consórcio do Active Directory do Azure. |
| ContractDeployedByUserFirstName    | nvarchar (50)  | Sim         | O primeiro nome do usuário que implantou o contrato |
| ContractDeployedByUserLastName     | nvarchar (255) | Sim         | O sobrenome do usuário que implantou o contrato |
| ContractDeployedByUserEmailAddress | nvarchar (255) | Sim         | O endereço de email do usuário que implantou o contrato |
| WorkflowPropertyId                 | int           | Não           | Um identificador exclusivo para uma propriedade de fluxo de trabalho |
| WorkflowPropertyDataTypeId         | int           | Não           | A ID do tipo de dados da propriedade do fluxo de trabalho |
| WorkflowPropertyDataTypeName       | nvarchar (50)  | Não           | O nome do tipo de dados da propriedade do fluxo de trabalho |
| WorkflowPropertyName               | nvarchar (50)  | Não           | O nome da propriedade de fluxo de trabalho |
| WorkflowPropertyDisplayName        | nvarchar (255) | Não           | O nome de exibição da propriedade para exibir em uma interface do usuário |
| WorkflowPropertyDescription        | nvarchar (255) | Sim         | A descrição da propriedade |
| ContractPropertyValue              | nvarchar (255) | Não           | O valor de uma propriedade armazenada no contrato |
| StateName                          | nvarchar (50)  | Sim         | Se essa propriedade contém o estado, ele a nome de exibição para o estado. Se não estiver associado ao estado, o valor será nulo. |
| StateDisplayName                   | nvarchar (255) | Não           | Se essa propriedade contém o estado, ele é o nome para o estado de exibição. Se não estiver associado ao estado, o valor será nulo. |
| StateValue                         | nvarchar (255) | Sim         | Se essa propriedade contém o estado, é o valor de estado. Se não estiver associado ao estado, o valor será nulo. |

## <a name="vwuser"></a>vwUser

Essa exibição fornece detalhes sobre os membros do consórcio que são provisionados para usar o Azure Blockchain Workbench. Por padrão, os dados são preenchidos por meio do provisionamento inicial do usuário.

| NOME               | Type          | Pode ser nulo | DESCRIÇÃO                                                                                                                                                                                                                               |
|--------------------|---------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID                 | int           | Não           | Um identificador exclusivo de um usuário |
| ExternalId         | nvarchar (255) | Não           | Um identificador externo de um usuário. Por padrão, essa ID é o guid que representa a ID do Azure Active Directory para o usuário. |
| ProvisioningStatus | int           | Não           |Identifica o status atual do processo de provisionamento para o usuário. Os valores possíveis são: <br />0 – o usuário foi criado pela API<br />1 – uma chave foi sido associada ao usuário no banco de dados<br />2 – o usuário está totalmente provisionado |
| Nome          | nvarchar (50)  | Sim         | Primeiro nome do usuário |
| Sobrenome           | nvarchar (50)  | Sim         | O sobrenome do usuário |
| EmailAddress       | nvarchar (255) | Sim         | O endereço de email do usuário |

## <a name="vwworkflow"></a>vwWorkflow

Esta exibição representa os detalhes dos metadados do fluxo de trabalho de núcleo bem como as funções e parâmetros do fluxo de trabalho. Projetado para relatórios, também contém metadados sobre o aplicativo associado com o fluxo de trabalho. Essa exibição contém dados de várias tabelas subjacentes para facilitar a emissão de relatórios sobre fluxos de trabalho. Para cada fluxo de trabalho, essa exibição contém os seguintes dados:

-   Definição de aplicativo associado
-   Definição de fluxo de trabalho associado
-   Associado a informações de estado de início do fluxo de trabalho

| NOME                              | Type          | Pode ser nulo | DESCRIÇÃO                                                                                                                                |
|-----------------------------------|---------------|-------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                     | int           | Não           | Um identificador exclusivo para o aplicativo |
| ApplicationName                   | nvarchar (50)  | Não           | O nome do aplicativo |
| ApplicationDisplayName            | nvarchar (255) | Não           | O nome a ser exibido em uma interface do usuário |
| ApplicationEnabled                | bit           | Não           | Identifica se o aplicativo está habilitado |
| WorkflowId                        | int           | Sim         | Um identificador exclusivo para um fluxo de trabalho |
| WorkflowName                      | nvarchar (50)  | Não           | Nome do fluxo de trabalho |
| WorkflowDisplayName               | nvarchar (255) | Não           | O nome exibido na interface do usuário |
| WorkflowDescription               | nvarchar (255) | Sim         | Descrição do fluxo de trabalho. |
| WorkflowConstructorFunctionId     | int           | Não           | O identificador da função de fluxo de trabalho que serve como construtor para o fluxo de trabalho |
| WorkflowStartStateId              | int           | Não           | Um identificador exclusivo para o estado |
| WorkflowStartStateId            | nvarchar (50)  | Não           | O nome do estado |
| WorkflowStartStateDisplayName     | nvarchar (255) | Não           | O nome a ser exibido na interface do usuário para o estado |
| WorkflowStartStateDescription     | nvarchar (255) | Sim         | Uma descrição do estado do fluxo de trabalho |
| WorkflowStartStateStyle           | nvarchar (50)  | Sim         | Este valor identifica a porcentagem completa do fluxo de trabalho quando está neste estado |
| WorkflowStartStateStyle           | int           | Não           | O valor do estado |
| WorkflowStartStatePercentComplete | int           | Não           | Uma descrição de texto que fornece uma dica aos clientes sobre como renderizar esse estado na interface do usuário. Estados com suporte incluem *Sucesso* e *Falha* |

## <a name="vwworkflowfunction"></a>vwWorkflowFunction

Esta exibição representa os detalhes dos metadados do fluxo de trabalho de núcleo bem como as funções e parâmetros do fluxo de trabalho. Projetado para relatórios, também contém metadados sobre o aplicativo associado com o fluxo de trabalho. Essa exibição contém dados de várias tabelas subjacentes para facilitar a emissão de relatórios sobre fluxos de trabalho. Para cada função de fluxo de trabalho, essa exibição contém os seguintes dados:

-   Definição de aplicativo associado
-   Definição de fluxo de trabalho associado
-   Detalhes da função de fluxo de trabalho

| NOME                                 | Type          | Pode ser nulo | DESCRIÇÃO                                                                          |
|--------------------------------------|---------------|-------------|--------------------------------------------------------------------------------------|
| ApplicationId                        | int           | Não           | Um identificador exclusivo para o aplicativo |
| ApplicationName                      | nvarchar (50)  | Não           | O nome do aplicativo |
| ApplicationDisplayName               | nvarchar (255) | Não           | O nome a ser exibido em uma interface do usuário |
| ApplicationEnabled                   | bit           | Não           | Identifica se o aplicativo está habilitado |
| WorkflowId                           | int           | Não           | Um identificador exclusivo para um fluxo de trabalho |
| WorkflowName                         | nvarchar (50)  | Não           | Nome do fluxo de trabalho |
| WorkflowDisplayName                  | nvarchar (255) | Não           | O nome do fluxo de trabalho exibido na interface do usuário |
| WorkflowDescription                  | nvarchar (255) | Sim         | A descrição do fluxo de trabalho |
| WorkflowFunctionId                   | int           | Não           | Um identificador exclusivo para uma função |
| WorkflowFunctionName                 | nvarchar (50)  | Sim         | O nome da função |
| WorkflowFunctionName          | nvarchar (255) | Não           | O nome de uma função a ser exibido na interface do usuário |
| WorkflowFunctionDescription          | nvarchar (255) | Sim         | Descrição da função do fluxo de trabalho |
| WorkflowFunctionIsConstructor        | bit           | Não           | Identifica se a função de fluxo de trabalho é o construtor para o fluxo de trabalho |
| WorkflowFunctionParameterId          | int           | Não           | Um identificador exclusivo para um parâmetro de uma função |
| WorkflowFunctionParameterName        | nvarchar (50)  | Não           | O nome de um parâmetro da função |
| WorkflowFunctionParameterDisplayName | nvarchar (255) | Não           | O nome de um parâmetro de função a ser exibido na interface do usuário |
| WorkflowFunctionParameterDataTypeId  | int           | Não           | Um identificador exclusivo para o tipo de dados associado a um parâmetro de função de fluxo de trabalho |
| WorkflowParameterDataTypeName        | nvarchar (50)  | Não           | O nome de um tipo de dados associado a um parâmetro de função de fluxo de trabalho |

## <a name="vwworkflowproperty"></a>vwWorkflowProperty

Esta exibição representa as propriedades definidas para um fluxo de trabalho. Para cada ação propriedade, essa exibição contém os seguintes dados:

-   Definição de aplicativo associado
-   Definição de fluxo de trabalho associado
-   Detalhes da propriedade de fluxo de trabalho

| NOME                         | Type          | Pode ser nulo | DESCRIÇÃO                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | Não           | Um identificador exclusivo para o aplicativo |
| ApplicationName              | nvarchar (50)  | Não           | O nome do aplicativo |
| ApplicationDisplayName       | nvarchar (255) | Não           | O nome a ser exibido em uma interface do usuário |
| ApplicationEnabled           | bit           | Não           | Identifica se o aplicativo está habilitado no momento.<br />**Observação:** Mesmo que um aplicativo possa ser refletido como desabilitado no banco de dados, os contratos associados permanecem no blockchain e os dados sobre esses contratos permanecem no banco de dados. |
| WorkflowId                   | int           | Não           | Um identificador exclusivo para o fluxo de trabalho |
| WorkflowName                 | nvarchar (50)  | Não           | Nome do fluxo de trabalho |
| WorkflowDisplayName          | nvarchar (255) | Não           | O nome a ser exibido para o fluxo de trabalho em uma interface do usuário |
| WorkflowDescription          | nvarchar (255) | Sim         | Uma descrição do fluxo de trabalho |
| WorkflowPropertyId           | int           | Não           | Um identificador exclusivo para uma propriedade de um fluxo de trabalho |
| WorkflowPropertyName         | nvarchar (50)  | Não           | O nome da propriedade |
| WorkflowPropertyDescription  | nvarchar (255) | Sim         | Uma descrição da propriedade |
| WorkflowPropertyDisplayName  | nvarchar (255) | Não           | O nome a ser exibido em uma interface do usuário |
| WorkflowPropertyWorkflowId   | int           | Não           | A ID do fluxo de trabalho ao qual esta propriedade é associada |
| WorkflowPropertyDataTypeId   | int           | Não           | A ID do tipo de dados definido para a propriedade |
| WorkflowPropertyDataTypeName | nvarchar (50)  | Não           | O nome do tipo de dados definido para a propriedade |
| WorkflowPropertyIsState      | bit           | Não           | Este campo identifica se essa propriedade do fluxo de trabalho contém o estado do fluxo de trabalho |

## <a name="vwworkflowstate"></a>vwWorkflowState

Esta exibição representa as propriedades associadas ao fluxo de trabalho. Para cada contrato, essa exibição contém os seguintes dados:

-   Definição de aplicativo associado
-   Definição de fluxo de trabalho associado
-   Informações do estado do fluxo de trabalho

| NOME                         | Type          | Pode ser nulo | DESCRIÇÃO                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | Não           | Um identificador exclusivo para o aplicativo |
| ApplicationName              | nvarchar (50)  | Não           | O nome do aplicativo |
| ApplicationDisplayName       | nvarchar (255) | Não           | Descrição do aplicativo |
| ApplicationEnabled           | bit           | Não           | Identifica se o aplicativo está habilitado no momento.<br />**Observação:** Mesmo que um aplicativo possa ser refletido como desabilitado no banco de dados, os contratos associados permanecem no blockchain e os dados sobre esses contratos permanecem no banco de dados. |
| WorkflowId                   | int           | Não           | O identificador exclusivo para o fluxo de trabalho |
| WorkflowName                 | nvarchar (50)  | Não           | Nome do fluxo de trabalho |
| WorkflowDisplayName          | nvarchar (255) | Não           | O nome exibido na interface do usuário para o fluxo de trabalho |
| WorkflowDescription          | nvarchar (255) | Sim         | A descrição do fluxo de trabalho |
| WorkflowStateID              | int           | Não           | O identificador exclusivo para o estado |
| WorkflowStateName            | nvarchar (50)  | Não           | O nome do estado |
| WorkflowStateDisplayName     | nvarchar (255) | Não           | O nome a ser exibido na interface do usuário para o estado |
| WorkflowStateDescription     | nvarchar (255) | Sim         | Uma descrição do estado do fluxo de trabalho |
| WorkflowStatePercentComplete | int           | Não           | Este valor identifica a porcentagem completa do fluxo de trabalho quando está neste estado |
| WorkflowStateValue           | nvarchar (50)  | Não           | Valor do estado |
| WorkflowStateStyle           | nvarchar (50)  | Não           | Uma descrição de texto que fornece uma dica aos clientes sobre como renderizar esse estado na interface do usuário. Estados com suporte incluem *Sucesso* e *Falha* |
