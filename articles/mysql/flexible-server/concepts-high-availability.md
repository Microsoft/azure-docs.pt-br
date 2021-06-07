---
title: Visão geral de alta disponibilidade com redundância de zona com o banco de dados do Azure para MySQL servidor flexível
description: Saiba mais sobre os conceitos de alta disponibilidade com redundância de zona com o banco de dados do Azure para MySQL servidor flexível
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 6629beacb5c3edc6fe1d21509051b915c0894479
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105109685"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>Conceitos de alta disponibilidade no banco de dados do Azure para MySQL servidor flexível (versão prévia)

> [!IMPORTANT] 
> O Banco de Dados do Azure para MySQL – Servidor Flexível está atualmente na versão prévia pública.

O banco de dados do Azure para MySQL servidor flexível (visualização) permite configurar a alta disponibilidade com failover automático usando a opção de alta disponibilidade com **redundância de zona** . Quando implantado em uma configuração com redundância de zona, o servidor flexível provisiona e gerencia automaticamente uma réplica em espera em uma zona de disponibilidade diferente. Usando a replicação de nível de armazenamento, os dados são **replicados** de forma síncrona para o servidor em espera na zona secundária para permitir perda de dados zero após um failover. O failover é totalmente transparente do aplicativo cliente e não requer nenhuma ação do usuário. O servidor em espera não está disponível para nenhuma operação de leitura ou gravação, mas é uma espera passiva para habilitar o failover rápido. Os tempos de failover normalmente variam de 60-120 segundos.

A configuração de alta disponibilidade com redundância de zona habilita o failover automático durante eventos planejados, como operações de computação de escala iniciadas pelo usuário e eventos não planejados, como falhas de hardware e software subjacentes, falhas de rede e até mesmo falhas de zona de disponibilidade.

:::image type="content" source="./media/concepts-high-availability/1-flexible-server-overview-zone-redundant-ha.png" alt-text="exibição de alta disponibilidade com redundância de zona":::

## <a name="zone-redundancy-architecture"></a>Arquitetura de redundância de zona

O servidor primário é implantado na região e em uma zona de disponibilidade específica. Quando a alta disponibilidade é escolhida, um servidor de réplica em espera com a mesma configuração do servidor primário é implantado automaticamente, incluindo a camada de computação, o tamanho da computação, o tamanho do armazenamento e a configuração de rede. Os dados de log são replicados de forma síncrona para a réplica em espera para garantir zero perda de dados em qualquer situação de falha. Os backups automáticos, instantâneos e backups de log, são executados no servidor de banco de dados primário. 

A integridade da HA é monitorada e relatada continuamente na página Visão geral.

Os vários status de replicação são listados abaixo:

| **Status** | **Descrição** |
| :----- | :------ |
| <b>Não habilitado | A HA com redundância de zona não está habilitada |
| <b>CreatingStandby | No processo de criação de um novo modo de espera |
| <b>ReplicatingData | Depois que o modo de espera é criado, ele é atualizado com o servidor primário. |
| <b>FailingOver | O servidor de banco de dados está em processo de failover do primário para o em espera. |
| <b>Íntegros | HA com redundância de zona está em estado estável e íntegro. |
| <b>RemovingStandby | Com base na ação do usuário, a réplica em espera está no processo de exclusão.| 

## <a name="advantages"></a>Vantagens

Aqui estão algumas vantagens para usar o recurso de HA de redundância de zona: 

- A réplica em espera é implantada em uma configuração exata da VM como a do primário, como vCores, armazenamento, configurações de rede (VNET, firewall) etc.
- Capacidade de remover a réplica em espera desabilitando a alta disponibilidade.
- Os backups automáticos são baseados em instantâneo, executados do servidor de banco de dados primário e armazenados em um armazenamento com redundância de zona.
- No caso de failover, o banco de dados do Azure para MySQL servidor flexível faz failover automaticamente para a réplica em espera se a alta disponibilidade estiver habilitada. A configuração de alta disponibilidade monitora o servidor primário e o coloca online novamente.
- Os clientes sempre se conectam ao servidor de banco de dados primário.
- Se houver uma falha de banco de dados ou de nó, a reinicialização será tentada primeiro no mesmo nó. Se isso falhar, o failover automático será disparado.
- Capacidade de reiniciar o servidor para selecionar qualquer alteração de parâmetro de servidor estático.

## <a name="steady-state-operations"></a>Operações de estado estacionário

Os aplicativos são conectados ao servidor primário usando o nome do servidor de banco de dados. As informações de réplica em espera não são expostas para acesso direto. As confirmações e gravações são confirmadas para o aplicativo somente depois que os arquivos de log são persistidos no disco do servidor primário e na réplica em espera de forma síncrona. Devido a esse requisito adicional de ida e volta, os aplicativos podem esperar uma latência elevada para gravações e confirmações. Você pode monitorar a integridade da alta disponibilidade no Portal.

## <a name="failover-process"></a>Processo de failover 
Para a continuidade dos negócios, você precisa ter um processo de failover para eventos planejados e não planejados. 

### <a name="planned-events"></a>Eventos planejados

Os eventos de tempo de inatividade planejados incluem atividades agendadas pelo Azure, como atualizações de software periódicas, atualizações de versão secundárias ou que são iniciadas por clientes, como escala de operações de computação e escala. Todas essas alterações são aplicadas primeiro à réplica em espera. Durante esse tempo, os aplicativos continuam acessando o servidor primário. Depois que a réplica em espera for atualizada, as conexões do servidor primário serão descarregadas, um failover será disparado, o que ativa a réplica em espera para ser a primária com o mesmo nome do servidor de banco de dados atualizando o registro DNS. As conexões de cliente são desconectadas e precisam se reconectar e podem retomar suas operações. Um novo servidor em espera é estabelecido na mesma zona que o primário antigo. Espera-se que o tempo de failover geral seja de 60-120 s. 

>[!NOTE]
> No caso da operação de dimensionamento de computação, dimensionamos o servidor de réplica secundária seguido pelo servidor primário. Não há nenhum failover envolvido.

### <a name="failover-process---unplanned-events"></a>Processo de failover-eventos não planejados
Inatividade de serviço não planejada incluem bugs de software que ou falhas de infraestrutura, como computação, rede, falhas de armazenamento ou interrupções de energia, afetam a disponibilidade do banco de dados. No caso da indisponibilidade do banco de dados, a replicação para a réplica em espera é severa e a réplica em espera é ativada para ser o banco de dados primário. O DNS é atualizado e os clientes se reconectam ao servidor de banco de dados e retomam suas operações. O tempo de failover geral deve levar 60-120 s. No entanto, dependendo da atividade no servidor de banco de dados primário no momento do failover, como transações grandes e tempo de recuperação, o failover pode levar mais tempo.

## <a name="schedule-maintenance-window"></a>Agendar janela de manutenção 

Os servidores flexíveis oferecem capacidade de agendamento de manutenção, onde você pode escolher uma janela de 30 minutos em um dia de sua preferência durante a qual a manutenção do Azure funciona, como a aplicação de patch ou atualizações de versão secundárias acontecerão. Para seus servidores flexíveis configurados com alta disponibilidade, essas atividades de manutenção são executadas na réplica em espera primeiro. 

## <a name="point-in-time-restore"></a>Restauração em um momento determinado 
À medida que o servidor flexível é configurado em alta disponibilidade replica dados de forma síncrona, o servidor em espera é atualizado com o primário. Qualquer erro de usuário no primário, como um descarte acidental de uma tabela ou atualizações incorretas, é reproduzido de forma precisa para o em espera. Portanto, você não pode usar o modo de espera para se recuperar dessas falhas lógicas. Para se recuperar desses erros lógicos, você precisa executar a restauração pontual até o momento certo antes que o erro tenha ocorrido. Usando o recurso de restauração pontual do servidor flexível, quando você restaura o banco de dados configurado com alta disponibilidade, um novo servidor de banco de dados é restaurado como um novo servidor flexível com um nome fornecido pelo usuário. Em seguida, você pode exportar o objeto do servidor de banco de dados e importá-lo para seu servidor de banco de dados de produção. Da mesma forma, se você quiser clonar seu servidor de banco de dados para fins de teste e desenvolvimento, ou se desejar restaurar para quaisquer outras finalidades, poderá escolher o ponto de restauração mais recente ou um ponto de restauração personalizado. A operação de restauração cria um servidor de zona única flexível.

## <a name="mitigate-downtime"></a>Reduzir o tempo de inatividade 
Quando você não estiver usando o recurso de HA de redundância de zona, ainda precisará ser capaz de reduzir o tempo de inatividade para seu aplicativo. O planejamento de inatividade de serviço, como patches agendados, atualizações secundárias de versão ou as operações iniciadas pelo usuário, podem ser executadas como parte das janelas de manutenção agendadas. O tempo de inatividade de serviço planejado, como patches agendados, atualizações de versão secundárias ou operações iniciadas pelo usuário, como a computação de escala, incorre em tempo Para atenuar o impacto do aplicativo para as tarefas de manutenção iniciadas pelo Azure, você pode optar por agendá-las durante o dia da semana e o tempo, o que afeta o aplicativo. 

Durante eventos de tempo de inatividade não planejados, como falha do banco de dados ou falha do servidor, o servidor afetado é reiniciado dentro da mesma zona. Embora seja raro, se toda a zona for afetada, você poderá restaurar o banco de dados em outra zona dentro da região. 

## <a name="things-to-know-with-zone-redundancy"></a>Coisas a saber com a redundância de zona 

Aqui estão algumas considerações para ter em mente quando você usa alta disponibilidade de redundância de zona: 

- A alta disponibilidade **só** pode ser definida durante o tempo de criação do servidor flexível.
- Não há suporte para alta disponibilidade na camada de computação expansível.
- Devido à replicação síncrona para outra zona de disponibilidade, o servidor de banco de dados primário pode experimentar a latência de confirmação e gravação elevada.
- A réplica em espera não pode ser usada para consultas somente leitura.
- Dependendo da atividade no servidor primário no momento do failover, pode levar até 60-120 segundos ou mais para que o failover seja concluído.
- Reiniciar o servidor de banco de dados primário para escolher as alterações de parâmetros estáticos também reinicia a réplica em espera.
- Não há suporte para a configuração de réplicas de leitura para servidores de alta disponibilidade com redundância de zona.
- Configurar tarefas de gerenciamento iniciadas pelo cliente não pode ser automatizado durante a janela de manutenção gerenciada.
- Eventos planejados, como a computação em escala e atualizações secundárias de versão, ocorrem tanto no primário quanto no modo de espera ao mesmo tempo. 


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [continuidade dos negócios](./concepts-business-continuity.md)
- Saiba mais sobre [alta disponibilidade redundante de zona](./concepts-high-availability.md)
- Saiba mais sobre [backup e recuperação](./concepts-backup-restore.md)
