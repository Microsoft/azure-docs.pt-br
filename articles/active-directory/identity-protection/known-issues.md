---
title: Perguntas frequentes e problemas conhecidos com o Identity Protection (atualizada) no Azure Active Directory | Microsoft Docs
description: Perguntas frequentes e problemas conhecidos com a proteção de identidade (atualizada) no Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 01/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d5aa50aec98b3944aed92b9da49182f0608f34c
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333894"
---
# <a name="faqs-and-known-issues-with-identity-protection-refreshed-in-azure-active-directory"></a>Perguntas frequentes e problemas conhecidos com o Identity Protection (atualizado) no Azure Active Directory

## <a name="dismiss-user-risk-known-issues"></a>Ignorar problemas conhecidos de risco do usuário

**Ignorar o risco de usuário** no Identity Protection clássico define o ator no histórico de risco do usuário no Identity Protection (atualizado) para o **Azure AD**.

**Ignorar o risco de usuário** no Identity Protection (atualizado) define o ator no histórico de risco do usuário no Identity Protection (atualizado) para **\<nome do administrador com um hiperlink que aponta para a folha do usuário\>** .

Há um problema conhecido atual causando latências no fluxo de descarte de risco de usuário. Se você tiver uma "política de risco do usuário", ela deixará de ser aplicada aos usuários descartados alguns minutos após clicar em "Ignorar o risco de usuário". No entanto, há atrasos conhecidos na experiência do usuário que atualizam o "Estado de risco" de usuários descartados. Como alternativa, atualize a página no nível do navegador para ver o "Estado de risco" mais recente do usuário.

## <a name="risky-users-report-known-issues"></a>Os usuários arriscados relatam problemas conhecidos

As consultas no campo **nome de usuário** diferenciam maiúsculas de minúsculas, enquanto consultas no campo **nome** não diferenciam.

Ativar/desativar **Mostrar datas como** oculta a coluna **ÚLTIMA ATUALIZAÇÃO DO RISCO**. Para adicionar novamente o clique de coluna **Colunas** na parte superior da folha Usuários Arriscados.

**Ignorar todos os eventos** no Identity Protection clássico define o status dos eventos de risco para **Fechado (resolvido)** .

## <a name="risky-sign-ins-report-known-issues"></a>Problemas conhecidos do relatório de entradas arriscadas

**Resolver** em um evento de risco define o status como **Usuários passaram por MFA orientada por política com base no risco**.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-event"></a>Por que não é possível definir meus próprios níveis de risco para cada evento de risco?

Os níveis de risco no Identity Protection baseiam-se na precisão da detecção e são alimentados por nosso aprendizado de máquina supervisionado. Para personalizar a quais experiências os usuários são apresentados, o administrador pode incluir/excluir determinados usuários/grupos das políticas de Risco de Usuário e Risco de Entrada.

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>Por que o local de uma entrada não corresponde àquele de onde o usuário realmente entrou?

O mapeamento de localização geográfica do IP é um desafio de toda a indústria. Se você achar que o local indicado no relatório de entradas não corresponde com o local real, entre em contato com o suporte. 

### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Como funcionam os mecanismos de comentários no Identity Protection?

**Confirmar como comprometido** (em uma entrada) – informa o Azure AD Identity Protection que a entrada não foi executada pelo proprietário da identidade e indica um comprometimento.

- Depois de receber seus comentários, movemos o estado de risco de entrada e de usuário para **Confirmado como comprometido** e o nível de risco para **Alto**.

- Além disso, fornecemos as informações para nossos sistemas de aprendizado de máquina para futuras melhorias na avaliação de risco.

    > [!NOTE]
    > Se o usuário já foi corrigido, não clique em **Confirmar como comprometido** porque ele muda o estado de risco de entrada e de usuário para **Confirmado como comprometido** e o nível de risco para **Alto**.

**Confirmar como seguro** (em uma entrada) – informa o Azure AD Identity Protection que a entrada foi executada pelo proprietário da identidade e não indica um comprometimento.

- Depois de receber esses comentários, movemos o estado de risco de entrada (não o de usuário) para **Confirmado como seguro** e o nível de risco para **-** .

- Além disso, fornecemos as informações para nossos sistemas de aprendizado de máquina para futuras melhorias na avaliação de risco.

    > [!NOTE]
    > Se você achar que o usuário não foi comprometido, use **Ignorar o risco de usuário** no nível do usuário em vez de usar **Confirmado como seguro** no nível de entrada. Um comando **Ignorar o risco de usuário** no nível do usuário fecha o risco do usuário e todas as últimas entradas de risco e eventos de risco.

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-events-are-shown-in-identity-protection"></a>Por que estou vendo um usuário com uma pontuação de risco baixa (ou superior), mesmo se não há entradas arriscadas ou eventos de risco sendo mostrados no Identity Protection?

Já que o risco de usuário é cumulativo por natureza e não expira, um usuário pode ter um risco de usuário de baixo ou acima, mesmo se não existem entradas arriscadas ou eventos de risco recentes mostrados no Identity Protection. Isso pode ocorrer se a única atividade mal-intencionada em um usuário ocorreu além do período de tempo para o qual armazenamos os detalhes de entradas arriscadas e eventos de risco. Nós não expiramos o risco do usuário porque os atores ruins comprovadamente permanecem no ambiente do cliente por mais de 140 dias por trás de uma identidade comprometida antes aumentar seu ataque. Os clientes podem examinar a linha do tempo de risco do usuário para entender por que um usuário está em risco ao acessar: `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>Por que uma entrada tem uma pontuação de "risco de entrada (agregação)" alta quando as detecções associadas a ela são de risco baixo ou médio?

A pontuação de risco de agregação alta pode ser baseada em outros recursos de entrada ou o fato de que mais de uma detecção foi acionada para essa entrada. E, por outro lado, uma entrada pode ter um risco de entrada (agregação) médio, mesmo se as detecções associadas com a entrada são de alto risco. 
