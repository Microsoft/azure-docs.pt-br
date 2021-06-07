---
title: Como configurar Azure Functions com uma rede virtual
description: Artigo que mostra como executar determinadas tarefas de rede virtual para Azure Functions.
ms.topic: conceptual
ms.date: 3/13/2021
ms.custom: template-how-to
ms.openlocfilehash: a28a59a0de40bba7914d1920b42034fbbc223ddc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608949"
---
# <a name="how-to-configure-azure-functions-with-a-virtual-network"></a>Como configurar Azure Functions com uma rede virtual

Este artigo mostra como executar tarefas relacionadas à configuração de seu aplicativo de funções para se conectar e executar em uma rede virtual. Para saber mais sobre Azure Functions e rede, consulte [Azure Functions opções de rede](functions-networking-options.md).

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>Restringir a sua conta de armazenamento a uma rede virtual 

Quando você cria um aplicativo de funções, é necessário criar ou vincular uma conta de Armazenamento do Azure de uso geral que dá ao armazenamento de Tabelas, Blobs e Filas. Você pode substituir essa conta de armazenamento por uma que seja protegida por pontos de extremidade de serviço ou ponto de extremidades particular. 

> [!NOTE]  
> Esse recurso atualmente funciona para todas as SKUs com suporte da rede virtual do Windows no plano dedicado (serviço de aplicativo) e para planos Premium. Não há suporte para o plano de consumo. 

Para configurar uma função com uma conta de armazenamento restrita a uma rede privada:

1. Crie uma função com uma conta de armazenamento que não tenha pontos de extremidade de serviço habilitados.

1. Configure a função para se conectar à sua rede virtual.

1. Crie ou configure uma conta de armazenamento diferente.  Essa será a conta de armazenamento que protegemos com os pontos de extremidade de serviço e conectamos nossa função.

1. [Crie um compartilhamento de arquivos](../storage/files/storage-how-to-create-file-share.md#create-file-share) na conta de armazenamento protegida.

1. Habilite os pontos de extremidade de serviço ou o Endpoint particular para a conta de armazenamento.  
    * Se você estiver usando conexões de ponto de extremidade privado, a conta de armazenamento precisará de um ponto de extremidade privado para os `file` `blob` subrecursos e.  Se estiver usando determinados recursos como Durable Functions, você também precisará `queue` e poderá `table` ser acessado por meio de uma conexão de ponto de extremidade privada.
    * Se estiver usando pontos de extremidade de serviço, habilite a sub-rede dedicada para seus aplicativos de funções para contas de armazenamento.

1. Copie o conteúdo do arquivo e do blob da conta de armazenamento do aplicativo de funções para a conta de armazenamento protegida e o compartilhamento de arquivos.

1. Copie a cadeia de conexão para esta conta de armazenamento.

1. Atualize as **configurações do aplicativo** em **configuração** para o aplicativo de funções para o seguinte:

    | Nome da configuração | Valor | Comentário |
    |----|----|----|
    | `AzureWebJobsStorage`| Cadeia de conexão de armazenamento | Esta é a cadeia de conexão para uma conta de armazenamento protegida. |
    | `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` |  Cadeia de conexão de armazenamento | Esta é a cadeia de conexão para uma conta de armazenamento protegida. |
    | `WEBSITE_CONTENTSHARE` | Compartilhamento de arquivo | O nome do compartilhamento de arquivos criado na conta de armazenamento protegido em que residem os arquivos de implantação do projeto. |
    | `WEBSITE_CONTENTOVERVNET` | 1 | Nova configuração |
    | `WEBSITE_VNET_ROUTE_ALL` | 1 | Força todo o tráfego de saída por meio da rede virtual. Necessário quando a conta de armazenamento estiver usando conexões de ponto de extremidade privadas. |
    | `WEBSITE_DNS_SERVER` | `168.63.129.16` | O servidor DNS usado pelo aplicativo. Necessário quando a conta de armazenamento estiver usando conexões de ponto de extremidade privadas. |

1. Selecione **salvar** para salvar as configurações do aplicativo. Alterar as configurações do aplicativo faz com que o aplicativo seja reiniciado.  

Depois que o aplicativo de funções for reiniciado, ele agora estará conectado a uma conta de armazenamento protegida.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Opções de rede do Azure Functions](functions-networking-options.md)

