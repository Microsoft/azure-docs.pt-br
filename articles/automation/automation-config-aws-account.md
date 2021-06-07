---
title: Autenticar runbooks da Automação do Azure com a Amazon Web Services
description: Este artigo informa como autenticar runbooks com a Amazon Web Services.
keywords: autenticação aws, configurar aws
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: acb056fb959fda320a14059e2b36a0f5e3fb0b37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "83837171"
---
# <a name="authenticate-runbooks-with-amazon-web-services"></a>Autenticar runbooks com a Amazon Web Services

A automação de tarefas comuns com recursos do AWS (Amazon Web Services) pode ser realizada com runbooks de Automação no Azure. Você pode automatizar muitas tarefas no AWS usando runbooks de Automação, assim como faz com recursos no Azure. Para autenticação, você deve ter uma assinatura do Azure.

## <a name="obtain-aws-subscription-and-credentials"></a>Obter uma assinatura do AWS e credenciais

Para autenticar com o AWS, você deve obter uma assinatura do AWS e especificar um conjunto de credenciais do AWS para autenticar seus runbooks em execução na Automação do Azure. Credenciais específicas necessárias são a chave secreta e a chave de acesso do AWS. Consulte [Usar credenciais do AWS](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).

## <a name="configure-automation-account"></a>Configurar conta de Automação

Você pode usar uma conta de automação existente para autenticar com o AWS. Como alternativa, você pode dedicar uma conta para runbooks direcionados a recursos do AWS. Nesse caso, crie uma nova [conta de automação](automation-create-standalone-account.md).  

## <a name="store-aws-credentials"></a>Armazenar credenciais do AWS

Você deve armazenar as credenciais do AWS como ativos na Automação do Azure. Consulte [Gerenciar chaves de acesso da conta do AWS](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) para obter instruções sobre como criar a chave de acesso e a chave secreta. Quando as chaves estiverem disponíveis, copie a ID da chave de acesso e a ID da chave secreta em um local seguro. Você pode baixar o arquivo de chave para armazená-lo em algum lugar seguro.

## <a name="create-credential-asset"></a>Criar ativo de credencial

Depois de criar e copiar as chaves de segurança do AWS, crie um ativo de credencial com a conta de automação. O ativo permite armazenar com segurança as chaves do AWS e fazer referência a elas em seus runbooks. Consulte [Criar um novo ativo de credencial com o portal do Azure](shared-resources/credentials.md#create-a-new-credential-asset-with-the-azure-portal). Insira as seguintes informações sobre o AWS nos campos fornecidos:
    
* **Nomeie** - **AWScred** ou um valor apropriado seguindo seus padrões de nomenclatura
* **Nome de usuário** - sua ID de acesso
* **Senha** - nome da sua chave secreta 

## <a name="next-steps"></a>Próximas etapas

* Para saber como criar runbooks para automatizar tarefas no AWS, consulte [Implantar uma VM da Amazon Web Services com um runbook](automation-scenario-aws-deployment.md).