---
title: "Azure Active Directory Domain Services: Atualizar as configurações do DNS para a rede virtual do Azure | Microsoft Docs"
description: "Introdução aos Serviços de Domínio do Active Directory do Azure"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/26/2017
ms.author: maheshu
ms.openlocfilehash: ab8e3215e8e73d3943af06cffafa730cf1b7744b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="enable-azure-active-directory-domain-services"></a>Habilitar o Azure Active Directory Domain Services

## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Tarefa 4: atualizar as configurações do DNS para a rede virtual do Azure
Nas tarefas de configuração anteriores, você habilitou o Azure Active Directory Domain Services para seu diretório com êxito. A próxima tarefa é garantir que os computadores na rede virtual possam se conectar e consumir esses serviços. Neste artigo, você atualiza as configurações do servidor DNS para sua rede virtual para apontar para os dois endereços IP em que o Azure Active Directory Domain Services fica disponível na rede virtual.

Para atualizar uma configuração do servidor DNS para a rede virtual na qual você tiver habilitado o Azure Active Directory Domain Services, conclua as seguintes etapas:

1. A guia **Visão Geral** lista um conjunto de **etapas de configuração necessárias** que deverão ser executadas depois que o domínio gerenciado for totalmente provisionado. A primeira etapa de configuração é **Atualizar configurações do servidor DNS para sua rede virtual**.

    ![Domain Services - guia Visão geral depois de totalmente provisionado](./media/getting-started/domain-services-provisioned-overview.png)

2. Quando o seu domínio está totalmente provisionado, dois endereços IP são exibidos neste bloco. Cada um do endereços IP representa um controlador de domínio para seu domínio gerenciado.

3. Para copiar o primeiro endereço IP na área de transferência, clique no botão Copiar ao lado dele. Em seguida, clique no botão **Configurar servidores DNS**.

4. Cole o primeiro endereço IP na caixa de texto **Adicionar servidor DNS** na folha **servidores DNS**. Role horizontalmente para a esquerda a fim de copiar o segundo endereço IP e cole-o na caixa de texto **Adicionar servidor DNS**.

    ![Domain Services- atualizar o DNS](./media/getting-started/domain-services-update-dns.png)

5. Clique em **Salvar** quando terminar de atualizar os servidores DNS para a rede virtual.

> [!NOTE]
> As máquinas virtuais na rede só recebem as novas configurações de DNS após uma reinicialização. Se você precisar deles para obter as configurações de DNS atualizadas imediatamente, dispare uma reinicialização do portal, do PowerShell ou da CLI.
>
>

## <a name="next-step"></a>Próxima etapa
Tarefa 5: [habilitar a sincronização de senhas para o Azure Active Directory Domain Services](active-directory-ds-getting-started-password-sync.md)
