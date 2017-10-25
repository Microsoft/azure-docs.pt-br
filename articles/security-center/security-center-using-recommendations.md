---
title: "Usar as recomendações da Central de Segurança do Azure para aprimorar a segurança | Microsoft Docs"
description: " Saiba como usar as políticas de segurança e as recomendações da Central de Segurança do Azure para ajudar a atenuar um ataque de segurança. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: terrylan
ms.openlocfilehash: 0616f5e501324bfd821c1455ce234602f1fcf1bd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Usar as recomendações da Central de Segurança do Azure para aprimorar a segurança
Você pode reduzir as chances de um evento de segurança significativo configurando uma política de segurança e implementando as recomendações fornecidas pela Central de Segurança do Azure. Esse artigo mostra como usar as políticas de segurança e as recomendações da Central de Segurança para ajudar a atenuar uma violação de segurança.

> [!NOTE]
> Este artigo se baseia nas funções e conceitos introduzidos no [guia de planejamento e operações](security-center-planning-and-operations-guide.md) da Central de Segurança. É uma boa ideia examinar o guia de planejamento antes de continuar.
>
>

## <a name="managing-security-recommendations"></a>Gerenciando recomendações de segurança
Uma política de segurança define o conjunto de controles recomendados para os recursos na assinatura ou grupo de recursos especificado. Na Central de Segurança, você define as políticas de acordo com os requisitos de segurança de sua empresa. Para saber mais, confira [Configurar políticas de segurança na Central de Segurança](security-center-policies.md).

As políticas de segurança para os grupos de recursos são herdadas do nível da assinatura.

![Herança da política de segurança][1]

Se você precisar de políticas personalizadas em grupos de recursos específicos, deverá desabilitar a herança no grupo de recursos. Para desabilitá-la, defina a Herança como Exclusiva na folha Política de segurança e personalize os controles para os quais a Central de Segurança mostra recomendações.

Por exemplo, se você tiver cargas de trabalho que não exigem a política TDE (Transparent Data Encryption) do Banco de Dados SQL, desligue-a no nível da assinatura e habilite-a somente nos grupos de recursos nos quais a TDE do SQL é necessária.

> [!NOTE]
> Se houver um conflito entre a política de nível de assinatura e a política de nível de grupo de recursos, a política de nível de grupo de recursos terá precedência.
>
>

A Central de Segurança analisa o estado de segurança de seus recursos do Azure. Quando a Central de Segurança identifica possíveis vulnerabilidades de segurança, ela cria recomendações com base nos controles definidos na política de segurança. As recomendações guiarão você pelo processo de configuração dos controles de segurança necessários.

As recomendações de política atuais na Central de Segurança se concentram em atualizações do sistema, configuração de sistema operacional, grupos de segurança de rede em sub-redes e máquinas virtuais (VMs), Auditoria do Banco de Dados SQL, TDE do Banco de Dados SQL e firewalls de aplicativo Web. Para ver a abrangência mais atualizada das recomendações da Central de Segurança, veja [Gerenciar recomendações de segurança na Central de Segurança](security-center-recommendations.md).

## <a name="scenario"></a>Cenário
Este cenário mostra como usar a Central de Segurança para ajudar a reduzir as chances de um incidente de segurança significativo monitorando as recomendações da Central de Segurança e executando ações. O cenário usa a empresa fictícia Contoso e funções apresentadas no [guia de planejamento e operações](security-center-planning-and-operations-guide.md#security-roles-and-access-controls) da Central de Segurança. As funções representam indivíduos e equipes que podem usar a Central de Segurança para executar diferentes tarefas relacionadas à segurança. As funções são:

![Funções de cenário][2]

A Contoso migrou recentemente alguns dos seus recursos locais para o Azure. A Contoso deseja implementar e manter as proteções que reduzem sua vulnerabilidade a violações de segurança dos recursos na nuvem.

## <a name="recommended-solution"></a>Solução recomendada
Uma solução é usar a Central de Segurança para evitar e detectar vulnerabilidades de segurança. A Contoso tem acesso à Central de Segurança por meio da assinatura do Azure. A [Camada gratuita](security-center-pricing.md) da Central de Segurança é habilitada automaticamente em todas as assinaturas do Azure e a coleta de dados está habilitada em todas as VMs na assinatura.

David, do departamento de Segurança de TI da Contoso, configura uma **política de segurança** usando a Central de Segurança. A Central de Segurança analisa o estado da segurança dos recursos do Azure da Contoso. Quando a Central de Segurança identifica possíveis vulnerabilidades de segurança, ela cria **recomendações** com base nos controles definidos na política de segurança.

Jefferson, um proprietário da carga de trabalho de nuvem, é responsável pela implementação e manutenção das proteções de acordo com a política de segurança da Contoso. Jefferson pode monitorar as recomendações criadas pela Central de Segurança para aplicar as proteções. As recomendações guiarão Jefferson pelo processo de configuração dos controles de segurança necessários.

Para Jefferson implementar e manter as proteções e eliminar vulnerabilidades de segurança, ele precisa:

- Monitorar as recomendações de segurança fornecidas pela Central de Segurança
- Avaliar as recomendações de segurança e decidir quais ele deve aplicar ou ignorar
- Aplicar as recomendações de segurança

Vejamos as etapas que Jefferson realiza para ver como ele usa as recomendações da Central de Segurança para orientá-lo durante o processo de configuração dos controles para eliminar vulnerabilidades de segurança.

## <a name="how-to-implement-this-solution"></a>Como implementar esta solução
Jefferson entra no [Portal do Azure](https://azure.microsoft.com/features/azure-portal/) e abre o console da Central de Segurança. Como parte de suas atividades diárias de monitoramentos, ele verifica se há recomendações de segurança executando as seguintes etapas:

1. Jefferson seleciona o bloco **Recomendações** para abrir **Recomendações**.
   ![Selecionar o bloco de recomendações][3]
2. Ele examina a lista de recomendações. Ele vê que a Central de Segurança forneceu a lista de recomendações na ordem de prioridade, da mais alta para a mais baixa. Ele decide tratar de uma recomendação de Alta prioridade na lista. Ele seleciona **Instalar o Endpoint Protection** em **Recomendações**.
3. **Instalar o Endpoint Protection** abre exibindo uma lista de VMs sem antimalware habilitado. Jefferson examina a lista de VMs, seleciona todas elas e, em seguida, escolhe **Instalar em 3 VMs**.
   ![Instalar o Endpoint Protection][4]
4. **Selecionar Endpoint Protection** se abre, fornecendo a Jefferson duas soluções antimalware. Jefferson seleciona a solução **Microsoft Antimalware**.
5. Informações adicionais sobre a solução antimalware são exibidas. Jefferson seleciona **Criar**.
   ![Microsoft antimalware][5]
6. Ele insere as definições de configuração necessárias em **Instalar** e seleciona **OK**.

O [Microsoft Antimalware](../security/azure-security-antimalware.md) agora está ativo nas VMs selecionadas.

Jefferson continua a seguir as recomendações de alta e média prioridade, tomando decisões quanto à implementação. Jefferson consulta o artigo [Gerenciar recomendações de segurança](security-center-recommendations.md) para entender as recomendações e o que cada uma delas faz caso ele as aplique.

Ele descobre que o [MSRC (Microsoft Security Response Center)](../security/azure-security-response-center.md) executa monitoramento de segurança seleto na rede e na infraestrutura do Azure, bem como recebe inteligência de ameaça e reclamações de abuso de terceiros. Se Jefferson fornecer detalhes de contato de segurança para a assinatura do Azure da Contoso, a Microsoft contatará a Contoso se o MSRC descobrir que os dados dos clientes da Contoso foram acessados de forma ilegal ou não autorizada. Vamos acompanhar Jefferson para ver como ele aplica a recomendação para **Fornecer detalhes de contato de segurança** (uma recomendação com severidade Média na lista de recomendações acima).

1. Jefferson seleciona **Fornecer detalhes de contato de segurança** em **Recomendações**, o que abre **Fornecer detalhes de contato de segurança**.
2. Ele seleciona a assinatura do Azure à qual as informações de contato se referem. Uma segunda folha **Fornecer detalhes de contato de segurança** será aberta.
   ![Detalhes do contato de segurança][6]
3. Em **Fornecer detalhes de contato de segurança**, Jefferson insere:

  - os endereços de email do contato de segurança são separados por vírgulas (não há um limite para o número de endereços de email que ele pode inserir)
  - um número de telefone do contato de segurança

4. Jefferson também ativa a opção **Enviar-me emails sobre alertas** para receber emails sobre alertas de alta severidade.
5. Ele seleciona **OK** para aplicar as informações de contato de segurança à assinatura da Contoso.

Por fim, Jefferson examina a recomendação de baixa prioridade **Remediar as vulnerabilidades do sistema operacional** e determina que essa recomendação não é aplicável. Ele deseja ignorar a recomendação. Ele seleciona três pontos que aparecem à direita e, em seguida, seleciona **Ignorar**.
   ![Ignorar a recomendação][7]

## <a name="conclusion"></a>Conclusão
Monitorar as recomendações na Central de Segurança pode ajudar a eliminar vulnerabilidades de segurança antes de uma violação ocorrer. Você pode impedir um incidente de segurança implementando e mantendo as proteções com políticas de segurança na Central de Segurança.

## <a name="next-steps"></a>Próximas etapas
Esse cenário mostrou como usar as políticas de segurança e as recomendações da Central de Segurança para ajudar a atenuar uma violação de segurança. Consulte o [cenário de resposta a incidente](security-center-incident-response.md) para saber como ter um plano de resposta a incidente em vigor antes que ocorra um ataque.

Para saber mais sobre a Central de Segurança, confira:

* [Monitoramento da integridade da segurança](security-center-monitoring.md) – saiba como monitorar a integridade dos seus recursos do Azure.
* [Gerenciar e responder aos alertas de segurança](security-center-managing-and-responding-alerts.md) – aprenda a gerenciar e responder aos alertas de segurança.
* [Monitoramento e processamento de eventos de segurança](security-center-events-dashboard.md) – saiba como monitorar e processar eventos de segurança coletados ao longo do tempo.
* [Monitorar as soluções de parceiros](security-center-partner-solutions.md) – saiba como monitorar o status de integridade de suas soluções de parceiros.
* [Perguntas frequentes da Central de Segurança do Azure](security-center-faq.md) : encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) : obtenha as últimas notícias de segurança e informações do Azure.

<!--Image references-->
[1]: ./media/security-center-using-recommendations/security-center-policy-inheritance.png
[2]: ./media/security-center-using-recommendations/scenario-roles.png
[3]: ./media/security-center-using-recommendations/select-recommendations-tile.png
[4]: ./media/security-center-using-recommendations/install-endpoint-protection.png
[5]:./media/security-center-using-recommendations/microsoft-antimalware.png
[6]: ./media/security-center-using-recommendations/provide-security-contact-details.png
[7]: ./media/security-center-using-recommendations/dismiss-recommendation.png
