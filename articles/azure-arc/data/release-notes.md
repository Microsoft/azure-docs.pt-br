---
title: Serviços de dados habilitados para Arc do Azure – notas de versão
description: Notas sobre a versão mais recentes
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/02/2021
ms.topic: conceptual
ms.openlocfilehash: 6b4d5c1372a8351f1fe5a6608aff38bf232aabd8
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121942"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Notas de versão – serviços de dados habilitados para o Azure Arc (versão prévia)

Este artigo realça os recursos, os recursos e os aprimoramentos lançados recentemente ou aprimorados para os serviços de dados habilitados para o Azure Arc. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="february-2021"></a>Fevereiro de 2021

### <a name="new-capabilities-and-features"></a>Novos recursos e recursos

Número de versão da CLI de dados do Azure ( `azdata` ): 20.3.1. Baixe em [https://aka.ms/azdata](https://aka.ms/azdata) . Você pode instalar o `azdata` de [instalar a CLI de dados do Azure ( `azdata` )](/sql/azdata/install/deploy-install-azdata).

As atualizações adicionais incluem:

- Instância Gerenciada de SQL habilitada para Azure Arc
   - Alta disponibilidade com o grupos de disponibilidade Always On

- Azure Data Studio de hiperescala PostgreSQL habilitado para Arc do Azure: 
   - A página Visão geral agora mostra o status do grupo de servidores discriminados por nó
   - Uma nova página de propriedades agora está disponível para mostrar mais detalhes sobre o grupo de servidores
   - Configurar parâmetros do mecanismo postgres na página **parâmetros do nó**

Para problemas associados a esta versão, consulte [problemas conhecidos – serviços de dados habilitados para Arc do Azure (versão prévia)](known-issues.md)

## <a name="january-2021"></a>Janeiro de 2021

### <a name="new-capabilities-and-features"></a>Novos recursos e recursos

Número de versão da CLI de dados do Azure ( `azdata` ): 20.3.0. Baixe em [https://aka.ms/azdata](https://aka.ms/azdata) . Você pode instalar o `azdata` de [instalar a CLI de dados do Azure ( `azdata` )](/sql/azdata/install/deploy-install-azdata).

As atualizações adicionais incluem:
- Portal localizado disponível para 17 novos idiomas
- Alterações secundárias em arquivos Kube-Native. YAML
- Novas versões do Grafana e do Kibana
- Problemas com ambientes do Python ao usar o azdata em blocos de anotações no Azure Data Studio resolvido
- A extensão pg_audit agora está disponível para a hiperescala PostgreSQL
- Uma ID de backup não é mais necessária ao fazer uma restauração completa de um banco de dados de hiperescala PostgreSQL
- O status (estado de integridade) é relatado para cada uma das instâncias do PostgreSQL que constituem um grupo de servidores

   Em versões anteriores, o status era agregado no nível do grupo de servidores e não é discriminado no nível do nó PostgreSQL.

- As implantações do PostgreSQL agora respeitam os parâmetros de tamanho do volume indicados em criar comandos
- Os parâmetros de versão do mecanismo agora são respeitados ao editar um grupo de servidores
- A Convenção de nomenclatura do pods para a hiperescala PostgreSQL habilitada para Arc do Azure foi alterada

    Agora ele está no formato: `ServergroupName{c, w}-n` . Por exemplo, um grupo de servidores com três nós, um nó de coordenador e dois nós de trabalho são representados como:
   - `Postgres01c-0` (nó de coordenador)
   - `Postgres01w-0` (nó de trabalho)
   - `Postgres01w-1` (nó de trabalho)

## <a name="december-2020"></a>Dezembro de 2020

### <a name="new-capabilities--features"></a>Novos recursos & recursos

Número de versão da CLI de dados do Azure ( `azdata` ): 20.2.5. Baixe em [https://aka.ms/azdata](https://aka.ms/azdata) .

Exiba pontos de extremidade para o SQL Instância Gerenciada e PostgreSQL hiperescala usando a CLI de dados do Azure ( `azdata` ) com `azdata arc sql endpoint list` `azdata arc postgres endpoint list` comandos e.

Edite as solicitações e os limites do recurso SQL Instância Gerenciada (CPU Core e Memory) usando Azure Data Studio.

A hiperescala do PostgreSQL habilitado para Arc do Azure agora dá suporte à restauração pontual, além da restauração de backup completo para as versões 11 e 12 do PostgreSQL. A capacidade de restauração pontual permite que você indique uma data e hora específicas para restaurar.

A Convenção de nomenclatura do pods para a hiperescala PostgreSQL habilitada para Arc do Azure foi alterada. Agora ele está no formato: ServergroupName {r, s}-_n_. Por exemplo, um grupo de servidores com três nós, um nó de coordenador e dois nós de trabalho são representados como:
- `postgres02r-0` (nó de coordenador)
- `postgres02s-0` (nó de trabalho)
- `postgres02s-1` (nó de trabalho)

### <a name="breaking-change"></a>Alteração da falha

#### <a name="new-resource-provider"></a>Novo provedor de recursos

Esta versão apresenta um [provedor de recursos](../../azure-resource-manager/management/azure-services-resource-providers.md) atualizado chamado `Microsoft.AzureArcData`. Para poder usar esse recurso, você precisa registrar este provedor de recursos. 

Para registrar este provedor de recursos: 

1. No portal do Azure, selecione **assinaturas** 
2. Escolha sua assinatura
3. Em **Configurações**, selecione **Provedores de recursos** 
4. Procure `Microsoft.AzureArcData` e selecione **Registrar** 

Você pode examinar etapas detalhadas em [provedores de recursos e tipos do Azure](../../azure-resource-manager/management/resource-providers-and-types.md). Essa alteração também remove todos os recursos existentes do Azure que você carregou para o portal do Azure. Para usar o provedor de recursos, você precisa atualizar o controlador de dados e usar a CLI mais recente `azdata` .  

### <a name="platform-release-notes"></a>Notas sobre a versão da plataforma

#### <a name="direct-connectivity-mode"></a>Modo de conectividade direta

Esta versão apresenta o modo de conectividade direta. O modo de conectividade direta permite que o controlador de dados carregue automaticamente as informações de uso no Azure. Como parte do carregamento de uso, o recurso do controlador de dados Arc é criado automaticamente no portal, se ele ainda não tiver sido criado via `azdata` upload.  

Você pode especificar a conectividade direta ao criar o controlador de dados. O exemplo a seguir cria um controlador de dados com o `azdata arc dc create` nome `arc` usando o modo de conectividade direta ( `connectivity-mode direct` ). Antes de executar o exemplo, substitua `<subscription id>` pela sua ID de assinatura.

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group my-resource-group --location eastus --connectivity-mode direct
```

### <a name="known-issues"></a>Problemas conhecidos

- No serviço de kubernetes do Azure (AKS), não há suporte para kubernetes versão 1.19. x.
- No kubernetes 1,19 `containerd` não tem suporte.
- O recurso de controlador de dados no Azure é atualmente um recurso do Azure. Todas as atualizações como excluir não são propagadas de volta para o cluster kubernetes.
- Os nomes de instância não podem ter mais de 13 caracteres
- Nenhuma atualização in-loco para o controlador de dados do Arc do Azure ou instâncias de banco de dado.
- As imagens de contêiner dos serviços de dados habilitados para Arc não foram assinadas.  Talvez seja necessário configurar os nós do Kubernetes para permitir que as imagens de contêiner não assinadas sejam recebidas.  Por exemplo, se você estiver usando o Docker como o tempo de execução do contêiner, poderá definir a variável de ambiente DOCKER_CONTENT_TRUST = 0 e reiniciar.  Outros runtimes de contêiner têm opções semelhantes, como em [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Não é possível criar instâncias gerenciadas SQL do ARC ou grupos de servidores de hiperescala PostgreSQL habilitados do Azure no portal do Azure.
- Por enquanto, se você estiver usando o NFS, precisará definir `allowRunAsRoot` como `true` em seu arquivo de perfil de implantação antes de criar o controlador de dados de arco do Azure.
- Somente autenticação de logon do SQL e PostgreSQL.  Não há suporte para Azure Active Directory ou Active Directory.
- A criação de um controlador de dados no OpenShift requer restrições de segurança relaxadas.  Confira a documentação para obter detalhes.
- Se você estiver usando o mecanismo do AKS (serviço de kubernetes do Azure) no Hub Azure Stack com o controlador de dados Arc do Azure e instâncias de banco de dado, não haverá suporte para a atualização para uma versão mais recente do kubernetes. Desinstale o controlador de dados Arc do Azure e todas as instâncias de banco de dado antes de atualizar o cluster kubernetes.
- Os clusters AKS que abrangem [várias zonas de disponibilidade](../../aks/availability-zones.md) não têm suporte no momento para serviços de dados habilitados para Arc do Azure. Para evitar esse problema, ao criar o cluster AKS no portal do Azure, se você selecionar uma região em que as zonas estão disponíveis, desmarque todas as zonas do controle de seleção. Veja a seguinte imagem:

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Desmarque as caixas de seleção de cada zona para especificar nenhuma.":::

#### <a name="postgresql"></a>PostgreSQL

- A hiperescala do PostgreSQL habilitado para Arc do Azure retorna uma mensagem de erro imprecisa quando não pode restaurar para o ponto relativo no tempo que você indica. Por exemplo, se você especificou um ponto no tempo para restaurar que é mais antigo do que os seus backups contêm, a restauração falhará com uma mensagem de erro como: erro: (404). Motivo: não encontrado. Corpo da resposta HTTP: {"código": 404, "internalStatus": "NOT_FOUND", "motivo": "falha ao restaurar o backup do servidor...}
Quando isso acontecer, reinicie o comando depois de indicar um ponto no tempo que está dentro do intervalo de datas para o qual você tem backups. Você determinará esse intervalo listando os backups e examinando as datas em que eles foram tirados.
- Há suporte para a restauração pontual somente em grupos de servidores. O servidor de destino de uma operação de restauração pontual não pode ser o servidor do qual você realizou o backup. Ele deve ser um grupo de servidores diferente. No entanto, a restauração completa tem suporte no mesmo grupo de servidores.
- Uma ID de backup é necessária ao fazer uma restauração completa. Por padrão, se você não estiver indicando uma ID de backup, o backup mais recente será usado. Isso não funciona nesta versão.

## <a name="october-2020"></a>Outubro de 2020 

Número de versão da CLI de dados do Azure ( `azdata` ): 20.2.3. Baixe em [https://aka.ms/azdata](https://aka.ms/azdata) .

### <a name="breaking-changes"></a>Alterações de quebra

Esta versão apresenta as seguintes alterações significativas: 

* Na definição de recurso personalizado PostgreSQL (CRD), o termo `shards` é renomeado para `workers` . Este termo ( `workers` ) corresponde ao nome do parâmetro de linha de comando.

* `azdata arc postgres server delete` solicita confirmação antes de excluir uma instância de Postgres.  Use `--force` para ignorar o prompt.

### <a name="additional-changes"></a>Alterações adicionais

* Um novo parâmetro opcional foi adicionado ao `azdata arc postgres server create` chamado `--volume-claim mounts` . O valor é uma lista separada por vírgulas de montagens de declaração de volume. Uma montagem de declaração de volume é um par de tipo de volume e nome de PVC. O único tipo de volume com suporte no momento é `backup` .  No PostgreSQL, quando o tipo de volume é `backup` , o PVC é montado no `/mnt/db-backups` .  Isso permite o compartilhamento de backups entre instâncias de PostgresSQL para que o backup de uma instância PostgresSQL possa ser restaurado em outra instância.

* Novos nomes curtos para definições de recursos personalizados do PostgresSQL: 
  * `pg11` 
  * `pg12`
* O upload de telemetria fornece ao usuário:
   * Número de pontos carregados no Azure ou 
   * Se nenhum dado tiver sido carregado no Azure, um prompt para tentar novamente.
* `azdata arc dc debug copy-logs` Agora também lê da `/var/opt/controller/log` pasta e coleta logs do mecanismo PostgreSQL no Linux.
*   Exibir um indicador de funcionamento durante a criação e restauração de backup com o PostgreSQL de hiperescala.
* `azdata arc postrgres backup list` Agora inclui informações de tamanho do backup.
* A propriedade nome do administrador do SQL Instância Gerenciada foi adicionada à coluna direita da folha visão geral na portal do Azure.
* O Azure Data Studio dá suporte à configuração do número de nós de trabalho, vCore e configurações de memória para a hiperescala PostgreSQL. 
* A visualização dá suporte a backup/restauração para postgres versão 11 e 12.

## <a name="september-2020"></a>Setembro de 2020

Os serviços de dados habilitados para Arc do Azure são liberados para visualização pública. Os serviços de dados habilitados para Arc permitem que você gerencie serviços de dados em qualquer lugar.

- Instância Gerenciada de SQL
- Hiperescala do PostgreSQL

Para obter instruções, consulte [o que são os serviços de dados habilitados para o Azure Arc?](overview.md)

## <a name="next-steps"></a>Próximas etapas

> **Quer apenas experimentar as novidades?**  
> Comece rapidamente com o [Azure Arc JumpStart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) no AKs, AWS elástico kubernetes Service (EKS), Google Cloud kubernetes Engine (GKE) ou em uma VM do Azure.

- [Instalar as ferramentas de cliente](install-client-tools.md)
- [Criar o controlador de dados do Azure Arc](create-data-controller.md) (é necessária a instalação das ferramentas de cliente primeiro)
- [Criar uma instância gerenciada de SQL do Azure no Azure Arc](create-sql-managed-instance.md) (é necessária a criação de um controlador de dados do Azure Arc primeiro)
- [Criar um grupo de servidores de Hiperescala do Banco de Dados do Azure para PostgreSQL no Azure Arc](create-postgresql-hyperscale-server-group.md) (é necessária a criação de um controlador de dados do Azure Arc primeiro)
- [Provedores de recursos para serviços do Azure](../../azure-resource-manager/management/azure-services-resource-providers.md)
