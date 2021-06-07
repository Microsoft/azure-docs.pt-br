---
title: Solucionar problemas com imagens compartilhadas no Azure
description: Saiba como solucionar problemas com galerias de imagens compartilhadas.
author: olayemio
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 10/27/2020
ms.author: olayemio
ms.reviewer: cynthn
ms.openlocfilehash: 015fa201fe1c31dde2e30c2fe689ac13452b1b01
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607585"
---
# <a name="troubleshoot-shared-image-galleries-in-azure"></a>Solucionar problemas de galerias de imagens compartilhadas no Azure

Se você tiver problemas ao executar qualquer operação em galerias de imagens compartilhadas, definições de imagem e versões de imagem, execute o comando com falha novamente no modo de depuração. Você ativa o modo de depuração passando a `--debug` opção com o CLI do Azure e a `-Debug` opção com o PowerShell. Depois de localizar o erro, siga este artigo para solucionar o problema.


## <a name="creating-or-modifying-a-gallery"></a>Criando ou modificando uma galeria ##

**Mensagem**: o *nome da galeria é inválido. Os caracteres permitidos são caracteres alfanuméricos em inglês, com sublinhados e pontos permitidos no meio, até 80 caracteres no total. Todos os outros caracteres especiais, incluindo traços, não são permitidos.*  
**Causa**: o nome da Galeria não atende aos requisitos de nomenclatura.  
**Solução alternativa**: escolha um nome que atenda às seguintes condições: 
- Tem um limite de 80 caracteres
- Contém apenas letras em inglês, números, sublinhados e pontos
- Inicia e termina com letras ou números em inglês

**Mensagem**: *o nome da entidade ' galleryname ' é inválido de acordo com sua regra de validação: ^ [^ \_ \w] [\w-. \_ ] {0,79} (? <! [-.]) $.*  
**Causa**: o nome da Galeria não atende aos requisitos de nomenclatura.  
**Solução alternativa**: escolha um nome para a galeria que atenda às seguintes condições: 
- Tem um limite de 80 caracteres
- Contém apenas letras em inglês, números, sublinhados e pontos
- Inicia e termina com letras ou números em inglês

**Mensagem**: *o nome de recurso fornecido <galleryname \> tem estes caracteres à direita inválidos: <caractere \> . O nome não pode terminar com caracteres: <caractere \>*  
**Causa**: o nome da galeria termina com um ponto ou sublinhado.  
**Solução alternativa**: escolha um nome para a galeria que atenda às seguintes condições: 
- Tem um limite de 80 caracteres
- Contém apenas letras em inglês, números, sublinhados e pontos
- Inicia e termina com letras ou números em inglês

**Mensagem**: *a região <local fornecida \> não está disponível para o tipo de recurso ' Microsoft. Compute/galerias '. A lista de regiões disponíveis para o tipo de recurso é...*  
**Causa**: a região especificada para a galeria está incorreta ou requer uma solicitação de acesso.  
**Solução alternativa**: Verifique se o nome da região está correto. Se o nome da região estiver correto, envie [uma solicitação de acesso](/troubleshoot/azure/general/region-access-request-process) para a região.

**Mensagem**: *não é possível excluir o recurso antes de os recursos aninhados serem excluídos.*  
**Causa**: você tentou excluir uma galeria que contém pelo menos uma definição de imagem existente. Uma galeria deve estar vazia antes que possa ser excluída.  
**Solução alternativa**: exclua todas as definições de imagem dentro da galeria e, em seguida, continue a excluir a galeria. Se a definição de imagem contiver versões de imagem, você deverá excluir as versões da imagem antes de excluir as definições de imagem.

**Mensagem**: *o nome da Galeria ' <galleryname \> ' não é exclusivo na assinatura ' <subscriptionID> '. Escolha outro nome de galeria.*  
**Causa**: você tem uma galeria existente com o mesmo nome e tentou criar outra galeria com o mesmo nome.  
**Solução alternativa**: escolha um nome diferente para a galeria.

**Mensagem**: *o recurso <galleryname \> já existe no local <região \_ 1 \> no grupo de recursos <resourcegroup \> . Um recurso com o mesmo nome não pode ser criado no local <região \_ 2 \> . Selecione um novo nome de recurso.*  
**Causa**: você tem uma galeria existente com o mesmo nome e tentou criar outra galeria com o mesmo nome.  
**Solução alternativa**: escolha um nome diferente para a galeria.

## <a name="creating-or-modifying-image-definitions"></a>Criando ou modificando definições de imagem ##

**Mensagem**: *\> não é permitido alterar a propriedade ' galleryImage. Properties. <Property '.*  
**Causa**: você tentou alterar o tipo de sistema operacional, estado do sistema operacional, geração do Hyper-V, oferta, Publicador ou SKU. A alteração de qualquer uma dessas propriedades não é permitida.  
**Solução alternativa**: Crie uma nova definição de imagem em vez disso.

**Mensagem**: *o recurso <Gallery/imageDefinitionName \> já existe no local <região \_ 1 \> no grupo de recursos <resourcegroup \> . Um recurso com o mesmo nome não pode ser criado no local <região \_ 2 \> . Selecione um novo nome de recurso.*  
**Causa**: você tem uma definição de imagem existente na mesma galeria e no mesmo grupo de recursos com o mesmo nome. Você tentou criar outra definição de imagem com o mesmo nome e na mesma galeria, mas em uma região diferente.  
**Solução alternativa**: Use um nome diferente para a definição de imagem ou coloque a definição de imagem em uma galeria ou grupo de recursos diferente.

**Mensagem**: *o nome de recurso fornecido <galleryname \> /<imageDefinitionName \> tem estes caracteres inválidos: <caractere \> . O nome não pode terminar com caracteres: <caractere \>*  
**Causa**: o nome da <imageDefinitionName \> termina com um ponto ou sublinhado.  
**Solução alternativa**: escolha um nome para a definição de imagem que atenda às seguintes condições: 
- Tem um limite de 80 caracteres
- Contém apenas letras em inglês, números, sublinhados, hifens e pontos
- Inicia e termina com letras ou números em inglês.

**Mensagem**: *o nome da entidade <imageDefinitionName \> é inválido de acordo com sua regra de validação: ^ [^ \_ \\ W] [ \\ W-. \_ ] {0,79} (? <! [-.]) $"*  
**Causa**: o nome da <imageDefinitionName \> termina com um ponto ou sublinhado.  
**Solução alternativa**: escolha um nome para a definição de imagem que atenda às seguintes condições: 
- Tem um limite de 80 caracteres
- Contém apenas letras em inglês, números, sublinhados, hifens e pontos
- Inicia e termina com letras ou números em inglês

**Mensagem**: o *nome do ativo galleryImage. Properties. ID. <propriedade \> não é válido. Ele não pode ficar vazio. Os caracteres permitidos são letras maiúsculas ou minúsculas, dígitos, hífen (-), ponto final (.), sublinhado ( \_ ). Os nomes não têm permissão para terminar com o ponto (.). O comprimento do nome não pode exceder <\> caracteres numéricos.*  
**Causa**: o valor de Publicador, oferta ou SKU não atende aos requisitos de nomenclatura.  
**Solução alternativa**: escolha um valor que atenda às seguintes condições: 
- Tem um limite de 128 caracteres para o fornecedor ou o limite de 64 caracteres para oferta e SKU
- Contém apenas letras em inglês, números, hifens, sublinhados e pontos
- Não termina com um ponto

**Mensagem**: *não é possível executar a operação solicitada no recurso aninhado. O recurso pai <galleryname \> não foi encontrado.*  
**Causa**: não há uma galeria com o nome <galleryname \> na assinatura atual e no grupo de recursos.  
**Solução alternativa**: Verifique se os nomes da galeria, da assinatura e do grupo de recursos estão corretos. Caso contrário, crie uma nova galeria chamada <galleryname \> .

**Mensagem**: *a região <local fornecida \> não está disponível para o tipo de recurso ' Microsoft. Compute/galerias '. A lista de regiões disponíveis para o tipo de recurso é...*  
**Causa**: o nome da região de <\> está incorreto ou requer uma solicitação de acesso.  
**Solução alternativa**: Verifique se o nome da região está grafado corretamente. Você pode executar esse comando para ver as regiões às quais você tem acesso. Se a região não estiver na lista, envie [uma solicitação de acesso](/troubleshoot/azure/general/region-access-request-process).

**Mensagem**: *não é possível serializar o valor: <valor \> como tipo: ' ISO-8601 '., ISO8601Error: ISO 8601 time Designator ' T' ausente. Não é possível analisar a cadeia de \> caracteres datetime <valor*  
**Causa**: o valor fornecido à propriedade não está formatado corretamente como uma data.  
**Solução alternativa**: forneça uma data no formato aaaa-mm-dd, yyyy-mm-dd'T'HH: mm: Sszzz ou [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-válido.

**Mensagem**: *não foi possível converter a cadeia de caracteres em DateTimeOffset: <valor \> . Caminho ' Properties. <property \> '*  
**Causa**: o valor fornecido à propriedade não está formatado corretamente como uma data.  
**Solução alternativa**: forneça uma data no formato aaaa-mm-dd, yyyy-mm-dd'T'HH: mm: Sszzz ou [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-válido.

**Mensagem**: *EndOfLifeDate deve ser definido como uma data futura.*  
**Causa**: a propriedade de data de fim da vida útil não está formatada corretamente como uma data após a data de hoje.  
**Solução alternativa**: forneça uma data no formato aaaa-mm-dd, yyyy-mm-dd'T'HH: mm: Sszzz ou [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-válido.

**Mensagem**: *argumento--<propriedade \> : valor int inválido: valor \> de <*  
**Causa**: <propriedade \> aceita apenas valores inteiros e <valor \> não é um inteiro.  
**Solução alternativa**: escolha um valor inteiro.

**Mensagem**: *o valor mínimo da propriedade <\> não deve ser maior que o valor máximo da propriedade <\> .*  
**Causa**: o valor mínimo fornecido para <propriedade \> é maior que o valor máximo fornecido para <propriedade \> .  
**Solução alternativa**: altere os valores para que o mínimo seja menor ou igual ao máximo.

**Mensagem**: *imagem da Galeria: <imageDefinitionName \> identificados por (Publicador: <publisher \> , oferta: <oferta \> , SKU: <SKU \> ) já existe. Escolha uma combinação de Publicador, oferta, SKU diferente.*  
**Causa**: você tentou criar uma nova definição de imagem com o mesmo editor, oferta e SKU terceto como uma definição de imagem existente na mesma galeria.  
**Solução alternativa**: em uma galeria, todas as definições de imagem devem ter uma combinação exclusiva de Publicador, oferta e SKU. Escolha uma combinação exclusiva ou escolha uma nova galeria e crie a definição de imagem novamente.

**Mensagem**: *não é possível excluir o recurso antes de os recursos aninhados serem excluídos.*  
**Causa**: você tentou excluir uma definição de imagem que contém versões de imagem. Uma definição de imagem deve estar vazia antes que possa ser excluída.  
**Solução alternativa**: exclua todas as versões de imagem dentro da definição de imagem e, em seguida, prossiga para excluir a definição de imagem.

**Mensagem**: *não é possível associar o parâmetro <propriedade \> . Não é possível converter valor <valor \> para o tipo <PropertyType \> . Não é possível corresponder o valor do identificador <\> ao nome de um enumerador válido. Especifique um dos seguintes nomes de enumerador e tente novamente: <opção \_ 1 \> , <opção \_ 2 \> ,...*  
**Causa**: a propriedade tem uma lista restrita de valores possíveis e <valor \> não é um deles.  
**Solução alternativa**: escolha um dos possíveis valores de <opção \> .

**Mensagem**: *não é possível associar o parâmetro <propriedade \> . Não é possível converter valor <valor \> para o tipo &quot; System. DateTime &quot;*  
**Causa**: o valor fornecido à propriedade não está formatado corretamente como uma data.  
**Solução alternativa**: forneça uma data no formato aaaa-mm-dd, yyyy-mm-dd'T'HH: mm: Sszzz ou [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-válido.

**Mensagem**: *não é possível associar o parâmetro <propriedade \> . Não é possível converter valor <valor \> para o tipo &quot; System. Int32 &quot;*  
**Causa**: <propriedade \> aceita apenas valores inteiros e <valor \> não é um inteiro.  
**Solução alternativa**: escolha um valor inteiro.

**Mensagem**: *não há suporte para o tipo de conta de armazenamento ZRS nesta região.*  
**Causa**: você escolheu o ZRS (armazenamento com redundância de zona padrão) em uma região que ainda não oferece suporte a ele.  
**Solução alternativa**: altere o tipo de conta de armazenamento para **Premium \_ LRS** ou **Standard \_ LRS**. Verifique nossa documentação para obter a [lista mais recente de regiões](../storage/common/storage-redundancy.md#zone-redundant-storage) com a visualização de ZRS habilitada.

## <a name="creating-or-updating-image-versions"></a>Criando ou atualizando versões de imagem ##

**Mensagem**: *a região <local fornecida \> não está disponível para o tipo de recurso ' Microsoft. Compute/galerias '. A lista de regiões disponíveis para o tipo de recurso é...*  
**Causa**: o nome da região de <\> está incorreto ou requer uma solicitação de acesso.  
**Solução alternativa**: Verifique se o nome da região está grafado corretamente. Você pode executar esse comando para ver as regiões às quais você tem acesso. Se a região não estiver na lista, envie [uma solicitação de acesso](/troubleshoot/azure/general/region-access-request-process).

**Mensagem**: *não é possível executar a operação solicitada no recurso aninhado. O recurso pai <Gallery/imageDefinitionName \> não foi encontrado.*  
**Causa**: não há uma galeria com o nome <Gallery/imageDefinitionName \> na assinatura atual e no grupo de recursos.  
**Solução alternativa**: Verifique se os nomes da galeria, da assinatura e do grupo de recursos estão corretos. Caso contrário, crie uma nova galeria com o nome <Gallery \> e/ou uma definição de imagem chamada <imageDefinitionName \> no grupo de recursos indicado.

**Mensagem**: *não é possível associar o parâmetro <propriedade \> . Não é possível converter valor <valor \> para o tipo &quot; System. DateTime &quot;*  
**Causa**: o valor fornecido à propriedade não está formatado corretamente como uma data.  
**Solução alternativa**: forneça uma data no formato aaaa-mm-dd, yyyy-mm-dd'T'HH: mm: Sszzz ou [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-válido.

**Mensagem**: *não é possível associar o parâmetro <propriedade \> . Não é possível converter valor <valor \> para o tipo &quot; System. Int32 &quot;*  
**Causa**: <propriedade \> aceita apenas valores inteiros e <valor \> não é um inteiro.  
**Solução alternativa**: escolha um valor inteiro.

**Mensagem**: *Galeria de imagens versão de imagem regiões do perfil de publicação <publishingRegions \> deve conter o local \> da versão da imagem <sourceRegion*  
**Causa**: o local da imagem de origem (<sourceRegion \> ) deve ser incluído na lista de <publishingRegions \> .  
**Solução alternativa**: inclua <sourceRegion \> na lista <publishingRegions \> .

**Message**: *o valor <valor \> do parâmetro <propriedade \> está fora do intervalo. O valor deve estar entre <minValue \> e <MaxValue \> , inclusive.*  
**Causa**: <valor \> está fora do intervalo de valores possíveis para a propriedade <\> .  
**Solução alternativa**: escolha um valor que esteja dentro do intervalo de <minValue \> e <MaxValue \> , inclusive.

**Mensagem**: a *resourceid de <de origem \> não foi encontrada. Verifique se a origem existe e se está na mesma região que a versão da imagem da galeria que está sendo criada.*  
**Causa**: não há fonte localizada em <ResourceId \> ou a origem em <ResourceId \> não está na mesma região que a imagem da galeria que está sendo criada.  
**Solução alternativa**: Verifique se o valor <ResourceId \> está correto e se a região de origem da versão da imagem da galeria é a mesma que a região do valor <ResourceId \> .

**Mensagem**: *\> não é permitido alterar a propriedade ' GalleryImageVersion. properties. storageProfile. <. Source.ID '* .  
**Causa**: a ID de origem de uma versão de imagem da Galeria não pode ser alterada após a criação.  
**Solução alternativa**: Verifique se a ID de origem é igual à ID de origem existente, altere o número da versão da imagem ou exclua a versão da imagem atual e tente novamente.

**Mensagem**: foram *detectados números de LUN duplicados nos discos de dados de entrada. O número de LUN deve ser exclusivo para cada disco de dados.*  
**Causa**: quando você está criando uma versão de imagem usando uma lista de discos e/ou instantâneos de disco, dois ou mais discos ou instantâneos de disco têm o mesmo LUN.  
**Solução alternativa**: remova ou altere quaisquer LUNs duplicados.

**Mensagem**: *as IDs de origem duplicadas são encontradas nos discos de entrada. A ID de origem deve ser exclusiva para cada disco.*  
**Causa**: quando você está criando uma versão de imagem usando uma lista de discos e/ou instantâneos de disco, dois ou mais discos ou instantâneos de disco têm a mesma ID de recurso.  
**Solução alternativa**: remova ou altere quaisquer IDs de origem de disco duplicadas.

**Mensagem**: *a id da propriedade <ResourceId \> no caminho ' properties. storageProfile. <diskImages \> . Source.ID ' é inválido. Espere uma ID de recurso totalmente qualificada que comece com '/subscriptions/ <subscriptionID> ' ou '/Providers/ <resourceProviderNamespace> /'.*  
**Causa**: o valor de resourceid <\> está formatado incorretamente.  
**Solução alternativa**: Verifique se a ID do recurso está correta.

**Mensagem**: *a ID da origem: <ResourceId \> deve ser uma imagem gerenciada, uma máquina virtual ou outra versão da imagem da Galeria*  
**Causa**: o valor de resourceid <\> está formatado incorretamente.  
**Solução alternativa**: se você estiver usando uma VM, imagem gerenciada ou versão de imagem da galeria como a imagem de origem, verifique se a ID de recurso da VM, imagem gerenciada ou versão da imagem da galeria está correta.

**Mensagem**: *a ID de origem: <ResourceId \> deve ser um disco gerenciado ou um instantâneo.*  
**Causa**: o valor de resourceid <\> está formatado incorretamente.  
**Solução alternativa**: se você estiver usando discos e/ou instantâneos de disco como fontes para a versão da imagem, verifique se as IDs de recurso dos discos e/ou instantâneos do disco estão corretos.

**Mensagem**: *não é possível criar a versão da imagem da galeria de: <ResourceId \> , pois o estado do sistema operacional na imagem da Galeria pai (<OsState \_ 1 \> ) não é <OsState \_ 2 \> .*  
**Causa**: o estado do sistema operacional (generalizado ou especializado) não corresponde ao estado do sistema operacional especificado na definição de imagem.  
**Solução alternativa**: escolha uma fonte com base em uma VM com o estado do sistema operacional de <OsState \_ 1 \> ou crie uma nova definição de imagem para VMs com base em <OsState \_ 2 \> .

**Mensagem**: *o recurso com a ID ' <ResourceId \> ' tem uma geração de hipervisor diferente [' <V # \_ 1 \> '] que a geração de hipervisor de imagem da Galeria pai [' <V # \_ 2 \> ']*  
**Causa**: a geração de hipervisor da versão de imagem não corresponde à geração de hipervisor especificada na definição de imagem. O sistema operacional de definição de imagem é <V # \_ 1 \> e o sistema operacional da versão da imagem é <v # \_ 2 \> .  
**Solução alternativa**: escolha uma fonte com a mesma geração de hipervisor que a definição de imagem ou crie/escolha uma nova definição de imagem que tenha a mesma geração de hipervisor que a versão da imagem.

**Mensagem**: *o recurso com a ID ' <ResourceId \> ' tem um tipo de sistema operacional diferente [' <OsType \_ 1 \> '] que a geração de tipo de sistema operacional de imagem da Galeria pai [' <OsType \_ 2 \> ']*  
**Causa**: a geração de hipervisor da versão de imagem não corresponde à geração de hipervisor especificada na definição de imagem. O sistema operacional de definição de imagem é <OsType \_ 1 \> e o sistema operacional da versão da imagem é <OsType \_ 2 \> .  
**Solução alternativa**: escolha uma fonte com o mesmo sistema operacional (Linux/Windows) como a definição de imagem ou crie/escolha uma nova definição de imagem que tenha a mesma geração de sistema operacional que a versão da imagem.

**Mensagem**: *a máquina virtual de origem <ResourceId \> não pode conter um disco do sistema operacional efêmero.*  
**Causa**: a origem em <ResourceId \> contém um disco do sistema operacional efêmero. Atualmente, a Galeria de imagens compartilhadas não oferece suporte a discos do sistema operacional efêmero.  
**Solução alternativa**: escolha uma fonte diferente com base em uma VM que não usa um disco do sistema operacional efêmero.

**Mensagem**: a *máquina virtual de origem <ResourceId \> não pode conter o disco [' <DiskId \> '] armazenado em um tipo de conta UltraSSD.*  
**Causa**: o disco <DiskId \> é um disco SSD ultra. Atualmente, a Galeria de imagens compartilhadas não oferece suporte a discos SSD Ultra.  
**Solução alternativa**: Use uma fonte que contenha somente SSD Premium, SSD Standard e/ou HDD Standard Managed disks.

**Mensagem**: a *ResourceId da máquina virtual de origem <\> deve ser criada a partir de Managed disks.*  
**Causa**: a máquina virtual no <ResourceId \> usa discos não gerenciados.  
**Solução alternativa**: Use uma fonte baseada em uma VM que contenha somente SSD Premium, SSD Standard e/ou HDD Standard Managed disks.

**Mensagem**: *muitas solicitações na origem ' <ResourceId \> '. Reduza o número de solicitações na origem ou aguarde algum tempo antes de tentar novamente.*  
**Causa**: a origem desta versão de imagem está sendo limitada no momento devido a muitas solicitações.  
**Solução alternativa**: tente criar a versão da imagem mais tarde.

**Mensagem**: *o conjunto de criptografia de disco ' <diskEncryptionSetID \> ' deve estar na mesma assinatura ' <SubscriptionId \> ' que o recurso da galeria.*  
**Causa**: os conjuntos de criptografia de disco podem ser usados somente na mesma assinatura e região em que foram criados.  
**Solução alternativa**: crie ou use um conjunto de criptografia na mesma assinatura e região que a versão da imagem.

**Mensagem**: *fonte criptografada: ' <ResourceId \> ' está em uma ID de assinatura diferente da assinatura de versão de imagem da Galeria atual ' <SubscriptionId \_ 1 \> '. Tente novamente com uma ou mais fontes não criptografadas ou use a assinatura da origem ' <subassinaturaid \_ 2 \> ' para criar a versão da imagem da galeria.*  
**Causa**: a Galeria de imagens compartilhadas atualmente não oferece suporte à criação de versões de imagem em outra assinatura de outra imagem de origem se a imagem de origem estiver criptografada.  
**Solução alternativa**: Use uma fonte não criptografada ou crie a versão da imagem na mesma assinatura que a origem.

**Mensagem**: *o conjunto de criptografia de disco <diskEncryptionSetID \> não foi encontrado.*  
**Causa**: a criptografia de disco pode estar incorreta.  
**Solução alternativa**: Verifique se a ID de recurso do conjunto de criptografia de disco está correta.

**Mensagem**: *o nome da versão da imagem é inválido. O nome da versão da imagem deve seguir a principal (int). Secundária (int). Formato de patch (int), por exemplo: 1.0.0, 2018.12.1 etc.*  
**Causa**: o formato válido para uma versão de imagem é de três inteiros separados por um ponto. O nome da versão da imagem não atendeu ao formato válido.  
**Solução alternativa**: Use um nome de versão de imagem que segue o formato Major (int). Secundária (int). Patch (int). Por exemplo: 1.0.0. ou 2018.12.1.

**Mensagem**: *o valor do parâmetro galleryArtifactVersion. Properties. publishingProfile. targetRegions. encryption. dataDiskImages. diskEncryptionSetId é inválido*  
**Causa**: a ID de recurso do conjunto de criptografia de disco usada em uma imagem de disco de dados usa um formato inválido.  
**Solução alternativa**: Verifique se a ID de recurso do conjunto de criptografia de disco segue o formato/subscriptions/<SubscriptionId \> /ResourceGroups/<ResourceGroupName \> /Providers/Microsoft.Compute/<diskEncryptionSetName \> .

**Mensagem**: *o valor do parâmetro galleryArtifactVersion. Properties. publishingProfile. targetRegions. encryption. osDiskImage. diskEncryptionSetId é inválido.*  
**Causa**: a ID de recurso do conjunto de criptografia de disco usada na imagem de disco do sistema operacional usa um formato inválido.  
**Solução alternativa**: Verifique se a ID de recurso do conjunto de criptografia de disco segue o formato/subscriptions/<SubscriptionId \> /ResourceGroups/<ResourceGroupName \> /Providers/Microsoft.Compute/<diskEncryptionSetName \> .

**Mensagem**: *não é possível especificar nova data de criptografia de imagem de disco de dados [número <\> ] com um conjunto de criptografia de disco na região [região \> de <] para a solicitação atualizar versão da imagem da galeria. Para atualizar essa versão, remova o novo LUN. Se precisar alterar as configurações de criptografia de imagem de disco de dados, você deverá criar uma nova versão de imagem da galeria com as configurações corretas.*  
**Causa**: você adicionou criptografia ao disco de dados de uma versão de imagem existente. Você não pode adicionar criptografia a uma versão de imagem existente.  
**Solução alternativa**: Crie uma nova versão de imagem da galeria ou remova as configurações de criptografia adicionadas.

**Mensagem**: *a origem da versão do artefato da galeria só pode ser especificada diretamente sob storageProfile ou dentro de um sistema operacional ou discos de dados individuais. Apenas um tipo de origem (imagem do usuário, instantâneo, disco, máquina virtual) pode ser fornecido.*  
**Causa**: a ID de origem está ausente.  
**Solução alternativa**: Verifique se a ID de origem da origem está presente.

**Mensagem**: *fonte não encontrada: <ResourceId \> . Certifique-se de que a origem exista.*  
**Causa**: a ID de recurso da origem pode estar incorreta.  
**Solução alternativa**: Verifique se a ID de recurso da origem está correta.

**Mensagem**: *um conjunto de criptografia de disco é necessário para o disco ' galleryArtifactVersion. Properties. publishingProfile. targetRegions. encryption. osDiskImage. diskEncryptionSetId ' na região de destino ' <região \_ 1 \> ', pois o conjunto de criptografia de disco ' <diskEncryptionSetId \> ' é usado para o disco correspondente na região ' <região \_ 2 \> '*  
**Causa**: a criptografia foi usada no disco do sistema operacional na região do <\_ 2 \> , mas não na <região \_ 1 \> .  
**Solução alternativa**: se você estiver usando a criptografia no disco do sistema operacional, use a criptografia em todas as regiões.

**Mensagem**: *um conjunto de criptografia de disco é necessário para o disco ' LUN <Number \> ' na região de destino ' <Region \_ 1 \> ', pois o conjunto de criptografia de disco ' <diskEncryptionSetID \> ' é usado para o disco correspondente na região ' <Region \_ 2 \> '*  
**Causa**: a criptografia foi usada no disco de dados no LUN <número \> na região <\_ 2 \> , mas não na <região \_ 1 \> .  
**Solução alternativa**: se você estiver usando a criptografia em um disco de dados, use a criptografia em todas as regiões.

**Mensagem**: *um lun [número <\> ] inválido foi especificado em Encryption. dataDiskImages. O LUN deve ser um dos seguintes valores [' 0, 9 '].*  
**Causa**: o LUN especificado para a criptografia não corresponde a nenhum dos LUNs para discos anexados à VM.  
**Solução alternativa**: altere o LUN na criptografia para o LUN de um disco de dados presente na VM.

**Mensagem**: os *LUNs duplicados ' número de <\> ' foram especificados na região de destino ' <região \> ' Encryption. dataDiskImages.*  
**Causa**: as configurações de criptografia usadas em <região \> especificou um LUN pelo menos duas vezes.  
**Solução alternativa**: altere o LUN na região <\> para garantir que todos os LUNs sejam exclusivos na região <\> .

**Mensagem**: *OSDiskImage e DataDiskImage não podem apontar para o mesmo blob <\> SourceID*  
**Causa**: as origens do disco do sistema operacional e de pelo menos um disco de dados não são exclusivas.  
**Solução alternativa**: altere a origem do disco do sistema operacional e/ou dos discos de dados para garantir que o disco do sistema operacional, bem como cada disco de dados seja exclusivo.

**Mensagem**: *regiões duplicadas não são permitidas em regiões de publicação de destino.*  
**Causa**: uma região é listada entre as regiões de publicação mais de uma vez.  
**Solução alternativa**: Remova a região duplicada.

**Mensagem**: *não é permitido adicionar novos discos de dados ou alterar o LUN de um disco de dados em uma imagem existente.*  
**Causa**: uma chamada de atualização para a versão da imagem contém um novo disco de dados ou tem um novo LUN para um disco.  
**Solução alternativa**: Use os LUNs e discos de dados da versão da imagem existente.

**Mensagem**: *o conjunto de criptografia de disco <diskEncryptionSetID \> deve estar na mesma assinatura <SubscriptionId \> que o recurso da galeria.*  
**Causa**: atualmente, a Galeria de imagens compartilhadas não oferece suporte ao uso de uma criptografia de disco definida em uma assinatura diferente.  
**Solução alternativa**: Crie a versão da imagem e o conjunto de criptografia de disco na mesma assinatura.

**Mensagem**: *falha na replicação nesta região devido a ' o tamanho do recurso de origem GalleryImageVersion 2048 excede o tamanho máximo 1024 com suporte. '*  
**Causa**: um disco de dados na origem é maior que 1 TB.  
**Solução alternativa**: Redimensione o disco de dados para menos de 1 TB.

## <a name="creating-or-updating-a-vm-or-scale-sets-from-an-image-version"></a>Criando ou atualizando uma VM ou conjuntos de dimensionamento de uma versão de imagem ##

**Mensagem**: não *existe nenhuma versão da imagem mais recente para "<imageDefinitionResourceID \> "*  
**Causa**: a definição de imagem usada para implantar a máquina virtual não contém nenhuma versão de imagem incluída na versão mais recente.  
**Solução alternativa**: Verifique se há pelo menos uma versão de imagem que tenha ' excluir do mais recente ' definida como false. 

**Mensagem**: *o cliente tem permissão para executar a ação ' Microsoft. Compute/galerias/images/Versions/Read ' no escopo <ResourceId \> , no entanto, o locatário atual <tenantid \> não está autorizado a acessar a assinatura vinculada <SubscriptionId \> .*  
**Causa**: a máquina virtual ou o conjunto de dimensionamento foi criado por meio de uma imagem SIG em outro locatário. Você tentou fazer uma alteração na máquina virtual ou no conjunto de dimensionamento, mas não tem acesso à assinatura que possui a imagem.  
**Solução alternativa**: entre em contato com o proprietário da assinatura da versão da imagem para conceder acesso de leitura à versão da imagem.

**Mensagem**: *a imagem da Galeria <ResourceId \> não está disponível na região da região <\> . Contate o proprietário da imagem para replicar nessa região ou altere a região solicitada.*  
**Causa**: a VM está sendo criada em uma região que não está entre a lista de regiões publicadas para a imagem da galeria.  
**Solução alternativa**: replique a imagem para a região ou crie uma VM em uma das regiões nas regiões de publicação da imagem da galeria.

**Mensagem**: o *parâmetro ' osProfile ' não é permitido.*  
**Causa**: o nome de usuário do administrador, a senha ou as chaves SSH foram fornecidas para uma VM que foi criada a partir de uma versão de imagem especializada.  
**Solução alternativa**: não inclua o nome de usuário do administrador, a senha ou as chaves SSH se você pretende criar uma VM a partir dessa imagem. Caso contrário, use uma versão de imagem generalizada e forneça o nome de usuário, a senha ou as chaves SSH do administrador.

**Mensagem**: o *parâmetro ' osProfile ' necessário está ausente (nulo).*  
**Causa**: a VM é criada a partir de uma imagem generalizada e falta o nome de usuário, a senha ou as chaves SSH do administrador. Como as imagens generalizadas não retêm o nome de usuário, a senha ou as chaves SSH do administrador, esses campos devem ser especificados durante a criação de uma VM ou um conjunto de dimensionamento.  
**Solução alternativa**: especifique o nome de usuário do administrador, a senha ou as chaves SSH ou use uma versão de imagem especializada.

**Mensagem**: *não é possível criar a versão da imagem da galeria de: <ResourceId \> , pois o estado do sistema operacional na imagem da Galeria pai (' especializada ') não é ' generalizado '.*  
**Causa**: a versão da imagem é criada a partir de uma fonte generalizada, mas sua definição pai é especializada.  
**Solução alternativa**: Crie a versão da imagem usando uma fonte especializada ou use uma definição pai que seja generalizada.

**Mensagem**: *não é possível atualizar o conjunto de dimensionamento de máquinas virtuais <vmssName \> como o estado atual do sistema operacional do conjunto de dimensionamento de VM é generalizado, o que é diferente do estado atualizado do sistema operacional da imagem da galeria, que é especializado.*  
**Causa**: a imagem de origem atual do conjunto de dimensionamento é uma imagem de origem generalizada, mas está sendo atualizada com uma imagem de origem especializada. A imagem de origem atual e a nova imagem de origem para um conjunto de dimensionamento devem ser do mesmo estado.  
**Solução alternativa**: para atualizar o conjunto de dimensionamento, use uma versão de imagem generalizada.

**Mensagem**: o *conjunto de criptografia de disco <diskEncryptionSetID \> na Galeria de imagens compartilhadas <VersionId \> pertence à assinatura <SubscriptionId \_ 1 \> e não pode ser usado com o recurso ' ' na assinatura <SubscriptionId \_ 2 \>*  
**Causa**: o conjunto de criptografia de disco usado para criptografar a versão da imagem reside em uma assinatura diferente da assinatura para hospedar a versão da imagem.  
**Solução alternativa**: Use a mesma assinatura para a versão de imagem e o conjunto de criptografia de disco.

**Mensagem**: *a criação da VM ou do conjunto de dimensionamento de máquinas virtuais leva muito tempo.*  
**Solução alternativa**: Verifique se o **OSType** da versão da imagem da qual você está tentando criar a VM ou o conjunto de dimensionamento de máquinas virtuais tem o mesmo **OSType** da fonte que você usou para criar a versão da imagem. 

**Mensagem**: *o recurso com ID <vmID \> tem um plano diferente [' { \" name \" : \" <name> \" , \" Publisher \" : \" <publisher> \" , \" Product \" : \" <product> \" , \" promotionCode \" : \" <promotionCode> \" } '] que o plano de imagem da Galeria pai [' NULL '].*  
**Causa**: a definição da imagem pai para a versão da imagem que está sendo implantada não tem informações do plano de compra.  
**Solução alternativa**: Crie uma definição de imagem com os mesmos detalhes do plano de compra da mensagem de erro e crie a versão da imagem na definição da imagem.


## <a name="creating-a-disk-from-an-image-version"></a>Criando um disco com base em uma versão de imagem ##

**Mensagem**: *o valor do parâmetro imageReference é inválido.*  
**Causa**: você tentou exportar de uma versão de imagem SIG para um disco, mas usou uma posição de LUN que não existe na imagem.    
**Solução alternativa**: Verifique a versão da imagem para ver quais posições de LUN estão em uso.

## <a name="sharing-resources"></a>Compartilhando recursos

O compartilhamento de galeria de imagens, definição de imagem e recursos de versão de imagem entre assinaturas é habilitado usando o [controle de acesso baseado em função do Azure (RBAC do Azure)](../role-based-access-control/rbac-and-directory-admin-roles.md). 

## <a name="replication-speed"></a>Velocidade de replicação

Use o sinalizador **--Expand ReplicationStatus** para verificar se a replicação para todas as regiões de destino especificadas foi concluída. Caso contrário, aguarde até seis horas para que o trabalho seja concluído. Se falhar, dispare o comando novamente para criar e replicar a versão da imagem. Se houver muitas regiões de destino para as quais a versão da imagem está sendo replicada, considere fazer a replicação em fases.

## <a name="azure-limits-and-quotas"></a>Limites e cotas do Azure 

[Limites e cotas do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md) se aplicam a todos os recursos de versão de imagem, definição da imagem e galeria de imagens compartilhadas. Verifique se você está dentro dos limites de suas assinaturas. 


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [galerias de imagens compartilhadas](./shared-image-galleries.md).