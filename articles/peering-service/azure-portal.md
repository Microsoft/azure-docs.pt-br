---
title: Registrar serviço de emparelhamento do Azure-portal do Azure
description: Saiba como registrar o serviço de emparelhamento do Azure usando o portal do Azure
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 62210105ba4ba84cff4b246c5b7cf8cc794fb5e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91534940"
---
# <a name="register-peering-service-by-using-the-azure-portal"></a>Registrar o serviço de emparelhamento usando o portal do Azure

O Serviço de Emparelhamento do Azure é um serviço de rede que aprimora a conectividade do cliente com os serviços em nuvem da Microsoft, como o Microsoft 365, o Dynamics 365, os serviços de SaaS (software como serviço), o Azure ou qualquer serviço da Microsoft acessível por meio da Internet pública.

Neste artigo, você aprenderá a registrar uma conexão de serviço de emparelhamento usando o portal do Azure.

Caso não tenha uma assinatura do Azure, crie uma [conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

> 

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter o seguinte:

### <a name="azure-account"></a>Conta do Azure

Você deve ter uma conta válida e ativa do Microsoft Azure. Essa conta é necessária para configurar a conexão do Serviço de Emparelhamento. O Serviço de Emparelhamento é um recurso dentro das assinaturas do Azure. 

### <a name="connectivity-provider"></a>Provedor de conectividade

Você pode trabalhar com um provedor de serviços de Internet ou um parceiro do Exchange da Internet para obter o Serviço de Emparelhamento para conectar sua rede com a rede da Microsoft.

Verifique se os [provedores de conectividade](location-partners.md) estão em parceria com a Microsoft.



## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Em um navegador, vá para o portal do Azure e entre com sua conta do Azure.

## <a name="register-a-peering-service-connection"></a>Registrar uma conexão do Serviço de Emparelhamento

1. Para registrar uma conexão de Serviço de Emparelhamento, selecione **Criar um recurso** > **Serviço de Emparelhamento**.

    ![Registrar Serviço de Emparelhamento](./media/peering-service-portal/peering-servicecreate.png)
1. Insira os detalhes a seguir na guia **Noções Básicas** na página **Criar uma conexão de Serviço de Emparelhamento**.

 
1. Selecione a assinatura e o grupo de recursos associados à assinatura.

   ![Guia registrar nível básico de emparelhamento](./media/peering-service-portal/peering-servicebasics.png)

1. Insira um **Nome** para o qual a instância do Serviço de Emparelhamento deve ser registrada.
 
1. Agora, selecione o botão **Avançar: Configuração** na parte inferior da página. A página **Configuração** é exibida.

## <a name="configure-the-peering-service-connection"></a>Configure a conexão do Serviço de Emparelhamento

1. Na página **Configuração**, selecione o local para o qual o Serviço de Emparelhamento deve ser habilitado selecionando o mesmo na lista suspensa **Local do Serviço de Emparelhamento**.

1. Selecione o provedor de serviços do qual o Serviço de Emparelhamento deve ser obtido selecionando um nome de provedor na lista suspensa **Provedor de Serviço de Emparelhamento**.
 
1. Selecione **Criar prefixo** na parte inferior da seção **Prefixos** e as caixas de texto aparecerão. Agora, digite o nome do recurso de prefixo e os prefixos associados ao provedor de serviços.

1. Selecione **Chave de Prefixo** e adicione a chave de prefixo que foi atribuída a você pelo seu provedor (ISP ou PTT). Essa chave permite que o MS valide o prefixo e o provedor que alocou o prefixo de IP.
   > ![A captura de tela mostra a guia Configuração da página Criar uma conexão de serviço de emparelhamento, em que é possível inserir a chave Prefixo.](./media/peering-service-portal/peering-serviceconfiguration.png)

1. Selecione o botão **Examinar + criar** na parte inferior esquerda da página. A página **Examinar + criar** é exibida e o Azure valida sua configuração.
    

1. Quando a mensagem **Validação aprovada** for exibida, selecione **Criar**.

   > ![A captura de tela mostra a guia Examinar + criar da página Criar uma conexão de serviço de emparelhamento.](./media/peering-service-portal/peering-service-prefix.png)


1. Depois de registrar uma conexão de Serviço de Emparelhamento, a validação adicional é executada nos prefixos incluídos. Você pode examinar o status de validação na seção **Prefixos** do nome do recurso. Se a validação falhar, uma das seguintes mensagens de erro será exibida:

   - Prefixo do Serviço de Emparelhamento inválido, o prefixo deve ser um formato válido, só há suporte para o prefixo Ipv4.
   - O prefixo não foi recebido do provedor de Serviço de Emparelhamento.
   - O anúncio de prefixo não tem uma comunidade BGP válida, contate o provedor de serviço de emparelhamento.
   - Rota de backup não encontrada, contate o provedor de serviço de emparelhamento.
   - Prefixo recebido com mais tempo como caminho, contate o provedor de serviço de emparelhamento.
   - Prefixo recebido com privado como no caminho, contate o provedor de serviço de emparelhamento.

### <a name="add-or-remove-a-prefix"></a>Adicionar ou remover um prefixo

Selecione **Adicionar prefixos** na página **Prefixos** para adicionar prefixos.

Selecione as reticências (…) ao lado do prefixo listado e selecione a opção **Excluir**.

### <a name="delete-a-peering-service-connection"></a>Excluir uma conexão do Serviço de Emparelhamento

Na página **Todos os Recursos**, marque a caixa de seleção no Serviço de Emparelhamento e selecione a opção **Excluir** na parte superior da página.

> [!NOTE]
> Não é possível modificar um prefixo existente.
>

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre a conexão do Serviço de Emparelhamento, confira [Conexão do Serviço de Emparelhamento](connection.md).
- Para saber mais sobre telemetria de conexão do Serviço de Emparelhamento, confira [Telemetria de conexão do Serviço de Emparelhamento](connection-telemetry.md).
- Para medir a telemetria, confira [Medir telemetria de conexão](measure-connection-telemetry.md).
- Para registrar a conexão usando o Azure PowerShell, confira [Registrar uma conexão do Serviço de Emparelhamento – Azure PowerShell](powershell.md).
- Para registrar a conexão usando a CLI do Azure, confira [Registrar uma conexão de Serviço de Emparelhamento – CLI do Azure](cli.md).
