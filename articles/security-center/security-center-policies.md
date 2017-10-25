---
title: "Definir políticas de segurança na Central de Segurança do Azure | Microsoft Docs"
description: "Este documento ajuda você a configurar políticas de segurança na Central de segurança do Azure."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 3b9e1c15-3cdb-4820-b678-157e455ceeba
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: yurid
ms.openlocfilehash: 67564e930310433bf4d51f7642bdd7ebf7e8e600
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="set-security-policies-in-azure-security-center"></a>Configurar políticas de segurança na Central de Segurança do Azure
Este documento ajuda a configurar as políticas de segurança na Central de Segurança, guiando você nas etapas necessárias para executar essa tarefa.


## <a name="how-security-policies-work"></a>Como funcionam as políticas de segurança?
A Central de Segurança cria automaticamente uma política de segurança padrão para cada uma de suas assinaturas do Azure. Você pode editar a política na Central de Segurança ou usar a [Política do Azure](http://docs.microsoft.com/azure/azure-policy/azure-policy-introduction) para criar novas definições, definir políticas adicionais e atribuir políticas entre Grupos de Gerenciamento (que pode representar toda a organização, uma unidade de negócios nela etc.) e monitorar a conformidade com essas políticas nesses escopos.

> [!NOTE]
> A Política do Azure está em versão prévia limitada. Clique [aqui](https://aka.ms/getpolicy) para participar. Para saber mais sobre as Políticas do Azure, leia [Criar e gerenciar políticas para impor a conformidade](http://docs.microsoft.com/en-us/azure/azure-policy/create-manage-policy).

## <a name="how-to-change-security-policies-in-security-center"></a>Como alterar as políticas de segurança na Central de Segurança?
Você pode editar a política de segurança padrão para cada uma de suas assinaturas do Azure na Central de Segurança. Para modificar uma política de segurança, você deve ser proprietário, colaborador ou Administrador de Segurança dessa assinatura, ou o Grupo de Gerenciamento que a contém. Acesse o Portal do Azure e execute as etapas abaixo para exibir as políticas de segurança na Central de Segurança:

1. No painel **Central de Segurança**, em **Geral**, clique em **Política de Segurança**.
2. Selecione a assinatura na qual você deseja habilitar a política de segurança.

    ![Gerenciamento de política](./media/security-center-policies/security-center-policies-fig10.png)

3. Na seção **COMPONENTES DA POLÍTICA**, clique em **Política de segurança**.

    ![Componentes da política](./media/security-center-policies/security-center-policies-fig12.png)

4. Esta é a política padrão atribuída à Central de Segurança por meio da Política do Azure. Você pode excluir os itens que estão sob **POLÍTICAS E PARÂMETROS**, ou você pode adicionar outras definições de política que estão sob **OPÇÕES DISPONÍVEIS**. Para fazer isso, basta clicar no sinal de adição ao lado do nome da definição.

    ![Definições de política](./media/security-center-policies/security-center-policies-fig11.png)

5. Se você quiser uma explicação mais detalhada sobre a política, clique nela, e outra página será aberta com os detalhes e o código JSON que tem a estrutura de [definição de política (https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy#policy-definition-structure):

    ![Json](./media/security-center-policies/security-center-policies-fig13.png)

6. Ao concluir a edição, clique em **Salvar**.

## <a name="see-also"></a>Consulte também
Neste documento, você aprendeu como configurar políticas de segurança na Central de segurança do Azure. Para saber mais sobre a Central de Segurança do Azure, veja o seguinte:

* [Guia de planejamento e operações da Central de Segurança do Azure](security-center-planning-and-operations-guide.md). Saiba como planejar e entender as considerações de design para adotar a Central de Segurança do Azure.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md). Saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md). Saiba como gerenciar e responder aos alertas de segurança.
* [Monitoramento das soluções de parceiros na Central de Segurança do Azure](security-center-partner-solutions.md). Saiba como monitorar o status da integridade das soluções dos parceiros.
* [Perguntas Frequentes sobre a Central de Segurança do Azure](security-center-faq.md). Encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/). Encontre postagens no blog sobre a conformidade e segurança do Azure.
