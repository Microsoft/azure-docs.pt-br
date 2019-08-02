---
title: Gerenciar dados dos usuários no Azure Active Directory B2C | Microsoft Docs
description: Saiba como excluir ou exportar dados de usuário no Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/06/2018
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 4cbca467b50dd0e43132b6d09dc0785c501fca0f
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204691"
---
# <a name="manage-user-data-in-azure-active-directory-b2c"></a>Gerenciar dados dos usuários no Azure Active Directory B2C

 Este artigo discute sobre como você pode gerenciar os dados de usuário no Azure Active Directory (Azure AD) B2C usando as operações fornecidas pelo [API do Graph do Azure Active Directory](/previous-versions/azure/ad/graph/api/api-catalog). Gerenciamento de dados de usuário inclui excluindo ou exportando dados de logs de auditoria.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>Excluir dados de usuário

Os dados de usuário são armazenados no diretório do Azure AD B2C e nos logs de auditoria. Todos os dados de auditoria de usuário é retido por sete dias no Azure AD B2C. Se você quiser excluir dados de usuário dentro desse período de 7 dias, você pode usar o [excluir um usuário](/previous-versions/azure/ad/graph/api/users-operations#DeleteUser) operação. É necessária uma operação de exclusão para cada um dos locatários do Azure AD B2C onde os dados podem residir. 

A cada usuário no Azure AD B2C é atribuído uma ID de objeto. A ID de objeto fornece um identificador não ambíguo usado para excluir dados de usuário no Azure AD B2C. Dependendo da arquitetura, a ID de objeto pode ser um identificador de correlação útil em outros serviços, como finanças, marketing e bancos de dados de gerenciamento de relacionamento com o cliente. 

A maneira mais precisa obter a ID de objeto para um usuário é obtê-la como parte de uma viagem de autenticação com o Azure AD B2C. Se você receber uma solicitação válida para dados de um usuário usando outros métodos, um processo offline, como uma pesquisa por um agende de serviço de atendimento, talvez seja necessário localizar o usuário e anotar a ID de objeto associada. 

O exemplo a seguir mostra um fluxo de exclusão de dados possível:

1. O usuário faz logon e seleciona **Excluir meus dados**.
2. O aplicativo oferece uma opção para excluir os dados dentro de uma seção de administração do aplicativo.
3. O aplicativo força uma autenticação do Azure AD B2C. B2C do Azure Active Directory fornece um token com a ID de objeto do usuário de volta para o aplicativo. 
4. O token é recebido pelo aplicativo e a ID do objeto é usada para excluir os dados de usuário por meio de uma chamada à API do Graph do Azure Active Directory. A API do Graph do Azure Active Directory exclui os dados de usuário e retorna um código de status 200 OK.
5. O aplicativo orquestra a exclusão dos dados de usuário em outros sistemas organizacionais conforme necessário usando a ID de objeto ou outros identificadores.
6. O aplicativo confirma a exclusão dos dados e fornece as próximas etapas para o usuário.

## <a name="export-customer-data"></a>Exportar dados do cliente

O processo para exportar os dados do cliente do Azure AD B2C é semelhante ao processo de exclusão.

Os dados de usuário do Azure AD B2C são limitados a:

- **Dados armazenados no Azure Active Directory**: Você pode recuperar dados em um percurso do usuário de autenticação no Azure AD B2C usando a ID de objeto ou qualquer nome de entrada, como um endereço de email ou nome de usuário. 
- **Relatório de eventos de auditoria específico do usuário**: Você pode indexar dados usando a ID de objeto.

No exemplo a seguir de uma exportação de fluxo de dados, as etapas descritas como sendo executadas pelo aplicativo também podem ser executadas por um processo de back-end ou por um usuário com uma função de administrador no Diretório:

1. O usuário entra no alicativo. O Azure AD B2C impõe a autenticação com autorização multifator do Azure se necessário.
2. O aplicativo usa as credenciais do usuário para chamar uma operação de API do Graph do Azure AD para recuperar os atributos de usuário. A API do Graph do Azure AD fornece os dados de atributo no formato JSON. Dependendo do esquema, você pode configurar o conteúdo do token de identificação para incluir todos os dados pessoais de um usuário.
3. O aplicativo recupera a atividade de auditoria do usuário. A API do Graph do Azure AD fornece os dados de evento para o aplicativo.
4. O aplicativo agrega os dados e os torna disponíveis para o usuário.

## <a name="next-steps"></a>Próximas etapas

- Para aprender a gerenciar como os usuários podem acessar seu aplicativo, consulte [Gerenciar o acesso do usuário](manage-user-access.md).




