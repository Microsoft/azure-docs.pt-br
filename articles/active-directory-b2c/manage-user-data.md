---
title: Gerenciar dados dos usuários no Azure Active Directory B2C | Microsoft Docs
description: Saiba como excluir ou exportar dados de usuário no Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/06/2018
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: d9bdf7258296b82e65e03f6b8af8021b9a7be0f0
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94952464"
---
# <a name="manage-user-data-in-azure-active-directory-b2c"></a>Gerenciar dados dos usuários no Azure Active Directory B2C

 Este artigo discute como você pode gerenciar os dados do usuário em Azure Active Directory B2C (Azure AD B2C) usando as operações fornecidas pela [API do Microsoft Graph](/graph/use-the-api). Gerenciamento de dados de usuário inclui excluindo ou exportando dados de logs de auditoria.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>Excluir dados de usuário

Os dados de usuário são armazenados no diretório do Azure AD B2C e nos logs de auditoria. Todos os dados de auditoria de usuário são mantidos por 7 dias em Azure AD B2C. Se você quiser excluir os dados do usuário dentro desse período de 7 dias, poderá usar a operação [excluir um usuário](/graph/api/user-delete) . É necessária uma operação de exclusão para cada um dos locatários do Azure AD B2C onde os dados podem residir.

A cada usuário no Azure AD B2C é atribuído uma ID de objeto. A ID de objeto fornece um identificador não ambíguo usado para excluir dados de usuário no Azure AD B2C. Dependendo da arquitetura, a ID de objeto pode ser um identificador de correlação útil em outros serviços, como finanças, marketing e bancos de dados de gerenciamento de relacionamento com o cliente.

A maneira mais precisa obter a ID de objeto para um usuário é obtê-la como parte de uma viagem de autenticação com o Azure AD B2C. Se você receber uma solicitação válida para dados de um usuário usando outros métodos, um processo offline, como uma pesquisa por um agende de serviço de atendimento, talvez seja necessário localizar o usuário e anotar a ID de objeto associada.

O exemplo a seguir mostra um fluxo de exclusão de dados possível:

1. O usuário faz logon e seleciona **Excluir meus dados**.
2. O aplicativo oferece uma opção para excluir os dados dentro de uma seção de administração do aplicativo.
3. O aplicativo força uma autenticação do Azure AD B2C. B2C do Azure Active Directory fornece um token com a ID de objeto do usuário de volta para o aplicativo.
4. O token é recebido pelo aplicativo e a ID do objeto é usada para excluir os dados do usuário por meio de uma chamada para a API do Microsoft Graph. A API Microsoft Graph exclui os dados do usuário e retorna um código de status de 200 OK.
5. O aplicativo orquestra a exclusão dos dados de usuário em outros sistemas organizacionais conforme necessário usando a ID de objeto ou outros identificadores.
6. O aplicativo confirma a exclusão dos dados e fornece as próximas etapas para o usuário.

## <a name="export-customer-data"></a>Exportar dados do cliente

O processo para exportar os dados do cliente do Azure AD B2C é semelhante ao processo de exclusão.

Os dados de usuário do Azure AD B2C são limitados a:

- **Dados armazenados no Azure Active Directory**:Você pode recuperar dados em um percurso do usuário de autenticação no Azure AD B2C usando a ID de objeto ou qualquer nome de entrada, como um endereço de email ou nome de usuário.
- **Relatório de eventos de auditoria específicas do usuário**: Você pode indexar os dados usando a ID de objeto.

No exemplo a seguir de uma exportação de fluxo de dados, as etapas descritas como sendo executadas pelo aplicativo também podem ser executadas por um processo de back-end ou por um usuário com uma função de administrador no Diretório:

1. O usuário entra no alicativo. Azure AD B2C impõe a autenticação com a autenticação multifator do Azure AD, se necessário.
2. O aplicativo usa as credenciais do usuário para chamar uma Microsoft Graph operação da API para recuperar os atributos do usuário. A API de Microsoft Graph fornece os dados de atributo no formato JSON. Dependendo do esquema, você pode configurar o conteúdo do token de identificação para incluir todos os dados pessoais de um usuário.
3. O aplicativo recupera a atividade de auditoria do usuário. A API de Microsoft Graph fornece os dados de evento para o aplicativo.
4. O aplicativo agrega os dados e os torna disponíveis para o usuário.

## <a name="next-steps"></a>Próximas etapas

Para aprender a gerenciar como os usuários podem acessar seu aplicativo, consulte [Gerenciar o acesso do usuário](manage-user-access.md).