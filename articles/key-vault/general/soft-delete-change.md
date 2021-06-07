---
title: Habilitar a exclusão reversível em todos os objetos de cofre de chaves – Azure Key Vault | Microsoft Docs
description: Use este documento para adotar a exclusão reversível para todos os cofres de chaves e fazer alterações de aplicativo e administrativas para evitar erros de conflito.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: 7e1b2ee95864affa6e5e72e1f8354767dc95bdb1
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753318"
---
# <a name="soft-delete-will-be-enabled-on-all-key-vaults"></a>A exclusão reversível será habilitada em todos os cofres de chaves

> [!WARNING]
> Alteração interruptiva: a capacidade de recusar a exclusão reversível será preterida em breve. Os usuários e administradores do Azure Key Vault devem habilitar a exclusão reversível nos seus cofres de chaves imediatamente.
>
> Para o HSM Gerenciado do Azure Key Vault, a exclusão reversível é habilitada por padrão e não pode ser desabilitada.

Quando um segredo for excluído de um cofre de chaves sem proteção de exclusão reversível, o segredo será excluído permanentemente. Atualmente, os usuários podem recusar a exclusão reversível durante a criação do cofre de chaves. No entanto, a Microsoft logo habilitará a proteção de exclusão reversível em todos os cofres de chaves para proteger segredos de exclusão acidental ou mal-intencionada por um usuário. Os usuários não poderão mais recusar nem desativar a exclusão reversível.

:::image type="content" source="../media/softdeletediagram.png" alt-text="Diagrama mostrando como um cofre de chaves é excluído com a proteção de exclusão reversível versus sem a proteção de exclusão reversível.":::

Para obter detalhes completos sobre a funcionalidade de exclusão reversível, confira [Visão geral da exclusão reversível do Azure Key Vault](soft-delete-overview.md).

## <a name="can-my-application-work-with-soft-delete-enabled"></a>Meu aplicativo poderá funcionar com a exclusão reversível habilitada?

> [!Important] 
> Examine as informações a seguir antes de ativar a exclusão reversível em seus cofres de chaves.

Os nomes do cofre de chaves são globalmente exclusivos. Os nomes de segredos armazenados em um cofre de chaves também são exclusivos. Você não poderá reutilizar o nome de um cofre de chaves ou um objeto do cofre de chaves que exista no estado de exclusão reversível. 

Por exemplo, se o seu aplicativo criar programaticamente um cofre de chaves chamado "Cofre A" e, posteriormente, excluir o "Cofre A", o cofre de chaves será movido para o estado excluído reversivelmente. O aplicativo não poderá recriar outro cofre de chaves chamado "Cofre A" até que o cofre de chaves seja limpo do estado de exclusão reversível. 

Além disso, se o aplicativo criar uma chave chamada `test key` no "Cofre A" e posteriormente excluir essa chave, ele não poderá criar uma nova chave chamada `test key` no "Cofre A" até que o objeto `test key` seja limpo do estado excluído reversivelmente. 

Tentativas de excluir um objeto do cofre de chaves e recriá-lo com o mesmo nome sem limpá-lo primeiro do estado de exclusão reversível poderão resultar em erros de conflito. Esses erros podem fazer com que os seus aplicativos ou automação falhem. Consulte sua equipe de desenvolvimento antes de fazer as alterações administrativas e de aplicativo necessárias a seguir. 

### <a name="application-changes"></a>Mudanças do aplicativo

Se o seu aplicativo pressupor que a exclusão reversível não está habilitada e espera que os nomes do segredo ou do cofre de chaves excluídos estejam disponíveis para reutilização imediata, você precisará fazer as alterações a seguir na lógica do aplicativo.

1. Exclua o cofre de chaves ou o segredo original.
1. Limpe o cofre de chaves ou o segredo no estado de exclusão reversível.
1. Aguarde a conclusão da limpeza. A recriação imediata pode resultar em um conflito.
1. Crie novamente o cofre de chaves com o mesmo nome.
1. Se a operação de criação ainda resultar em um erro de conflito de nome, tente recriar o cofre de chaves novamente. Os registros de DNS do Azure podem levar até dez minutos para serem atualizados no pior cenário.

### <a name="administration-changes"></a>Mudanças na administração

As entidades de segurança que precisam de acesso para excluir permanentemente os segredos precisam receber permissões de política de acesso adicionais para limpar esses segredos e o cofre de chaves.

Se você tiver alguma política do Azure nos cofres de chaves que exige que a exclusão reversível seja desativada, desabilite essa política. Talvez seja necessário escalonar esse problema para um administrador que controla as políticas do Azure aplicadas ao seu ambiente. Se essa política não for desabilitada, você poderá perder a capacidade de criar cofres de chaves no escopo da política aplicada.

Se a sua organização estiver sujeita a requisitos de conformidade legal e não puder permitir que os cofres de chaves e os segredos excluídos permaneçam em um estado recuperável por um longo período, você precisará ajustar o período de retenção da exclusão reversível para atender aos padrões da sua organização. Você pode configurar o período de retenção para durar de 7 a 90 dias.

## <a name="procedures"></a>Procedimentos

### <a name="audit-your-key-vaults-to-check-if-soft-delete-is-enabled"></a>Auditar os seus cofres de chaves para verificar se a exclusão reversível está habilitada

1. Entre no portal do Azure.
1. Pesquise por **Azure Policy**.
1. Selecione **Definições**.
1. Em **Categoria**, selecione **Key Vault** no filtro.
1. Selecione a política **O cofre de chaves deve estar com a exclusão reversível habilitada**.
1. Selecione **Atribuir**.
1. Defina o escopo da sua assinatura.
1. Defina o efeito da política como **Auditoria**.
1. Selecione **Examinar + criar**. Pode levar até 24 horas para que uma verificação completa do seu ambiente seja concluída.
1. No painel **Azure Policy**, clique em **Conformidade**.
1. Selecione a política que você aplicou.

Agora, você conseguirá filtrar e ver quais cofres de chaves têm a exclusão reversível habilitada (recursos em conformidade) e quais deles não têm a exclusão reversível habilitada (recursos sem conformidade).

### <a name="turn-on-soft-delete-for-an-existing-key-vault"></a>Ativar a exclusão reversível para um cofre de chaves existente

1. Entre no portal do Azure.
1. Pesquise o seu cofre de chaves.
1. Selecione **Propriedades** em **Configurações**.
1. Em **Exclusão Reversível**, selecione a opção **Habilitar a recuperação deste cofre e dos respectivos objetos**.
1. Defina o período de retenção para a exclusão reversível.
1. Clique em **Salvar**.

### <a name="grant-purge-access-policy-permissions-to-a-security-principal"></a>Conceder permissões de política de acesso de limpeza a uma entidade de segurança

1. Entre no portal do Azure.
1. Pesquise o seu cofre de chaves.
1. Selecione **Políticas de Acesso** em **Configurações**.
1. Selecione a entidade de serviço à qual você deseja permitir acesso.
1. Percorra cada menu suspenso em **Chave**, **Segredo** e **Permissões de certificado** até ver **Operações Privilegiadas**. Selecione a permissão **Limpar**.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="does-this-change-affect-me"></a>Esta alteração me afeta?

Se você já tiver a exclusão reversível ativada ou se você não excluir e recriar os objetos do cofre de chaves com o mesmo nome, provavelmente não observará nenhuma alteração no comportamento do cofre de chaves.

Se você tiver um aplicativo que exclui e recria com frequência objetos do cofre de chaves com as mesmas convenções de nomenclatura, precisará fazer alterações na lógica do aplicativo para manter o comportamento esperado. Consulte a seção [Alterações no aplicativo](#application-changes) neste artigo.

### <a name="how-do-i-benefit-from-this-change"></a>Como fazer para me beneficiar desta mudança?

A proteção de exclusão reversível oferece à sua organização uma camada adicional de proteção contra exclusão acidental ou mal-intencionada. Como administrador do cofre de chaves, você pode restringir o acesso às permissões de recuperação e de limpeza.

Se um usuário excluir acidentalmente um cofre de chaves ou um segredo, você poderá conceder a eles permissões de acesso para recuperar o segredo em si sem criar o risco de excluir permanentemente o segredo ou o cofre de chaves. Esse processo de autoatendimento minimizará o tempo de inatividade no seu ambiente e garantirá a disponibilidade dos seus segredos.

### <a name="how-do-i-find-out-if-i-need-to-take-action"></a>Como fazer para descobrir se preciso executar ações?

Siga as etapas na seção [Auditar seus cofres de chaves para verificar se a exclusão reversível está habilitada](#audit-your-key-vaults-to-check-if-soft-delete-is-enabled) neste artigo. Qualquer cofre de chaves que não tenha a exclusão reversível ativada será afetado por essa alteração.

### <a name="what-action-do-i-need-to-take"></a>Quais ações preciso realizar?

Depois de confirmar que você não precisa fazer alterações na lógica do aplicativo, ative a exclusão reversível em todos os cofres de chaves.

### <a name="when-do-i-need-to-take-action"></a>Quando preciso executar ações?

Para garantir que os seus aplicativos não sejam afetados, ative a exclusão reversível nos seus cofres de chaves assim que possível.

## <a name="next-steps"></a>Próximas etapas

- Entre em contato conosco com perguntas sobre essa alteração em [akvsoftdelete@microsoft.com](mailto:akvsoftdelete@microsoft.com).
- Leia a [Visão geral da exclusão reversível](soft-delete-overview.md).
