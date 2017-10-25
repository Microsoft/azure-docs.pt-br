---
title: "Introdução aos modelos privados | Microsoft Docs"
description: Adicione, gerencie e compartilhe seus modelos privados usando o portal do Azure, a CLI do Azure ou o PowerShell.
services: marketplace-customer
documentationcenter: 
author: VybavaRamadoss
manager: asimm
editor: 
tags: marketplace, azure-resource-manager
keywords: 
ms.assetid: 6ec20778-b578-4885-acb5-104b0e51ea1a
ms.service: marketplace
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: vybavar
ms.openlocfilehash: 01657619cbe579c6818a790cc3ab95a33936a565
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-private-templates-on-the-azure-portal"></a>Introdução aos modelos privados no Portal do Azure
Um modelo do [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) é um modelo declarativo usado para definir a sua implantação. Você define os recursos a serem implantados em uma solução e especifica os parâmetros e variáveis que lhe permitem inserir valores para diferentes ambientes. O modelo consiste em JSON e expressões que podem ser usados na construção de valores para sua implantação.

Você pode usar o novo recurso **Modelos** no [Portal do Azure](https://portal.azure.com) juntamente com o provedor de recursos **Microsoft.Gallery** como uma extensão do [Azure Marketplace](https://azure.microsoft.com/marketplace/) para permitir aos usuários criar, gerenciar e implantar modelos privados de uma biblioteca pessoal.

Este documento explica como adicionar, gerenciar e compartilhar um **Modelo** privado usando o Portal do Azure.

## <a name="guidance"></a>Diretrizes
As sugestões a seguir ajudarão você a aproveitar os **Modelos** ao máximo quando estiver trabalhando com suas soluções.

* Um **modelo** é um recurso de encapsulamento que contém um modelo do Resource Manager e metadados adicionais. Ele se comporta de maneira muito semelhante a um item no Marketplace. A principal diferença é que se trata de um item privado, e não itens públicos do Marketplace.
* A biblioteca **Modelos** funciona bem com usuários que precisam personalizar suas implantações.
* **Modelos** funcionam bem com usuários que precisam de um repositório simples no Azure.
* Comece com um modelo existente do Resource Manager. Encontre modelos no [github](https://github.com/Azure/azure-quickstart-templates) ou [exporte o modelo](../azure-resource-manager/resource-manager-export-template.md) de um grupo de recursos existente.
* **Modelos** ficam vinculados ao usuário que os publicar. O nome do publicador fica visível para qualquer pessoa que tenha acesso de leitura.
* **Modelos** são recursos do Resource Manager e não podem ser renomeados depois de publicados.

## <a name="add-a-template-resource"></a>Adicionar um recurso de modelo
Há duas maneiras de criar um recurso de **modelo** no portal do Azure.

### <a name="method-1--create-a-new-template-resource-from-a-running-resource-group"></a>Método 1: criar um novo recurso de modelo de um grupo de recursos em execução
1. Navegue até um grupo de recursos existente no Portal do Azure. Selecione **Exportar modelo** em **Configurações**.
2. Depois que o modelo do Resource Manager é exportado, use o botão **Salvar Modelo** para salvá-lo no repositório **Modelos**. Encontre todos os detalhes sobre a exportação de modelos [aqui](../azure-resource-manager/resource-manager-export-template.md).
   <br /><br />
   ![Exportação do grupo de recursos](media/rg-export-portal1.PNG)  <br />
3. Selecione o botão de comando **Salvar no Modelo** .
   <br /><br />
4. Insira as seguintes informações:
   
   * Nome – nome do objeto de modelo (OBSERVAÇÃO: esse é um nome baseado no Azure Resource Manager. Todas as restrições de nomenclatura são aplicáveis e ele não pode ser alterado depois de criado).
   * Descrição – resumo rápido do modelo.
     
     ![Salvar Modelo](media/save-template-portal1.PNG)  <br />
5. Clique em **Salvar**.
   
   > [!NOTE]
   > A folha Exportar modelo mostra notificações quando o modelo do Resource Manager exportado tem erros, mas você ainda poderá salvar esse modelo do Resource Manager em Modelos. Verifique e corrija problemas no modelo do Resource Manager antes de reimplantar o modelo do Resource Manager exportado.
   > 
   > 

### <a name="method-2--add-a-new-template-resource-from-browse"></a>Método 2: adicionar um novo recurso de modelo por meio de navegação
Você também pode adicionar um novo **Modelo** do zero usando o botão de comando +Adicionar em **Procurar > Modelos**. Você precisará fornecer um nome, uma descrição e o JSON do modelo do Resource Manager.

![Adicionar Modelo](media/add-template-portal1.PNG)  <br />

> [!NOTE]
> Microsoft.Gallery é um provedor de recursos do Azure baseado em locatário. O recurso de modelo está vinculado ao usuário que o criou. Ele não está associado a nenhuma assinatura específica. A assinatura só precisa ser escolhida quando o modelo for implantado.
> 
> 

## <a name="view-template-resources"></a>Exibir recursos de modelo
Todos os **modelos disponíveis** podem ser exibidos em **Procurar > Modelos**. Isso inclui **modelos** que você criou e os que foram compartilhados com você com vários níveis de permissões. Mais detalhes na seção [controle de acesso](#access-control-for-a-tenant-resource-provider) abaixo.

![Exibir Modelo](media/view-template-portal1.PNG)  <br />

Você pode exibir os detalhes de um **modelo** clicando em um item na lista.

![Exibir Modelo](media/view-template-portal2c.png)  <br />

## <a name="edit-a-template-resource"></a>Editar um recurso de modelo
Inicie o fluxo de edição de um **modelo** clicando no item com o botão direito do mouse na lista Procurar ou escolhendo o botão de comando Editar.

![Editar modelo](media/edit-template-portal1a.PNG)  <br />

Edite a descrição ou o texto do modelo do Resource Manager. Você não pode editar o nome, já que ele é um nome de recurso do Resource Manager. Quando você editar o JSON do modelo do Resource Manager, faremos o teste de validação para garantir que ele é um JSON válido. Escolha **OK** e **Salvar** para salvar o modelo atualizado.

![Editar modelo](media/edit-template-portal2a.PNG)  <br />

Depois que o **modelo** for salvo, você verá uma notificação de confirmação.

![Editar modelo](media/edit-template-portal3b.png)  <br />

## <a name="deploy-a-template-resource"></a>Implantar um recurso de modelo
Você pode implantar qualquer **Modelo** para o qual tiver permissões de **Leitura**. O fluxo de implantação inicia a folha de implantação padrão de modelo do Azure. Preencha os valores dos parâmetros de modelo do Resource Manager para prosseguir com a implantação.

![Implantar o modelo](media/deploy-template-portal1b.png)  <br />

## <a name="share-a-template-resource"></a>Compartilhar um recurso de modelo
Um recurso de **modelo** pode ser compartilhados com seus colegas. O compartilhamento se comporta da mesma forma que a [atribuição de função de qualquer recurso no Azure](../active-directory/role-based-access-control-configure.md). O proprietário do **modelo** fornece permissões para outros usuários que podem interagir com um recurso de modelo. A pessoa ou grupo de pessoas com quem você compartilhar o **modelo** será capaz de ver o modelo do Resource Manager e suas propriedades de galeria.

### <a name="access-control-for-the-microsoftgallery-resources"></a>Controle de acesso aos recursos da Microsoft.Gallery
| Função | Permissões |
| --- | --- |
| Proprietário |Permite o controle total sobre o recurso de modelo, incluindo compartilhamento |
| Leitor |Permite leitura e execução (Implantação) no recurso de modelo |
| Colaborador |Concede permissão de edição e exclusão no recurso de modelo. O usuário não pode compartilhar o modelo com outras pessoas |

Selecione **Compartilhar** no item de navegação clicando com o botão direito do mouse ou na folha do modo de exibição de um item específico. Isso inicia uma experiência de compartilhamento.

![Compartilhar modelo](media/share-template-portal1a.png)  <br />

 Agora você pode escolher uma função e um usuário ou grupo para fornecer acesso a determinado **modelo**. As funções disponíveis são Proprietário, Leitor e Colaborador. Mais detalhes na seção [controle de acesso](#access-control-for-a-tenant-resource-provider) acima.

![Compartilhar modelo](media/share-template-portal2b.png)  <br />

![Compartilhar modelo](media/share-template-portal3b.png)  <br />

Clique em **Selecionar** e em **Ok**. Agora você pode ver os usuários ou grupos adicionados ao recurso.

![Compartilhar modelo](media/share-template-portal4b.png)  <br />

> [!NOTE]
> Um modelo só pode ser compartilhado com usuários e grupos no mesmo locatário do Azure Active Directory. Se você compartilhar um modelo com um endereço de email que não está em seu locatário, um convite será enviado com um pedido ao usuário para ingressar no locatário como convidado.
> 
> 

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre a criação de modelos do Resource Manager, confira [Criando modelos](../azure-resource-manager/resource-group-authoring-templates.md)
* Para conhecer as funções que podem ser usadas em um modelo do Resource Manager, veja [Funções do modelo](../azure-resource-manager/resource-group-template-functions.md)
* Para obter diretrizes sobre como criar os modelos, confira [Práticas recomendadas para a criação de modelos do Gerenciador de Recursos do Azure](../azure-resource-manager/best-practices-resource-manager-design-templates.md)

