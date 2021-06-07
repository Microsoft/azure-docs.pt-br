---
title: Aplicar automaticamente rótulos de sensibilidade aos seus dados
description: Saiba como criar rótulos de sensibilidade e aplicá-los automaticamente aos seus dados durante uma verificação.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/09/2021
ms.openlocfilehash: 37ac292acc76c681ea38b2ae881ff8cd2ae5ec3c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102502439"
---
# <a name="automatically-label-your-data-in-azure-purview"></a>Rotular seus dados automaticamente no Azure alcance

Este artigo descreve como criar rótulos de sensibilidade de MIP (proteção de informações da Microsoft) e aplicá-los automaticamente aos seus ativos do Azure no Azure alcance.

## <a name="what-are-sensitivity-labels"></a>O que são rótulos de sensibilidade? 

Para realizar o trabalho, as pessoas em sua organização colaboram com outras pessoas dentro e fora da organização. Os dados nem sempre permanecem em sua nuvem e, muitas vezes, são móveis em todos os lugares, em dispositivos, aplicativos e serviços. 

Quando seus dados estiverem em roaming, você desejará fazê-lo em uma maneira segura e protegida que atenda às políticas de conformidade e de negócios da sua organização.

A aplicação de rótulos de sensibilidade permite que você declare a confidencialidade de determinados dados em sua organização. Por exemplo, um nome de projeto específico pode ser altamente confidencial em sua organização, enquanto esse mesmo termo não é confidencial para outras organizações. 

### <a name="sensitivity-labels-in-azure-purview"></a>Rótulos de sensibilidade no Azure alcance

No alcance, as classificações são semelhantes às marcas de assunto e são usadas para marcar e identificar dados de um tipo específico que é encontrado em seu estado de dados durante a verificação.

O alcance usa as mesmas classificações, também conhecidas como tipos de informações confidenciais, como Microsoft 365.  Os rótulos de sensibilidade MIP são criados no Microsoft 365 Security and Compliance Center (SCC). Isso permite que você estenda os rótulos de sensibilidade existentes nos ativos de alcance do Azure.

As **classificações** são correspondidas diretamente, como um número do seguro social, que tem uma classificação de número do seguro **social**. 

Por outro lado, os **Rótulos de sensibilidade** são aplicados quando uma ou mais classificações e condições são encontradas em conjunto. Nesse contexto, as [condições](/microsoft-365/compliance/apply-sensitivity-label-automatically) se referem a todos os parâmetros que você pode definir para dados não estruturados, como *proximidade a outra classificação* e *% de confiança*. 

Os rótulos de sensibilidade no Azure alcance podem ser usados para aplicar rótulos automaticamente a arquivos e colunas de banco de dados.

Para obter mais informações, consulte:

- [Saiba mais sobre rótulos de sensibilidade](/microsoft-365/compliance/sensitivity-labels) na documentação do Microsoft 365
- [O que são regras de rotulação automática?](#what-are-auto-labeling-rules)
- [Tipos de dados com suporte para rótulos de sensibilidade no Azure alcance](#supported-data-types-for-sensitivity-labels-in-azure-purview)
- [Rotulação de colunas do banco de dados SQL](#labeling-for-sql-database-columns)

#### <a name="what-are-auto-labeling-rules"></a>O que são regras de rotulação automática?

Seus dados estão constantemente crescendo e mudando. Controlar os dados que não estão rotulados no momento e tomar medidas para aplicar rótulos manualmente não é apenas um incômodo, mas também é uma dor de cabeça desnecessária. 

As regras de rotulação automática são condições que você especifica, informando quando um determinado rótulo deve ser aplicado. Quando essas condições são atendidas, o rótulo é atribuído automaticamente aos dados, retendo rótulos de sensibilidade consistentes em seus dados, em escala.

Ao criar seus rótulos, certifique-se de definir as regras de rotulagem automática para [arquivos](#define-auto-labeling-rules-for-files) e [colunas de banco de dados](#define-auto-labeling-rules-for-database-columns) para aplicar os rótulos automaticamente a cada verificação de dados. 

Depois de verificar os dados no alcance, você pode exibir os rótulos aplicados automaticamente no catálogo do alcance e nos relatórios de insights.
#### <a name="supported-data-types-for-sensitivity-labels-in-azure-purview"></a>Tipos de dados com suporte para rótulos de sensibilidade no Azure alcance

Os rótulos de sensibilidade têm suporte no Azure alcance para os seguintes tipos de dados:

|Tipo de dados  |Origens  |
|---------|---------|
|Rotulamento automático de arquivos     |      – Armazenamento de Blobs do Azure  </br>-Azure Data Lake Storage Gen 1 e Gen 2  |
|Rotulamento automático para colunas de banco de dados     |  -SQL Server </br>-Banco de dados SQL do Azure </br>-Instância Gerenciada do Banco de Dados SQL do Azure   <br> -Synapse do Azure  <br> – Azure Cosmos DB <br><br>Para obter mais informações, consulte [rotulando para colunas do banco de dados SQL](#labeling-for-sql-database-columns) abaixo.  |
| | |

#### <a name="labeling-for-sql-database-columns"></a>Rotulação de colunas do banco de dados SQL

Além de rotular alcance para colunas de banco de dados, a Microsoft também dá suporte a rótulos de colunas de banco de dados SQL usando a classificação de SQL data no [SQL Server Management Studio (SSMS)](/sql/ssms/sql-server-management-studio-ssms). Embora alcance use os [Rótulos de sensibilidade MIP](/microsoft-365/compliance/sensitivity-labels)globais, o SSMS usa apenas rótulos definidos localmente.

Rotular em alcance e rotular no SSMS são processos separados que atualmente não interagem entre si. Portanto, os rótulos aplicados no SSMS não são mostrados em alcance e vice-versa. Recomendamos o Azure alcance para rotular bancos de dados SQL, pois ele usa rótulos MIP globais que podem ser aplicados em várias plataformas.

Para obter mais informações, consulte a [documentação de classificação e descoberta de dados SQL](/sql/relational-databases/security/sql-data-discovery-and-classification).

## <a name="how-to-create-sensitivity-labels-in-microsoft-365"></a>Como criar rótulos de sensibilidade no Microsoft 365

Se você ainda não tiver rótulos de sensibilidade, será necessário criá-los e disponibilizá-los para o Azure alcance. Os rótulos de sensibilidade existentes também podem ser modificados para torná-los disponíveis para o Azure alcance.

Para obter mais informações, consulte:

- [Requisitos de licenciamento](#licensing-requirements)
- [Estendendo rótulos de sensibilidade para o Azure alcance](#extending-sensitivity-labels-to-azure-purview)
- [Criando novos rótulos de sensibilidade ou modificando rótulos existentes](#creating-new-sensitivity-labels-or-modifying-existing-labels)
### <a name="licensing-requirements"></a>Requisitos de licenciamento

Os rótulos de sensibilidade MIP são criados e gerenciados no centro de segurança e conformidade Microsoft 365. Para criar rótulos de sensibilidade para uso no Azure alcance, você deve ter uma licença do Active Microsoft 365 e5.

Se você ainda não tiver a licença necessária, poderá se inscrever para obter uma avaliação do [Microsoft 365 E5](https://www.microsoft.com/microsoft-365/business/compliance-solutions#midpagectaregion).

### <a name="extending-sensitivity-labels-to-azure-purview"></a>Estendendo rótulos de sensibilidade para o Azure alcance

Por padrão, os rótulos de sensibilidade MIP só estão disponíveis para ativos em Microsoft 365, onde você pode aplicá-los a arquivos e emails.

Para aplicar rótulos de sensibilidade MIP aos ativos do Azure no Azure alcance, você deve consentir explicitamente para estender os rótulos e selecionar os rótulos específicos que deseja disponibilizar no alcance.

Ao estender os rótulos de sensibilidade de MIP com o Azure alcance, as organizações agora podem descobrir, classificar e obter informações sobre a sensibilidade em uma variedade maior de fontes de dados, minimizando o risco de conformidade.

> [!NOTE]
> Como Microsoft 365 e alcance do Azure são serviços separados, há a possibilidade de que eles sejam implantados em regiões diferentes. Nomes de rótulo e nomes de tipos de informações confidenciais personalizadas são considerados dados do cliente e são mantidos na mesma localização geográfica por padrão para proteger a sensibilidade dos seus dados e para evitar leis GDPRs.
>
> Por esse motivo, os rótulos e os tipos de informações confidenciais personalizadas não são compartilhados no Azure alcance por padrão e exigem seu consentimento para usá-los no Azure alcance.

**Para estender os rótulos de sensibilidade para alcance:**

As etapas a seguir permitem que seus rótulos de sensibilidade estejam disponíveis para uso no Azure alcance, onde você pode aplicar seus rótulos de sensibilidade a ativos como colunas SQL, arquivos no armazenamento de BLOBs do Azure e muito mais.

1. Em Microsoft 365, navegue até a página **proteção de informações** . 
1. Em **estender o rótulo para ativos no Azure alcance**, selecione o botão **Ativar** e, em seguida, selecione **Sim** na caixa de diálogo de confirmação que aparece.

Por exemplo:

:::image type="content" source="media/create-sensitivity-label/extend-sensitivity-labels-to-purview-small.png" alt-text="Selecione * * Ativar * * para estender os rótulos de sensibilidade para alcance" lightbox="media/create-sensitivity-label/extend-sensitivity-labels-to-purview.png":::
 
Depois de estender o rótulo para ativos no Azure alcance, você pode selecionar os rótulos que deseja disponibilizar no alcance. Para obter mais informações, consulte [criando novos rótulos de sensibilidade ou modificando rótulos existentes](#creating-new-sensitivity-labels-or-modifying-existing-labels).
### <a name="creating-new-sensitivity-labels-or-modifying-existing-labels"></a>Criando novos rótulos de sensibilidade ou modificando rótulos existentes

Quando você usa rótulos de sensibilidade para aplicativos do Office no Windows, macOS, iOS e Android, os usuários veem novos rótulos dentro de quatro horas e dentro de uma hora para o Office na Web. No entanto, permita até 24 horas para que as alterações sejam replicadas para todos os aplicativos e serviços.

> [!IMPORTANT]
> Não exclua um rótulo, a menos que você compreenda o impacto para os usuários. Para obter mais informações, consulte [removendo e excluindo rótulos](/microsoft-365/compliance/create-sensitivity-labels#removing-and-deleting-labels) na documentação do Microsoft 365.
>

**Para criar novos rótulos de sensibilidade ou modificar rótulos existentes**:

1. Abra o [centro de conformidade e segurança do Microsoft 365](https://protection.office.com/homepage). 

1. Em **soluções**, selecione **proteção de informações** e, em seguida, selecione **criar um rótulo**. 

    :::image type="content" source="media/create-sensitivity-label/create-sensitivity-label-full-small.png" alt-text="Criar rótulos de sensibilidade no centro de conformidade e segurança Microsoft 365" lightbox="media/create-sensitivity-label/create-sensitivity-label-full.png":::

1. Nomeie o rótulo. Em seguida, em **definir o escopo para este rótulo**:

    - Em todos os casos, selecione **ativos de alcance do Azure**.
    - Para rotular arquivos, selecione também **arquivos & emails**. Essa opção não é necessária para rotular somente os ativos do banco de dados. 
    
    :::image type="content" source="media/create-sensitivity-label/create-label-scope-small.png" alt-text="Crie seu rótulo no centro de conformidade e segurança do Microsoft 365" lightbox="media/create-sensitivity-label/create-label-scope.png":::

1. Siga o restante dos prompts no Assistente para as configurações de rótulo. 

    Especificamente, defina regras de rotulamento automático para arquivos e colunas de banco de dados:

    - [Definir regras de rotulamento automático para arquivos](#define-auto-labeling-rules-for-files)
    - [Definir regras de rotulamento automático para colunas de banco de dados](#define-auto-labeling-rules-for-database-columns)

    Para obter mais informações sobre as opções do assistente, consulte [o que os rótulos de sensibilidade podem fazer](/microsoft-365/compliance/sensitivity-labels#what-sensitivity-labels-can-do) na documentação do Microsoft 365.

1. Repita as etapas listadas acima para criar mais rótulos. 

    Para criar um subrótulo, selecione o rótulo pai > **...**  >  **Mais ações**  >  **Adicionar subrótulo**.

1. Para modificar os rótulos existentes, navegue até rótulos de **proteção de informações**  >  e selecione seu rótulo. 

    Em seguida, selecione **Editar rótulo** para abrir o assistente **Editar rótulo de Sensibilidade** novamente, com todas as configurações que você definiu ao criar o rótulo.

    :::image type="content" source="media/create-sensitivity-label/edit-sensitivity-label-full-small.png" alt-text="Editar um rótulo de sensibilidade existente" lightbox="media/create-sensitivity-label/edit-sensitivity-label-full.png":::

1. Quando terminar de criar todos os seus rótulos, certifique-se de exibir sua ordem de etiqueta e reordená-los conforme necessário. 

    Para alterar a ordem de um rótulo, selecione **...** **> mais ações** se  >  **movem para cima** ou **para baixo.** 

    Para obter mais informações, consulte [prioridade de rótulo (ordem importante)](/microsoft-365/compliance/sensitivity-labels#label-priority-order-matters) na documentação do Microsoft 365.


Continue [examinando os dados para aplicar os rótulos automaticamente](#scan-your-data-to-apply-labels-automatically)e, em seguida:

- [Exibir rótulos em ativos](#view-labels-on-assets)
- [Exibir relatórios de insights para as classificações e rótulos de sensibilidade](#view-insight-reports-for-the-classifications-and-sensitivity-labels)

#### <a name="define-auto-labeling-rules-for-files"></a>Definir regras de rotulamento automático para arquivos

Defina regras de rotulamento automático para arquivos no assistente quando você criar ou editar seu rótulo. 

Na página **rotulação automática para aplicativos do Office** , habilite a **rotulagem automática para aplicativos do Office** e defina as condições em que você deseja que seu rótulo seja aplicado automaticamente aos seus dados.

Por exemplo:

:::image type="content" source="media/create-sensitivity-label/create-auto-labeling-rules-files-small.png" alt-text="Definir regras de rotulação automática para arquivos no centro de conformidade e segurança Microsoft 365" lightbox="media/create-sensitivity-label/create-auto-labeling-rules-files.png":::
 
Para obter mais informações, consulte [aplicar um rótulo de sensibilidade aos dados automaticamente](/microsoft-365/compliance/apply-sensitivity-label-automatically#how-to-configure-auto-labeling-for-office-apps) na documentação do Microsoft 365. 

#### <a name="define-auto-labeling-rules-for-database-columns"></a>Definir regras de rotulamento automático para colunas de banco de dados

Defina regras de rotulamento automático para colunas de banco de dados no assistente quando você criar ou editar seu rótulo. 

Na opção **ativos do alcance do Azure (versão prévia)** :

1. Selecione o controle deslizante de **rotulagem automática para colunas de banco de dados** .

1. Selecione **verificar tipos de informações confidenciais** para escolher os tipos de informações confidenciais que você deseja aplicar ao seu rótulo.

Por exemplo:
        
:::image type="content" source="media/create-sensitivity-label/create-auto-labeling-rules-db-columns-small.png" alt-text="Definir regras de rotulamento automático para colunas SQL no centro de conformidade e segurança Microsoft 365" lightbox="media/create-sensitivity-label/create-auto-labeling-rules-db-columns.png":::

## <a name="scan-your-data-to-apply-labels-automatically"></a>Examinar seus dados para aplicar rótulos automaticamente

Digitalize seus dados no Azure alcance para aplicar automaticamente os rótulos que você criou, com base nas regras de rotulagem automática que você definiu. 

Para obter mais informações sobre como configurar verificações em vários ativos no Azure alcance, consulte:

|Fonte  |Referência  |
|---------|---------|
|**Armazenamento de Blobs do Azure**     |[Registrar e verificar o armazenamento de BLOBs do Azure](register-scan-azure-blob-storage-source.md)         |
|**Armazenamento do Azure Data Lake**     |[Registrar e verificar Azure Data Lake Storage Gen1](register-scan-adls-gen1.md) </br>[Registrar e verificar Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)         |
|**Bancos de Dados SQL do Azure**|[Registrar e examinar um Banco de Dados SQL do Azure](register-scan-azure-sql-database.md) </br>[Registrar e examinar uma Instância Gerenciada do Banco de Dados SQL do Azure](register-scan-azure-sql-database-managed-instance.md)|
| | |

## <a name="view-labels-on-assets"></a>Exibir rótulos em ativos

Depois de definir as regras de rotulagem automática para seus rótulos no Microsoft 365 e examinar seus dados no Azure alcance, os rótulos serão aplicados automaticamente aos seus ativos. 

**Para exibir os rótulos aplicados aos seus ativos no catálogo alcance do Azure:**

No catálogo alcance do Azure, use as opções de filtragem de **rótulo** para mostrar somente os arquivos com rótulos específicos. Por exemplo: 

:::image type="content" source="media/create-sensitivity-label/filter-search-results-small.png" alt-text="Pesquisar ativos por rótulo" lightbox="media/create-sensitivity-label/filter-search-results.png":::

Por exemplo:

:::image type="content" source="media/create-sensitivity-label/view-labeled-files-blob-storage-small.png" alt-text="Exibir um rótulo de sensibilidade em um arquivo em seu armazenamento de BLOBs do Azure" lightbox="media/create-sensitivity-label/view-labeled-files-blob-storage.png":::

## <a name="view-insight-reports-for-the-classifications-and-sensitivity-labels"></a>Exibir relatórios de insights para as classificações e rótulos de sensibilidade

Encontre informações sobre seus dados classificados e rotulados no Azure alcance usando os relatórios de rótulos de **classificação** e **Sensibilidade** .

> [!div class="nextstepaction"]
> [Informações de classificação](./classification-insights.md)

> [!div class="nextstepaction"]
> [Informações de rótulo de sensibilidade](sensitivity-insights.md)
