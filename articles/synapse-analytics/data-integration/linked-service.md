---
title: Proteger um serviço vinculado
description: Saiba como provisionar e proteger um serviço vinculado com VNet gerenciada
services: synapse-analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: be7ee2f4b2b8472c5edeff63e143d99c958bfc5a
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627210"
---
# <a name="secure-a-linked-service-with-private-links"></a>Proteger um serviço vinculado com links privados

Neste artigo, você aprenderá a proteger um serviço vinculado no Synapse com um ponto de extremidade privado.

## <a name="prerequisites"></a>Pré-requisitos

* **Assinatura do Azure**: Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* **Conta de Armazenamento do Azure**: Use o Azure Data Lake Gen 2 como uma *fonte* de armazenamento de dados. Se você não tiver uma conta de armazenamento, consulte [Criar uma conta de armazenamento do Azure](../../storage/common/storage-account-create.md) para obter as etapas para criar uma. Verifique se a conta de armazenamento tem a filtragem de IP do Synapse Studio para acessá-la e se você só permite que as **redes selecionadas** acessem a conta de armazenamento. A configuração na folha **firewalls e redes virtuais** deve ser parecida com a imagem abaixo.

![Conta de armazenamento protegida](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>Criar um serviço vinculado com links privados

No Azure Synapse Analytics, um serviço vinculado é onde você define as informações de conexão com outros serviços. Nesta seção, você adicionará o Azure Synapse Analytics e o Azure Data Lake Gen 2 como serviços vinculados.

1. Abra o Azure Synapse Studio e vá para a guia **gerenciar** .
1. Em **Conexões externas**, selecione **Serviços vinculados**.
1. Para adicionar um serviço vinculado, selecione **Novo**.
1. Selecione o bloco Azure Data Lake Storage Gen2 na lista e selecione **continuar**.
1. Lembre-se de habilitar **Criação Interativa**. Pode levar cerca de um minuto para habilitar essa opção. 
1. Insira as credenciais de autenticação. A chave de conta, a entidade de serviço e a identidade gerenciada são tipos de autenticação atualmente suportados. Selecione testar conexão para verificar se suas credenciais estão corretas.
1. Selecione **testar conexão**, ele deve falhar porque a conta de armazenamento não habilita o acesso a ele sem a criação e a aprovação de um ponto de extremidade privado. Na mensagem de erro, você deve ver um link para criar um **ponto de extremidade privado** que você pode seguir para ir para a próxima parte. Se você seguir esse link, ignore a próxima parte.
1. Selecione **Criar** quando terminar.

## <a name="create-a-managed-private-endpoint"></a>Criar um ponto de extremidade privado gerenciado

Se você não selecionou o hiperlink ao testar a conexão acima, siga o caminho a seguir. Crie um ponto de extremidade privado gerenciado que você se conectará ao serviço vinculado criado acima.

1. Acesse a guia **Gerenciar**.
1. Vá para a seção **redes virtuais gerenciadas** .
1. Selecione **+ novo** em ponto de extremidade privado gerenciado.
1. Selecione o bloco Azure Data Lake Storage Gen2 na lista e selecione **continuar**.
1. Insira o nome da conta de armazenamento criada acima.
1. Escolha **Criar**
1. Depois de aguardar alguns segundos, você verá que o link privado criado precisa de uma aprovação.

## <a name="private-link-approval"></a>Aprovação de link particular
1. Selecione o ponto de extremidade privado criado acima. Você pode ver um hiperlink que permitirá aprovar o ponto de extremidade privado no nível da conta de armazenamento. *Uma alternativa é ir diretamente para a conta de armazenamento portal do Azure e ir para a folha **conexões de ponto de extremidade privado** .*
1. Marque o ponto de extremidade privado que você criou no estúdio e selecione **aprovar**.
1. Adicione uma descrição e selecione **Sim**
1. Volte para o Synapse Studio na seção **redes virtuais gerenciadas** da guia **gerenciar** .
1. Deve levar cerca de 1 minuto para que a aprovação seja refletida para seu ponto de extremidade particular.

## <a name="check-the-connection-works"></a>Verificar a conexão funciona
1. Vá para a guia **gerenciar** e selecione o serviço vinculado que você criou.
1. Verifique se a **criação interativa** está ativa.
1. Selecione **Testar conexão**. Você verá que a conexão foi bem-sucedida.

Agora você estabeleceu uma conexão segura e privada entre o Synapse e o serviço vinculado.

## <a name="next-steps"></a>Próximas etapas


Para desenvolver mais conhecimento do ponto de extremidade privado gerenciado na análise de Synapse do Azure, consulte [pontos de extremidades privados gerenciados](../security/synapse-workspace-managed-private-endpoints.md).


Para obter mais informações sobre a integração de dados para o Azure Synapse Analytics, consulte o artigo [ingerir dados em um data Lake](data-integration-data-lake.md) .