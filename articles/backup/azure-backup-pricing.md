---
title: Preços do Backup do Azure
description: Saiba como estimar os custos para orçar os preços do Backup do Azure.
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 03ec0076d3089562ddaace6db413fb3f1ba949a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88654524"
---
# <a name="azure-backup-pricing"></a>Preços do Backup do Azure

Para saber mais sobre os preços do Backup do Azure, acesse a [página de preços do Backup do Azure](https://azure.microsoft.com/pricing/details/backup/).

## <a name="download-detailed-estimates-for-azure-backup-pricing"></a>Baixe estimativas detalhadas dos preços do Backup do Azure

Se você pretende estimar os custos para fins de orçamento ou comparação de custos, baixe o [avaliador de preços detalhado do Backup do Azure](https://aka.ms/AzureBackupCostEstimates).  

### <a name="what-does-the-estimator-contain"></a>O que o avaliador contém?

A folha do avaliador de custo do Backup do Azure traz uma opção para você estimar todas as cargas de trabalho possíveis que pretende copiar em backup por meio do Backup do Azure. Essas cargas de trabalho incluem:

- VMs do Azure
- Servidores locais
- SQL em VMs do Azure
- SAP HANA em VMs do Azure
- Compartilhamentos de arquivos do Azure

## <a name="estimate-costs-for-backing-up-azure-vms-or-on-premises-servers"></a>Estimar os custos para fazer backup de VMs do Azure ou de servidores locais

Para estimar os custos de backup de VMs do Azure ou de servidores locais usando o Backup do Azure, você precisará dos seguintes parâmetros:

- Tamanho das VMs ou dos servidores locais dos quais você está tentando fazer backup
  - Insira o “tamanho utilizado” dos discos ou dos servidores necessários para o backup

- Número de servidores com esse tamanho

- Qual é o volume esperado da variação de dados nesses servidores?<br>
  A variação refere-se ao volume de alterações nos dados. Por exemplo, se você tiver uma VM com 200 GB de dados para backup e 10 GB desse volume forem alterados todos os dias, a variação diária será de 5%.

  - Uma variação mais alta significa que você faz backup de mais dados

  - Escolha **Baixa** ou **Moderada** para servidores de arquivos e **Alta** se estiver executando bancos de dados

  - Se você sabe o **% da variação**, use a opção **Inserir um % próprio**

- Escolher a política de backup

  - Por quanto tempo você espera manter os backups “Diários”? (em dias)

  - Por quanto tempo você espera manter os backups “Semanais”? (em semanas)

  - Por quanto tempo você espera manter os backups “Mensais”? (em meses)

  - Por quanto tempo você espera manter os backups “Anuais”? (em anos)

  - Por quanto tempo você espera manter os “Instantâneos de restauração instantânea”? (1 a 5 dias)

    - Essa opção permite que você faça uma restauração que abranja até sete dias de maneira rápida usando instantâneos armazenados em discos.

- **Opcional**: backup de disco seletivo

  - Se você estiver usando a opção **Backup de Disco Seletivo** ao fazer backup de VMs do Azure, escolha a opção **Excluir Disco** e insira o percentual de discos excluídos do backup em termos de tamanho. Por exemplo, se você tiver uma VM conectada a três discos com 200 GB utilizados em cada um e quiser excluir dois deles do backup, insira 66,7%.

- **Opcional**: redundância do armazenamento de backup

  - Isso indica a redundância da conta de armazenamento na qual os dados de backup serão copiados. Recomendamos usar o **GRS** para obter a maior disponibilidade. Como ele garante que uma cópia dos seus dados de backup seja mantida em uma região diferente, ele ajuda a atender a vários padrões de conformidade. Altere a redundância para **LRS** se estiver fazendo backup de ambientes de desenvolvimento ou de teste que não precisem ter um backup de nível empresarial. Selecione a opção **RAGRS** na folha se desejar entender os custos quando a [Restauração Entre Regiões](backup-azure-arm-restore-vms.md#cross-region-restore) estiver habilitada para os backups.

- **Opcional**: modificar os preços regionais ou aplicar taxas com desconto

  - Caso deseje verificar suas estimativas para outra região ou as taxas com desconto, selecione **Sim** na opção **Experimentar estimativas para outra região?** e insira as taxas com as quais deseja executar as estimativas.

## <a name="estimate-costs-for-backing-up-sql-servers-in-azure-vms"></a>Estimar os custos para backup de SQL Servers em VMs do Azure

Para estimar os custos de backup de SQL Servers em execução em VMs do Azure usando o Backup do Azure, você precisará dos seguintes parâmetros:

- Tamanho dos SQL Servers dos quais você está tentando fazer backup

- Número de SQL Servers com o tamanho indicado acima

- Qual é a compactação esperada para os dados de backup dos SQL Servers?

  - A maioria dos clientes do Backup do Azure observa que os dados de backup têm 80% de compactação comparado ao tamanho do SQL Server quando a compactação do SQL está **habilitada**.

  - Se você espera ver outra compactação, insira o número neste campo

- Qual é o tamanho esperado de backups de log?

  - O sinal de % indica o tamanho diário do log como um % do tamanho do SQL Server

- Qual é o volume esperado da variação de dados diários nesses servidores?

  - Normalmente, os bancos de dados têm uma variação “Alta”

  - Se você sabe o **% da variação**, use a opção **Inserir um % próprio**

- Escolher a política de backup

  - Tipo de backup

    - A política mais efetiva que você pode escolher é **Diferenciais diários** com backups completos semanais/mensais/anuais. O Backup do Azure também pode ser restaurado por meio de diferenciais com um clique.

    - Você também pode optar por ter uma política com backups completos diários/semanais/mensais/anuais. Essa opção consumirá um pouco mais de armazenamento do que a primeira.

  - Por quanto tempo você espera manter os backups de “log”? (em dias) [7-35]

  - Por quanto tempo você espera manter os backups “Diários”? (em dias)

  - Por quanto tempo você espera manter os backups “Semanais”? (em semanas)

  - Por quanto tempo você espera manter os backups “Mensais”? (em meses)

  - Por quanto tempo você espera manter os backups “Anuais”? (em anos)

- **Opcional**: redundância do armazenamento de backup

  - Isso indica a redundância da conta de armazenamento na qual os dados de backup serão copiados. Recomendamos usar o **GRS** para obter a maior disponibilidade. Como ele garante que uma cópia dos seus dados de backup seja mantida em uma região diferente, ele ajuda a atender a vários padrões de conformidade. Altere a redundância para **LRS** se estiver fazendo backup de ambientes de desenvolvimento ou de teste que não precisem ter um backup de nível empresarial.

- **Opcional**: modificar os preços regionais ou aplicar taxas com desconto

  - Caso deseje verificar suas estimativas para outra região ou as taxas com desconto, selecione **Sim** na opção **Experimentar estimativas para outra região?** e insira as taxas com as quais deseja executar as estimativas.

## <a name="estimate-costs-for-backing-up-sap-hana-servers-in-azure-vms"></a>Estimar os custos para backup de servidores SAP HANA em VMs do Azure

Para estimar os custos de backup de servidores SAP HANA em execução em VMs do Azure usando o Backup do Azure, você precisará dos seguintes parâmetros:

- Tamanho total dos bancos de dados SAP HANA que você está tentando fazer backup. Essa deve ser a soma do tamanho de backup completo de cada um dos bancos de dados, conforme relatado pelo SAP HANA.
- Número de servidores SAP HANA com o tamanho indicado acima
- Qual é o tamanho esperado de backups de log?
  
  - O % indica o tamanho médio diário do log como um % do tamanho total dos bancos de dados SAP HANA que você está fazendo backup no servidor SAP HANA
- Qual é o volume esperado da variação de dados diários nesses servidores?
  - O % indica o tamanho médio diário da variação como um % do tamanho total dos bancos de dados SAP HANA que você está fazendo backup no servidor SAP HANA
  - Normalmente, os bancos de dados têm uma variação “Alta”
  - Se você sabe o **% da variação**, use a opção **Inserir um % próprio**
- Escolher a política de backup
  - Tipo de backup
    - A política mais efetiva que você pode escolher é **Diferenciais diários** com backups completos **semanais/mensais/anuais**. O Backup do Azure também pode ser restaurado por meio de diferenciais com um clique.
    - Você também pode optar por ter uma política com backups completos **diários/semanais/mensais/anuais**. Essa opção consumirá um pouco mais de armazenamento do que a primeira.
  - Por quanto tempo você espera manter os backups de “log”? (em dias) [7-35]
  - Por quanto tempo você espera manter os backups “Diários”? (em dias)
  - Por quanto tempo você espera manter os backups “Semanais”? (em semanas)
  - Por quanto tempo você espera manter os backups “Mensais”? (em meses)
  - Por quanto tempo você espera manter os backups “Anuais”? (em anos)
- **Opcional**: redundância do armazenamento de backup
  
  - Isso indica a redundância da conta de armazenamento na qual os dados de backup serão copiados. Recomendamos usar o **GRS** para obter a maior disponibilidade. Como ele garante que uma cópia dos seus dados de backup seja mantida em uma região diferente, ele ajuda a atender a vários padrões de conformidade. Altere a redundância para **LRS** se estiver fazendo backup de ambientes de desenvolvimento ou de teste que não precisem ter um backup de nível empresarial.
- **Opcional**: modificar os preços regionais ou aplicar taxas com desconto
  
  - Caso deseje verificar suas estimativas para outra região ou as taxas com desconto, selecione **Sim** na opção **Experimentar estimativas para outra região?** e insira as taxas com as quais deseja executar as estimativas.
  
## <a name="estimate-costs-for-backing-up-azure-file-shares"></a>Estimar os custos para fazer backup de compartilhamentos de arquivo do Azure

Para estimar os custos de fazer backup de compartilhamentos de arquivo do Azure usando a [solução de backup baseada em instantâneo](azure-file-share-backup-overview.md) oferecida pelo Backup do Azure, você precisará dos seguintes parâmetros:

- Tamanho (**em GB**) dos compartilhamentos de arquivo que você deseja fazer backup.

- Se você quiser fazer backup de compartilhamentos de arquivo distribuídos entre várias contas de armazenamento, especifique o número de contas de armazenamento que hospedam os compartilhamentos de arquivo com o tamanho indicado acima.

- Volume esperado de variação de dados nos compartilhamentos de arquivo que você deseja copiar em backup. <br>A variação refere-se ao volume de alterações nos dados e afeta diretamente o tamanho do armazenamento do instantâneo. Por exemplo, se você tiver um compartilhamento de arquivo com 200 GB de dados para backup e 10 GB desse volume forem alterados todos os dias, a variação diária será de 5%.
  - Uma variação mais alta significa que o volume de alteração de dados no conteúdo do compartilhamento de arquivo todos os dias é alto e, portanto, o instantâneo incremental (captura apenas das alterações de dados) também é maior.
  - Selecione Baixa (1%), Moderada (3%) ou Alta (5%) com base nas características e no uso do seu compartilhamento de arquivo.
  - Se você sabe o **% exato de variação** do compartilhamento de arquivo, selecione a opção **Inserir um % próprio** na lista suspensa. Especifique os valores (em %) para a variação diária, semanal, mensal e anual.

- Tipo de conta de armazenamento (Standard ou Premium) e a configuração de redundância de armazenamento da conta de armazenamento que hospeda o compartilhamento de arquivo copiado em backup. <br>Na solução de backup atual para compartilhamentos de arquivo do Azure, os instantâneos são armazenados na mesma conta de armazenamento do compartilhamento de arquivo copiado em backup. Portanto, o custo de armazenamento associado aos instantâneos é cobrado como parte da sua fatura dos arquivos do Azure, com base no preço do instantâneo para o tipo de conta e na configuração de redundância da conta de armazenamento que hospeda o compartilhamento de arquivo e os instantâneos copiados em backup.

- Retenção para diferentes backups
  - Por quanto tempo você espera manter os backups “Diários”? (em dias)
  - Por quanto tempo você espera manter os backups “Semanais”? (em semanas)
  - Por quanto tempo você espera manter os backups “Mensais”? (em meses)
  - Por quanto tempo você espera manter os backups “Anuais”? (em anos)

  Veja [a matriz de suporte do compartilhamento de arquivo do Azure](azure-file-share-support-matrix.md#retention-limits) para obter os valores máximos de retenção compatíveis em cada categoria.

- **Opcional**: modificar os preços regionais ou aplicar taxas com desconto.
  - Os valores padrão definidos para o custo do armazenamento de instantâneo por GB e o custo da instância protegida no avaliador referem-se à região Leste dos EUA. Caso deseje verificar suas estimativas para outra região ou as taxas com desconto, selecione **Sim** na opção **Experimentar estimativas para outra região?** e insira as taxas com as quais deseja executar as estimativas.

## <a name="next-steps"></a>Próximas etapas

[O que é o serviço de Backup do Azure?](backup-overview.md)
