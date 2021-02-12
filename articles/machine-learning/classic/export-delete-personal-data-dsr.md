---
title: 'ML Studio (clássico): exportar & excluir seus dados-Azure'
description: Os dados no produto armazenados por Azure Machine Learning Studio (clássico) estão disponíveis para exportação e exclusão por meio do portal do Azure e também por meio de APIs REST autenticadas. Os dados de telemetria podem ser acessados através do Portal de Privacidade do Azure. Este artigo mostra como fazer isso.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 05/25/2018
ms.openlocfilehash: 550603b9315c1abaa025aac804afc4fe674159d2
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93310013"
---
# <a name="export-and-delete-in-product-user-data-from-azure-machine-learning-studio-classic"></a>Exportar e excluir dados de usuário no produto do Azure Machine Learning Studio (clássico)

**APLICA-SE A:**  ![Aplica-se a.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (clássico) ![Não se aplica a. ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)




Você pode excluir ou exportar dados no produto armazenados por Azure Machine Learning Studio (clássico) usando o portal do Azure, a interface do Studio (clássico), o PowerShell e as APIs REST autenticadas. Este artigo informa como fazer isso. 

É possível acessar os dados telemétricos por meio do Portal de Privacidade do Azure. 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-classic-collect"></a>Que tipos de dados do usuário são coletados pelo estúdio (clássico)?

Para este serviço, os dados do usuário consistem em informações sobre usuários autorizados a acessar workspaces e registros de telemetria das interações do usuário com o serviço.

Há dois tipos de dados de usuário em Machine Learning Studio (clássico):
- **Dados da conta pessoal:** IDs de conta e endereços de email associados a uma conta.
- **Dados do cliente:** dados carregados para análise.

## <a name="studio-classic-account-types-and-how-data-is-stored"></a>Tipos de conta do Studio (clássico) e como os dados são armazenados

Há três tipos de contas no Machine Learning Studio (clássico). O tipo de conta que você tem determina como os dados são armazenados e como é possível excluí-los ou exportá-los.

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

## <a name="delete-workspace-data-in-studio-classic"></a><a name="delete"></a>Excluir dados do espaço de trabalho no estúdio (clássico) 

### <a name="delete-individual-assets"></a>Excluir recursos individuais

Os usuários podem excluir ativos em um workspace, selecionando-os e, em seguida, clicando no botão Excluir.

![Excluir ativos em Machine Learning Studio (clássico)](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>Excluir um workspace inteiro

Os usuários também podem excluir o workspace inteiro:
- Workspace pago: exclua através do portal do Azure.
- Workspace gratuito: use o botão Excluir no painel **Configurações**.

![Excluir um espaço de trabalho gratuito no Machine Learning Studio (clássico)](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-classic-data-with-powershell"></a>Exportar dados do estúdio (clássico) com o PowerShell
Use o PowerShell para exportar todas as suas informações para um formato portátil do Azure Machine Learning Studio (clássico) usando comandos. Para obter informações, consulte o artigo [módulo do PowerShell para Azure Machine Learning Studio (clássico)](powershell-module.md) .

## <a name="next-steps"></a>Próximas etapas

Para obter a documentação que aborda os serviços Web e a cobrança do plano de compromisso, consulte [Azure Machine Learning Studio (clássico) referência da API REST](/rest/api/machinelearning/).