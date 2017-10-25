---
title: "Organizar seus recursos com grupos de gerenciamento do Azure – Azure | Microsoft Docs"
description: "Saiba mais sobre os grupos de gerenciamento e como usá-los."
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: rithorn
ms.openlocfilehash: 18541c68b02ae1b59ae4a6a85122dff614c9978c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Organizar seus recursos com grupos de gerenciamento do Azure 

Se você tem várias assinaturas, você pode organizá-las em contêineres chamados de "grupos de gerenciamento" para ajudá-lo a gerenciar o acesso, a política, os custos e a conformidade em suas assinaturas. Por exemplo, você pode aplicar políticas a um grupo de gerenciamento, para limitar quais tipos de recursos podem ser criados.

> [!Note]
> Esse recurso está atualmente em uma versão prévia particular. [Inscreva-se aqui](https://aka.ms/MGPreviewSignup) para ingressar seu registro na versão prévia.   
 


Os grupos de gerenciamento podem ser organizados em uma hierarquia. A estrutura mostrada é uma representação de exemplo de uma hierarquia de grupo de gerenciamento que poderia existir:


![Árvore hierárquica](media/billing-enterprise-mgmt-groups/tree.png)



## <a name="how-management-groups-are-related-to-your-azure-enterprise-enrollment"></a>Como os grupos de gerenciamento estão relacionados ao seu registro do Azure Enterprise

A introdução dos grupos de gerenciamento é uma etapa da jornada maior para a transição das funcionalidades do [Enterprise Portal](https://ea.azure.com) para o [Portal do Azure](https://portal.azure.com).

A estrutura do grupo de gerenciamento é criada da mesma forma como foi definida no Enterprise Portal. Toda a hierarquia, consistindo em registro, departamentos e contas, é mapeada em grupos de gerenciamento correspondentes. 

Aqui está como a estrutura atual do EA mapeia para a hierarquia de grupo de gerenciamento. 

![Árvore hierárquica](media/billing-enterprise-mgmt-groups/tree2.png)

A tabela abaixo mostra como os usuários do Enterprise Portal são mapeados para a hierarquia do grupo de gerenciamento.

|    Função do EA                                       |    Função no nó do grupo de gerenciamento mapeado    |    Permissões no nó do grupo de gerenciamento                                                          |
|--------------------------------------------------|--------------------------------------------------|----------------------------------------------------------------------------------------------------|
|    Administrador do EA                              |    Colaborador da política de recurso                   |    Pode exibir custos, gerenciar política de recursos e exibir a hierarquia no nó de registro e abaixo dele    |
|    Administrador de EA em modo somente leitura            |    Leitor de cobrança                                |    Pode ler custos e exibir a hierarquia no nó de registro e abaixo dele                              |
|    Administrador de departamento                      |    Leitor de cobrança                                |    Pode ler custos e exibir a hierarquia abaixo do nó de departamento                                 |
|    Administrador de departamento em modo somente leitura    |    Leitor de cobrança                                |    Pode ler custos e exibir a hierarquia abaixo do nó de departamento                                 |
|    Proprietário da conta                                 |    Colaborador da política de recurso                   |    Pode exibir custos, gerenciar política de recursos e exibir a hierarquia no nó da conta e abaixo dele       |




## <a name="view-management-groups-in-the-azure-portal"></a>Exibir grupos de gerenciamento no Portal do Azure

Para exibir um registro, um departamento ou uma conta na versão prévia, entre no Portal do Azure com o link no email de boas-vindas.   

![hierarquia](media/billing-enterprise-mgmt-groups/hierarchy.png)

### <a name="viewing-costs"></a>Exibindo custos 
Nas telas de detalhe dos grupos de gerenciamento, você pode ver os custos atuais acumulados no mês. Esses custos são baseados no uso e não contam para valores pré-pagos, excedentes, quantidades incluídas, ajustes e impostos. Para o grupo de gerenciamento correspondente ao seu registro, a seção de custos mostra o compromisso restante.  

![detalhes do registro](media/billing-enterprise-mgmt-groups/enrollment.png)

 Os "Custos cobrados separadamente" são o acúmulo mensal de alterações separadas, como marketplace, excedentes e outros custos que não vão de encontro ao seu compromisso de registro.  Para obter mais informações sobre o detalhamento dos custos, consulte o [Enterprise Portal](https://ea.azure.com). 

### <a name="enabling-access-to-costs"></a>Habilitando o acesso aos custos
Se você não estiver vendo os custos, consulte nosso documento [Solução de problemas de exibições de custos empresariais](https://aka.ms/enableazurecosts) para obter ajuda.  

### <a name="delays-between-the-enterprise-portal-and-azure-portal"></a>Atrasos entre o Enterprise Portal e o Portal do Azure 
* Durante a versão prévia, as quantidades exibidas no Portal do Azure podem ser atrasadas em comparação com os valores no Enterprise Portal. Esse problema é temporário está sendo resolvido.
* As configurações atualizadas no Enterprise Portal têm um atraso de alguns minutos antes que sejam refletidas no Portal do Azure. 

## <a name="management-groups-have-a-relationship-with-your-directory"></a>Os grupos de gerenciamento têm uma relação com seu diretório   
Assim como as assinaturas, os grupos de gerenciamento também têm uma relação de confiança com o Azure AD. Uma hierarquia de grupo de gerenciamento confia em um único diretório para autenticar usuários. Todos os administradores associados a uma hierarquia de grupo de gerenciamento devem pertencer ao mesmo diretório. 

Como sua hierarquia de registro é mapeada em grupos de gerenciamento, uma relação de confiança é estabelecida com um único diretório. Sempre que possível, é selecionado um diretório existente associado às contas de usuário do registro. Em alguns casos, um novo diretório é criado e todos os usuários de registros existentes são convidados a entrar nesse diretório. Os diretórios associados às assinaturas do registro não são afetados. Assim, a hierarquia poderá ser criada em um diretório diferente das assinaturas. [Saiba mais](billing-enterprise-mgmt-grp-find.md) sobre como esse processo afeta a experiência de navegação entre a hierarquia e as respectivas assinaturas.

## <a name="administering-your-management-groups"></a>Administrando seus grupos de gerenciamento
Os grupos de gerenciamento no Portal do Azure estão em versão prévia e são somente leitura nessa versão inicial. Para fazer atualizações, acesse o Enterprise Portal. As atualizações serão refletidas automaticamente no Portal do Azure. Consulte a documentação no Enterprise Portal para obter ajuda sobre como fazer adições e edições.   

## <a name="policy-management"></a>Gerenciamento de política
O Gerenciador de Recursos permite que você crie políticas personalizadas para gerenciar seus recursos. Com os grupos de gerenciamento, as políticas poderão ser atribuídas a qualquer nível na hierarquia e os recursos herdarão essas políticas.  [Saiba mais](https://go.microsoft.com/fwlink/?linkid=858942)

> [!Note]
> A política não é imposta nos diretórios. 


