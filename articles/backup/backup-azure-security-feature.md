---
title: Recursos de segurança que protegem backups híbridos
description: Saiba como usar os recursos de segurança no Backup do Azure para tornar os backups mais seguros
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 06/08/2017
ms.openlocfilehash: 8c671b1b54b937f518f7179bb6940f31a28a78d4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94841011"
---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>Recursos de segurança para ajudar a proteger backups híbridos usando o Backup do Azure

Preocupações sobre problemas de segurança, como malware, ransomware e invasão, estão aumentando. Esses problemas de segurança podem ser dispendiosos em termos de dinheiro e dados. Para se proteger contra esses ataques, o Backup do Azure agora fornece recursos de segurança para ajudar a proteger os backups híbridos. Este artigo aborda como habilitar e usar esses recursos, usando um agente dos Serviços de Recuperação do Azure e o Servidor de Backup do Azure. Esses recursos incluem:

- **Prevenção**. Uma camada adicional de autenticação será adicionada sempre que uma operação crítica, como a alteração de senha, for executada. Essa validação é garantir que essas operações possam ser realizadas apenas por usuários com credenciais válidas do Azure.
- **Alertas**. Uma notificação por email é enviada ao administrador da assinatura sempre que uma operação crítica, como excluir dados do backup, for executada. Este email garante que o usuário receba uma notificação rapidamente sobre tais ações.
- **Recuperação**. Os dados de backup excluídos são retidos por mais 14 dias desde a data da exclusão. Isso garante a capacidade de recuperação de dados em um determinado período de tempo de maneira que não haja perda de dados mesmo se houver um ataque. Além disso, mais pontos de recuperação mínimos são mantidos para proteção contra dados corrompidos.

> [!NOTE]
> Os recursos de segurança não devem ser habilitados se você estiver usando o backup de VM de IaaS (infraestrutura como serviço). Esses recursos ainda não estão disponíveis para backup de VM IaaS, portanto, habilitá-los não terá nenhum impacto. Os recursos de segurança só devem ser habilitados se você estiver usando: <br/>
>
> - **Agente de backup do Azure**. Versão mínima do agente 2.0.9052. Depois de habilitar esses recursos, você deve atualizar para esta versão do agente para executar operações críticas. <br/>
> - **Servidor de backup do Azure**. Versão mínima do agente de Backup do Azure 2.0.9052 com o Servidor de Backup do Azure atualização 1. <br/>
> - **Data Protection Manager do System Center**. Versão mínima do agente de Backup do Azure 2.0.9052 com o Data Protection Manager 2012 R2 UR12 ou Data Protection Manager 2016 UR2. <br/>

> [!NOTE]
> Esses recursos estão disponíveis somente para o cofre dos Serviços de Recuperação. Todos os cofres dos Serviços de Recuperação recém-criados têm esses recursos habilitados por padrão. Para cofres dos Serviços de Recuperação existentes, os usuários habilitam esses recursos usando as etapas mencionadas na seção a seguir. Após a habilitação dos recursos, eles se aplicam a todos computadores do agente dos Serviços de Recuperação, instâncias do Servidor de Backup do Azure e servidores Data Protection Manager registrados com o cofre. A habilitação dessa configuração é uma ação única, e você não pode desabilitar esses recursos depois de habilitá-los.
>

## <a name="enable-security-features"></a>Habilitar recursos de segurança

Se você estiver criando um cofre dos serviços de recuperação, poderá usar todos os recursos de segurança. Se você estiver trabalhando com um cofre existente, habilite os recursos de segurança seguindo estas etapas:

1. Entre no portal do Azure usando suas credenciais do Azure.
2. Selecione **Procurar** e digite **Serviços de Recuperação**.

    ![Captura de tela da opção Procurar no portal do Azure](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    A lista de cofres de Serviços de Recuperação aparecerá. Nesta lista, selecione um cofre. O painel de cofres selecionados será aberto.
3. Na lista de itens que aparece no cofre, em **configurações**, selecione **Propriedades**.

    ![Captura de tela das opções do cofre dos Serviços de Recuperação](./media/backup-azure-security-feature/vault-list-properties.png)
4. Em **configurações de segurança**, selecione **Atualizar**.

    ![Captura de tela das propriedades do cofre dos Serviços de Recuperação](./media/backup-azure-security-feature/security-settings-update.png)

    O link de atualização abre o painel **configurações de segurança** , que fornece um resumo dos recursos e permite que você os habilite.
5. Na lista suspensa **, você configurou a autenticação multifator do Azure ad?**, selecione um valor para confirmar se você habilitou a [autenticação multifator do Azure ad](../active-directory/authentication/concept-mfa-howitworks.md). Se ele estiver habilitado, você será solicitado a autenticar de outro dispositivo (por exemplo, um telefone celular) ao entrar no portal do Azure.

   Ao realizar operações críticas no Backup, você poderá inserir um PIN de segurança, disponível no portal do Azure. A habilitação da autenticação multifator do Azure AD adiciona uma camada de segurança. Apenas usuários autorizados com credenciais válidas do Azure, e autenticados de um segundo dispositivo, podem acessar o portal do Azure.
6. Para salvar as configurações de segurança, selecione **habilitar** e selecione **salvar**. Você pode selecionar **habilitar** somente depois de selecionar um valor da lista **você configurou a autenticação multifator do Azure ad?** na etapa anterior.

    ![Captura de tela das configurações de segurança](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>Recuperar dados de backup excluídos

O backup retém os dados de backup excluídos por mais 14 dias e não os exclui imediatamente se a operação **parar backup com excluir dados de backup** for executada. Para restaurar esses dados no período de 14 dias, execute as seguintes etapas, dependendo do que você está usando:

Para usuários do **Agente de Serviços de Recuperação do Azure**:

1. Se o computador em que os backups estavam ocorrendo ainda estiver disponível, proteja novamente as fontes de dados excluídas e use os dados de recuperação no [mesmo computador](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) nos serviços de recuperação do Azure para recuperar de todos os pontos de recuperação antigos.
2. Se esse computador não estiver disponível, use [recuperar em um computador alternativo](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) para usar outro computador dos serviços de recuperação do Azure para obter esses dados.

Para usuários do **Servidor de Backup do Azure**:

1. Se o servidor onde os backups estavam ocorrendo ainda estiver disponível, proteja novamente as fontes de dados excluídas e use o recurso **recuperar dados** para se recuperar de todos os pontos de recuperação antigos.
2. Se esse servidor não estiver disponível, use [recuperar dados de outro servidor de backup do Azure](backup-azure-alternate-dpm-server.md) para usar outra instância de servidor de backup do Azure para obter esses dados.

Para usuários do **Data Protection Manager**:

1. Se o servidor onde os backups estavam ocorrendo ainda estiver disponível, proteja novamente as fontes de dados excluídas e use o recurso **recuperar dados** para se recuperar de todos os pontos de recuperação antigos.
2. Se esse servidor não estiver disponível, use [Adicionar DPM externo](backup-azure-alternate-dpm-server.md) para usar outro servidor de Data Protection Manager para obter esses dados.

## <a name="prevent-attacks"></a>Impedir ataques

Foram adicionadas verificações para garantir que somente os usuários válidos possam executar várias operações. Isso inclui a adição de uma camada extra de autenticação, e a manutenção de um período de retenção mínimo para fins de recuperação.

### <a name="authentication-to-perform-critical-operations"></a>Autenticação para realizar operações críticas

Como parte da adição de uma camada extra de autenticação para operações críticas, você será solicitado a inserir um PIN de segurança ao executar **parar proteção com excluir dados** e **alterar** as operações de senha.

> [!NOTE]
>
> Atualmente, o PIN de segurança não tem suporte para **interromper a proteção com dados de exclusão** para o DPM e o mAbs.

Para receber esse PIN:

1. Entre no portal do Azure.
2. Navegue até **serviços de recuperação**  >  **configurações** do cofre  >  **Propriedades**.
3. Em **PIN de segurança**, selecione **gerar**. Isso abre um painel que contém o PIN a ser inserido na interface do usuário do agente dos serviços de recuperação do Azure.
    Esse PIN é válido somente por cinco minutos e é gerado automaticamente após esse período.

### <a name="maintain-a-minimum-retention-range"></a>Manter um intervalo de retenção mínimo

Para garantir que sempre haja um número válido de pontos de recuperação disponíveis, as verificações a seguir foram adicionadas:

- Para a retenção diária, é necessário no mínimo **sete** dias de retenção.
- Para a retenção semanal, é necessário no mínimo **quatro** semanas de retenção.
- Para a retenção mensal, é necessário no mínimo **três** meses de retenção.
- Para a retenção anual, é necessário no mínimo **um** ano de retenção.

## <a name="notifications-for-critical-operations"></a>Notificações para operações críticas

Normalmente, quando uma operação crítica for executada, o administrador de assinatura receberá uma notificação por email com detalhes sobre a operação. Você pode configurar outros destinatários de email para essas notificações usando o portal do Azure.

Os recursos de Segurança mencionados neste artigo fornecem mecanismos de defesa contra ataques direcionados. Mais importante, se ocorrer um ataque, esses recursos oferecerão a capacidade de recuperar seus dados.

## <a name="troubleshooting-errors"></a>Solucionando erros

| Operação | Detalhes do erro | Resolução |
| --- | --- | --- |
| Alteração da política |Não foi possível modificar a política de backup. Erro: A operação atual falhou devido a um erro de serviço interno [0x29834]. Repita a operação após algum tempo. Se o problema persistir, contate o suporte da Microsoft. |**Causa:**<br/>Esse erro aparece quando as configurações de segurança estão habilitadas, você tenta reduzir o período de retenção abaixo dos valores mínimos especificados acima e você está em uma versão sem suporte (as versões com suporte são especificadas na primeira observação deste artigo). <br/>**Ação recomendada:**<br/> Nesse caso, você deve definir o período de retenção acima do período de retenção mínimo especificado (sete dias para diário, quatro semanas para semanal, três semanas para mensal ou um ano para anual) para continuar com as atualizações relacionadas à política. Opcionalmente, uma abordagem preferida seria atualizar o agente de backup, Servidor de Backup do Azure e/ou a UR do DPM para aproveitar todas as atualizações de segurança. |
| Alterar frase secreta |O PIN de Segurança inserido está incorreto. (ID: 100130) Forneça o PIN de Segurança correto para concluir esta operação. |**Causa:**<br/> Esse erro ocorre quando você insere um PIN de Segurança inválido ou expirado ao executar uma operação crítica (como alteração da frase secreta). <br/>**Ação recomendada:**<br/> Para concluir a operação, você deve inserir um PIN de Segurança válido. Para obter o PIN, entre no portal do Azure e navegue até o cofre dos serviços de recuperação > configurações > Propriedades > gerar PIN de segurança. Use esse PIN para alterar a frase secreta. |
| Alterar frase secreta |Falha na operação. ID: 120002 |**Causa:**<br/>Esse erro aparece quando as configurações de segurança estão habilitadas, você tenta alterar a senha e você está em uma versão sem suporte (versões válidas especificadas na primeira observação deste artigo).<br/>**Ação recomendada:**<br/> Para alterar a senha, primeiro você deve atualizar o agente de backup para a versão mínima 2.0.9052, Servidor de Backup do Azure para a atualização mínima 1 e/ou o DPM para o DPM 2012 R2 UR12 ou o DPM 2016 UR2 (baixar links abaixo) e, em seguida, inserir um PIN de segurança válido. Para obter o PIN, entre no portal do Azure e navegue até o cofre dos serviços de recuperação > configurações > Propriedades > gerar PIN de segurança. Use esse PIN para alterar a frase secreta. |

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao cofre dos Serviços de Recuperação do Azure](backup-azure-vms-first-look-arm.md) para habilitar esses recursos.
- [Baixe o agente dos Serviços de Recuperação do Azure mais recente](https://aka.ms/azurebackup_agent) para ajudar a proteger computadores com Windows e proteger seus dados de backup contra ataques.
- [Baixe o Servidor de Backup do Azure mais recente](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3) para ajudar a proteger as cargas de trabalho e proteger seus dados de backup contra ataques.
- [Baixar UR12 para o System Center 2012 R2 Data Protection Manager](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) ou [Baixar UR2 para System Center 2016 Data Protection Manager](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager) para ajudar a proteger as cargas de trabalho e seus dados de backup contra ataques.
