---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: dc871b29cdafa57d337f9be6cf01e76212f31b67
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171898"
---
## <a name="migrate-iaas-resources-from-the-classic-deployment-model-to-azure-resource-manager"></a>Migrar recursos de IaaS do modelo de implantação clássico para o Azure Resource Manager
Primeiro, é importante compreender a diferença entre as operações de plano de dados e de plano de gerenciamento nos recursos de IaaS (infraestrutura como serviço).

* O *Plano de gerenciamento/controle* descreve as chamadas que vão para o plano de gerenciamento/controle ou para a API para modificar recursos. Por exemplo, operações como a criação de uma VM, reinicialização de uma VM e atualização de uma rede virtual com uma nova sub-rede para gerenciar os recursos em execução. Elas não afetam diretamente a conexão com as VMs.
* *Plano de dados* (aplicativo) descreve o tempo de execução do próprio aplicativo e envolve a interação com instâncias que não passam pela API do Azure. Por exemplo, o acesso ao seu site ou o pull de dados de uma instância do SQL Server ou servidor MongoDB em execução são plano de dados ou interação com o aplicativo. Outros exemplos incluem copiar um blob de uma conta de armazenamento e acessar um endereço IP público para usar o protocolo RDP ou SSH (Secure Shell) na máquina virtual. Essas operações mantêm o aplicativo em execução entre computação, rede e armazenamento.

O plano de dados é o mesmo entre o modelo de implantação Clássico e a pilha do Gerenciador de Recursos. A diferença é que, durante o processo de migração, a Microsoft converte a representação dos recursos do modelo de implantação Clássico para aquela da pilha do Gerenciador de Recursos. Como resultado, você precisa usar novas ferramentas, APIs e SDKs para gerenciar seus recursos na pilha do Gerenciador de Recursos.

![Diagrama que mostra a diferença entre o plano de gerenciamento/controle e o plano de dados](../articles/virtual-machines/media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)


> [!NOTE]
> Em alguns cenários de migração, a plataforma Azure interrompe, desaloca e reinicia as máquinas virtuais. Isso causa um breve tempo de inatividade de plano de dados.
>

## <a name="the-migration-experience"></a>A experiência de migração
Antes de iniciar a migração:

* Certifique-se de que os recursos que você deseja migrar não usam nenhum recurso ou nenhuma configuração sem suporte. Normalmente a plataforma detecta esses problemas e gera um erro.
* Se você tiver VMs que não estão em uma rede virtual, elas serão interrompidas e desalocadas como parte da operação de preparação. Se você não quiser perder o endereço IP público, procure reservar o endereço IP antes de disparar a operação de preparação. Se as VMs estiverem em uma rede virtual, elas não serão interrompidas ou desalocadas.
* Planeje a migração fora do horário comercial para acomodar falhas inesperadas que possam ocorrer durante a migração.
* Baixe a configuração atual das VMs usando o PowerShell, os comandos da CLI (interface de linha de comando) ou as APIs REST para facilitar a validação após a conclusão da etapa de preparação.
* Atualize os scripts de automação e operacionalização para lidar com o modelo de implantação do Gerenciador de Recursos antes de iniciar a migração. Se preferir, você poderá realizar operações GET quando os recursos estiverem no estado preparado.
* Avalie as políticas de RBAC (controle de acesso baseado em função) que são configuradas nos recursos de IaaS no modelo de implantação clássico e planeje a conclusão da migração.

O fluxo de trabalho de migração está descrito abaixo:

![Diagrama que mostra o fluxo de trabalho de migração](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> As operações descritas nas seções a seguir são todas idempotentes. Caso você tenha algum problema que não seja um recurso sem suporte ou um erro de configuração, repita a operação de preparação, anulação ou confirmação. O Azure tenta a ação novamente.
>
>

### <a name="validate"></a>Validar
A operação de validação é a primeira etapa do processo de migração. O objetivo desta etapa é analisar o estado dos recursos que você deseja migrar no modelo de implantação clássico. A operação avalia se os recursos podem ser migrados (êxito ou falha).

Você seleciona a rede virtual ou o serviço de nuvem (se não for em uma rede virtual) que deseja validar para a migração. Se o recurso não puder ser migrado, o Azure indicará os motivos.

#### <a name="checks-not-done-in-the-validate-operation"></a>As verificações não feitas na operação de validação

A operação de validação apenas analisa o estado dos recursos no modelo de implantação clássico. Ela pode verificar todas as falhas e cenários sem suporte devido a diferentes configurações no modelo de implantação clássico. Não é possível verificar todos os problemas que a pilha do Azure Resource Manager pode causar nos recursos durante a migração. Esses problemas são verificados apenas quando os recursos são submetidos à transformação na próxima etapa da migração (a operação de preparação). A tabela abaixo lista todos os problemas que não são verificados na operação de validação:


|Verificações de rede que não ocorrem na operação de validação|
|-|
|Uma rede virtual com gateways ER e VPN.|
|Uma conexão de gateway de rede virtual em estado desconectado.|
|Todos os circuitos ER são pré-migrados para a pilha do Azure Resource Manager.|
|A cota do Azure Resource Manager verifica recursos de rede. Por exemplo: IP público estático, IPs públicos dinâmicos, balanceador de carga, grupos de segurança de rede, tabelas de rotas e adaptadores de rede. |
| Todas as regras de balanceador de carga são válidas na implantação e na rede virtual. |
| IPs privados em conflito entre a VMs paradas e desalocadas na mesma rede virtual. |

### <a name="prepare"></a>Preparar
A operação de preparação é a segunda etapa do processo de migração. O objetivo dessa etapa é simular a transformação dos recursos de IaaS do modelo de implantação clássico para os recursos do Gerenciador de Recursos. Além disso, a operação de preparação apresenta esse lado a lado para que você possa visualizar.

> [!NOTE] 
> Os recursos no modelo de implantação clássico não são modificados durante esta etapa. É uma etapa segura a ser executada se você estiver experimentando fazer uma migração. 

Você seleciona a rede virtual ou o serviço de nuvem (se não for uma rede virtual) que deseja preparar para a migração.

* Se o recurso não for capaz de migração, o Azure interrompe o processo de migração e lista o motivo pelo qual a operação de preparação falhou.
* Se o recurso for capaz de fazer migração, o Azure bloqueará as operações do plano de gerenciamento para os recursos em migração. Por exemplo, você não pode adicionar um disco de dados a uma VM em migração.

Em seguida, o Azure inicia a migração de metadados do modelo de implantação clássico para o Gerenciador de Recursos em relação aos recursos em migração.

Assim que a operação de preparação for concluída, você tem a opção de visualizar os recursos no modelo de implantação clássico e no Gerenciador de Recursos. Para todos os serviços de nuvem no modelo de implantação clássica, a plataforma Azure cria um nome de grupo de recursos com o padrão `cloud-service-name>-Migrated`.

> [!NOTE]
> Não é possível selecionar o nome de um grupo de recursos criado para recursos migrados (ou seja, "-Migrated"). No entanto, após a conclusão da migração, você pode usar o recurso de movimentação do Azure Resource Manager para mover os recursos para qualquer grupo de recursos desejado. Para saber mais, confira [Mover recursos para um novo grupo de recursos ou assinatura](../articles/resource-group-move-resources.md).

As duas telas a seguir mostram o resultado após uma operação de preparação bem-sucedida. A primeira mostra um grupo de recursos que contém o serviço de nuvem original. A segunda mostra o novo grupo de recursos “-Migrated” que contém os recursos equivalentes do Azure Resource Manager.

![Captura de tela que mostra o serviço de nuvem original](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-classic.png)

![Captura de tela que mostra os recursos do Azure Resource Manager na operação de preparação](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-arm.png)

Aqui damos uma olhada nos bastidores dos seus recursos após a conclusão da fase de preparação. Observe que o recurso no plano de dados é o mesmo. Ele é representado no plano de gerenciamento (modelo de implantação clássico) e no plano de controle (Resource Manager).

![Diagrama da fase de preparação](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-prepare.png)

> [!NOTE]
> Máquinas virtuais que não estão em uma rede virtual no modelo de implantação clássico são interrompidas e desalocadas nesta fase de migração.
>

### <a name="check-manual-or-scripted"></a>Verificação (manual ou com scripts)
Nessa etapa de verificação, você pode optar por usar a configuração que baixou anteriormente a fim de validar se a migração parece correta. Se preferir, você pode entrar no portal e verificar pontualmente as propriedades e os recursos para validar se os metadados de migração parecem corretos.

Se estiver migrando uma rede virtual, a maior parte da configuração das máquinas virtuais não é reiniciada. Para aplicativos nessas VMs, você pode validar que o aplicativo está ainda em execução.

Você pode testar seus scripts de monitoramento e operacionais para ver se as VMs estão funcionando conforme o esperado e se os scripts atualizados funcionam corretamente. Somente as operações GET terão suporte quando os recursos estiverem no estado preparado.

Não haverá uma janela de tempo definida antes da qual você precisará confirmar a migração. Você pode levar tanto tempo quanto desejar nesse estado. No entanto, o plano de gerenciamento é bloqueado para esses recursos até você anular ou confirmar.

Caso encontre problemas, sempre será possível anular a migração e voltar para o modelo de implantação clássica. Depois que você voltar, o Azure abrirá as operações do plano de gerenciamento nos recursos, para que você possa retomar as operações normais nessas VMs no modelo de implantação clássico.

### <a name="abort"></a>Anular
Essa é uma etapa opcional se você quiser reverter as alterações para o modelo de implantação clássico e interromper a migração. Essa operação exclui os metadados dos seus recursos no Gerenciador de Recursos (criados na etapa de preparação). 

![Diagrama da etapa de anulação](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-abort.png)


> [!NOTE]
> Essa operação não pode ser executada depois que a operação de confirmação é disparada.     
>

### <a name="commit"></a>Confirmar
Após a conclusão da validação, é possível confirmar a migração. Os recursos não aparecerão mais no modelo de implantação clássico e estão disponíveis apenas no modelo de implantação do Gerenciador de Recursos. Os recursos migrados só podem ser gerenciados no novo portal.

> [!NOTE]
> Esta é uma operação idempotente. Se ela falhar, repita a operação. Se a falha persistir, crie um tíquete de suporte ou uma postagem no fórum com uma marcação “ClassicIaaSMigration” em nosso [fórum sobre VMs](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows).
>
>

![Diagrama da etapa de confirmação](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-commit.png)

## <a name="migration-flowchart"></a>Fluxograma de migração

Aqui está um fluxograma que mostra como proceder com a migração:

![Screenshot that shows the migration steps](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-the-classic-deployment-model-to-resource-manager-resources"></a>Conversão do modelo de implantação clássico para recursos do Gerenciador de Recursos
Você pode encontrar o modelo de implantação clássico e representações do Resource Manager dos recursos na tabela a seguir. Atualmente, não há suporte para outros recursos e funcionalidades.

| Representação do clássico | Representação do Gerenciador de Recursos | Observações |
| --- | --- | --- |
| Nome do serviço de nuvem |Nome DNS |Durante a migração, um novo grupo de recursos é criado para cada serviço de nuvem com o padrão de nomenclatura `<cloudservicename>-migrated`. Esse grupo de recursos contém todos os seus recursos. O nome do serviço de nuvem torna-se um nome DNS associado ao endereço IP público. |
| Máquina virtual |Máquina virtual |As propriedades específicas da VM são migradas sem alterações. Determinadas informações de osProfile, como nome do computador, não foram armazenadas no modelo de implantação clássica e permanecem vazias após a migração. |
| Recursos de disco anexados à VM |Discos implícitos anexados à VM |Os discos não são modelados como recursos de nível superior no modelo de implantação do Gerenciador de Recursos. Eles são migrados como discos implícitos na VM. No momento, há suporte apenas aos discos anexados a uma VM. Agora, VMs do Gerenciador de Recursos podem usar contas de armazenamento no modelo de implantação clássico, o que permite que os discos sejam migrados facilmente sem quaisquer atualizações. |
| Extensões de VM |Extensões de VM |Todas as extensões de recurso, exceto as extensões XML, são migradas do modelo de implantação clássica. |
| Certificados de máquina virtual |Certificados no Cofre da Chave do Azure |Se um serviço de nuvem contiver certificados de serviço, a migração criará um novo cofre de chaves do Azure por serviço de nuvem e moverá os certificados para o cofre de chaves. As VMs são atualizadas para fazer referência aos certificados por meio do cofre de chaves. <br><br> Não exclua o cofre de chaves. Isso pode fazer com que a VM entre em estado de falha. |
| Configuração do WinRM |Configuração do WinRM em osProfile |A configuração do Gerenciamento Remoto do Windows é movida sem alterações, como parte da migração. |
| Propriedade do conjunto de disponibilidade |Recurso do conjunto de disponibilidade | A especificação do conjunto de disponibilidade é uma propriedade da VM no modelo de implantação clássica. Os conjuntos de disponibilidade se tornam um recurso de nível superior como parte da migração. Não há suporte para a seguinte configuração: vários conjuntos de disponibilidade por serviço de nuvem ou um ou mais conjuntos de disponibilidade junto com VMs que não estão em nenhum conjunto de disponibilidade em um serviço de nuvem. |
| Configuração de rede em uma VM |Interface de rede primária |A configuração de rede em uma VM é representada como o recurso de interface de rede primária após a migração. Para as VMs que não estão em uma rede virtual, o endereço IP interno é alterado durante a migração. |
| Várias interfaces de rede em uma VM |Interfaces de rede |Se uma VM tiver várias interfaces de rede associadas, cada adaptador de rede se tornará um recurso de nível superior como parte da migração, juntamente com todas as propriedades. |
| Conjunto de pontos de extremidade com balanceamento de carga |Balanceador de carga |No modelo de implantação clássica, a plataforma atribuía um balanceador de carga implícito a cada serviço de nuvem. Durante a migração, um novo recurso de balanceador de carga é criado e o conjunto de pontos de extremidade com balanceamento de carga se torna as regras do balanceador de carga. |
| Regras NAT de entrada |Regras NAT de entrada |Os pontos de extremidade de entrada definidos na VM são convertidos em regras de conversão de endereços de rede de entrada sob o balanceador de carga durante a migração. |
| Endereço VIP |Endereço IP público com o nome DNS |O endereço IP virtual se torna um endereço IP público e é associado ao balanceador de carga. Um IP virtual só pode ser migrado se houver um ponto de extremidade de entrada atribuído a ele. |
| Rede virtual |Rede virtual |A rede virtual é migrada com todas as propriedades para o modelo de implantação do Gerenciador de Recursos. É criado um novo grupo de recursos com o nome `-migrated`. |
| IPs Reservados |Endereço IP público com método de alocação estático |Os IPs Reservados associados ao balanceador de carga são migrados, juntamente com a migração do serviço de nuvem ou da máquina virtual. No momento, não há suporte para a migração de IP reservado não associado. |
| Endereço IP público por VM |Endereço IP público com método de alocação dinâmico |O endereço IP público associado à VM é convertido como um recurso de endereço IP público, com o método de alocação definido como estático. |
| NSGs |NSGs |Os grupos de segurança de rede associados a uma sub-rede são clonados como parte da migração para o modelo de implantação do Gerenciador de Recursos. O NSG no modelo de implantação clássica não é removido durante a migração. No entanto, as operações do plano de gerenciamento referentes ao NSG serão bloqueadas quando a migração estiver em andamento. |
| Servidores DNS |Servidores DNS |Os servidores DNS associados a uma rede virtual ou à VM são migrados como parte da migração do recurso correspondente, juntamente com todas as propriedades. |
| UDRs |UDRs |As rotas definidas pelo usuário associados a uma sub-rede são clonadas como parte da migração para o modelo de implantação do Gerenciador de Recursos. A UDR no modelo de implantação clássica não é removida durante a migração. As operações do plano de gerenciamento referentes à UDR serão bloqueadas quando a migração estiver em andamento. |
| Propriedade de encaminhamento IP em uma configuração de rede da VM |Propriedade de encaminhamento IP na NIC |A propriedade de encaminhamento IP em uma VM é convertida em uma propriedade na interface de rede durante a migração. |
| Balanceador de carga com vários IPs |Balanceador de carga com vários recursos IP públicos |Cada IP público associado ao balanceador de carga é convertido em um recurso IP público e associado ao balanceador de carga após a migração. |
| Nomes DNS internos na VM |Nomes DNS internos na NIC |Durante a migração, os sufixos DNS internos das VMs são migrados para uma propriedade somente leitura chamada "InternalDomainNameSuffix" no NIC. O sufixo permanece inalterado após a migração, e a resolução da VM deve continuar a funcionar como antes. |
| Gateway de rede virtual |Gateway de rede virtual |As propriedades do gateway de rede virtual são migradas sem alterações. O VIP associado ao gateway também não é alterado. |
| Site de rede local |Gateway de rede local |Propriedades do site de rede local são migradas sem alterações para um novo recurso chamado gateway de rede local. Ele representa prefixos de endereço local e o IP do gateway remoto. |
| Referências de conexões |Conexão |As referências de conectividade entre o gateway e o site de rede local na configuração de rede são representadas por um novo recurso chamado Conexão. Todas as propriedades de referência de conectividade em arquivos de configuração de rede são copiadas sem alterações para o recurso Conexão. A conectividade entre redes virtuais no modelo de implantação clássico é feita com a criação de dois túneis IPsec para os sites de rede local que representam as redes virtuais. Isso vira o tipo de conexão rede virtual a rede virtual no modelo do Gerenciador de Recursos, sem exigir gateways de rede locais. |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Alterações à automação e às ferramentas após a migração
Como parte da migração dos recursos do modelo de implantação clássico para o modelo de implantação do Gerenciador de Recursos, você precisa atualizar sua automação ou ferramentas existentes para garantir que elas continuem funcionando após a migração.
