---
title: Solucionar problemas Azure NetApp Files erros do provedor de recursos | Microsoft Docs
description: Descreve as causas, soluções e soluções alternativas para erros comuns do provedor de recursos Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: b-juche
ms.openlocfilehash: f417d83a67f2f3afa33a83a56a72d0d82c64ab0d
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849999"
---
# <a name="troubleshoot-azure-netapp-files-resource-provider-errors"></a>Solucionar problemas de erros do Provedor de Recursos do Azure NetApp Files 

Este artigo descreve os erros comuns do provedor de recursos Azure NetApp Files, suas causas, soluções e soluções alternativas (se disponíveis).

## <a name="common-azure-netapp-files-resource-provider-errors"></a>Erros comuns do provedor de recursos de Azure NetApp Files

***BareMetalTenantId não pode ser alterado.***  

Esse erro ocorre quando você tenta atualizar ou aplicar um patch a um volume `BaremetalTenantId` e a propriedade tem um valor alterado.

* Causa:   
Você está tentando atualizar um volume e a `BaremetalTenantId` propriedade tem um valor diferente do valor armazenado no Azure.
* Solução:   
Não inclua `BaremetalTenantId` o patch e a solicitação Update (put). Como alternativa, certifique- `BaremetalTenantId` se de que é o mesmo na solicitação.

***O imnível não pode ser alterado.***  

Esse erro ocorre quando você tenta atualizar ou aplicar patch em um pool de capacidade com um nível de serviço diferente quando o pool de capacidade já tem volumes.

* Causa:   
Você está tentando atualizar um nível de serviço do pool de capacidade quando o pool contém volumes.
* Solução:   
Exclua todos os volumes do pool de capacidade e, em seguida, altere o nível de serviço.
* Solução alternativa:   
Crie outro pool de capacidade e, em seguida, crie os volumes novamente no novo pool de capacidade.

***Poolid não pode ser alterado***  

Esse erro ocorre quando você tenta atualizar ou aplicar patch em um pool de capacidade com `PoolId` uma propriedade alterada.

* Causa:   
Você está tentando atualizar uma propriedade de pool `PoolId` de capacidade. A `PoolId` propriedade é uma propriedade somente leitura e não pode ser alterada.
* Solução:   
Não inclua `PoolId` o patch e a solicitação Update (put).  Como alternativa, certifique- `PoolId` se de que é o mesmo na solicitação.

***CreationToken não pode ser alterado.***

Esse erro ocorre quando você tenta alterar o caminho do arquivo (`CreationToken`) após a criação do volume. O caminho do`CreationToken`arquivo () deve ser definido quando o volume é criado e não pode ser alterado posteriormente.

* Causa:   
Você está tentando alterar o caminho do arquivo (`CreationToken`) após a criação do volume, o que não é uma operação com suporte. 
* Solução:   
Se a alteração do caminho do arquivo não for necessária, considere remover o parâmetro da solicitação para ignorar a mensagem de erro.
* Solução alternativa:   
Se precisar alterar o caminho do arquivo (`CreationToken`), você poderá criar um novo volume com um novo caminho de arquivo e, em seguida, migrar os dados para o novo volume.

***CreationToken deve ter pelo menos 16 caracteres.***

Esse erro ocorre quando o caminho do arquivo`CreationToken`() não atende ao requisito de comprimento. O comprimento do caminho do arquivo deve ter pelo menos um caractere de comprimento.

* Causa:   
O caminho do arquivo está vazio.  Quando você cria um volume usando a API, um token de criação é necessário. Se você estiver usando o portal do Azure, o caminho do arquivo será gerado automaticamente.
* Solução:   
Insira pelo menos um caractere como o caminho do arquivo`CreationToken`().

***O nome de domínio não pode ser alterado.***

Esse erro ocorre quando você tenta alterar o nome de domínio em Active Directory.

* Causa:   
Você está tentando atualizar a propriedade de nome de domínio.
* Solução:    
nenhuma. Você não pode alterar o nome de domínio.
* Solução alternativa:   
Exclua todos os volumes usando a configuração de Active Directory. Em seguida, exclua a configuração de Active Directory e recrie os volumes.

***Erro de valor duplicado para o objeto ExportPolicy. Rules [RuleIndex].***

Esse erro ocorre quando a política de exportação não está definida com um índice exclusivo. Quando você define políticas de exportação, todas as regras de política de exportação devem ter um índice exclusivo entre 1 e 5.

* Causa:   
A política de exportação definida não atende ao requisito de regras de política de exportação. Você deve ter uma regra de política de exportação no mínimo e cinco regras de política de exportação no máximo.
* Solução:   
Verifique se o índice já não está sendo usado e se está no intervalo de 1 a 5.
* Solução alternativa:   
Use um índice diferente para a regra que você está tentando definir.

***Erro {ação} {resourceTypename}***

Esse erro é exibido quando outro tratamento de erros falha ao manipular o erro durante a execução de uma ação em um recurso.   Ele inclui o texto ' error '. O `{action}` pode ser qualquer um de`getting`( `creating`, `updating`, ou `deleting`).  O `{resourceTypeName}` é o `resourceTypeName` `netAppAccount` (`volume`porexemplo,,, e assim por diante). `capacityPool`

* Causa:   
Esse erro é uma exceção sem tratamento em que a causa não é conhecida.
* Solução:   
Contate o centro de suporte do Azure para relatar o motivo detalhado nos logs.
* Solução alternativa:   
nenhuma.

***O nome do caminho do arquivo pode conter apenas letras, números e hifens (""-"").***

Esse erro ocorre quando o caminho do arquivo contém caracteres sem suporte, por exemplo, um ponto final ("."), vírgula (","), sublinhado ("_") ou cifrão ("$").

* Causa:   
O caminho do arquivo contém caracteres sem suporte, por exemplo, um ponto final ("."), vírgula (","), sublinhado ("_") ou cifrão ("$").
* Solução:   
Remova os caracteres que não são letras, números ou hifens ("-") do caminho de arquivo que você inseriu.
* Solução alternativa:   
Você pode substituir um sublinhado por um hífen ou usar maiúsculas e minúsculas, em vez de espaços, para indicar o início das novas palavras.  Por exemplo, use "NewVolume" em vez de "novo volume".

***Não é possível alterar filesystemid.***

Esse erro ocorre quando você tenta alterar `FileSystemId`.  A `FileSystemdId` alteração não é uma operação com suporte. 

* Causa:   
A ID do sistema de arquivos é definida quando o volume é criado. `FileSystemId`Não pode ser alterado subsequentemente.
* Solução:   
Não inclua `FileSystemId` em uma solicitação de patch e atualização (put).  Como alternativa, verifique se éomesmonasolicitação.`FileSystemId`

***O ActiveDirectory com a ID: ' {String} ' não existe.***

A `{string}` parte é o valor que você inseriu `ActiveDirectoryId` na propriedade para a conexão Active Directory.

* Causa:   
Quando você criou uma conta com a configuração de Active Directory, você inseriu um valor `ActiveDirectoryId` para que deve estar vazio.
* Solução:   
Não inclua `ActiveDirectoryId` na solicitação de criação (put).

***Versão de API inválida.***

A versão da API não foi enviada ou contém um valor inválido.

* Causa:   
O valor no parâmetro `api-version` de consulta contém um valor inválido.
* Solução:   
Use o valor correto da versão da API.  O provedor de recursos dá suporte a várias versões de API. O valor está no formato aaaa-mm-dd.

***Um valor inválido ' {value} ' foi recebido para {1}.***

Essa mensagem indica um erro nos campos para `RuleIndex`, `AllowedClients`, `UnixReadOnly`, `UnixReadWrite` `Nfsv3`, e `Nfsv4`.

* Causa:   
A solicitação de validação de entrada falhou para pelo menos um dos seguintes campos: `RuleIndex`, `AllowedClients`, `UnixReadOnly` `UnixReadWrite`,, `Nfsv`3 e `Nfsv4`.
* Solução:   
Certifique-se de definir todos os parâmetros necessários e não conflitantes na linha de comando. Por exemplo, você não pode definir os `UnixReadOnly` parâmetros `UnixReadWrite` e ao mesmo tempo.
* Solução alternativa:   
Consulte a solução acima.

***O intervalo {0} de {1} IP para {2} para VLAN já está em uso***

Esse erro ocorre porque os registros internos dos intervalos de IP usados têm um conflito com o endereço IP atribuído recentemente.

* Causa:   
O endereço IP atribuído para a criação do volume já está registrado.
O motivo pode ser uma criação de volume com falha anterior.
* Solução:   
Contate o centro de suporte do Azure.

***Valor ausente para ' {Property} '.***

Esse erro indica que uma propriedade necessária está ausente na solicitação. A cadeia de caracteres {Property} contém o nome da propriedade ausente.

* Causa:   
A solicitação de validação de entrada falhou para pelo menos uma das propriedades.
* Solução:   
Certifique-se de definir todas as propriedades obrigatórias e não conflitantes na solicitação, especialmente, a propriedade da mensagem de erro.

***MountTargets não pode ser alterado.***

Esse erro ocorre quando um usuário está tentando atualizar ou aplicar patch na propriedade MountTargets do volume.

* Causa:   
Você está tentando atualizar a propriedade de `MountTargets` volume. Não há suporte para a alteração dessa propriedade.
* Solução:   
Não inclua `MountTargets` em uma solicitação de patch e atualização (put).  Como alternativa, verifique `MountTargets` se é o mesmo na solicitação.

***O nome já está em uso.***

Esse erro indica que o nome do recurso já está em uso.

* Causa:   
Você está tentando criar um recurso com um nome que é usado para um recurso existente.
* Solução:   
Use um nome exclusivo ao criar o recurso.

***O caminho do arquivo já está em uso.***

Esse erro indica que o caminho do arquivo para o volume já está em uso.

* Causa:   
Você está tentando criar um volume com um caminho de arquivo que é igual a um volume existente.
* Solução:   
Use um caminho de arquivo exclusivo ao criar o volume.

***Nome muito longo.***

Esse erro indica que o nome do recurso não atende ao requisito de comprimento máximo.

* Causa:   
O nome do recurso é muito longo.
* Solução:   
Use um nome mais curto para o recurso.

***Caminho de arquivo muito longo.***

Esse erro indica que o caminho do arquivo para o volume não atende ao requisito de comprimento máximo.

* Causa:   
O caminho do arquivo de volume é muito longo.
* Solução:   
Use um caminho de arquivo mais curto.

***Nome muito curto.***

Esse erro indica que o nome do recurso não atende ao requisito de comprimento mínimo.

* Causa:   
O nome do recurso é muito curto.
* Solução:   
Use um nome mais longo para o recurso.

***Caminho de arquivo muito curto.***

Esse erro indica que o caminho do arquivo de volume não atende ao requisito de comprimento mínimo.

* Causa:   
O caminho do arquivo de volume é muito curto.
* Solução:   
Aumente o comprimento do caminho do arquivo de volume.

***API Azure NetApp Files inacessível.***

A API do Azure depende da API de Azure NetApp Files para gerenciar volumes. Esse erro indica um problema com a conexão de API.

* Causa:   
A API subjacente não está respondendo, resultando em um erro interno. Esse erro é provavelmente temporário.
* Solução:   
O problema provavelmente será temporário. A solicitação deve ter sucesso depois de algum tempo.
* Solução alternativa:   
nenhuma. A API subjacente é essencial para o gerenciamento de volumes.

***Nenhuma ID de resultado de operação encontrada{0}para ' '.***

Esse erro indica que um erro interno está impedindo a conclusão da operação.

* Causa:   
Ocorreu um erro interno e impediu a conclusão da operação.
* Solução:   
Esse erro é provavelmente temporário. Aguarde alguns minutos e tente novamente. Se o problema persistir, crie um tíquete para que o suporte técnico investigue o problema.
* Solução alternativa:   
Aguarde alguns minutos e verifique se o problema persiste.

***Não é permitido misturar tipos de protocolo CIFS e NFS***

Esse erro ocorre quando você está tentando criar um volume e há ambos os tipos de protocolo CIFS (SMB) e NFS nas propriedades do volume.

* Causa:   
Os tipos de protocolo CIFS (SMB) e NFS são usados nas propriedades do volume.
* Solução:   
Remova um dos tipos de protocolo.
* Solução alternativa:   
Deixe a propriedade de tipo de protocolo vazia ou nula.

***Número de itens: {Value} para o objeto: ExportPolicy. Rules [RuleIndex] está fora do intervalo mín.-máx.***

Esse erro ocorre quando as regras de política de exportação não atendem ao requisito de intervalo mínimo ou máximo. Se você definir a política de exportação, ela deverá ter uma regra de política de exportação no mínimo e cinco regras de política de exportação no máximo.

* Causa:   
A política de exportação que você definiu não atende ao intervalo necessário.
* Solução:   
Verifique se o índice já não está sendo usado e se está no intervalo de 1 a 5.
* Solução alternativa:   
Não é obrigatório usar a política de exportação nos volumes. Você pode omitir a política de exportação inteiramente se não precisar usar regras de política de exportação.

***Somente um Active Directory é permitido***

Esse erro ocorre quando você tenta criar uma configuração de Active Directory e uma já existe para a assinatura na região. O erro também pode ocorrer quando você tenta criar mais de uma configuração de Active Directory.

* Causa:   
Você está tentando criar (não atualizar) um Active Directory, mas já existe um.
* Solução:   
Se a configuração de Active Directory não estiver em uso, você poderá primeiro excluir a configuração existente e, em seguida, repetir a operação de criação.
* Solução alternativa:   
nenhuma. Somente um Active Directory é permitido.

***Não há suporte para a operação ' {Operation} '.***

Esse erro indica que a operação não está disponível para a assinatura ou o recurso ativo.

* Causa:   
A operação não está disponível para a assinatura ou o recurso.
* Solução:   
Verifique se a operação foi inserida corretamente e se está disponível para o recurso e a assinatura que você está usando.

***OwnerId não pode ser alterada***

Esse erro ocorre quando você tenta alterar a propriedade OwnerId do volume. Não é uma operação com suporte para alterar a OwnerId. 

* Causa:   
A `OwnerId` propriedade é definida quando o volume é criado. A propriedade não pode ser alterada subsequentemente.
* Solução:   
Não inclua `OwnerId` em uma solicitação de patch e atualização (put). Como alternativa, verifique `OwnerId` se é o mesmo na solicitação.

***Pool pai não encontrado***

Esse erro ocorre quando você tenta criar um volume e o pool de capacidade no qual você está criando o volume não é encontrado.

* Causa:   
O pool de capacidade em que o volume está sendo criado não foi encontrado.
* Solução:   
Provavelmente, o pool não foi totalmente criado ou já foi excluído no momento da criação do volume.

***Não há suporte para a operação de patch para este tipo de recurso.***

Esse erro ocorre quando você tenta alterar o destino ou o instantâneo de montagem.

* Causa:   
O destino de montagem é definido quando é criado e não pode ser alterado posteriormente.
Os instantâneos não contêm nenhuma propriedade que possa ser alterada.
* Solução:   
nenhuma. Esses recursos não têm nenhuma propriedade que possa ser alterada.

***Tamanho do pool muito pequeno para o tamanho total do volume.***

Esse erro ocorre quando você está atualizando o tamanho do pool de capacidade e o tamanho é menor do `usedBytes` que o valor total de todos os volumes nesse pool de capacidade.  Esse erro também pode ocorrer quando você está criando um novo volume ou redimensionando um volume existente, e o novo tamanho do volume excede o espaço livre no pool de capacidade.

* Causa:   
Você está tentando atualizar o pool de capacidade para um tamanho menor do que usedBytes em todos os volumes no pool de capacidade.  Ou, você está tentando criar um volume que seja maior do que o espaço livre no pool de capacidade.  Como alternativa, você está tentando redimensionar um volume e o novo tamanho excede o espaço livre no pool de capacidade.
* Solução:   
Defina o tamanho do pool de capacidade para um valor maior ou crie um volume menor para um volume.
* Solução alternativa:   
Remova volumes suficientes para que o tamanho do pool de capacidade possa ser atualizado para esse tamanho.

***A propriedade: O local do instantâneo deve ser igual ao volume***

Esse erro ocorre quando você está criando um instantâneo com um local diferente do volume que possui o instantâneo.

* Causa:   
Valor inválido na propriedade Location do instantâneo.
* Solução:   
Defina uma cadeia de caracteres válida na propriedade Location.

***O nome {resourceType} deve ser igual ao nome do identificador de recurso.***

Esse erro ocorre quando você está criando um recurso e preenche a propriedade Name com outro valor da propriedade Name de `resourceId`.

* Causa:   
Valor inválido na propriedade Name quando você cria um recurso.
* Solução:   
Deixe a propriedade Name vazia ou permita que ela use o mesmo valor que a propriedade Name (entre a última barra invertida "/" e o ponto de interrogação "? `resourceId`") em.

***Tipo de protocolo {value} não conhecido***

Esse erro ocorre quando você está criando um volume com um tipo de protocolo desconhecido.  Os valores válidos são "NFSv3" e "CIFS".

* Causa:   
Você está tentando definir um valor inválido na propriedade volume `protocolType` .
* Solução:   
Defina uma cadeia de caracteres `protocolType`válida em.
* Solução alternativa:   
Definir `protocolType` como nulo.

***Tipos de protocolo não podem ser alterados***

Esse erro ocorre quando você tenta atualizar ou aplicar patch `ProtocolType` para um volume.  A alteração de ProtocolType não é uma operação com suporte.

* Causa:   
A `ProtocolType` propriedade é definida quando o volume é criado.  Ele não pode ser atualizado.
* Solução:   
nenhuma.
* Solução alternativa:   
Crie outro volume com novos tipos de protocolo.

***A criação do recurso do tipo {resourceType} excederia a cota de recursos de {quota} do tipo {resourceType} por {parentResourceType}. A contagem de recursos atual é {currentCount}, exclua alguns recursos desse tipo antes de criar um novo.***

Esse erro ocorre quando você está tentando criar um recurso`NetAppAccount`(, `CapacityPool`, `Volume`ou `Snapshot`), mas sua cota atingiu seu limite.

* Causa:   
Você está tentando criar um recurso, mas o limite de cota é atingido (exemplo: `NetAppAccounts` por assinatura ou `CapacityPools` por `NetAppAccount`).
* Solução:   
Aumente o limite de cota.
* Solução alternativa:   
Exclua os recursos não utilizados do mesmo tipo e crie-os novamente.

***Foi recebido um valor para a propriedade somente leitura ' {propertyName} '.***

Esse erro ocorre quando você define um valor para uma propriedade que não pode ser alterada. Por exemplo, você não pode alterar a ID do volume.

* Causa:   
Você está tentando modificar um parâmetro (por exemplo, a ID do volume) que não pode ser alterado.
* Solução:   
Não modifique um valor para a propriedade.

***O {Resource} solicitado não foi encontrado.***

Esse erro ocorre quando você tenta fazer referência a um recurso inexistente, por exemplo, um volume ou instantâneo. O recurso pode ter sido excluído ou ter um nome de recurso soletrada.

* Causa:   
Você está tentando fazer referência a um recurso inexistente (por exemplo, um volume ou instantâneo) que já foi excluído ou tem um nome de recurso digitado incorretamente.
* Solução:   
Verifique a solicitação de erros de ortografia para certificar-se de que ele está referenciado corretamente.
* Solução alternativa:   
Consulte a seção da solução acima.

***O nível de serviço ' {volumeServiceLevel} ' é maior que o pai ' {poolServiceLevel} '***

Esse erro ocorre quando você está criando ou atualizando um volume e definiu o nível de serviço para um nível mais alto do que o pool de capacidade que o contém.

* Causa:   
Você está tentando criar ou atualizar um volume com um nível de serviço mais alto com classificação do que o pool de capacidade pai.
* Solução:   
Defina o nível de serviço como o mesmo ou uma classificação mais baixa do que o pool de capacidade pai.
* Solução alternativa:   
Crie o volume em outro pool de capacidade com um nível de serviço correto. Como alternativa, exclua todos os volumes do pool de capacidade e defina o nível de serviço para o pool de capacidade como uma classificação mais alta.

***O nome do servidor SMB não pode ter mais de 10 caracteres.***

Esse erro ocorre quando você está criando ou atualizando uma configuração de Active Directory para uma conta.

* Causa:   
O comprimento do nome do servidor SMB excede 10 caracteres.
* Solução:   
Use um nome de servidor mais curto. O comprimento máximo é de 10 caracteres.
* Solução alternativa:   
nenhuma.  Consulte a solução acima. 

***Sub-redeid não pode ser alterada.***

Esse erro ocorre quando você tenta alterar o `subnetId` depois que o volume é criado.  `SubnetId`deve ser definido quando o volume é criado e não pode ser alterado posteriormente.

* Causa:   
Você está tentando alterar o `subnetId` depois que o volume foi criado, o que não é uma operação com suporte. 
* Solução:   
Se a `subnetId` alteração não for necessária, considere remover o parâmetro da solicitação para ignorar a mensagem de erro.
* Solução alternativa:   
Se você precisar alterar o `subnetId`, poderá criar um novo volume com um novo `subnetId`e, em seguida, migrar os dados para o novo volume.

***Sub-rede está em um formato inválido.***

Esse erro ocorre quando você tenta criar um novo volume, mas o `subnetId` não é um `resourceId` para uma sub-rede.

* Causa:   
Esse erro ocorre quando você tenta criar um novo volume, mas o `subnetId` não é um `resourceId` para uma sub-rede. 
* Solução:   
Verifique o valor para `subnetId` para garantir que ele contenha um `resourceId` para a sub-rede usada.
* Solução alternativa:   
nenhuma. Consulte a solução acima. 

***A sub-rede deve ter uma delegação de ' Microsoft. NetApp/volumes '.***

Esse erro ocorre quando você está criando um volume e a sub-rede selecionada não é delegada para `Microsoft.NetApp/volumes`.

* Causa:   
Você tentou criar o volume e selecionou uma sub-rede que não é delegada para `Microsoft.NetApp/volumes`o.
* Solução:   
Selecione outra sub-rede à `Microsoft.NetApp/volumes`qual é delegada.
* Solução alternativa:   
Adicione uma delegação correta à sub-rede.

***O tipo de recurso especificado é desconhecido/não aplicável.***

Esse erro ocorre quando uma verificação de nome foi solicitada em um tipo de recurso não aplicável ou para um tipo de recurso desconhecido.

* Causa:   
Foi solicitada uma verificação de nome para um tipo de recurso desconhecido ou sem suporte.
* Solução:   
Verifique se o recurso para o qual você está fazendo a solicitação tem suporte ou não contém erros de ortografia.
* Solução alternativa:   
Consulte a solução acima.

***Erro de Azure NetApp Files desconhecido.***

A API do Azure depende da API de Azure NetApp Files para gerenciar volumes. O erro indica um problema na comunicação com a API.

* Causa:   
A API subjacente está enviando um erro desconhecido. Esse erro é provavelmente temporário.
* Solução:   
O problema provavelmente será temporário e a solicitação deverá ter sucesso após algum tempo. Se o problema persistir, crie um tíquete de suporte para que o problema seja investigado.
* Solução alternativa:   
nenhuma. A API subjacente é essencial para o gerenciamento de volumes.

***Valor recebido para uma propriedade desconhecida ' {propertyName} '.***

Esse erro ocorre quando Propriedades inexistentes são fornecidas para um recurso como o volume, o instantâneo ou o destino de montagem.

* Causa:   
A solicitação tem um conjunto de propriedades que podem ser usadas com cada recurso. Você não pode incluir nenhuma propriedade inexistente na solicitação.
* Solução:   
Verifique se todos os nomes de propriedade estão escritos corretamente e se as propriedades estão disponíveis para a assinatura e o recurso.
* Solução alternativa:   
Reduza o número de propriedades definidas na solicitação para eliminar a propriedade que está causando o erro.

***Não há suporte para a operação de atualização para este tipo de recurso.***

Somente volumes podem ser atualizados. Esse erro ocorre quando você tenta executar uma operação de atualização sem suporte, por exemplo, atualizando um instantâneo.

* Causa:   
O recurso que você está tentando atualizar não oferece suporte para a operação de atualização. Somente os volumes podem ter suas propriedades modificadas.
* Solução:   
nenhuma. O recurso que você está tentando atualizar não oferece suporte à operação de atualização. Portanto, ele não pode ser alterado.
* Solução alternativa:   
Para um volume, crie um novo recurso com a atualização em vigor e migre os dados.

***O volume não pode ser criado em um pool que não está no estado com êxito.***

Esse erro ocorre quando você tenta criar um volume em um pool que não está no estado com êxito. Provavelmente, a operação de criação para o pool de capacidade falhou por algum motivo.

* Causa:   
O pool de capacidade que contém o novo volume está em um estado de falha.
* Solução:   
Verifique se o pool de capacidade foi criado com êxito e se ele não está em um estado de falha.
* Solução alternativa:   
Crie um novo pool de capacidade e crie o volume no novo pool.

***O volume está sendo criado e não pode ser excluído no momento.***

Esse erro ocorre quando você tenta excluir um volume que ainda está sendo criado.

* Causa:   
Um volume ainda está sendo criado quando você tenta excluir o volume.
* Solução:   
Aguarde até que a criação do volume seja concluída e repita a exclusão.
* Solução alternativa:   
Consulte a solução acima.

***O volume está sendo excluído e não pode ser excluído no momento.***

Esse erro ocorre quando você tenta excluir um volume quando ele já está sendo excluído.

* Causa:   
Um volume já está sendo excluído quando você tenta excluir o volume.
* Solução:   
Aguarde até que a operação de exclusão atual seja concluída.
* Solução alternativa:   
Consulte a solução acima.

***O volume está sendo atualizado e não pode ser excluído no momento.***

Esse erro ocorre quando você tenta excluir um volume que está sendo atualizado.

* Causa:   
Um volume está sendo atualizado quando você tenta excluir o volume.
* Solução:   
Aguarde até que a operação de atualização seja concluída e repita a exclusão.
* Solução alternativa:   
Consulte a solução acima.

***O volume não foi encontrado ou não foi criado com êxito.***

Esse erro ocorre quando a criação do volume falha e você está tentando alterar o volume ou criar um instantâneo para o volume.

* Causa:   
O volume não existe ou a criação falhou.
* Solução:   
Verifique se você está alterando o volume correto e se a criação do volume foi bem-sucedida. Ou então, verifique se o volume para o qual você está criando um instantâneo existe.
* Solução alternativa:   
nenhuma.  Consulte a solução acima. 

***O token de criação especificado já existe***

Esse erro ocorre quando você tenta criar um volume e especifica um token de criação (caminho de exportação) para o qual um volume já existe.

* Causa:   
O token de criação (caminho de exportação) especificado durante a criação do volume já está associado a outro volume. 
* Solução:   
Escolha um token de criação diferente.  Como alternativa, exclua o outro volume.

***O token de criação especificado está reservado***

Esse erro ocorre quando você tenta criar um volume e especifica "default" ou "None" como o caminho do arquivo (token de criação).

* Causa:    
Você está tentando criar um volume e especifica "default" ou "None" como o caminho do arquivo (token de criação).
* Solução:   
Escolha um caminho de arquivo diferente (token de criação).
 
***Active Directory credenciais estão em uso***

Esse erro ocorre quando você tenta excluir a configuração de Active Directory de uma conta em que pelo menos um volume SMB ainda existe.  O volume SMB foi criado usando a configuração de Active Directory que você está tentando excluir.

* Causa:   
Você está tentando excluir a configuração de Active Directory de uma conta, mas pelo menos um volume SMB ainda existe, que foi inicialmente criado usando a configuração de Active Directory. 
* Solução:   
Primeiro, exclua todos os volumes SMB que foram criados usando a configuração Active Directory.  Em seguida, repita a exclusão da configuração.

***Não é possível modificar a atribuição de unidade organizacional se as credenciais estiverem em uso***

Esse erro ocorre quando você tenta alterar a unidade organizacional de uma configuração de Active Directory, mas pelo menos um volume SMB ainda existe.  O volume SMB foi criado usando essa configuração de Active Directory que você está tentando excluir.

* Causa:   
Você está tentando alterar a unidade organizacional de uma configuração de Active Directory.  Mas pelo menos um volume SMB ainda existe, que foi inicialmente criado usando a configuração de Active Directory.
* Solução:   
 Primeiro, exclua todos os volumes SMB que foram criados usando a configuração Active Directory.  Em seguida, repita a exclusão da configuração. 

***A atualização do Active Directory já está em andamento***

Esse erro ocorre quando você tenta editar uma configuração de Active Directory para a qual uma operação de edição já está em andamento.

* Causa:   
Você está tentando editar uma configuração de Active Directory, mas outra operação de edição já está em andamento.
* Solução:   
Aguarde até que a operação de edição em execução no momento seja concluída.

***Excluir todos os volumes usando as credenciais selecionadas primeiro***

Esse erro ocorre quando você tenta excluir uma configuração de Active Directory, mas pelo menos um volume SMB ainda existe.  O volume SMB foi criado usando a configuração de Active Directory que você está tentando excluir.

* Causa:   
Você está tentando excluir uma configuração de Active Directory, mas pelo menos um volume SMB ainda existe, que foi inicialmente criado usando a configuração de Active Directory.
* Solução:   
Primeiro, exclua todos os volumes SMB que foram criados usando a configuração Active Directory.  Em seguida, repita a exclusão da configuração. 

***Nenhuma credencial de Active Directory encontrada na região***

Esse erro ocorre quando você tenta criar um volume SMB, mas nenhuma configuração de Active Directory foi adicionada à conta para a região.

* Causa:   
Você está tentando criar um volume SMB, mas nenhuma configuração de Active Directory foi adicionada à conta. 
* Solução:   
Adicione uma configuração de Active Directory à conta antes de criar um volume SMB.

***Não foi possível consultar o servidor DNS. Verifique se a configuração de rede está correta e se os servidores DNS estão disponíveis.***

Esse erro ocorre quando você tenta criar um volume SMB, mas um servidor DNS (especificado em sua configuração de Active Directory) está inacessível. 

* Causa:   
Você está tentando criar um volume SMB, mas um servidor DNS (especificado em sua configuração de Active Directory) está inacessível.
* Solução:   
Examine sua configuração de Active Directory e verifique se os endereços IP do servidor DNS estão corretos e acessíveis.
Se não houver problemas com os endereços IP do servidor DNS, verifique se nenhum firewall está bloqueando o acesso.

***Muitos trabalhos simultâneos***

Esse erro ocorre quando você tenta criar um instantâneo quando três outras operações de criação de instantâneos já estão em andamento para a assinatura.

* Causa:   
Você está tentando criar um instantâneo quando três outras operações de criação de instantâneo já estão em andamento para a assinatura. 
* Solução:   
Os trabalhos de criação de instantâneo levam alguns segundos para serem concluídos.  Aguarde alguns segundos e repita a operação de criação do instantâneo.

***Não é possível gerar trabalhos adicionais. Aguarde até que os trabalhos em andamento sejam concluídos e tente novamente***

Esse erro pode ocorrer quando você tenta criar ou excluir um volume sob circunstâncias específicas.

* Causa:   
Você está tentando criar ou excluir um volume em circunstâncias específicas.
* Solução:   
Aguarde um minuto ou então e repita a operação.

***O volume já está em transição entre Estados***

Esse erro pode ocorrer quando você tenta excluir um volume que está atualmente em um estado de transição (ou seja, atualmente no estado criando, atualizando ou excluindo).

* Causa:   
Você está tentando excluir um volume que está atualmente em um estado de transição.
* Solução:   
Aguarde até que a operação em execução no momento (transição de estado) seja concluída e repita a operação.

***Falha ao dividir o novo volume do instantâneo do volume de origem***

 Esse erro pode ocorrer quando você tenta criar um volume a partir de um instantâneo.  

* Causa:   
Você tenta criar um volume a partir de um instantâneo e o volume termina em um estado de erro.
* Solução:   
Exclua o volume e repita a operação de criação do volume a partir do instantâneo.

 
## <a name="next-steps"></a>Próximas etapas

* [Desenvolver para Azure NetApp Files com a API REST](azure-netapp-files-develop-with-rest-api.md)
