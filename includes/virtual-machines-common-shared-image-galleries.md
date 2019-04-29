---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 01/09/2018
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 8c7da8d04b456642b158dda77d9c745891aa18e6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60620325"
---
Galeria de Imagens Compartilhadas é um serviço que ajuda você a criar a estrutura e a organização em torno de suas imagens de VM gerenciadas personalizadas. Usando uma Galeria de Imagens Compartilhadas, é possível compartilhar suas imagens com diferentes usuários, entidades de serviço ou grupos do AD dentro de sua organização. As imagens compartilhadas podem ser replicadas para várias regiões para dimensionar suas implantações mais rápido.

Uma imagem gerenciada é uma cópia de uma VM completa (incluindo quaisquer discos de dados anexados) ou apenas o disco do SO, dependendo de como você cria a imagem. Quando você cria uma VM a partir da imagem, uma cópia dos VHDs na imagem é usada para criar os discos da nova VM. A imagem gerenciada permanece no armazenamento e pode ser usada repetidamente para criar novas VMs.

Se você tem um grande número de imagens gerenciadas que precisa manter e gostaria de disponibilizá-las em toda a empresa, é possível usar uma Galeria de imagens compartilhadas como um repositório que facilita a atualização e o compartilhamento de imagens. As cobranças por usar uma Galeria de Imagens Compartilhada são apenas os custos para o armazenamento usado pelas imagens, além de custos de saída de rede para replicar imagens da região de origem para as regiões publicadas.

O recurso Galeria de Imagens Compartilhadas tem vários tipos de recursos:

| Resource | DESCRIÇÃO|
|----------|------------|
| **Imagem gerenciada** | Esta é uma imagem básica que pode ser usada sozinha ou para criar uma **versão da imagem** em uma galeria de imagens. Imagens gerenciadas são criadas de VMs generalizadas. Uma imagem gerenciada é um tipo especial de VHD que pode ser usado para criar várias VMs e agora pode ser usado para criar versões de imagem compartilhada. |
| **Galeria de imagens** | Como o Azure Marketplace, uma **galeria de imagens** é um repositório para gerenciar e compartilhar imagens, mas você controla quem tem acesso. |
| **Definição da imagem** | As imagens são definidas dentro de uma galeria e transportam informações sobre a imagem e os requisitos para usá-la internamente. Isso inclui se a imagem é Windows ou Linux, notas sobre a versão e requisitos mínimos e máximos de memória. É uma definição de um tipo de imagem. |
| **Versão da imagem** | Uma **versão da imagem** é usada para criar uma VM ao usar uma galeria. Você pode ter diversas versões de uma imagem conforme necessário para seu ambiente. Como uma imagem gerenciada, quando você usa uma **versão da imagem** para criar uma VM, a versão da imagem é usada para criar novos discos para a VM. Versões de imagem podem ser usadas várias vezes. |

<br>


![Gráfico mostrando como você pode ter várias versões de uma imagem na sua galeria](./media/shared-image-galleries/shared-image-gallery.png)

### <a name="regional-support"></a>Suporte regional

O suporte regional para galerias de imagens compartilhadas está na pré-visualização limitada, mas se expandirá com o tempo. Para a pré-visualização limitada, aqui está a lista de regiões onde você pode criar galerias e a lista de regiões onde você pode replicar qualquer imagem da galeria: 

| Criar Galeria em  | Replicar Versão para |
|--------------------|----------------------|
| Centro-Oeste dos EUA    |Todas as regiões públicas &#42;|
| Leste dos EUA 2          ||
| Centro-Sul dos Estados Unidos   ||
| Sudeste Asiático     ||
| Oeste da Europa        ||
| Oeste dos EUA            ||
| Leste dos EUA            ||
| Canadá Central     ||
|                    ||



&#42; Para replicar para Austrália Central e Austrália Central 2, é necessário incluir sua assinatura na lista de permissões. Para solicitar a inclusão na lista de permissões, acesse: https://www.microsoft.com/en-au/central-regions-eligibility/

## <a name="scaling"></a>Dimensionamento
A Galeria de Pesquisa de Imagem permite que você especifique o número de réplicas que você deseja que o Azure mantenha das imagens. Isso ajuda em cenários de implantação de várias VMs, já que as implantações de VM podem ser distribuídas para diferentes réplicas, reduzindo a chance de o processamento de criação de instância ser limitado devido à sobrecarga de uma única réplica.

![Gráfico mostrando como você pode dimensionar imagens](./media/shared-image-galleries/scaling.png)


## <a name="replication"></a>Replicação
A Galeria de Imagens Compartilhadas também permite replicar imagens para outras regiões do Azure automaticamente. Cada versão de imagem compartilhada pode ser replicada para diferentes regiões, dependendo do que faz sentido para sua organização. Um exemplo é sempre replicar a imagem mais recente em várias regiões, enquanto todas as versões mais antigas só estão disponíveis em uma região. Isso pode ajudar a economizar nos custos de armazenamento das versões de imagem compartilhada. 

As regiões para as quais uma versão de Imagem compartilhada é replicada podem ser atualizadas após o horário de criação. O tempo necessário para replicar em diferentes regiões depende da quantidade de dados copiados e do número de regiões para as quais a versão é replicada. Isso pode levar algumas horas em alguns casos. Enquanto a replicação está em andamento, você pode exibir o status da replicação por região. Depois que a replicação de imagem for concluída em uma região, você poderá implantar uma VM ou VMSS usando essa versão de imagem na região.

![Gráfico mostrando como você pode replicar imagens](./media/shared-image-galleries/replication.png)


## <a name="access"></a>Access
Como a versão da Galeria de Imagem Compartilhada, Imagem Compartilhada e Imagem Compartilhada são todos recursos, eles podem ser compartilhados usando os controles nativos do Azure RBAC. Usando RBAC, você pode compartilhar esses recursos com outros usuários, entidades de serviço e grupos em sua organização. O escopo do compartilhamento desses recursos está no mesmo locatário do Azure Active Directory. Depois que um usuário tiver acesso à versão da Imagem Compartilhada, ele poderá implantar uma VM ou um Conjunto de Escala de Máquina Virtual em qualquer uma das assinaturas às quais tenha acesso no mesmo locatário do Azure Active Directory que a versão de Imagem Compartilhada.  Aqui está a matriz de compartilhamento que ajuda a entender ao que o usuário obtém acesso:

| Compartilhado com o usuário     | Galeria de imagens compartilhadas | Imagem Compartilhada | Versão de imagem compartilhada |
|----------------------|----------------------|--------------|----------------------|
| Galeria de imagens compartilhadas | Sim                  | sim          | Sim                  |
| Imagem Compartilhada         | Não                    | sim          | Sim                  |
| Versão de imagem compartilhada | Não                    | Não            | Sim                  |



## <a name="billing"></a>Cobrança
Não há custo adicional para usar o serviço de Galeria de Imagens Compartilhadas. Você será cobrado pelos seguintes recursos:
- Custos de armazenamento do armazenamento das versões de imagem compartilhada. Depende do número de réplicas da versão e o número de regiões em para as quais a versão é replicada.
- Taxas de saída de rede para replicação da região de origem da versão para as regiões replicadas.

## <a name="sdk-support"></a>Suporte a SDK

Os seguintes SDKs dão suporte à criação de Galerias de Imagens Compartilhadas:

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/azure-arm-compute/?view=azure-node-latest)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Go](https://docs.microsoft.com/go/azure/)

## <a name="templates"></a>Modelos

Você pode criar um recurso de Galeria de Imagens Compartilhadas usando modelos. Há vários Modelos de Início Rápido do Azure disponíveis: 

- [Criar uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma Definição de Imagem em uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma Versão da Imagem em uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Criar uma VM por meio de uma Versão da Imagem](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Perguntas frequentes 

**P.** Como faço para me inscrever para a Versão Prévia Pública da Galeria de Imagens Compartilhadas?
 
 a. Para se inscrever na visualização pública da Galeria de Imagem Compartilhada, você precisa se registrar para o recurso executando os seguintes comandos de cada uma das assinaturas nas quais pretende criar uma Galeria compartilhada de imagens, uma definição de imagem ou recursos de versão da imagem, e também onde você pretende implantar máquinas virtuais usando as versões de imagem.

**CLI**: 

```bash 
az feature register --namespace Microsoft.Compute --name GalleryPreview
az provider register --name Microsoft.Compute
```

**PowerShell**: 

```powershell
Register-AzProviderFeature -FeatureName GalleryPreview -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

**P.** Como posso listar todos os recursos da Galeria de Imagens Compartilhadas entre assinaturas? 
 
 a. Para listar todos os recursos da Galeria de Imagens Compartilhadas entre assinaturas às quais você tem acesso no portal do Azure, siga as etapas abaixo:

1. Abra o [Portal do Azure](https://portal.azure.com).
1. Vá para **Todos os recursos**.
1. Selecione todas as assinaturas sob as quais você gostaria de listar todos os recursos.
1. Procure recursos do tipo **Galeria privada**.
 
   Para ver as definições de imagem e as versões da imagem, você também deve selecionar **Mostrar tipos ocultos**.
 
   Para listar todos os recursos da Galeria de Imagens Compartilhada entre assinaturas para as quais você tem permissões, use o seguinte comando na CLI do Azure:

   ```bash
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```


**P.** Como faço para compartilhar minhas imagens entre assinaturas?
 
 a. Você pode compartilhar imagens entre assinaturas usando o RBAC (Controle de Acesso Baseado em Função). Qualquer usuário que tenha permissões de leitura para uma versão de imagem, mesmo entre assinaturas, poderá implantar uma máquina virtual usando a versão da imagem.


**P.** Posso mover minha imagem existente para a galeria de imagens compartilhadas?
 
 a. Sim. Há três cenários com base nos tipos de imagens que você pode ter.

 Cenário 1: se você tiver uma imagem gerenciada, poderá criar uma definição de imagem e a versão da imagem usando essa definição.

 Cenário 2: se você tiver uma imagem generalizada não gerenciada, poderá criar uma imagem gerenciada com base nela e então criar uma definição de imagem e a versão da imagem com base nessa definição. 

 Cenário 3: se você tiver um VHD em seu sistema de arquivos local, precisará carregar o VHD, criar uma imagem gerenciada e então criar a definição da imagem e a versão da imagem com base nela.
- Se o VHD for de uma VM do Windows, veja [Carregar um VHD generalizado](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
- Se o VHD for para uma VM do Linux, veja [Carregar um VHD](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)


**P.** Posso criar uma versão da imagem de um disco especializado?

 a. Não, no momento não damos suporte para discos especializados como imagens. Se você tiver um disco especializado, será necessário [criar uma VM a partir do VHD](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal#create-a-vm-from-a-disk), anexando o disco especializado a uma nova VM. Depois de ter uma VM em execução, você precisa seguir as instruções para criar uma imagem gerenciada a partir da [VM do Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-custom-images) ou [Linux VM](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). Quando você tiver uma imagem gerenciada generalizada, poderá iniciar o processo para criar uma descrição da imagem compartilhada e a versão da imagem.


**P.** Posso criar uma galeria de imagens compartilhadas, definição de imagem e versão da imagem no portal do Azure?

 a. Não, no momento não damos suporte para a criação de nenhum dos recursos de Galeria de Imagens Compartilhadas por meio do portal do Azure. No entanto, nós oferecemos suporte para a criação de recursos da Galeria de Imagens Compartilhadas por meio da CLI, de Modelos e de SDKs. PowerShell também será lançado em breve.

 
**P.** Depois de criada, posso atualizar a definição da imagem ou a versão da imagem? Que tipo de detalhes posso modificar?

 a. Os detalhes que podem ser atualizados em cada um dos recursos são mencionados a seguir:
 
Galeria de imagens compartilhadas:
- DESCRIÇÃO

definição da imagem:
- vCPUs recomendadas
- Memória
- DESCRIÇÃO
- Data de fim da vida útil

Versão da imagem:
- Contagem de réplicas regionais
- Regiões de destino
- Exclusão da mais recente
- Data de fim da vida útil


**P.** Depois de criada, posso mover o recurso de Galeria de Imagens Compartilhadas para uma assinatura diferente?

 a. Não, não é possível mover o recurso de Galeria de Imagens Compartilhadas para uma assinatura diferente. No entanto, você poderá replicar as versões da imagem na galeria para outras regiões conforme necessário.

**P.** É possível replicar minhas versões de imagem entre nuvens – 21Vianet do Azure China, Azure Alemanha e Nuvem do Azure Governamental? 

 a. Não, você não pode replicar as versões de imagem entre nuvens.

**P.** Posso replicar minhas versões de imagem entre assinaturas? 

 a. Não, você não pode replicar as versões de imagem entre regiões em uma assinatura e usá-las em outras assinaturas por meio de RBAC.

**P.** Posso compartilhar versões de imagens entre os locatários do Azure AD? 

 a. Não, a galeria de imagens compartilhada atualmente não suporta o compartilhamento de versões de imagem entre os locatários do Azure Active Directory. No entanto, você pode usar o recurso de ofertas privadas no Azure Marketplace para fazer isso.


**P.** Quanto tempo leva para replicar as versões de imagem entre todas as regiões de destino?

 a. O tempo de replicação de versão de imagem depende inteiramente do tamanho da imagem e do número de regiões para as quais ela está sendo replicada. No entanto, como uma melhor prática, é recomendável manter a imagem pequena e as regiões de origem e destino perto para melhores resultados. Você pode verificar o status da replicação usando o sinalizador -ReplicationStatus.


**P.** Quantas galerias de imagens compartilhadas eu posso criar em uma assinatura?

 a. A cota padrão é: 
- 10 galerias de imagens compartilhada por assinatura por região
- 200 definições de imagem por assinatura por região
- 2.000 versões de imagem por assinatura por região


**P.** Qual é a diferença entre região de origem e região de destino?

 a. Região de origem é a região em que sua versão da imagem será criada e regiões de destino são as regiões em que uma cópia da sua versão da imagem será armazenada. Para cada versão de imagem, você pode ter apenas uma região de origem. Além disso, passe o local da região de origem como uma das regiões de destino ao criar uma versão da imagem.  


**P.** Como faço para especificar a região de origem ao criar a versão da imagem?

 a. Durante a criação de uma versão de imagem, você pode usar a marca **--location** na CLI e a marca **-Location** no PowerShell para especificar a região de origem. Verifique se a imagem gerenciada que você está usando como imagem base para criar a versão da imagem está no local em que você pretende criar a versão da imagem. Além disso, passe o local da região de origem como uma das regiões de destino ao criar uma versão da imagem.  


**P.** Como faço para especificar o número de réplicas de versão da imagem a serem criadas em cada região?

 a. Há duas maneiras de especificar o número de réplicas de versão da imagem a serem criadas em cada região:
 
1. A contagem de réplica regionais que especifica o número de réplicas que você deseja criar por região. 
2. A contagem de réplicas comuns, que é a contagem padrão por região caso a contagem de réplicas regionais não seja especificada. 

Para especificar a contagem de réplicas regionais, passe a localização junto com o número de réplicas que você deseja criar nessa região desta maneira: "Centro-Sul dos EUA=2". 

Se a contagem de réplicas regionais não for especificada com cada local, o número de réplicas padrão será a contagem de réplicas comuns que você especificou. 

Para especificar a contagem de réplicas comuns na CLI, use o argumento **--replica-count** no comando `az sig image-version create`.


**P.** Posso criar a Galeria de Imagens Compartilhadas em um local diferente daquele em que desejo criar a definição de imagem e a versão da imagem?

 a. Sim, isso é possível. Porém, como uma melhor prática, incentivamos manter o grupo de recursos, a Galeria de Imagens Compartilhadas, a definição da imagem e a versão da imagem no mesmo local.


**P.** Quais são os encargos para usar a Galeria de Imagens Compartilhadas?

 a. Não há encargos para usar o serviço Galeria de Imagens Compartilhadas, exceto pelos encargos de armazenamento para armazenar as versões de imagem e os encargos de saída de rede para replicar as versões de imagem da região de origem para as regiões de destino.

**P.** Que versão de API devo usar para criar a Galeria de Imagens Compartilhadas, a definição de imagem, a versão da imagem e a VM/VMSS com base na Versão da Imagem?

 a. Para implantações de conjunto de dimensionamento de máquina virtual e VM usando uma versão de imagem, recomendamos usar a versão da API 2018-04-01 ou superior. Para trabalhar com galerias de imagens compartilhadas, definições de imagem e versões de imagem, é recomendável usar a API versão 2018-06-01. 
