---
title: Como configurar um ponto de extremidade privado-QnA Maker
description: Entenda a criação de ponto de extremidade particular disponível no QnA Maker gerenciado.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/12/2021
ms.openlocfilehash: 5a7ddcf25993ab4ce87bdc47c5e0ae32fa24b07f
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956455"
---
# <a name="private-endpoints"></a>Pontos de extremidade privados

O ponto de extremidade privado do Azure é uma interface de rede que conecta você de forma privada e segura a um serviço com tecnologia do Link Privado do Azure. Agora, QnA Maker fornece suporte para criar pontos de extremidade privados para o serviço Azure Search. Essa funcionalidade está disponível em QnA Maker gerenciados. 

Pontos de extremidade privados são fornecidos pelo [link privado do Azure](https://docs.microsoft.com/azure/private-link/private-link-overview), como um serviço separado. Para obter mais informações sobre os custos, consulte a [página de preços.](https://azure.microsoft.com/pricing/details/private-link/) 

## <a name="prerequisites"></a>Pré-requisitos
> [!div class="checklist"]
> * Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.
> * Um QnA Maker [recurso gerenciado](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) criado no portal do Azure. Lembre-se de sua ID e sua assinatura do Azure Active Directory, bem como do nome do recurso do QnA que você selecionou ao criar o recurso.

## <a name="steps-to-enable-private-endpoint"></a>Etapas para habilitar o ponto de extremidade privado
1. Atribua a função do *Contributor* para QnA Maker serviço gerenciado na instância do serviço de Azure Search. Esta operação requer acesso de *proprietário* à assinatura. Vá para a guia identidade no recurso de serviço para obter a identidade.
![Identidade de serviço gerenciada](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-identity.png)

2. Adicione a identidade acima como *Contributor* acessando Azure Search guia iam do serviço. ![ IAM de serviço gerenciado](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-access-control.png)

3. Clique em *Adicionar atribuições de função*, adicione a identidade e clique em *salvar*.
![Atribuição de função gerenciada](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-role-assignment.png)

4. Agora, vá para a guia *rede* na instância do serviço de Azure Search e alterne dados de conectividade de ponto de extremidade de *público* para *privado*. Esta operação é um processo de execução longa e pode levar até 30 minutos para ser concluída. 
![Rede gerenciada do Azure Search](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking.png)

5. Vá para a guia *rede* de QnA Maker serviço gerenciado e, na opção *permitir acesso de*, selecione as *redes selecionadas e pontos de extremidade particulares* e clique em *salvar*. 
![Newtorking do QnA Maker gerenciado](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-2.png)

Isso estabelecerá uma conexão de ponto de extremidade privada entre o serviço do QnA Maker e a instância do serviço de pesquisa cognitiva do Azure. Você pode verificar a conexão de ponto de extremidade privado na guia *rede* da instância do serviço Azure Search. Quando a operação inteira for concluída, você será bom usar seu serviço de QnA Maker. 
![Serviço de rede gerenciado](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-3.png)


## <a name="support-details"></a>Detalhes do suporte
 * Não há suporte para alterar Azure Search serviço depois de habilitar o acesso privado ao serviço QnAMaker. Se você alterar o serviço de Azure Search por meio da guia ' configuração ' depois de habilitar o acesso privado, o serviço QnAMaker ficará inutilizável.
 * Depois de estabelecer a conexão de ponto de extremidade privado, se você alternar Azure Search rede de serviço para ' pública ', não será possível usar o serviço QnAMaker. Azure Search rede de serviço precisa ser ' particular ' para que a conexão de ponto de extremidade particular funcione
