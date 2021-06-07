---
title: Início Rápido – Criar um servidor do Analysis Services no portal do Azure | Microsoft Docs
description: Este guia de início rápido descreve como criar uma instância de servidor do Azure Analysis Services usando o portal do Azure.
author: minewiskan
ms.author: owend
ms.reviewer: minewiskan
ms.date: 08/31/2020
ms.topic: quickstart
ms.service: azure-analysis-services
ms.custom:
- mode-portal
ms.openlocfilehash: 78e4e008a4c4dfcee1e4f0a11b519e0a2bbf5f56
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535071"
---
# <a name="quickstart-create-a-server---portal"></a>Início Rápido: Criar um servidor - Portal

Este início rápido descreve como criar um recurso de servidor do Analysis Services em sua assinatura do Azure por meio do portal.

## <a name="prerequisites"></a>Prerequisites 

* **Assinatura do Azure**: visite a [Avaliação Gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) para criar uma conta.
* **Azure Active Directory**: sua assinatura deve estar associada a um locatário do Azure Active Directory. Também é preciso estar conectado ao Azure com uma conta no Azure Active Directory em questão. Para obter mais informações, confira [Autenticação e permissões de usuário](analysis-services-manage-users.md).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure 

[Entre no portal](https://portal.azure.com)


## <a name="create-a-server"></a>Criar um servidor

1. Clique em **+ Criar um recurso** > **Análise** > **Analysis Services**.

    ![Portal](./media/analysis-services-create-server/aas-create-server-portal.png)

2. Em **Analysis Services**, preencha os campos obrigatórios e pressione **Criar**.
   
   * **Nome do servidor**: digite um nome exclusivo usado para fazer referência ao servidor. O nome do servidor deve começar com um caractere minúsculo e conter entre 3 e 128 caracteres minúsculos e números. Espaços e caracteres especiais não são permitidos.
   * **Assinatura**: selecione a assinatura a qual o servidor será associado.
   * **Grupo de recursos**: crie um novo grupo de recursos ou selecione um que você já tenha. Os grupo de recursos são projetados para ajudar você a gerenciar uma coleção de recursos do Azure. Para saber mais, veja [grupos de recursos](../azure-resource-manager/management/overview.md).
   * **Local**: local do datacenter do Azure que hospeda o servidor. Escolha um local mais próximo da sua maior base de usuários.
   * **Tipo de preço**: selecione um tipo de preço. Se você estiver testando e pretende instalar o banco de dados do modelo de exemplo, selecione a camada gratuita **D1**. Para saber mais, veja [Preços do Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/). 
   * **Administrador**: por padrão, essa será a conta com a qual você entrou. Você pode escolher uma conta diferente no Microsoft Azure Active Directory.
   * **Configuração de armazenamento de backup**: opcional. Se você já tiver uma [conta de armazenamento](../storage/common/storage-introduction.md), você pode especificá-la como o padrão para o backup do banco de dados de modelo. Você também pode especificar a configuração de [backup e restauração](analysis-services-backup.md) mais tarde.
   * **Expiração da chave de armazenamento**: opcional. Especifique um período de expiração para a chave de armazenamento.

A criação do servidor geralmente demora menos de um minuto. Se você tiver selecionado **Adicionar ao Portal**, navegue até o portal para ver o novo servidor. Ou navegue até **Todos os serviços** > **Analysis Services** para ver se o servidor está pronto. Servidores oferecem suporte a modelos tabulares nos níveis de compatibilidade 1200 e superior. O nível de compatibilidade do modelo é especificado no Visual Studio ou no SSMS.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, exclua o seu servidor. Em **Visão geral**, clique em **Excluir**. 

 ![Limpeza](./media/analysis-services-create-server/aas-create-server-cleanup.png)


## <a name="next-steps"></a>Próximas etapas
Neste início rápido, você aprendeu a criar um servidor em sua assinatura do Azure. Agora que você tem um servidor, pode ajudar a protegê-lo configurando um firewall do servidor (opcional). Você também pode adicionar um modelo de dados de amostra básico ao servidor diretamente do portal. Ter um modelo de exemplo é útil para saber mais sobre como configurar funções do modelo de banco de dados e testar conexões de cliente. Para saber mais, continue para o tutorial para adicionar um modelo de exemplo.

> [!div class="nextstepaction"]
> [Início Rápido: Configurar firewall do servidor – Portal](analysis-services-qs-firewall.md)   
