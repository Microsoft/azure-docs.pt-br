---
title: Solucionar problemas do Azure Data Share
description: Saiba como solucionar problemas com convites e erros ao criar ou receber compartilhamentos de dados no compartilhamento de dados do Azure.
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.openlocfilehash: 3aa1c0b8579bd37d2bb51cbde70997131c696813
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964500"
---
# <a name="troubleshoot-common-problems-in-azure-data-share"></a>Solucionar problemas comuns no compartilhamento de dados do Azure 

Este artigo explica como solucionar problemas comuns no compartilhamento de dados do Azure. 

## <a name="azure-data-share-invitations"></a>Convites do Azure Data Share 

Em alguns casos, quando novos usuários selecionam **aceitar convite** em um convite por email, eles podem ver uma lista vazia de convites. 

:::image type="content" source="media/no-invites.png" alt-text="Captura de tela mostrando uma lista vazia de convites.":::

Esse problema pode ter uma das seguintes causas:

* **O serviço de compartilhamento de dados do Azure não está registrado como um provedor de recursos de nenhuma assinatura do Azure no locatário do Azure.** Esse problema ocorre quando seu locatário do Azure não tem nenhum recurso de compartilhamento de dados. 

    Quando você cria um recurso Azure Data Share, ele registra automaticamente o provedor de recursos na assinatura do Azure. Você pode registrar manualmente o serviço de compartilhamento de dados usando as etapas a seguir. Para concluir essas etapas, você precisa da [função de colaborador](../role-based-access-control/built-in-roles.md#contributor) para a assinatura do Azure. 

    1. No portal do Azure, vá para **Assinaturas**.
    1. Selecione a assinatura que você deseja usar para criar o recurso de compartilhamento de dados do Azure.
    1. Selecione **provedores de recursos**.
    1. Procure **Microsoft. DataShare**.
    1. Selecione **Registrar**.

* **O convite é enviado para seu alias de email em vez de seu endereço de email de entrada do Azure.** Se você já registrou o serviço de compartilhamento de dados do Azure ou criou um recurso de compartilhamento de dados no locatário do Azure, mas ainda não consegue ver o convite, seu alias de email pode estar listado como o destinatário. Entre em contato com seu provedor de dados e verifique se o convite será enviado ao seu endereço de email de entrada do Azure e não ao seu alias de email.

* **O convite já foi aceito.** O link no email leva você para a página **convites de compartilhamento de dados** no portal do Azure. Esta página lista apenas os convites pendentes. Os convites aceitos não aparecem na página. Para exibir os compartilhamentos recebidos e configurar sua configuração de cluster de Data Explorer do Azure de destino, vá para o recurso de compartilhamento de dados que você usou para aceitar o convite.

## <a name="creating-and-receiving-shares"></a>Criando e recebendo compartilhamentos

Os erros a seguir podem aparecer ao criar um novo compartilhamento, Adicionar conjuntos de valores ou mapear conjuntos de valores:

* Falha ao adicionar conjuntos de os.
* Falha ao mapear conjuntos de os.
* Não é possível conceder ao recurso de compartilhamento de dados o acesso x para y.
* Você não tem as permissões adequadas para x.
* Não foi possível adicionar permissões de gravação para a conta do compartilhamento de dados do Azure a um ou mais dos recursos selecionados.

Você poderá ver um desses erros se não tiver permissões suficientes para o armazenamento de dados do Azure. Para obter mais informações, consulte [funções e requisitos](concepts-roles-permissions.md). 

Você precisa da permissão de gravação para compartilhar ou receber dados de um armazenamento de dados do Azure. Essa permissão normalmente é parte da função colaborador. 

Se você estiver compartilhando dados ou recebendo dados do armazenamento de dados do Azure pela primeira vez, também precisará da permissão *Microsoft. Authorization/role/Write* . Essa permissão normalmente é parte da função de proprietário. Mesmo que você tenha criado o recurso de armazenamento de dados do Azure, você não é necessariamente o proprietário do recurso. 

Quando você tem as permissões adequadas, o serviço de compartilhamento de dados do Azure permite automaticamente que a identidade gerenciada do recurso de compartilhamento de dados acesse o armazenamento de dados. Esse processo pode levar alguns minutos. Se você tiver uma falha devido a esse atraso, tente novamente após alguns minutos.

O compartilhamento baseado em SQL requer permissões adicionais. Para obter informações sobre os pré-requisitos, consulte [compartilhar de fontes SQL](how-to-share-from-sql.md).

## <a name="snapshots"></a>Instantâneos
Um instantâneo pode falhar por vários motivos. Abra uma mensagem de erro detalhada selecionando a hora de início do instantâneo e o status de cada conjunto de informações. 

Os instantâneos normalmente falham por esses motivos:

* O compartilhamento de dados não tem permissão para ler do armazenamento de dados de origem ou para gravar no armazenamento de dados de destino. Para obter mais informações, consulte [funções e requisitos](concepts-roles-permissions.md). Se você estiver tirando um instantâneo pela primeira vez, o recurso de compartilhamento de dados poderá precisar de alguns minutos para obter acesso ao armazenamento de dados do Azure. Depois de alguns minutos, tente novamente.
* A conexão de compartilhamento de dados com o armazenamento de dados de origem ou o armazenamento de dados de destino é bloqueada por um firewall.
* Um conjunto de dados compartilhado, um armazenamento de dados de origem ou um armazenamento de destino foi excluído.

Para contas de armazenamento, um instantâneo pode falhar porque um arquivo está sendo atualizado na origem enquanto o instantâneo está acontecendo. Como resultado, um arquivo de 0 byte pode aparecer no destino. Após a atualização na origem, os instantâneos devem ter sucesso.

Para fontes SQL, um instantâneo pode falhar por esses outros motivos:

* O script SQL de origem ou o script SQL de destino que concede permissão de compartilhamento de dados não foi executado. Ou para o banco de dados SQL do Azure ou o Azure Synapse Analytics (anteriormente SQL Data Warehouse do Azure), o script é executado usando a autenticação do SQL em vez de Azure Active Directory autenticação.  
* O armazenamento de dados de origem ou o armazenamento de dados SQL de destino está em pausa.
* O processo de instantâneo ou o armazenamento de dados de destino não dá suporte a tipos de dados SQL. Para obter mais informações, consulte [compartilhar de fontes SQL](how-to-share-from-sql.md#supported-data-types).
* O armazenamento de dados de origem ou o armazenamento de dados SQL de destino está bloqueado por outros processos. O compartilhamento de dados do Azure não bloqueia esses armazenamentos de dados. Mas os bloqueios existentes nesses armazenamentos de dados podem fazer um instantâneo falhar.
* A tabela SQL de destino é referenciada por uma restrição FOREIGN KEY. Durante um instantâneo, se uma tabela de destino tiver o mesmo nome que uma tabela nos dados de origem, o compartilhamento de dados do Azure descartará a tabela e criará uma nova tabela. Se a tabela SQL de destino for referenciada por uma restrição FOREIGN KEY, a tabela não poderá ser descartada.
* Um arquivo CSV de destino é gerado, mas os dados não podem ser lidos no Excel. Você poderá ver esse problema quando a tabela SQL de origem contiver dados que incluem caracteres que não são do inglês. No Excel, selecione a guia **obter dados** e escolha o arquivo CSV. Selecione a origem do arquivo **65001: Unicode (UTF-8)** e, em seguida, carregue os dados.

## <a name="updated-snapshot-schedules"></a>Agendamentos de instantâneos atualizados
Depois que o provedor de dados atualizar o agendamento de instantâneo para o compartilhamento enviado, o consumidor de dados precisará desabilitar a agenda de instantâneo anterior. Em seguida, habilite o agendamento de instantâneo atualizado para o compartilhamento recebido. 

## <a name="next-steps"></a>Próximas etapas

Para saber como começar a compartilhar dados, prossiga para o tutorial [compartilhar dados](share-your-data.md) . 

Para saber como receber dados, continue no tutorial [aceitar e receber dados](subscribe-to-data-share.md) .