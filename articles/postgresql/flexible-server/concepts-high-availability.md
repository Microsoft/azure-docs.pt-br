---
title: Visão geral de alta disponibilidade com redundância de zona com o banco de dados do Azure para PostgreSQL – servidor flexível (versão prévia)
description: Saiba mais sobre os conceitos de alta disponibilidade com redundância de zona com o banco de dados do Azure para PostgreSQL-servidor flexível
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: c0d9b6042ae695caa73d926653f237b756bf4971
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366716"
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql---flexible-server"></a>Conceitos de alta disponibilidade no banco de dados do Azure para PostgreSQL – servidor flexível

> [!IMPORTANT]
> O Servidor Flexível do Banco de Dados do Azure para PostgreSQL está em versão prévia

Banco de dados do Azure para PostgreSQL-o servidor flexível oferece configuração de alta disponibilidade com o recurso de failover automático usando a implantação de servidor com **redundância de zona** . Quando implantado em uma configuração com redundância de zona, o servidor flexível provisiona e gerencia automaticamente uma réplica em espera em uma zona de disponibilidade diferente. Usando a replicação de streaming do PostgreSQL, os dados são replicados para o servidor de réplica em espera no modo **síncrono** . 

A configuração com redundância de zona habilita o recurso de failover automático sem perda de dados durante eventos planejados, como a operação de computação de escala iniciada pelo usuário, e também durante eventos não planejados, como falhas de hardware e software subjacentes, falhas de rede e falhas de zona de disponibilidade. 

:::image type="content" source="./media/business-continuity/concepts-zone-redundant-high-availability-architecture.png" alt-text="alta disponibilidade com redundância de zona"::: 

## <a name="zone-redundant-high-availability-architecture"></a>Arquitetura de alta disponibilidade com redundância de zona

Você pode escolher a região e a zona de disponibilidade para implantar o servidor de banco de dados primário. Um servidor de réplica em espera é provisionado em uma zona de disponibilidade diferente com a mesma configuração do servidor primário, incluindo a camada de computação, o tamanho da computação, o tamanho do armazenamento e a configuração da rede. Os logs de transações são replicados no modo síncrono para a réplica em espera usando replicação de streaming PostgreSQL. Os backups automáticos são executados periodicamente a partir do servidor de banco de dados primário, enquanto os logs de transações são arquivados continuamente no armazenamento de backup da réplica em espera. 

A integridade da configuração de alta disponibilidade é monitorada e relatada continuamente no Portal. Os status de alta disponibilidade com redundância de zona são listados abaixo:

| **Status** | **Descrição** |
| ------- | ------ |
| <b> Inicializando | No processo de criação de um novo servidor em espera |
| <b> Replicando dados | Depois que o modo de espera é criado, ele é atualizado com o primário. |
| <b> Íntegro | A replicação está em estado estável e íntegra. |
| <b> Fazendo failover | O servidor de banco de dados está em processo de failover para o modo de espera. |
| <b> Removendo em espera | No processo de exclusão do servidor em espera. | 
| <b> Não habilitado | A alta disponibilidade com redundância de zona não está habilitada.  |

## <a name="steady-state-operations"></a>Operações de estado estacionário

Os aplicativos cliente PostgreSQL estão conectados ao servidor primário usando o nome do servidor de BD. As leituras de aplicativo são servidas diretamente do servidor primário, enquanto as confirmações e as gravações são confirmadas para o aplicativo somente depois que os dados são persistidos no servidor primário e na réplica em espera. Devido a esse requisito adicional de ida e volta, os aplicativos podem esperar uma latência elevada para gravações e confirmações. Você pode monitorar a integridade da alta disponibilidade no Portal.

:::image type="content" source="./media/business-continuity/concepts-high-availability-steady-state.png" alt-text="alta disponibilidade redundante de zona-estado estável"::: 

1. Os clientes se conectam ao servidor flexível e executam operações de gravação.
2. As alterações são replicadas para o site em espera.
3. O primário recebe a confirmação.
4. Gravações/confirmações são confirmadas.

## <a name="failover-process---planned-downtimes"></a>Processo de failover-tempos de inatividade planejados

Os eventos de tempo de inatividade planejado incluem atualizações de software periódicas agendadas do Azure e atualizações de versão secundária Quando configurado em alta disponibilidade, essas operações são aplicadas primeiro à réplica em espera, enquanto os aplicativos continuam a acessar o servidor primário. Depois que a réplica em espera for atualizada, as conexões do servidor primário serão descarregadas e um failover será disparado, o que ativa a réplica em espera para ser a primária com o mesmo nome do servidor de banco de dados. Os aplicativos cliente precisarão se reconectar com o mesmo nome do servidor de banco de dados para o novo servidor primário e podem retomar suas operações. Um novo servidor em espera será estabelecido na mesma zona que o primário antigo. 

Para outras operações iniciadas pelo usuário, como escala de computação ou armazenamento de escala, as alterações são aplicadas primeiro no modo de espera, seguido pelo primário. Atualmente, as conexões não têm failover no modo de espera e, portanto, incorrem em tempo de inatividade enquanto a operação é executada no servidor primário.

### <a name="reducing-planned-downtime-with-managed-maintenance-window"></a>Redução do tempo de inatividade planejado com a janela de manutenção gerenciada

 Você pode agendar atividades de manutenção iniciadas do Azure escolhendo uma janela de 30 minutos em um dia de sua preferência onde as atividades nos bancos de dados devem ser baixas. As tarefas de manutenção do Azure, como aplicação de patch ou atualizações secundárias de versão, ocorrerão durante essa janela.  Para servidores flexíveis configurados com alta disponibilidade, essas atividades de manutenção são executadas na réplica em espera primeiro e, em seguida, são ativadas. Os aplicativos reconectam-se ao novo servidor primário e retomam suas operações enquanto um novo modo de espera é provisionado.

## <a name="failover-process---unplanned-downtimes"></a>Processo de failover-tempos de inatividade não planejados

Interrupções não planejadas incluem bugs de software ou falhas de componentes de infraestrutura que afetam a disponibilidade do banco de dados. No caso da indisponibilidade do servidor ser detectada pelo sistema de monitoramento, a replicação para a réplica em espera é severa e a réplica em espera é ativada para ser o servidor de banco de dados primário. Os clientes podem se reconectar ao servidor de banco de dados usando a mesma cadeia de conexão e retomar suas operações. Espera-se que o tempo de failover geral tenha 60 120s. No entanto, dependendo da atividade no servidor de banco de dados primário no momento do failover, como transações grandes e tempo de recuperação, o failover pode levar mais tempo.

:::image type="content" source="./media/business-continuity/concepts-high-availability-failover-state.png" alt-text="alta disponibilidade redundante de zona-failover"::: 

1. O servidor de banco de dados primário está inoperante e os clientes perdem a conectividade do banco de dados. 
2. O servidor em espera é ativado para se tornar o novo servidor primário. O cliente se conecta ao novo servidor primário usando a mesma cadeia de conexão. Ter o aplicativo cliente na mesma zona que o servidor de banco de dados primário reduz a latência e melhora o desempenho.
3. O servidor em espera é estabelecido na mesma zona que o servidor primário antigo e a replicação de streaming é iniciada. 
4. Depois que a replicação de estado estacionário é estabelecida, as confirmações e gravações do aplicativo cliente são confirmadas depois que os dados são persistidos em ambos os sites.

## <a name="point-in-time-restore"></a>Restauração em um momento determinado 

Os servidores flexíveis configurados com alta disponibilidade, replicam dados em tempo real para o servidor em espera para mantê-los atualizados. Os erros do usuário no servidor primário, como um descarte acidental de uma tabela ou atualizações de dados incorretas, são reproduzidos de forma precisa na réplica em espera. Portanto, você não pode usar o modo de espera para recuperar esses erros lógicos. Para se recuperar desses erros, você precisa executar a restauração pontual de backups.  Usando o recurso de restauração pontual do servidor flexível, você pode restaurar para o momento anterior ao erro. Para bancos de dados configurados com alta disponibilidade, um novo servidor de banco de dados será restaurado como um servidor de zona única flexível com um nome fornecido pelo usuário. Em seguida, você pode exportar o objeto do servidor de banco de dados e importá-lo para seu servidor de banco de dados de produção. Da mesma forma, se você quiser clonar seu servidor de banco de dados para fins de teste e desenvolvimento, ou se quiser restaurar para quaisquer outras finalidades, poderá executar restaurações pontuais.

## <a name="zone-redundant-high-availability---features"></a>Alta disponibilidade com redundância de zona-recursos

-   A réplica em espera será implantada em uma configuração de VM exata igual ao servidor primário, incluindo vCores, armazenamento, configurações de rede (VNET, firewall) etc.

-   Capacidade de adicionar alta disponibilidade para um servidor de banco de dados existente.

-   Capacidade de remover a réplica em espera desabilitando a alta disponibilidade.

-   Capacidade de escolher sua zona de disponibilidade para o servidor de banco de dados primário.

-   Capacidade de parar, iniciar e reiniciar os servidores de banco de dados primário e em espera.

-   Os backups automáticos são executados do servidor de banco de dados primário e armazenados em um armazenamento com redundância de zona.

-   Os clientes sempre se conectam ao servidor de banco de dados primário.

-   Capacidade de reiniciar o servidor para selecionar qualquer alteração de parâmetro de servidor estático.
  
-   Atividades de manutenção periódicas, como atualizações de versão secundárias, acontecem em espera primeiro e o failover do serviço para reduzir o tempo de inatividade.  

## <a name="zone-redundant-high-availability---limitations"></a>Alta disponibilidade redundante de zona-limitações

-   Não há suporte para alta disponibilidade com a camada de computação expansível.
-   Há suporte para alta disponibilidade apenas em regiões em que várias zonas estão disponíveis.
-   Devido à replicação síncrona para outra zona de disponibilidade, os aplicativos podem experimentar a latência elevada de gravação e confirmação.

-   A réplica em espera não pode ser usada para consultas de leitura.

-   Dependendo da carga de trabalho e da atividade no servidor primário, o processo de failover pode levar mais de 120 segundos.

-   Reiniciar o servidor de banco de dados primário também reinicia a réplica em espera. 

-   Não há suporte para a configuração de réplicas de leitura adicionais.

-   Configurar tarefas de gerenciamento iniciadas pelo cliente não pode ser agendado durante a janela de manutenção gerenciada.

-   Eventos planejados, como dimensionamento de computação e de armazenamento, ocorrem em espera primeiro e, em seguida, no servidor primário. O servidor não passou por failover para essas operações planejadas. 

-  Se a decodificação lógica ou a replicação lógica estiver configurada com um servidor flexível configurado com alta disponibilidade, no caso de um failover para o servidor em espera, os slots de replicação lógica não serão copiados para o servidor em espera.  

## <a name="next-steps"></a>Próximas etapas

-   Saiba mais sobre a [continuidade dos negócios](./concepts-business-continuity.md)
-   Saiba como [gerenciar a alta disponibilidade](./how-to-manage-high-availability-portal.md)
-   Saiba mais sobre [backup e recuperação](./concepts-backup-restore.md)