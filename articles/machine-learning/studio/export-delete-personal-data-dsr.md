---
title: Explore e exclua seus dados
titleSuffix: Azure Machine Learning Studio
description: Os dados armazenados no produto pelo Azure Machine Learning Studio estão disponíveis para exportação e exclusão por meio do portal do Azure e também por meio de APIs REST autenticadas. Os dados de telemetria podem ser acessados através do Portal de Privacidade do Azure. Este artigo mostra como fazer isso.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 05/25/2018
ms.openlocfilehash: 827714fea9618724ef058e1f76dc099f692482bc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60750085"
---
# <a name="export-and-delete-in-product-user-data-from-azure-machine-learning-studio"></a>Exportar e excluir dados do usuário no produto do Azure Machine Learning Studio

É possível excluir ou exportar dado armazenados no produto pelo Azure Machine Learning Studio usando o portal do Azure, a interface do Studio, o PowerShell e APIs REST autenticadas. Este artigo informa como fazer isso. 

Os dados de telemetria podem ser acessados através do portal de Privacidade do Azure. 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-collect"></a>Que tipos de dados do usuário o Studio coleta?

Para este serviço, os dados do usuário consistem em informações sobre usuários autorizados a acessar workspaces e registros de telemetria das interações do usuário com o serviço.

Há dois tipos de dados do usuário no Machine Learning Studio:
- **Dados de conta pessoal:** IDs de conta e endereços de email associados a uma conta.
- **Dados do cliente:** dados carregados para análise.

## <a name="studio-account-types-and-how-data-is-stored"></a>Tipos de conta do Studio e como os dados são armazenados

Há três tipos de contas no Machine Learning Studio. O tipo de conta que você tem determina como os dados são armazenados e como é possível excluí-los ou exportá-los.

- Um **workspace de convidado** é uma conta anônima gratuita. Você inscreve-se sem fornecer credenciais como um endereço de email ou senha.
    -  Os dados serão limpos após a expiração do workspace de convidado.
    - Os usuários convidados podem exportar dados do cliente por meio da interface do usuário, APIs REST ou pacote do PowerShell.
- Um **workspace gratuito** é uma conta gratuita com a qual você entra com as credenciais da conta da Microsoft - um endereço de email e senha.
    - É possível exportar e excluir dados pessoais e do cliente, que estão sujeitos a solicitações de DSR (direitos de entidades de dados).
    - É possível exportar dados do cliente por meio da interface do usuário, APIs REST ou pacote do PowerShell.
    - Para workspaces gratuitos que não usam contas do Azure AD, os dados telemétricos podem ser exportados usando o Portal de Privacidade.
    - Ao excluir o workspace, todos os dados pessoais do cliente serão excluídos.
- Um **workspace padrão** é uma conta paga que você acessa com credenciais de entrada.
    - É possível exportar e excluir dados pessoais e do cliente, que estão sujeitos a solicitações de DSR.
    - É possível acessar dados através do portal de privacidade do Azure
    - É possível exportar dados pessoais e de clientes por meio da interface do usuário, APIs REST ou pacote do PowerShell
    - É possível excluir os dados no portal do Azure.

## <a name="delete"></a>Excluir dados do workspace no Studio 

### <a name="delete-individual-assets"></a>Excluir recursos individuais

Os usuários podem excluir ativos em um workspace, selecionando-os e, em seguida, clicando no botão Excluir.

![Excluir ativos no Machine Learning Studio](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>Excluir um workspace inteiro

Os usuários também podem excluir o workspace inteiro:
- Workspace pago: excluir usando o portal do Azure.
- Workspace gratuito: use o botão Excluir no painel **Configurações**.

![Excluir um workspace gratuito no Machine Learning Studio](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-data-with-powershell"></a>Exportar dados do Studio com o PowerShell
Use o PowerShell para exportar todas as informações para um formato portátil do Azure Machine Learning Studio usando comandos. Para obter mais informações, confira o artigo [Módulo do PowerShell para Azure Machine Learning Studio](powershell-module.md).

## <a name="next-steps"></a>Próximas etapas

Para ter acesso a documentação que abrange serviços Web e cobrança do plano de compromisso, confira [Referência da API REST do Azure Machine Learning Studio](https://docs.microsoft.com/rest/api/machinelearning/). 
