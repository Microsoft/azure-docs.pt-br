---
title: Proteção de dados no Azure Stream Analytics
description: Este artigo explica como criptografar seus dados privados usados por um trabalho de Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: 102b03ad4fe247ae0abc4e2312d7027c6170333f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019458"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Proteção de dados no Azure Stream Analytics 

Azure Stream Analytics é uma plataforma como um serviço totalmente gerenciada que permite criar pipelines de análise em tempo real. Todo o trabalho pesado, como provisionamento de cluster, dimensionamento de nós para acomodar seu uso e gerenciamento de pontos de verificação internos, é gerenciado em segundo plano.

## <a name="private-data-assets-that-are-stored"></a>Ativos de dados privados que são armazenados

Azure Stream Analytics persiste os seguintes metadados e dados para serem executados: 

* Definição de consulta e sua configuração relacionada  

* Agregações ou funções definidas pelo usuário  

* Pontos de verificação necessários para o tempo de execução de Stream Analytics

* Instantâneos de dados de referência 

* Detalhes da conexão dos recursos usados pelo seu trabalho de Stream Analytics

Para ajudá-lo a atender suas obrigações de conformidade em qualquer ambiente ou setor regulamentado, você pode ler mais sobre as [ofertas de conformidade da Microsoft](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="in-region-data-residency"></a>Residência de dados de In-Region
Azure Stream Analytics armazena dados do cliente e outros metadados descritos acima. Os dados do cliente são armazenados por Azure Stream Analytics em uma única região por padrão, portanto, esse serviço atende automaticamente aos requisitos de residência de dados de região, incluindo aqueles especificados na [central de confiabilidade](https://azuredatacentermap.azurewebsites.net/).
Além disso, você pode optar por armazenar todos os ativos de dados (dados do cliente e outros metadados) relacionados ao trabalho do Stream Analytics em uma única região, criptografando-os em uma conta de armazenamento de sua escolha.

## <a name="encrypt-your-data"></a>Criptografar seus dados

Stream Analytics emprega automaticamente os melhores padrões de criptografia em toda a sua infraestrutura para criptografar e proteger seus dados. Você pode simplesmente confiar Stream Analytics para armazenar com segurança todos os seus dados para que você não precise se preocupar com o gerenciamento da infraestrutura.

Se você quiser usar chaves gerenciadas pelo cliente para criptografar seus dados, poderá usar sua própria conta de armazenamento (uso geral v1 ou v2) para armazenar quaisquer ativos de dados privados que são necessários para o tempo de execução de Stream Analytics. Sua conta de armazenamento pode ser criptografada conforme necessário. Nenhum dos seus ativos de dados privados são armazenados permanentemente pela infraestrutura de Stream Analytics. 

Essa configuração deve ser definida no momento da criação do trabalho de Stream Analytics e não pode ser modificada durante o ciclo de vida do trabalho. A modificação ou exclusão de armazenamento que está sendo usada pelo seu Stream Analytics não é recomendada. Se você excluir sua conta de armazenamento, excluirá permanentemente todos os ativos de dados privados, o que causará falha no trabalho. 

Não é possível atualizar ou alternar chaves para sua conta de armazenamento usando o portal de Stream Analytics. Você pode atualizar as chaves usando as APIs REST.


### <a name="configure-storage-account-for-private-data"></a>Configurar a conta de armazenamento para dados privados 

Criptografe sua conta de armazenamento para proteger todos os seus dados e escolha explicitamente o local dos seus dados privados. 

Para ajudá-lo a atender suas obrigações de conformidade em qualquer ambiente ou setor regulamentado, você pode ler mais sobre as [ofertas de conformidade da Microsoft](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

Use as etapas a seguir para configurar sua conta de armazenamento para ativos de dados privados. Essa configuração é feita de seu trabalho de Stream Analytics, não da sua conta de armazenamento.

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure. 

1. Selecione **análise**   >  **Stream Analytics trabalho**   na lista de resultados. 

1. Preencha a página Stream Analytics trabalho com os detalhes necessários, como nome, região e escala. 

1. Marque a caixa de seleção que diz *proteger todos os ativos de dados privados necessários para esse trabalho em minha conta de armazenamento*.

1. Selecione uma conta de armazenamento da sua assinatura. Observe que essa configuração não pode ser modificada durante o ciclo de vida do trabalho. Você também não poderá adicionar essa opção depois que o trabalho for criado.

1. Para autenticar com uma cadeia de conexão, selecione **cadeia de conexão** no menu suspenso modo de autenticação. A chave da conta de armazenamento é preenchida automaticamente de sua assinatura.

   ![Configurações de conta de armazenamento de dados particulares](./media/data-protection/storage-account-create.png)

1. Para autenticar com a identidade gerenciada (versão prévia), selecione **identidade gerenciada** no menu suspenso modo de autenticação. Se você escolher identidade gerenciada, precisará adicionar seu trabalho de Stream Analytics à lista de controle de acesso da conta de armazenamento com a função de *colaborador de dados de blob de armazenamento* . Se você não der acesso ao trabalho, o trabalho não poderá executar nenhuma operação. Para obter mais informações sobre como conceder acesso, consulte [usar o RBAC do Azure para atribuir um acesso de identidade gerenciada a outro recurso](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md#use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource).

   :::image type="content" source="media/data-protection/storage-account-create-msi.png" alt-text="Configurações de conta de armazenamento de dados particulares com autenticação de identidade gerenciada":::

## <a name="private-data-assets-that-are-stored-by-stream-analytics"></a>Ativos de dados privados armazenados por Stream Analytics

Todos os dados privados que precisam ser persistidos pelo Stream Analytics são armazenados em sua conta de armazenamento. Exemplos de ativos de dados privados incluem: 

* Consultas que você criou e suas configurações relacionadas  

* Funções definidas pelo usuário 

* Pontos de verificação necessários para o tempo de execução de Stream Analytics

* Instantâneos de dados de referência 

Os detalhes de conexão de seus recursos, que são usados pelo seu trabalho de Stream Analytics, também são armazenados. Criptografe sua conta de armazenamento para proteger todos os seus dados. 

Para ajudá-lo a atender suas obrigações de conformidade em qualquer ambiente ou setor regulamentado, você pode ler mais sobre as [ofertas de conformidade da Microsoft](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="enables-data-residency"></a>Habilita a residência de dados 
Você pode usar esse recurso para impor quaisquer requisitos de residência de dados que você possa ter fornecendo uma conta de armazenamento de acordo.

## <a name="known-issues"></a>Problemas conhecidos
Há um problema conhecido em que um trabalho que usa a chave gerenciada pelo cliente é executado em falhas ao usar a identidade gerenciada para se autenticar em qualquer entrada ou saída. 

## <a name="next-steps"></a>Próximas etapas

* [Crie uma conta de Armazenamento do Azure](../storage/common/storage-account-create.md)
* [Entender as entradas para o Azure Stream Analytics](stream-analytics-add-inputs.md)
* [Conceitos de ponto de verificação e de reprodução em trabalhos do Azure Stream Analytics](stream-analytics-concepts-checkpoint-replay.md)
* [Usar dados de referência para pesquisas no Stream Analytics](stream-analytics-use-reference-data.md)
