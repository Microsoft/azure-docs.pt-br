---
title: Solução de problemas – QnAMaker
titleSuffix: Azure Cognitive Services
description: O QnAMaker inclui componentes hospedados na conta do usuário do Azure. A depuração pode exigir que os usuários manipulem os recursos do QnAMaker Azure ou forneçam à equipe de suporte do QnAMaker informações adicionais sobre a configuração.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 94a3dfd9a3560321d7e2753ccd385fb1a5323107
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559931"
---
# <a name="troubleshooting-tips-to-support-the-qna-maker-service-and-runtime"></a>Dicas de solução de problemas para dar suporte ao tempo de execução e ao serviço do QnA Maker
O QnAMaker inclui componentes hospedados na conta do usuário do Azure. A depuração pode exigir que os usuários manipulem os recursos do QnAMaker Azure ou forneçam à equipe de suporte do QnAMaker informações adicionais sobre a configuração.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>Como obter as últimas atualizações do tempo de execução do QnAMaker
O tempo de execução do QnAMaker é parte do Serviço de Aplicativo do Azure implantado ao [criar um serviço do QnAMaker](./set-up-qnamaker-service-azure.md) no portal do Azure. Atualizações são feitas periodicamente para o tempo de execução. QnA Maker serviço de aplicativo estiver no modo de atualização automática, poste nossa versão de extensão de site de abril 2019 (versão 5 +). Isso já foi projetado para cuidar do tempo de inatividade ZERO durante as atualizações. Você pode verificar a versão atual em https://www.qnamaker.ai/UserSettings. Se sua versão for anterior à versão 5. x, você deverá reiniciar o serviço de aplicativo para aplicar as atualizações mais recentes a serem aplicadas à configuração do QnAMaker.

1. Acesse o serviço do QnAMaker (grupo de recursos) no [portal do Azure](https://portal.azure.com)

    ![Grupo de recursos do Azure do QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Clique no Serviço de Aplicativo e abra a seção Visão Geral

     ![Serviço de Aplicativo do QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. Reinicie o serviço de Aplicativo. Isso deve concluir dentro de alguns segundos. Observe que os bots/aplicativos de downstream compilados nesse serviço do QnAMaker estarão indisponíveis para os usuários finais durante esse período de reinicio.

    ![Reinício do serviço de aplicativo do QnAMaker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>Como obter o nome de host de serviço do QnAMaker
O nome do host de serviço do QnAMaker será útil para finalidades de depuração quando você contatar o Suporte do QnAMaker ou UserVoice. O nome do host é uma URL deste formulário: https:// *{nome do host}* .azurewebsites.net.
    
1. Acesse o serviço do QnAMaker (grupo de recursos) no [portal do Azure](https://portal.azure.com)

    ![Grupo de recursos do Azure do QnAMaker no portal do Azure](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Clique no Serviço de Aplicativo

     ![Selecione o Serviço de Aplicativo do QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. A URL do nome do host está disponível na seção Visão Geral

    ![Nome do host do QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Melhorar as perguntas da base de dados de conhecimento com o aprendizado ativo](./improve-knowledge-base.md)
