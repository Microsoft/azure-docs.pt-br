---
title: Sobre o backup do compartilhamento de arquivos do Azure
description: Saiba como fazer backup de compartilhamentos de arquivos do Azure no cofre dos serviços de recuperação
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: e955df7662bd18ed1d1d4ec1f0aa6c9474c5386f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89378076"
---
# <a name="about-azure-file-share-backup"></a>Sobre o backup do compartilhamento de arquivos do Azure

O backup de compartilhamento de arquivos do Azure é uma solução de backup nativa baseada em nuvem que protege seus dados na nuvem e elimina sobrecargas de manutenção adicionais envolvidas em soluções de backup locais. O serviço de backup do Azure integra-se perfeitamente ao Sincronização de Arquivos do Azure e permite centralizar seus dados de compartilhamento de arquivos, bem como seus backups. Essa solução simples, confiável e segura permite que você configure a proteção para seus compartilhamentos de arquivos empresariais em algumas etapas simples com uma garantia de que você pode recuperar seus dados em qualquer cenário de desastre.

## <a name="key-benefits-of-azure-file-share-backup"></a>Principais benefícios do backup de compartilhamento de arquivos do Azure

* **Infraestrutura zero**: nenhuma implantação é necessária para configurar a proteção para seus compartilhamentos de arquivos.
* **Retenção personalizada**: você pode configurar backups com retenção diária/semanal/mensal/anual de acordo com suas necessidades.
* **Recursos de gerenciamento internos**: você pode agendar backups e especificar o período de retenção desejado sem a sobrecarga adicional de remoção de dados.
* **Restauração instantânea**: o backup de compartilhamento de arquivos do Azure usa instantâneos de compartilhamento de arquivos, para que você possa selecionar apenas os arquivos que deseja restaurar instantaneamente.
* **Alertas e relatórios**: você pode configurar alertas para falhas de backup e restauração e usar a solução de relatórios fornecida pelo backup do Azure para obter informações sobre backups em seus compartilhamentos de arquivos.
* **Proteção contra exclusão acidental de compartilhamentos de arquivos**: o backup do Azure habilita o [recurso de exclusão reversível](../storage/files/storage-files-prevent-file-share-deletion.md) em um nível de conta de armazenamento com um período de retenção de 14 dias. Mesmo que um ator mal-intencionado exclua o compartilhamento de arquivos, o conteúdo e os pontos de recuperação (instantâneos) do compartilhamento de arquivos são mantidos para um período de retenção configurável, permitindo a recuperação bem-sucedida e completa de conteúdo de origem e instantâneos sem perda de dados.

## <a name="architecture"></a>Arquitetura

![Arquitetura de backup do compartilhamento de arquivos do Azure](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>Como funciona o processo de backup

1. A primeira etapa na configuração do backup para compartilhamentos de arquivos do Azure é criar um cofre dos serviços de recuperação. O cofre fornece uma exibição consolidada dos backups configurados em diferentes cargas de trabalho.

2. Depois de criar um cofre, o serviço de backup do Azure descobre as contas de armazenamento que podem ser registradas com o cofre. Você pode selecionar a conta de armazenamento que hospeda os compartilhamentos de arquivos que deseja proteger.

3. Depois de selecionar a conta de armazenamento, o serviço de backup do Azure lista o conjunto de compartilhamentos de arquivos presentes na conta de armazenamento e armazena seus nomes no catálogo da camada de gerenciamento.

4. Em seguida, configure a política de backup (agendamento e retenção) de acordo com seus requisitos e selecione os compartilhamentos de arquivos para backup. O serviço de backup do Azure registra as agendas no plano de controle para fazer backups agendados.

5. Com base na política especificada, o Agendador de backup do Azure dispara backups no horário agendado. Como parte desse trabalho, o instantâneo de compartilhamento de arquivos é criado usando a API de compartilhamento de arquivos. Somente a URL do instantâneo é armazenada no repositório de metadados.

    >[!NOTE]
    >Os dados de compartilhamento de arquivos não são transferidos para o serviço de backup, já que o serviço de backup cria e gerencia instantâneos que fazem parte de sua conta de armazenamento e os backups não são transferidos para o cofre.

6. Você pode restaurar o conteúdo do compartilhamento de arquivos do Azure (arquivos individuais ou o compartilhamento completo) de instantâneos disponíveis no compartilhamento de arquivo de origem. Depois que a operação é disparada, a URL do instantâneo é recuperada do repositório de metadados e os dados são listados e transferidos do instantâneo de origem para o compartilhamento de arquivos de destino de sua escolha.

7. Se você estiver usando Sincronização de Arquivos do Azure, o serviço de backup indicará para o serviço de Sincronização de Arquivos do Azure os caminhos dos arquivos que estão sendo restaurados, o que dispara um processo de detecção de alterações em segundo plano nesses arquivos. Todos os arquivos que foram alterados são sincronizados com o ponto de extremidade do servidor. Esse processo ocorre em paralelo com a restauração original para o compartilhamento de arquivos do Azure.

8. Os dados de monitoramento do trabalho de backup e restauração são enviados por push para o serviço de monitoramento do backup do Azure. Isso permite que você monitore os backups de nuvem para seus compartilhamentos de arquivos em um único painel. Além disso, você também pode configurar alertas ou notificações por email quando a integridade do backup for afetada. Os emails são enviados por meio do serviço de email do Azure.

## <a name="backup-costs"></a>Custos de backup

Há dois custos associados à solução de backup de compartilhamento de arquivos do Azure:

1. **Custo de armazenamento de instantâneos**: os encargos de armazenamento incorridos para instantâneos são cobrados junto com o uso de arquivos do Azure de acordo com os detalhes de preços mencionados [aqui](https://azure.microsoft.com/pricing/details/storage/files/)

2. **Taxa de instância protegida**: a partir de 1º de setembro de 2020, os clientes serão cobrados por uma taxa de instância protegida de acordo com os detalhes de preços mencionados [aqui](https://azure.microsoft.com/pricing/details/backup/). A taxa de instância protegida depende do tamanho total dos compartilhamentos de arquivos protegidos em uma conta de armazenamento.

Para obter estimativas detalhadas de backup de compartilhamentos de arquivos do Azure, você pode baixar o [avaliador de preço detalhado do backup do Azure](https://aka.ms/AzureBackupCostEstimates).  

## <a name="next-steps"></a>Próximas etapas

* Saiba como [Fazer backup de compartilhamentos de arquivos do Azure](backup-afs.md)
* Encontre respostas para [perguntas sobre como fazer backup de arquivos do Azure](backup-azure-files-faq.md)
