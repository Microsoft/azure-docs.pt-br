---
title: Guia de gerenciamento de custos para Azure Lab Services
description: Entenda as diferentes maneiras de exibir os custos dos serviços de laboratório.
author: rbest
ms.author: rbest
ms.date: 08/16/2020
ms.topic: article
ms.openlocfilehash: 29f6be5319c5a142ad3ea0d73deb2f95d8cb0d7a
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659719"
---
# <a name="cost-management-for-azure-lab-services"></a>Gerenciamento de custos para Azure Lab Services

Por Azure Lab Services, o gerenciamento de custos pode ser dividido em duas áreas distintas: estimativa de custo e análise de custo. A estimativa de custo ocorre quando você está configurando o laboratório para garantir que a estrutura inicial do laboratório se ajuste no orçamento esperado. A análise de custo geralmente ocorre no final do mês para determinar as ações necessárias para o próximo mês.

## <a name="estimate-the-lab-costs"></a>Estimar os custos do laboratório

Cada painel de laboratório tem uma seção **custos & cobrança** que apresenta uma estimativa aproximada do custo do laboratório para o mês. A estimativa de custo resume o uso de horas com o número máximo de usuários pelo custo estimado por hora. Para obter a estimativa mais precisa, configure o laboratório, incluindo a [agenda](how-to-create-schedules.md). O painel refletirá o custo estimado. 

Essa estimativa pode não mostrar todos os custos possíveis. Alguns recursos não estão incluídos:

- O custo de preparação do modelo. Ele pode variar significativamente na quantidade de tempo necessária para criar o modelo. O custo para executar o modelo é o mesmo que o custo geral do laboratório por hora. 
- Qualquer custo da [Galeria de imagens compartilhada](how-to-use-shared-image-gallery.md) , porque uma galeria pode ser compartilhada entre vários laboratórios. 
- Horas incorridas quando o criador do laboratório inicia uma VM (máquina virtual).

> [!div class="mx-imgBorder"]
> ![Captura de tela que mostra a estimativa de custo do painel.](./media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyze-the-previous-months-usage"></a>Analisar o uso do mês anterior

A análise de custo é para revisar o uso do mês anterior para ajudá-lo a determinar qualquer ajuste para o laboratório. Você pode encontrar a divisão dos custos passados na [análise de custo da assinatura](../cost-management-billing/costs/quick-acm-cost-analysis.md). No portal do Azure, você pode inserir **assinaturas** na caixa de pesquisa e, em seguida, selecionar a opção **assinaturas** . 

> [!div class="mx-imgBorder"]
> ![Captura de tela que mostra a caixa de pesquisa e a opção de assinaturas.](./media/cost-management-guide/subscription-search.png)

Selecione a assinatura específica que você deseja examinar.

> [!div class="mx-imgBorder"]
> ![Captura de tela que mostra a seleção de assinatura.](./media/cost-management-guide/subscription-select.png)

Selecione **análise de custo** no painel esquerdo em **Gerenciamento de custos**.

> [!div class="mx-imgBorder"]
> ![Captura de tela que mostra uma análise de custo de assinatura em um grafo.](./media/cost-management-guide/subscription-cost-analysis.png)

Esse painel permite uma análise de custo detalhada, incluindo a capacidade de exportar para diferentes tipos de arquivo em uma agenda. Para obter mais informações, consulte [visão geral de gerenciamento de custos + cobrança](../cost-management-billing/cost-management-billing-overview.md).

Você pode filtrar por tipo de recurso. `microsoft.labservices/labaccounts`O uso do mostrará apenas o custo associado aos serviços de laboratório.

## <a name="understand-the-usage"></a>Entender o uso

A captura de tela a seguir é um exemplo de uma análise de custo.

> [!div class="mx-imgBorder"]
> ![Captura de tela que mostra um exemplo de análise de custo para uma assinatura.](./media/cost-management-guide/cost-analysis.png)

Por padrão, há seis colunas: **recurso**, **tipo de recurso**, **local**, **nome do grupo de recursos**, **marcas** e **custo**. A coluna **recurso** contém as informações sobre a conta do laboratório, o nome do laboratório e a VM. As linhas que mostram a conta do laboratório, o nome do laboratório e o padrão (segunda e terceira linhas) são o custo do laboratório. As VMs usadas têm um custo que você pode ver para as linhas que mostram a conta do laboratório, o nome do laboratório, o padrão e o nome da VM. 

Neste exemplo, adicionar a primeira e segunda linhas (ambas iniciam com **aaalab/dockerlab**) fornecerá o custo total para o laboratório "dockerlab" na conta do laboratório "aaalab".

Para obter o custo geral da Galeria de imagens, altere o tipo de recurso para `Microsoft.Compute/Galleries` . Uma galeria de imagens compartilhada pode não aparecer nos custos, dependendo de onde a galeria está armazenada.

> [!NOTE]
> Uma galeria de imagens compartilhada está conectada à conta do laboratório. Isso significa que vários laboratórios podem usar a mesma imagem.

## <a name="separate-the-costs"></a>Separar os custos

Algumas universidades usaram a conta de laboratório e o grupo de recursos como maneiras de separar as classes. Cada classe tem sua própria conta de laboratório e grupo de recursos. 

No painel análise de custo, adicione um filtro com base no nome do grupo de recursos com o nome do grupo de recursos apropriado para a classe. Em seguida, somente os custos dessa classe ficarão visíveis. Isso permite uma delineação mais clara entre as classes quando você está visualizando os custos. Você pode usar o recurso de [exportação agendada](../cost-management-billing/costs/tutorial-export-acm-data.md) da análise de custo para baixar os custos de cada classe em arquivos separados.

## <a name="manage-costs"></a>Gerenciar os custos

Dependendo do tipo de classe, há maneiras de gerenciar os custos para reduzir as instâncias de VMs em execução sem um aluno que as utiliza.

### <a name="automatic-shutdown-settings-for-cost-control"></a>Configurações de desligamento automático para controle de custo

Os recursos de desligamento automático permitem evitar horas de uso de VM desperdiçadas nos laboratórios. As configurações a seguir capturam a maioria dos casos em que os usuários deixam acidentalmente suas máquinas virtuais em execução:

> [!div class="mx-imgBorder"]
> ![Captura de tela que mostra as três configurações de desligamento automático.](./media/cost-management-guide/auto-shutdown-disconnect.png)

Você pode definir essas configurações no nível da conta do laboratório e no nível do laboratório. Se você habilitá-los no nível da conta do laboratório, eles serão aplicados a todos os laboratórios dentro da conta do laboratório. Para todas as novas contas de laboratório, essas configurações são ativadas por padrão. 

#### <a name="automatically-disconnect-users-from-virtual-machines-that-the-os-deems-idle"></a>Desconectar automaticamente os usuários de máquinas virtuais que o sistema operacional considera ocioso

> [!NOTE]
> Essa configuração está disponível somente para máquinas virtuais do Windows.

Quando a configuração **Desconectar usuários quando as máquinas virtuais estiverem ociosas** estiver ativada, o usuário será desconectado de qualquer computador no laboratório quando o sistema operacional Windows considerar a sessão como ociosa (incluindo as máquinas virtuais de modelo). A [definição de ociosidade do sistema operacional Windows](/windows/win32/taskschd/task-idle-conditions#detecting-the-idle-state) usa dois critérios: 

* Ausência do usuário: nenhuma entrada de teclado ou mouse.
* Falta de consumo de recursos: todos os processadores e todos os discos estavam ociosos por um determinado percentual de tempo.

Os usuários verão uma mensagem como esta na VM antes que eles sejam desconectados: 

> [!div class="mx-imgBorder"]
> ![Captura de tela que mostra uma mensagem de aviso informando que uma sessão esteve ociosa ao longo de seu limite de tempo e será desconectada.](./media/cost-management-guide/idle-timer-expired.png)
 
A máquina virtual ainda está em execução quando o usuário está desconectado. Se o usuário se reconectar à máquina virtual entrando, o Windows ou os arquivos que estavam abertos ou que não foram salvos antes da desconexão ainda estarão lá. Nesse estado, como a máquina virtual está em execução, ela ainda conta como ativa e acumula o custo. 
 
Para desligar automaticamente as máquinas virtuais ociosas do Windows que estão desconectadas, use a combinação de **usuários desconectados quando as máquinas virtuais estiverem ociosas** e **desligar as máquinas virtuais quando os usuários desconectarem** as configurações.

Por exemplo, se você definir as configurações da seguinte maneira:
 
* **Desconectar usuários quando as máquinas virtuais estiverem ociosas**: 15 minutos depois que o estado ocioso for detectado.
* **Desligar as máquinas virtuais quando os usuários desconectarem**: 5 minutos após a desconexão do usuário.
 
As máquinas virtuais do Windows serão desligadas automaticamente 20 minutos depois que o usuário parar de usá-las. 
 
> [!div class="mx-imgBorder"]
> ![Diagrama que ilustra a combinação de configurações, resultando no desligamento automático da VM.](./media/cost-management-guide/vm-idle-diagram.png)

#### <a name="automatically-shut-down-virtual-machines-when-users-disconnect"></a>Desligar automaticamente as máquinas virtuais quando os usuários se desconectarem
 
A configuração **desligar máquinas virtuais quando os usuários se desconectam** dá suporte a máquinas virtuais Windows e Linux. Quando essa configuração estiver ativada, o desligamento automático ocorrerá quando:
 
* Para o Windows, uma conexão Área de Trabalho Remota (RDP) é desconectada.
* Para o Linux, uma conexão SSH é desconectada.
 
> [!NOTE]
> Há suporte apenas para [distribuições e versões específicas do Linux](../virtual-machines/extensions/diagnostics-linux.md#supported-linux-distributions) .
 
Você pode especificar por quanto tempo as máquinas virtuais devem aguardar até que o usuário se reconecte antes de desligar automaticamente. 

#### <a name="automatically-shut-down-virtual-machines-that-are-started-but-users-dont-connect"></a>Desligar automaticamente as máquinas virtuais que são iniciadas, mas os usuários não se conectam
 
Em um laboratório, um usuário pode iniciar uma máquina virtual, mas nunca se conectar a ela. Por exemplo:
 
* Uma agenda no laboratório inicia todas as máquinas virtuais para uma sessão de classe, mas alguns alunos não aparecem e não se conectam a seus computadores. 
* Um usuário inicia uma máquina virtual, mas se esquece de conectar. 
 
A configuração **desligar máquinas virtuais quando os usuários não se Conectas** irá capturar esses casos e desligar automaticamente as máquinas virtuais. 
 
Para obter informações sobre como configurar e habilitar o desligamento automático de VMs na desconexão, consulte estes artigos:

* [Configurar o desligamento automático de VMs para uma conta de laboratório](how-to-configure-lab-accounts.md)
* [Configurar o desligamento automático de VMs para um laboratório](how-to-enable-shutdown-disconnect.md)

### <a name="scheduled-time-vs-quota-time"></a>Hora agendada vs. hora da cota

Entender a [hora agendada](classroom-labs-concepts.md#schedules) e o [tempo de cota](classroom-labs-concepts.md#quota) ajudará você a configurar um laboratório para se adequar melhor às necessidades do professor e dos alunos. 

A hora agendada é uma hora definida em que todas as VMs de estudante foram iniciadas e estão disponíveis para conexão. A hora agendada é normalmente usada quando todos os alunos têm suas próprias VMs e estão seguindo as instruções do professor em um determinado momento durante o dia (como as horas de aula). A desvantagem é que todas as VMs dos alunos são iniciadas e estão acumulando custos, mesmo se um aluno não fizer logon em uma VM. 

O tempo de cota é o tempo alocado para cada aluno para uso a seu critério e geralmente é usado para estudar de forma independente. As VMs não são iniciadas até que o aluno inicie a VM. 

Um laboratório pode usar o tempo de cota ou o horário agendado, ou uma combinação de ambos. Se uma classe não precisar de horário agendado, use apenas o tempo de cota para o uso mais eficaz das VMs.

### <a name="scheduled-event-stop-only"></a>Evento agendado: somente parar

No agendamento, você pode adicionar um tipo de evento somente de parada que irá parar todos os computadores em um momento específico. Alguns proprietários de laboratório definiram um evento somente de parada para todos os dias à meia-noite para reduzir o custo e o uso de cota quando um aluno esquecer de desligar a VM que está usando. A desvantagem desse tipo de evento é que todas as VMs serão desligadas, mesmo se um aluno estiver usando uma VM.

### <a name="other-costs-related-to-labs"></a>Outros custos relacionados a laboratórios 

Alguns custos não são lançados no Lab Services, mas podem ser vinculados a um serviço de laboratório. Você pode conectar uma galeria de imagens compartilhada a um laboratório, mas ela não será mostrada nos custos dos serviços de laboratório e terá custos. Para ajudar a manter os custos gerais reduzidos, você deve remover qualquer imagem não utilizada da galeria, pois as imagens têm um custo de armazenamento inerente. 

Os laboratórios podem ter conexões com outros recursos do Azure por meio de uma rede virtual. Quando um laboratório é removido, você deve remover a rede virtual e os outros recursos.

## <a name="conclusion"></a>Conclusão

Espero que as informações neste artigo tenham lhe dado uma melhor compreensão das ferramentas que podem ajudá-lo a reduzir os custos de uso.