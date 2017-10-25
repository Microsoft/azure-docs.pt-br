---
title: "Perguntas frequentes sobre a migração da plataforma da Central de Segurança | Microsoft Docs"
description: "Encontre respostas para perguntas frequentes sobre a migração da plataforma da Central de Segurança do Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: terrylan
ms.openlocfilehash: 4b88b5015fcf44e8979b8b1a3aa1eb26f0fbb704
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="security-center-platform-migration-faq"></a>Perguntas frequentes sobre a migração da plataforma da Central de Segurança
No início de junho de 2017, a Central de Segurança do Azure começou a usar o Microsoft Monitoring Agent para coletar e armazenar dados. Para saber mais, veja [Migração da Plataforma Central de Segurança do Azure](security-center-platform-migration.md). Encontre respostas para perguntas frequentes sobre a migração da plataforma.

## <a name="data-collection-agents-and-workspaces"></a>Coleta de dados, agentes e espaços de trabalho

### <a name="how-is-data-collected"></a>Como os dados são coletados?
A Central de Segurança usa o Microsoft Monitoring Agent para coletar dados de segurança de suas VMs. Os dados de segurança incluem informações sobre as configurações de segurança, que são usadas para identificar vulnerabilidades, e eventos de segurança, que são usados para detectar ameaças. Os dados coletados pelo agente são armazenados em um espaço de trabalho do Log Analytics conectado à VM ou um novo espaço de trabalho criado pela Central de Segurança. Quando a Central de Segurança cria um novo espaço de trabalho, a localização geográfica da VM é levada em consideração.

> [!NOTE]
> O Microsoft Monitoring Agent é o mesmo agente usado pelo OMS (Operations Management Suite), o serviço Log Analytics e pelo SCOM (System Center Operations Manager).
>
>

Quando a coleta de dados é habilitada pela primeira vez, ou quando suas assinaturas são migradas, a Central de Segurança verifica se o Microsoft Monitoring Agent já está instalado como uma extensão do Azure em cada uma de suas VMs. Se o Microsoft Monitoring Agent não estiver instalado, a Central de segurança:

- instalará o Microsoft Monitoring Agent na VM
   - Se um espaço de trabalho criado pela Central de Segurança já existir na mesma localização geográfica da VM, o agente estará conectado a esse espaço de trabalho
   - Se não houver um espaço de trabalho, a Central de Segurança criará um novo grupo de recursos e um espaço de trabalho padrão nessa localização geográfica, e conectará o agente ao espaço de trabalho. A convenção de nomenclatura para o grupo de recursos e o espaço de trabalho é:

       Espaço de trabalho: DefaultWorkspace-[ID da assinatura]-[localização geográfica]

       Grupo de recursos: DefaultResouceGroup-[localização geográfica]
- instalará uma solução da Central de Segurança no espaço de trabalho

O local do espaço de trabalho tem base no local da VM. Para saber mais, consulte [Segurança dos Dados](security-center-data-security.md).

> [!NOTE]
> Antes da migração da plataforma, Central de Segurança coletava dados de segurança de suas VMs usando o Azure Monitoring Agent, e os dados eram armazenados em sua conta de armazenamento. Após a migração da plataforma, a Central de Segurança usa o Microsoft Monitoring Agent e o espaço de trabalho para coletar e armazenar os mesmos dados. A conta de armazenamento pode ser removida após a migração.
>
>

### <a name="am-i-billed-for-log-analytics-or-oms-on-the-workspaces-created-by-security-center"></a>Sou cobrado pelo Log Analytics ou pelo OMS nos espaços de trabalho criados pela Central de Segurança?
Não. Os espaços de trabalho criados pela Central de Segurança, embora sejam configurados para cobrança de OMS por nó, não incorrerão em encargos do OMS. A cobrança da Central de Segurança sempre tem base em sua política de segurança da Central de Segurança e nas soluções instaladas em um espaço de trabalho:

- **Camada gratuita** – A Central de Segurança instala a solução 'SecurityCenterFree' no espaço de trabalho padrão. Você não será cobrado pela Camada gratuita.
- **Camada Standard** – A Central de Segurança instala as soluções 'SecurityCenterFree' e 'Security' no espaço de trabalho padrão.

Para saber mais sobre preços, confira [preços da Central de Segurança](https://azure.microsoft.com/pricing/details/security-center/). A página de preços aborda as mudanças no armazenamento de dados de segurança e cobrança rateada começando em junho de 2017.

> [!NOTE]
> O tipo de preço do OMS para espaços de trabalho criados pela Central de Segurança não afeta a cobrança da Central de Segurança.
>
>

### <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Posso excluir os espaços de trabalho padrão criados pela Central de Segurança?
**Não recomendamos a exclusão do espaço de trabalho padrão.** A Central de Segurança usa os espaços de trabalho padrão para armazenar dados de segurança de suas VMs.  Se você excluir um espaço de trabalho, a Central de Segurança não poderá coletar esses dados, e algumas recomendações de segurança e alertas não estarão disponíveis

Para recuperar, remova o Microsoft Monitoring Agent nas VMs conectadas ao espaço de trabalho excluído. A Central de Segurança reinstala o agente e cria novos espaços de trabalho padrão.

### <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Como posso usar meu espaço de trabalho existente do Log Analytics?

Você pode selecionar um espaço de trabalho existente do Log Analytics para armazenar dados coletados pela Central de Segurança. Para usar o espaço de trabalho existente do Log Analytics:

- O espaço de trabalho deve ser associado à sua assinatura do Azure selecionada.
- No mínimo, você deve ter permissões de leitura para acessar o espaço de trabalho.

Para selecionar um espaço de trabalho existente do Log Analytics:

1. Em **Política de segurança – Coleta de dados**, selecione **Usar outro espaço de trabalho**.

   ![Usar outro espaço de trabalho][5]

2. No menu suspenso, selecione um espaço de trabalho para armazenar os dados coletados.

   > [!NOTE]
   > No menu suspenso, são mostrados apenas os espaços de trabalho aos quais você tem acesso e que estejam em sua assinatura do Azure.
   >
   >

3. Selecione **Salvar**.
4. Ao selecionar **Salvar**, você será questionado se deseja reconfigurar as VMs monitoradas.

   - Selecione **Não** se quiser que as novas configurações de espaço de trabalho sejam **aplicadas somente às novas VMs**. As novas configurações de espaço de trabalho se aplicam somente a novas instalações de agente: VMs recém-descobertas que não têm o Microsoft Monitoring Agent instalado.
   - Selecione **Sim** se quiser que as novas configurações de espaço de trabalho sejam **aplicadas a todas as VMs**. Além disso, todas as VMs conectadas a um espaço de trabalho criado da Central de Segurança serão reconectadas ao novo espaço de trabalho de destino.

   > [!NOTE]
   > Se selecionar Sim, você não deverá excluir os espaços de trabalho criados pela Central de Segurança até que todas as VMs sejam reconectadas ao novo espaço de trabalho de destino. Essa operação falhará se um espaço de trabalho for excluído muito cedo.
   >
   >

   - Selecione **Cancelar** para cancelar a operação.

      ![Reconfigurar VMs monitoradas][6]

### <a name="what-if-the-microsoft-monitoring-agent-was-already-installed-as-an-extension-on-the-vm"></a>E se o Microsoft Monitoring Agent já estivesse instalado como uma extensão na VM?
A Central de Segurança não substitui as conexões existentes nos espaços de trabalho do usuário. A Central de Segurança armazena dados de segurança da VM no espaço de trabalho que já está conectado.

### <a name="what-if-i-had-a-microsoft-monitoring-agent-installed-on-the-machine-but-not-as-an-extension"></a>E se eu já tivesse um Microsoft Monitoring Agent instalado no computador, mas não como uma extensão?
Se o Microsoft Monitoring Agent estiver instalado diretamente na VM (não como uma extensão do Azure), a Central de Segurança não instalará o Microsoft Monitoring Agent e o monitoramento de segurança será limitado.

### <a name="what-is-the-impact-of-removing-these-extensions"></a>Qual é o impacto da remoção dessas extensões?
Se você remover a Extensão de Monitoramento da Microsoft, a Central de Segurança não será capaz de coletar dados de segurança da VM e algumas recomendações de segurança e alertas não estarão disponíveis. Dentro de 24 horas, a Central de Segurança determinará que a VM não possui a extensão e reinstalará a extensão.

### <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Como fazer para impedir a instalação automática do agente e a criação do espaço de trabalho?
Você pode desligar o provisionamento automático para suas assinaturas na política de segurança, mas isso não é recomendado. Desativar o provisionamento automático limita as recomendações e os alertas da Central de Segurança. O provisionamento automático é necessário para assinaturas no tipo de preço Standard. Para desabilitar o provisionamento automático:

1. Se sua assinatura estiver configurada para a camada Standard, abra a política de segurança para essa assinatura e selecione a camada **Gratuita**.

   ![Tipo de preço ][1]

2. Em seguida, desligue o provisionamento automático, selecionando **Desativar** na folha **Política de segurança – Coleta de dados**.
   ![Coleta de dados][2]

### <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Como fazer para remover as extensões do OMS instaladas pela Central de Segurança?
Você pode remover manualmente o Microsoft Monitoring Agent. Isso não é recomendado, pois limita as recomendações e os alertas da Central de Segurança.

> [!NOTE]
> Se a coleta de dados estiver habilitada, a Central de Segurança reinstalará o agente após a remoção.  Você precisa desabilitar a coleta de dados antes de remover manualmente o agente. Confira [Como fazer para impedir a instalação automática do agente e a criação do espaço de trabalho?](#how-do-i-stop-the-automatic-agent-installation-and-workspace-creation?) para obter instruções sobre como desabilitar a coleta de dados.
>
>

Para remover manualmente o agente:

1.  No portal, abra o **Log Analytics**.
2.  Na folha Log Analytics, selecione um espaço de trabalho:
3.  Selecione cada VM que você não quer monitorar e selecione **Desconectar**.

   ![Remova o agente][3]

> [!NOTE]
> Se uma VM do Linux já tiver um agente do OMS, mas não como extensão, a remoção da extensão também removerá o agente, e o cliente precisará reinstalá-lo.
>
>

## <a name="existing-oms-customers"></a>Clientes existentes do OMS

### <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>A Central de Segurança substitui todas as conexões existentes entre as VMs e os espaços de trabalho?
Se uma VM já tiver o Microsoft Monitoring Agent instalado como uma extensão do Azure, a Central de Segurança não substituirá a conexão do espaço de trabalho existente. Em vez disso, a Central de Segurança usará o espaço de trabalho existente.

Uma solução da Central de Segurança será instalado no espaço de trabalho, se ainda não estiver presente, e a solução será aplicada apenas às VMs relevantes. Quando você adiciona uma solução, ela é implantada automaticamente por padrão em todos os agentes do Windows e Linux conectados ao seu espaço de trabalho do Log Analytics. [Direcionamento de Solução](../operations-management-suite/operations-management-suite-solution-targeting.md), que é um recurso do OMS que permite a aplicação de um escopo às suas soluções.

Se o Microsoft Monitoring Agent estiver instalado diretamente na VM (não como uma extensão do Azure), a Central de Segurança não instalará o Microsoft Monitoring Agent e o monitoramento de segurança será limitado.

### <a name="what-should-i-do-if-i-suspect-that-the-data-platform-migration-broke-the-connection-between-one-of-my-vms-and-my-workspace"></a>O que devo fazer se suspeitar de que a migração da plataforma de dados interrompeu a conexão entre uma das minhas VMs e meu espaço de trabalho?
Isso não deve ocorrer. Se ocorrer, [Crie uma solicitação de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md) e inclua os seguintes detalhes:

- A ID de recurso do Azure da VM afetada
- A ID de recurso do Azure do espaço de trabalho configurado na extensão antes da conexão ser interrompida
- O agente e a versão instalados anteriormente

### <a name="does-security-center-install-solutions-on-my-existing-oms-workspaces-what-are-the-billing-implications"></a>A Central de Segurança instala soluções em meus espaços de trabalho existentes do OMS? Quais são as implicações de cobrança?
Quando a Central de Segurança identificar que uma VM já está conectada a um espaço de trabalho que você criou, a Central de Segurança habilitará as soluções neste espaço de trabalho de acordo com seu tipo de preço. As soluções são aplicadas apenas às VMs do Azure relevantes, por meio do [direcionamento de solução](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting), portanto, a cobrança permanece igual.

- **Camada gratuita** – A Central de Segurança instala a solução 'SecurityCenterFree' no espaço de trabalho. Você não será cobrado pela Camada gratuita.
- **Camada Standard** – A Central de Segurança instala as soluções 'SecurityCenterFree' e 'Security' no espaço de trabalho.

   ![Soluções no espaço de trabalho padrão][4]

> [!NOTE]
> A solução 'Security' no Log Analytics é a solução de Segurança e Auditoria no OMS.
>
>

### <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Eu já tenho espaços de trabalho em meu ambiente, posso usá-los para coletar dados de segurança?
Se uma VM já tiver o Microsoft Monitoring Agent instalado como uma extensão do Azure, a Central de Segurança usará o espaço de trabalho existente conectado. Uma solução da Central de Segurança será instalado no espaço de trabalho, se ainda não estiver presente, e a solução será aplicada apenas às VMs relevantes por meio do [direcionamento de solução](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting).

Quando a Central de Segurança instala o Microsoft Monitoring Agent em VMs, ela usa os espaços de trabalho padrão criados pela Central de Segurança. Em breve, os clientes poderão configurar quais espaços de trabalho são usados.

### <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Já tenho a solução de segurança em meus espaços de trabalho. Quais são as implicações de cobrança?
A solução de Segurança e Auditoria é usada para habilitar recursos da camada Standard da Central de Segurança para VMs do Azure. Se a solução de Segurança e Auditoria já estiver instalada em um espaço de trabalho, a Central de Segurança usará a solução existente. Não há nenhuma alteração na cobrança.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a migração de plataforma da Central de Segurança, confira

- [Migração de plataforma da Central de Segurança do Azure](security-center-platform-migration.md)
- [Guia de solução de problemas da Central de Segurança do Azure](security-center-troubleshooting-guide.md)

<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png
[5]: ./media/security-center-platform-migration-faq/use-another-workspace.png
[6]: ./media/security-center-platform-migration-faq/reconfigure-monitored-vm.png
