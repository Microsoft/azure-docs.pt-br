---
title: Recursos de segurança para ajudar a proteger backups híbridos usando o Backup do Azure
description: Saiba como usar os recursos de segurança no Backup do Azure para tornar os backups mais seguros
author: utraghuv
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/08/2017
ms.author: utraghuv
ms.openlocfilehash: 1e643e74f35b381dfb329453d81615bc69785ed7
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465411"
---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>Recursos de segurança para ajudar a proteger backups híbridos usando o Backup do Azure
Preocupações sobre problemas de segurança, como malware, ransomware e invasão, estão aumentando. Esses problemas de segurança podem ser dispendiosos em termos de dinheiro e dados. Para se proteger contra esses ataques, o Backup do Azure agora fornece recursos de segurança para ajudar a proteger os backups híbridos. Este artigo aborda como habilitar e usar esses recursos, usando um agente dos Serviços de Recuperação do Azure e o Servidor de Backup do Azure. Esses recursos incluem:

- **Prevenção**. Uma camada adicional de autenticação será adicionada sempre que uma operação crítica, como a alteração de senha, for executada. Essa validação é garantir que essas operações possam ser realizadas apenas por usuários com credenciais válidas do Azure.
- **Alertas**. Uma notificação por email é enviada ao administrador da assinatura sempre que uma operação crítica, como excluir dados do backup, for executada. Este email garante que o usuário receba uma notificação rapidamente sobre tais ações.
- **Recuperação**. Os dados de backup excluídos são retidos por mais 14 dias desde a data da exclusão. Isso garante a capacidade de recuperação de dados em um determinado período de tempo de forma que não haja perda de dados mesmo se houver um ataque. Além disso, mais pontos de recuperação mínimos são mantidos para proteção contra dados corrompidos.

> [!NOTE]
> Os recursos de segurança não devem ser habilitados se você estiver usando a infraestrutura como um serviço (IaaS) do backup da VM. Esses recursos ainda não estão disponíveis para o backup de VM do IaaS e, portanto, habilitá-los não terá qualquer impacto. Os recursos de segurança só deverão ser habilitados se você estiver usando: <br/>
>  * **Agente de Backup do Azure**. Versão mínima do agente 2.0.9052. Depois de habilitar esses recursos, você deverá atualizar para esta versão do agente para realizar operações críticas. <br/>
>  * **Servidor de Backup do Azure**. Versão mínima do agente de Backup do Azure 2.0.9052 com o Servidor de Backup do Azure atualização 1. <br/>
>  * **System Center Data Protection Manager**. Versão mínima do agente de Backup do Azure 2.0.9052 com o Data Protection Manager 2012 R2 UR12 ou Data Protection Manager 2016 UR2. <br/>


> [!NOTE]
> Esses recursos estão disponíveis somente para o cofre dos Serviços de Recuperação. Todos os cofres dos Serviços de Recuperação recém-criados têm esses recursos habilitados por padrão. Para cofres dos Serviços de Recuperação existentes, os usuários habilitam esses recursos usando as etapas mencionadas na seção a seguir. Após a habilitação dos recursos, eles se aplicam a todos computadores do agente dos Serviços de Recuperação, instâncias do Servidor de Backup do Azure e servidores Data Protection Manager registrados com o cofre. Habilitar essa configuração é uma ação única e você não poderá desabilitar esses recursos depois de habilitá-los.
>

## <a name="enable-security-features"></a>Habilitar recursos de segurança
Se você estiver criando um cofre dos Serviços de Recuperação, você pode usar todos os recursos de segurança. Se você estiver trabalhando com um cofre existente, habilite os recursos de segurança executando estas etapas:

1. Entre no portal do Azure usando suas credenciais do Azure.
2. Selecione **Procurar** e digite **Serviços de Recuperação**.

    ![Captura de tela da opção Procurar no portal do Azure](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    A lista de cofres de Serviços de Recuperação aparecerá. Nesta lista, selecione um cofre. O painel de cofres selecionados será aberto.
3. Na lista de itens que aparece sob o cofre, em **Configurações**, clique em **Propriedades**.

    ![Captura de tela das opções do cofre dos Serviços de Recuperação](./media/backup-azure-security-feature/vault-list-properties.png)
4. Em **Configurações de Segurança**, clique em **Atualizar**.

    ![Captura de tela das propriedades do cofre dos Serviços de Recuperação](./media/backup-azure-security-feature/security-settings-update.png)

    O link de atualização abre a folha **Configurações de Segurança**, que fornece um resumo dos recursos e permite que você os habilite.
5. Na lista suspensa **Você configurou a Autenticação Multifator do Azure?** , selecione um valor para confirmar se você habilitou a [Autenticação Multifator do Azure](../active-directory/authentication/multi-factor-authentication.md). Se estiver habilitada, você deverá autenticar de outro dispositivo (por exemplo, telefone celular) ao fazer logon no portal do Azure.

   Ao realizar operações críticas no Backup, você poderá inserir um PIN de segurança, disponível no portal do Azure. A habilitação da Autenticação Multifator do Azure adiciona uma camada de segurança. Apenas usuários autorizados com credenciais válidas do Azure, e autenticados de um segundo dispositivo, podem acessar o portal do Azure.
6. Para salvar as configurações de segurança, selecione **Habilitar** e clique em **Salvar**. Você só poderá selecionar **Habilitar** depois de selecionar um valor da lista **Você configurou a Autenticação Multifator do Azure** na etapa anterior.

    ![Captura de tela das configurações de segurança](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>Recuperar dados de backup excluídos
O backup retém dados de backup excluídos por mais de 14 dias e não os exclui imediatamente se **Parar backup coma operação excluir dados de backup** for executado. Para restaurar esses dados no período de 14 dias, execute as seguintes etapas, dependendo do que você está usando:

Para usuários do **Agente de Serviços de Recuperação do Azure**:

1. Se o computador em que os backups estavam acontecendo ainda estiver disponível, proteja novamente as fontes de dados excluídas e use [Recuperar dados para o mesmo computador](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) nos Serviços de Recuperação do Azure para recuperar todos os pontos de recuperação antigos.
2. Se esse computador não estiver disponível, use [Recuperar em um computador alternativo](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) para usar outro computador dos Serviços de Recuperação do Azure para obter esses dados.

Para usuários do **Servidor de Backup do Azure**:

1. Se o servidor onde os backups estavam acontecendo ainda estiver disponível, proteja novamente as fontes de dados excluídas e use o recurso de **Recuperar Dados** para recuperar todos os pontos de recuperação antigos.
2. Se esse computador não estiver disponível, use [Recuperar dados de outro Servidor de Backup do Azure](backup-azure-alternate-dpm-server.md) para usar outra instância do Servidor de Backup do Azure para obter esses dados.

Para usuários do **Data Protection Manager**:

1. Se o servidor onde os backups estavam acontecendo ainda estiver disponível, proteja novamente as fontes de dados excluídas e use o recurso de **Recuperar Dados** para recuperar todos os pontos de recuperação antigos.
2. Se esse servidor não estiver disponível, use [Adicionar DPM Externo](backup-azure-alternate-dpm-server.md) para usar outro servidor do Data Protection Manager para obter esses dados.

## <a name="prevent-attacks"></a>Impedir ataques
Foram adicionadas verificações para garantir que somente os usuários válidos possam executar várias operações. Isso inclui a adição de uma camada extra de autenticação, e a manutenção de um período de retenção mínimo para fins de recuperação.

### <a name="authentication-to-perform-critical-operations"></a>Autenticação para realizar operações críticas
Como parte da adição de uma camada extra de autenticação para operações críticas, você recebe uma solicitação para inserir o PIN de segurança ao executar operações **Parar Proteção com as Excluir dados** e **Alterar Senha**.

> [!NOTE]
> 
> Atualmente, o PIN de segurança não tem suporte para **Interromper a proteção com Excluir dados** para DPM e MABS.

Para receber esse PIN:

1. Entre no Portal do Azure.
2. Navegue até **Cofre dos Serviços de Recuperação** > **Configurações** > **Propriedades**.
3. Em **PIN de Segurança**, clique em **Gerar**. Isso abre uma folha que contém o PIN a ser inserido na interface do usuário agente dos Serviços de Recuperação do Azure.
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

## <a name="troubleshooting-errors"></a>Solucionar erros
| Operação | Detalhes do erro | Resolução |
| --- | --- | --- |
| Alteração da política |Não foi possível modificar a política de backup. Erro: A operação atual falhou devido a um erro de serviço interno [0x29834]. Tente a operação novamente após algum tempo. Se o problema persistir, contate o suporte da Microsoft. |**Causa:**<br/>Esse erro ocorre quando as configurações de segurança estão habilitadas. Tente reduzir o intervalo de retenção para abaixo dos valores mínimos especificados acima e verifique se você está usando uma versão sem suporte (as versões com suporte são especificadas na primeira observação deste artigo). <br/>**Ação recomendada:**<br/> Nesse caso, você deve definir o período de retenção acima do período de retenção mínimo especificado (sete dias para diário, quatro semanas para semanal, três semanas para mensal ou um ano para anual) para prosseguir com atualizações relacionadas à política. Opcionalmente, a abordagem preferencial será atualizar o agente de backup, o Servidor de Backup do Azure e/ou o UR do DPM para utilizar todas as atualizações de segurança. |
| Alterar frase secreta |O PIN de segurança inserido está incorreto. (ID: 100130) Forneça o PIN de Segurança correto para concluir esta operação. |**Causa:**<br/> Esse erro ocorre quando você insere um PIN de Segurança inválido ou expirado ao executar uma operação crítica (como alteração da frase secreta). <br/>**Ação recomendada:**<br/> Para concluir a operação, você deve inserir um PIN de Segurança válido. Para obter o PIN, faça logon no portal do Azure e navegue para o cofre dos Serviços de Recuperação > Configurações > Propriedades > Gerar PIN de Segurança. Use esse PIN para alterar a frase secreta. |
| Alterar frase secreta |Falha na operação. ID: 120002 |**Causa:**<br/>Esse erro ocorre quando as configurações de segurança estão habilitadas. Tente alterar a frase secreta e verifique se você está usando uma versão sem suporte (as versões válidas são especificadas na primeira observação deste artigo).<br/>**Ação recomendada:**<br/> Para alterar a frase secreta, primeiro você deve atualizar o agente de backup para a versão mínima 2.0.9052, o servidor de Backup do Azure para a atualização mínima 1 e/ou o DPM para, no mínimo, DPM 2012 R2 UR12 ou DPM 2016 UR2 (links de download abaixo) e, em seguida, inserir um PIN de Segurança válido. Para obter o PIN, faça logon no portal do Azure e navegue para o cofre dos Serviços de Recuperação > Configurações > Propriedades > Gerar PIN de Segurança. Use esse PIN para alterar a frase secreta. |

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao cofre dos Serviços de Recuperação do Azure](backup-azure-vms-first-look-arm.md) para habilitar esses recursos.
* [Baixe o agente dos Serviços de Recuperação do Azure mais recente](https://aka.ms/azurebackup_agent) para ajudar a proteger computadores com Windows e proteger seus dados de backup contra ataques.
* [Baixe o Servidor de Backup do Azure mais recente](https://aka.ms/latest_azurebackupserver) para ajudar a proteger as cargas de trabalho e proteger seus dados de backup contra ataques.
* [Baixar UR12 para o System Center 2012 R2 Data Protection Manager](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) ou [Baixar UR2 para System Center 2016 Data Protection Manager](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager) para ajudar a proteger as cargas de trabalho e seus dados de backup contra ataques.
