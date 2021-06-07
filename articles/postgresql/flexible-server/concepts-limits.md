---
title: Limites-banco de dados do Azure para PostgreSQL-servidor flexível
description: Este artigo descreve os limites no banco de dados do Azure para PostgreSQL – servidor flexível, como o número de opções de mecanismo de armazenamento e conexão.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 351c959a4d3b6cc53064b9d1b65c1282647f308e
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605392"
---
# <a name="limits-in-azure-database-for-postgresql---flexible-server"></a>Limites no banco de dados do Azure para PostgreSQL – servidor flexível

> [!IMPORTANT]
> O Servidor Flexível do Banco de Dados do Azure para PostgreSQL está em versão prévia

As seções a seguir descrevem a capacidade e os limites funcionais no serviço de banco de dados. Se você quiser saber mais sobre as camadas de recurso (computação, memória, armazenamento), consulte o artigo [computação e armazenamento](concepts-compute-storage.md) .

## <a name="maximum-connections"></a>Número máximo de conexões

O número máximo de conexões por tipo de preço e vCores são mostrados abaixo. O sistema do Azure requer três conexões para monitorar o servidor do banco de dados do Azure para PostgreSQL-flexível.

| Nome do SKU             | vCores | Tamanho da memória | Máximo de conexões | Máximo de conexões de usuário |
|----------------------|--------|-------------|-----------------|----------------------|
| **Expansíveis**        |        |             |                 |                      |
| B1ms                 | 1      | 2 GiB       | 50              | 47                   |
| B2S                  | 2      | 4 GiB       | 100             | 97                   |
| **Uso Geral**  |        |             |                 |                      |
| D2s_v3               | 2      | 8 GiB       | 214             | 211                  |
| D4s_v3               | 4      | 16 GiB      | 429             | 426                  |
| D8s_v3               | 8      | 32 GiB      | 859             | 856                  |
| D16s_v3              | 16     | 64 GiB      | 1718            | 1715                 |
| D32s_v3              | 32     | 128 GiB     | 3437            | 3434                 |
| D48s_v3              | 48     | 192 GiB     | 5.000            | 4997                 |
| D64s_v3              | 64     | 256 GiB     | 5.000            | 4997                 |
| **Otimizado para memória** |        |             |                 |                      |
| E2s_v3               | 2      | 16 GiB      | 1718            | 1715                 |
| E4s_v3               | 4      | 32 GiB      | 3437            | 3434                 |
| E8s_v3               | 8      | 64 GiB      | 5.000            | 4997                 |
| E16s_v3              | 16     | 128 GiB     | 5.000            | 4997                 |
| E32s_v3              | 32     | 256 GiB     | 5.000            | 4997                 |
| E48s_v3              | 48     | 384 GiB     | 5.000            | 4997                 |
| E64s_v3              | 64     | 432 GiB     | 5.000            | 4997                 |

Quando as conexões excederem o limite, você poderá receber o seguinte erro:
> FATAL: já existem muitos clientes

> [!IMPORTANT]
> Para obter a melhor experiência, recomendamos que você use um pool de conexões como o PgBouncer para gerenciar conexões com eficiência.

Uma conexão PostgreSQL, mesmo ociosa, pode ocupar cerca de 10 MB de memória. Além disso, a criação de novas conexões leva tempo. A maioria dos aplicativos solicita muitas conexões de curta duração, o que agrega a essa situação. O resultado é um número menor de recursos disponíveis para sua carga de trabalho real, o que leva à redução do desempenho. O pooling de conexão pode ser usado para reduzir conexões ociosas e reutilizar conexões existentes. Para saber mais, visite nossa [postagem no blog](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

## <a name="functional-limitations"></a>Limitações funcionais

### <a name="scale-operations"></a>Operações de dimensionamento

- O dimensionamento do armazenamento do servidor requer uma reinicialização do servidor.
- O armazenamento do servidor só pode ser dimensionado em incrementos de 2x, consulte [computação e armazenamento](concepts-compute-storage.md) para obter detalhes.
- Atualmente, não há suporte para diminuir o tamanho de armazenamento do servidor.

### <a name="server-version-upgrades"></a>Upgrade da versão do servidor

- Não há suporte para a migração automatizada entre versões de mecanismo de banco de dados principal. Se você quiser atualizar para a próxima versão principal, faça um [despejo e restaure](../howto-migrate-using-dump-and-restore.md) para um servidor que foi criado com a nova versão do mecanismo.

### <a name="storage"></a>Armazenamento

- Uma vez configurado, o tamanho do armazenamento não pode ser reduzido. Você precisa criar um novo servidor com o tamanho de armazenamento desejado, executar [despejo manual e restaurar](../howto-migrate-using-dump-and-restore.md) e migrar seus bancos de dados para o novo servidor.
- Atualmente, o recurso de crescimento automático do armazenamento não está disponível. Monitore o uso e aumente o armazenamento para um tamanho maior. 
- Quando o uso de armazenamento atinge 95% ou se a capacidade disponível é inferior a 5 GiB, o servidor é alternado automaticamente para o **modo somente leitura** para evitar erros associados a situações de disco cheio. 
- É recomendável definir regras de alerta para o `storage used` ou `storage percent` quando eles excedem determinados limites para que você possa tomar medidas de forma proativa, como aumentar o tamanho do armazenamento. Por exemplo, você pode definir um alerta se a porcentagem de armazenamento exceder 80% de uso.
  
### <a name="networking"></a>Rede

- No momento, não há suporte para a movimentação para dentro e para fora da VNET.
- Atualmente, não há suporte para a combinação de acesso público com a implantação em uma VNET.
- As regras de firewall não têm suporte na VNET, os grupos de segurança de rede podem ser usados em vez disso.
- Os servidores de banco de dados de acesso público podem se conectar à Internet pública, por exemplo `postgres_fdw` , e esse acesso não pode ser restringido. Os servidores baseados em VNET podem ter acesso restrito à saída usando grupos de segurança de rede.

### <a name="high-availability-ha"></a>Alta disponibilidade (HA)

- No momento, não há suporte para a HA Zone-Redundant para servidores com intermitência.
- O endereço IP do servidor de banco de dados é alterado quando o servidor executa o failover para o modo de espera de alta disponibilidade. Certifique-se de usar o registro DNS em vez do endereço IP do servidor.
- Se a replicação lógica estiver configurada com um servidor flexível configurado com alta disponibilidade, no caso de um failover para o servidor em espera, os slots de replicação lógica não serão copiados para o servidor em espera. 
- Para obter mais detalhes sobre a HA com redundância de zona, incluindo as limitações, consulte a página de [documentação conceitos-ha](concepts-high-availability.md) .

### <a name="availability-zones"></a>Zonas de disponibilidade

- No momento, não há suporte para a movimentação manual de servidores para uma zona de disponibilidade diferente.
- A zona de disponibilidade do servidor de espera de HA não pode ser configurada manualmente.

### <a name="postgres-engine-extensions-and-pgbouncer"></a>Mecanismo, extensões e PgBouncer do postgres

- Não há suporte para postgres 10 e anteriores. É recomendável usar a opção de [servidor único](../overview-single-server.md) se você precisar de versões mais antigas do Postgres.
- O suporte de extensão está limitado atualmente às `contrib` extensões Postgres.
- O pool de conexões PgBouncer interno não está disponível atualmente para servidores de banco de dados em uma VNET ou para servidores que se estourem.

### <a name="stopstart-operation"></a>Parar/iniciar operação

- O servidor não pode ser interrompido por mais de sete dias.

### <a name="scheduled-maintenance"></a>Manutenção agendada

- Alterar a janela de manutenção em menos de cinco dias antes de uma atualização já planejada não afetará essa atualização. As alterações só entrarão em vigor com a próxima manutenção agendada.

### <a name="backing-up-a-server"></a>Fazendo backup de um servidor

- Os backups são gerenciados pelo sistema, atualmente não há nenhuma maneira de executar esses backups manualmente. É recomendável usar o `pg_dump` em vez disso.
- Os backups são sempre backups completos baseados em instantâneo (backups não diferenciais), possivelmente levando a uma utilização de armazenamento de backup mais alta. Observe que os logs de transação (logs de gravação antecipada-WAL) são separados dos backups completos/diferenciais e são arquivados continuamente.

### <a name="restoring-a-server"></a>Restaurando um servidor

- Ao usar o recurso de restauração pontual, o novo servidor é criado com as mesmas configurações de computação e armazenamento que o servidor no qual ele se baseia.
- Os servidores de banco de dados baseados em VNET são restaurados na mesma VNET quando você restaura a partir de um backup.
- O novo servidor criado durante uma restauração não possui as regras de firewall existentes no servidor original. Regras de firewall precisam ser criadas separadamente para o novo servidor.
- Não há suporte para restaurar um servidor eliminado.
- Não há suporte para a restauração entre regiões.

### <a name="other-features"></a>Outros recursos

* Ainda não há suporte para a autenticação do Azure AD. É recomendável usar a opção de [servidor único](../overview-single-server.md) se você precisar de autenticação do Azure AD.
* Ainda não há suporte para réplicas de leitura. É recomendável usar a opção de [servidor único](../overview-single-server.md) se você precisar de réplicas de leitura.
* Não há suporte para a movimentação de recursos para outra assinatura. 


## <a name="next-steps"></a>Próximas etapas

- Entenda [o que está disponível para as opções de computação e armazenamento](concepts-compute-storage.md)
- Saiba mais sobre [Versões de Banco de Dados PostgreSQL com suporte](concepts-supported-versions.md)
- Veja [Como fazer backup e restaurar um servidor no Banco de Dados do Azure para PostgreSQL usando o Portal do Azure](how-to-restore-server-portal.md)
