---
title: Sobre o processo de movimentação no Azure Resource Mover
description: Saiba mais sobre o processo de movimentação de recursos entre regiões com o Azure Resource Mover
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: overview
ms.date: 02/01/2021
ms.author: raynew
ms.openlocfilehash: 6eba77e1af2cd1a038f2ebd61a85ccd03b9a0741
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106074447"
---
# <a name="about-the-move-process"></a>Sobre o processo de movimentação

O [Azure Resource Mover](overview.md) ajuda você a mover os recursos do Azure entre regiões do Azure. Este artigo resume os componentes usados pelo Resource Mover e descreve o processo de movimentação. 


## <a name="components"></a>Componentes

Esses componentes são usados durante a movimentação da região.

**Componente** | **Detalhes**
--- | ---
**Resource Mover** |  O Resource Mover coordena-se com [provedores de recursos do Azure](../azure-resource-manager/management/resource-providers-and-types.md) para orquestrar a movimentação de recursos entre regiões. O Resource Mover analisa as dependências de recursos e mantém e gerencia o estado dos recursos durante o processo de movimentação. 
**Mover coleção** |  Uma coleção de movimentação é um objeto do [Azure Resource Manager](../azure-resource-manager/management/overview.md).<br/><br/> A coleção de movimentação é criada durante o processo de movimentação de região, para cada combinação emparelhada de regiões de origem e de destino em uma assinatura. A coleção contém metadados e informações de configuração sobre os recursos que você deseja mover.<br/><br/>Os recursos adicionados a uma coleção de movimentação precisam estar na mesma assinatura, mas podem estar em grupos de recursos diferentes. 
**Recurso de movimentação** | Quando você adiciona um recurso a uma coleção de movimentação, ele é rastreado pelo Resource Mover como um recurso de movimentação.<br/><br/> O Resource Mover mantém informações para todos os recursos de movimentação na coleção de movimentação e mantém uma relação um-para-um entre o recurso de origem e de destino. 
**Dependências** | O Resource Mover valida os recursos que você adiciona a uma coleção e verifica se os recursos têm dependências que não estejam na coleção de movimentação.<br/><br/> Depois de identificar as dependências de um recurso, você poderá adicionar as dependências à coleção de movimentação e movê-las também ou poderá selecionar recursos existentes alternativos na região de destino. Todas as dependências precisam ser resolvidas antes de você iniciar a movimentação. 



## <a name="move-region-process"></a>Processo da região de movimentação 

![Diagrama mostrando as etapas de movimentação](./media/about-move-process/move-steps.png)

Cada recurso de movimentação passa pelas etapas resumidas.

| **Step** | **Detalhes** | **Estado/problemas** |
| --- | --- | --- |
| **Etapa 1: Selecionar recursos** | Selecione um recurso. O recurso é adicionado à coleção de movimentação. | O estado do recurso é movido para *Preparação pendência*.<br/><br/> Se o recurso tiver dependências, *Validar dependência* indicará que você precisa adicionar recursos dependentes à coleção de movimentação. |
| **Etapa 2: Validar dependências** | Inicie o processo de validação.<br/><br/> Se a validação mostrar que os recursos dependentes estão pendentes, adicione-os à coleção de movimentação. <br/><br/> Adicione todos os recursos dependentes, mesmo se você não quiser movê-los. Posteriormente, você pode especificar que os recursos que está movendo devem usar recursos diferentes na região de destino.<br/><br/> Valide novamente até que não haja dependências pendentes. | Depois que todas as dependências forem adicionadas e a validação for bem-sucedida, o estado do recurso será movido para *Preparação pendente*, sem problemas. |
| **Etapa 3: Preparar** | Inicie o processo de preparação. As etapas de preparação dependem dos recursos que você está movendo:<br/><br/> - **Recursos sem estado**: os recursos sem estado têm apenas informações de configuração. Esses recursos não precisam da replicação contínua de dados para serem movidos. Os exemplos incluem VNets (redes virtuais) do Azure, adaptadores de rede, balanceadores de carga e grupos de segurança de rede. Para esse tipo de recurso, o processo de preparação gera um modelo do Azure Resource Manager.<br/><br/> - **Recursos com estado**: os recursos com estado têm as informações de configuração e os dados que precisam ser movidos. Os exemplos incluem VMs do Azure e bancos de dados SQL do Azure. O processo de preparação é diferente para cada recurso. Ele pode incluir a replicação do recurso de origem para a região de destino. | O início move o estado do recurso para *Preparação em andamento*.<br/><br/> Após a conclusão da preparação, o estado do recurso é movido para *Início da movimentação pendente* sem problemas.<br/><br/> Um processo sem êxito move o estado para *Preparação com falha*. |
| **Etapa 4: Iniciar movimentação** | Inicie o processo de movimentação. O método de movimentação depende do tipo de recurso:<br/><br/> - **Sem estado**: normalmente, para recursos sem estado, o processo de movimentação implanta um modelo importado na região de destino. O modelo é baseado nas configurações do recurso de origem e em todas as edições manuais feitas nas configurações de destino.<br/><br/> - **Com estado**: para recursos com estado, o processo de movimentação pode envolver a criação do recurso ou a habilitação de uma cópia na região de destino.<br/><br/>  Somente para recursos com estado, iniciar uma movimentação pode resultar em tempo de inatividade dos recursos de origem. Por exemplo, VMs e SQL. | O início da movimentação muda o estado para *Iniciar movimentação em andamento*.<br/><br/> Uma movimentação de início com êxito move o estado do recurso para *Confirmação de movimentação pendente* sem problemas. <br/><br/> Um processo de movimentação sem êxito move o estado para *Falha ao iniciar movimentação*. |
| **Etapa 5, Opção 1: descartar movimentação** | Após a movimentação inicial, você poderá decidir se deseja prosseguir com uma movimentação completa. Caso não deseje, você poderá descartar a movimentação, e o Resource Mover excluirá os recursos criados no destino. O processo de replicação para recursos com estado continua após o processo de descarte. Essa opção é útil para teste. | O descarte de recursos move o estado para *Descarte em andamento*.<br/><br/> O descarte bem-sucedido move o estado para *Início da movimentação pendente* sem problemas.<br/><br/> Um descarte com falha move o estado para *Falha ao descartar movimentação*. |
| **Etapa 5, Opção 2: confirmar movimentação** | Após a movimentação inicial, se desejar dar prosseguimento a uma movimentação completa, verifique os recursos na região de destino e, quando estiver pronto, confirme a movimentação.<br/><br/> Somente para recursos com estado, a confirmação pode resultar na inacessibilidade de recursos de origem como VMs ou SQL. | Se você confirmar a movimentação, o estado do recurso será movido para *Confirmar movimentação em andamento**.<br/><br/> Após uma confirmação bem-sucedida, o estado do recurso mostra *Confirmar movimentação concluída* sem problemas.<br/><br/> Uma confirmação com falha move o estado para *Falha ao confirmar movimentação*. |
| **Etapa 6: Excluir origem** | Após confirmar a movimentação e verificar os recursos na região de destino, você poderá excluir o recurso de origem. | Após a confirmação, o estado do recurso passará para *Exclusão da origem pendente*. Depois, você poderá selecionar o recurso de origem e excluí-lo.<br/><br/> – Somente os recursos no estado *Exclusão da origem pendente* podem ser excluídos. <br/><br/>Não há suporte para a exclusão de um grupo de recursos ou do SQL Server no portal do Resource Mover. Esses recursos só podem ser excluídos na página de propriedades do recurso. |


## <a name="move-region-states"></a>Estados da região de movimentação

O processo de movimentação tem vários estados e problemas que podem surgir durante cada estado. Eles são resumidos no fluxograma.

![Fluxograma para possíveis estados e problemas](./media/about-move-process/process.png)


### <a name="dependency-analysis"></a>dependency analysis

Durante o andamento do processo de movimentação, talvez você deva validar as dependências se:
- Um recurso usar recursos dependentes que não estão na coleção de movimentação.
- Um recurso dependente na coleção de movimentação tiver as próprias dependências que não estão na coleção de movimentação.
- Você tiver modificado as configurações de destino do recurso e precisar revalidar as dependências.


### <a name="remove-resources"></a>Remover recursos

Se você não quiser mover um recurso, poderá removê-lo da coleção de movimentação. Em geral, o recurso é excluído da coleção, juntamente com ações ou objetos associados, como replicação ou modelos armazenados. O que exatamente acontece quando você remove um recurso depende do tipo de recurso e do estado do recurso quando você o exclui. [Saiba mais](remove-move-resources.md) 

## <a name="move-impact"></a>Impacto da movimentação

A tabela resume o que é afetado quando você está se movimentando entre regiões.

**Comportamento** | **Entre regiões**
--- | --- | --- 
**Dados** | Os dados do recurso e os metadados são movidos.<br/><br/> Os metadados são armazenados temporariamente para acompanhar o status das dependências de recursos e operações.
**Recurso** | O recurso de origem permanece intacto para garantir que os aplicativos continuem funcionando e, opcionalmente, podem ser removidos após a movimentação.<br/><br/> Um recurso é criado na região de destino.
**Processo de movimentação** | Processo de várias etapas que exige intervenção e monitoramento manuais.
Testes. | O teste da movimentação é importante, pois os aplicativos devem continuar funcionando conforme o esperado na região de destino após a movimentação.
**Tempo de inatividade** |  Espera-se que não haja perda de dados, mas um tempo de inatividade para mover os recursos.



## <a name="next-steps"></a>Próximas etapas

[Mover](tutorial-move-region-virtual-machines.md) VMs do Azure para outra região.
[Mover](tutorial-move-region-sql.md) recursos do Azure SQL para outra região.
