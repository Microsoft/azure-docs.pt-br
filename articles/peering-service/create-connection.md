---
title: 'Serviço de Emparelhamento do Azure: Criar '
description: Neste tutorial, saiba como registrar o Serviço de Emparelhamento do Azure e um prefixo.
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/2/2020
ms.author: derekol
ms.openlocfilehash: 6f303f99374120522ebfcc13a2af50634a4f67b4
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061937"
---
# <a name="tutorial-create-a-peering-service-connection"></a>Tutorial: Criar uma conexão do Serviço de Emparelhamento

Este tutorial mostra como criar um recurso de Serviço de Emparelhamento e como configurar uma conexão de Serviço de Emparelhamento. 

1. Para registrar uma conexão de Serviço de Emparelhamento, selecione **Criar um recurso** > **Serviço de Emparelhamento**.

 
    ![Registrar Serviço de Emparelhamento](./media/peering-service-portal/peering-servicecreate.png)

2. Insira os detalhes a seguir na guia **Noções Básicas** na página **Criar uma conexão de Serviço de Emparelhamento**.
 
3. Selecione a assinatura e o grupo de recursos associados à assinatura.

    ![Guia de Noções básicas de registrar Serviço de Emparelhamento](./media/peering-service-portal/peering-servicebasics.png)

4. Insira um **Nome** para o qual a instância do Serviço de Emparelhamento deve ser registrada.

5. Agora, selecione o botão **Avançar: Configuração** na parte inferior da página. A página **Configuração** é exibida.
## <a name="configure-the-peering-service-connection"></a>Configure a conexão do Serviço de Emparelhamento

1. Na página **Configuração**, selecione o local para o qual o Serviço de Emparelhamento deve ser habilitado selecionando o mesmo na lista suspensa **Local do Serviço de Emparelhamento**.

1. Selecione o provedor de serviços do qual o Serviço de Emparelhamento deve ser obtido selecionando um nome de provedor na lista suspensa **Provedor de Serviço de Emparelhamento**.
 
1. Selecione **Criar prefixo** na parte inferior da seção **Prefixos** e as caixas de texto aparecerão. Agora, digite o nome do recurso de prefixo e os prefixos associados ao provedor de serviços.

1. Selecione **Chave de Prefixo** e adicione a chave de prefixo que foi atribuída a você pelo seu provedor (ISP ou PTT). Essa chave permite que o MS valide o prefixo e o provedor que alocou o prefixo de IP.

    ![A captura de tela mostra a guia Configuração da página Criar uma conexão de serviço de emparelhamento, em que é possível inserir a chave Prefixo.](./media/peering-service-portal/peering-serviceconfiguration.png)

1. Selecione o botão **Examinar + criar** na parte inferior esquerda da página. A página **Examinar + criar** é exibida e o Azure valida sua configuração.

 1. Quando a mensagem **Validação aprovada** for exibida, selecione **Criar**.

> ![A captura de tela mostra a guia Examinar + criar da página Criar uma conexão de serviço de emparelhamento.](./media/peering-service-portal/peering-service-prefix.png)

1. Depois de registrar uma conexão de Serviço de Emparelhamento, a validação adicional é executada nos prefixos incluídos. Você pode examinar o status de validação na seção **Prefixos** do nome do recurso. Se a validação falhar, uma das seguintes mensagens de erro será exibida:

   - Prefixo do Serviço de Emparelhamento inválido, o prefixo deve ser um formato válido, só há suporte para o prefixo Ipv4.
   - O prefixo não foi recebido do provedor de Serviço de Emparelhamento.
   - O comunicado de prefixo não tem uma comunidade BGP válida, entre em contato com o provedor de Serviço de Emparelhamento.
   - Rota de backup não encontrada, entre em contato com o provedor de Serviço de Emparelhamento.
   - Prefixo recebido com um caminho AS mais longo, entre em contato com o provedor de Serviço de Emparelhamento.
   - Prefixo recebido com AS privado no caminho, entre em contato com o provedor de Serviço de Emparelhamento.

### <a name="add-or-remove-a-prefix"></a>Adicionar ou remover um prefixo

Selecione **Adicionar prefixos** na página **Prefixos** para adicionar prefixos.

Selecione as reticências (…) ao lado do prefixo listado e selecione a opção **Excluir**.

### <a name="delete-a-peering-service-connection"></a>Excluir uma conexão do Serviço de Emparelhamento

Na página **Todos os Recursos**, marque a caixa de seleção no Serviço de Emparelhamento e selecione a opção **Excluir** na parte superior da página.
## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre a conexão do Serviço de Emparelhamento, confira [Conexão do Serviço de Emparelhamento](connection.md).
- Para saber mais sobre telemetria de conexão do Serviço de Emparelhamento, confira [Telemetria de conexão do Serviço de Emparelhamento](connection-telemetry.md).
- Para medir a telemetria, confira [Medir telemetria de conexão](measure-connection-telemetry.md).
- Para registrar a conexão usando o Azure PowerShell, confira [Registrar uma conexão do Serviço de Emparelhamento – Azure PowerShell](powershell.md).
- Para registrar a conexão usando a CLI do Azure, confira [Registrar uma conexão de Serviço de Emparelhamento – CLI do Azure](cli.md).