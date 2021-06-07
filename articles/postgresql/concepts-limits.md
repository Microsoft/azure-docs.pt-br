---
title: Limites-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve os limites no banco de dados do Azure para PostgreSQL-servidor único, como o número de opções de mecanismo de armazenamento e conexão.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 7cee2be3c1d7a97b87560873d6cef07ec361ee99
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605171"
---
# <a name="limits-in-azure-database-for-postgresql---single-server"></a>Limites no banco de dados do Azure para PostgreSQL-servidor único
As seções a seguir descrevem a capacidade e os limites funcionais no serviço de banco de dados. Se você quiser saber mais sobre as camadas de recurso (computação, memória, armazenamento), consulte o artigo [tipos de preço](concepts-pricing-tiers.md) .


## <a name="maximum-connections"></a>Número máximo de conexões
O número máximo de conexões por tipo de preço e vCores são mostrados abaixo. O sistema do Azure exige cinco conexões para monitorar o Banco de Dados do Azure para o servidor PostgreSQL. 

|**Tipo de preço**| **vCore(s)**| **Máximo de conexões** | **Máximo de conexões de usuário** |
|---|---|---|---|
|Basic| 1| 55 | 50|
|Basic| 2| 105 | 100|
|Uso Geral| 2| 150| 145|
|Uso Geral| 4| 250| 245|
|Uso Geral| 8| 480| 475|
|Uso Geral| 16| 950| 945|
|Uso Geral| 32| 1500| 1495|
|Uso Geral| 64| 1900| 1895|
|Otimizado para memória| 2| 300| 295|
|Otimizado para memória| 4| 500| 495|
|Otimizado para memória| 8| 960| 955|
|Otimizado para memória| 16| 1900| 1895|
|Otimizado para memória| 32| 1987| 1982|

Quando as conexões excederem o limite, você poderá receber o seguinte erro:
> FATAL: já existem muitos clientes

> [!IMPORTANT]
> Para obter a melhor experiência, recomendamos que você use um pool de conexões como o pgBouncer para gerenciar conexões com eficiência.

Uma conexão PostgreSQL, mesmo ociosa, pode ocupar cerca de 10 MB de memória. Além disso, a criação de novas conexões leva tempo. A maioria dos aplicativos solicita muitas conexões de curta duração, o que agrega a essa situação. O resultado é um número menor de recursos disponíveis para sua carga de trabalho real, o que leva à redução do desempenho. Um pool de conexões que diminui as conexões ociosas e reutiliza as conexões existentes ajudará a evitar isso. Para saber mais, visite nossa [postagem no blog](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

## <a name="functional-limitations"></a>Limitações funcionais
### <a name="scale-operations"></a>Operações de dimensionamento
- O dimensionamento dinâmico de e para as camadas de preços básicas não tem suporte no momento.
- Atualmente, não há suporte para diminuir o tamanho de armazenamento do servidor.

### <a name="server-version-upgrades"></a>Upgrade da versão do servidor
- Não há suporte para a migração automatizada entre versões de mecanismo de banco de dados principal. Se você quiser atualizar para a próxima versão principal, faça um [despejo e restaure](./howto-migrate-using-dump-and-restore.md) para um servidor que foi criado com a nova versão do mecanismo.

> Observe que, antes do PostgreSQL versão 10, [a política de controle de versão do PostgreSQL](https://www.postgresql.org/support/versioning/) considerou uma atualização de _versão principal_ para ser um aumento no primeiro _ou_ segundo número (por exemplo, 9,5 a 9,6 foi considerado uma atualização de versão _principal_ ).
> A partir da versão 10, apenas uma alteração no primeiro número é considerada uma atualização de versão principal (por exemplo, 10,0 a 10,1 é uma atualização de versão _secundária_ e 10 a 11 é uma atualização de versão _principal_ ).

### <a name="vnet-service-endpoints"></a>Ponto de extremidade de serviço VNet
- O suporte para ponto de extremidade de serviço de VNet é apenas para servidores de Uso Geral e Otimizados para Memória.

### <a name="restoring-a-server"></a>Restaurando um servidor
- Ao usar o recurso PITR, o novo servidor é criado com as mesmas configurações de camadas de preços que o servidor em que é baseado.
- O novo servidor criado durante uma restauração não possui as regras de firewall existentes no servidor original. As regras de firewall precisam ser configuradas separadamente para esse novo servidor.
- Não há suporte para restaurar um servidor eliminado.

### <a name="utf-8-characters-on-windows"></a>Caracteres UTF-8 no Windows
- Em alguns cenários, não há suporte completo para caracteres UTF-8 no PostgreSQL de software livre no Windows, o que afeta o Banco de Dados do Azure para PostgreSQL. Confira o thread [Bug nº 15476 nos arquivos do PostgreSQL](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html) para obter mais informações.

### <a name="gss-error"></a>Erro de GSS
Se aparecer um erro relacionado a **GSS**, provavelmente você está usando uma versão de cliente/driver mais recente que o servidor único do Azure Postgres ainda não oferece compatibilidade total. Esse erro é conhecido por afetar [versões 42.2.15 e 42.2.16 do driver JDBC](https://github.com/pgjdbc/pgjdbc/issues/1868).
   - Planejamos concluir a atualização até o final de novembro. Considere usar uma versão de driver que esteja funcionando enquanto isso.
   - Ou, considere desabilitar a solicitação de GSS.  Use um parâmetro de conexão como `gssEncMode=disable`.

### <a name="storage-size-reduction"></a>Redução do tamanho do armazenamento
O tamanho do armazenamento não pode ser reduzido. Você precisa criar um novo servidor com o tamanho de armazenamento desejado, executar [despejo manual e restaurar](./howto-migrate-using-dump-and-restore.md) e migrar seus bancos de dados para o novo servidor.

## <a name="next-steps"></a>Próximas etapas
- Entenda [o que está disponível em cada tipo de preço](concepts-pricing-tiers.md)
- Saiba mais sobre [Versões de Banco de Dados PostgreSQL com suporte](concepts-supported-versions.md)
- Veja [Como fazer backup e restaurar um servidor no Banco de Dados do Azure para PostgreSQL usando o Portal do Azure](howto-restore-server-portal.md)
