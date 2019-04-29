---
title: Solucionar problemas de erros do provedor de recursos de arquivos do Azure NetApp | Microsoft Docs
description: Descreve as causas, soluções e soluções alternativas para erros comuns de provedor de recursos de arquivos do Azure NetApp.
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
ms.date: 03/25/2019
ms.author: b-juche
ms.openlocfilehash: d4e06429aa1efec7c3301c7d0f0e7e17800fd520
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769430"
---
# <a name="troubleshoot-azure-netapp-files-resource-provider-errors"></a>Solucionar problemas de erros do Provedor de Recursos do Azure NetApp Files
Este artigo descreve comuns provedor de recursos de arquivos do Azure NetApp erros, suas causas, soluções e soluções alternativas. 

<a name="error_01"></a>***O Azure Key Vault não configurado.***   
O Azure Key Vault armazena as credenciais necessárias para acessar a API subjacente. Esse erro indica que o Cofre de chaves do Azure não recebeu as credenciais completas para acessar a API básica.

* Causa  
O Azure Key Vault não recebeu as credenciais corretas ou as credenciais estão incompletas.  

* Solução   
O serviço de arquivos do Azure NetApp usa o Azure Key Vault. O Azure Key Vault é autenticado usando um token do Azure Active Directory. Portanto, o proprietário do aplicativo deve registrar o aplicativo no Azure Active Directory.

* Solução alternativa   
Nenhuma.  O Azure Key Vault deve ser configurado corretamente para o uso de arquivos do Azure NetApp.  

<a name="error_02"></a>***Criação de Token não pode ser alterado.***   
Esse erro ocorre quando você tenta alterar o token de criação depois que o volume foi criado.
Token de criação deve ser definido quando o volume é criado e não pode ser alterado posteriormente.

* Causa   
Você está tentando alterar o token de criação depois que o volume tiver sido criado, o que não é uma operação com suporte.

* Solução   
Depois que o Volume tiver sido criado, considere a remoção do parâmetro da solicitação para ignorar a mensagem de erro.

* Solução alternativa   
Se você precisar alterar o token de criação, você pode criar um novo volume com um novo token de criação e, em seguida, migrar os dados para o novo volume.


<a name="error_03"></a>***Criação de Token deve ter pelo menos 16 caracteres.***   
Esse erro ocorre quando o token de criação não atende o requisito de comprimento. O comprimento do token de criação deve ser pelo menos 16 caracteres.

* Causa   
O token de criação não atende o requisito de comprimento.  Quando você cria um volume por meio da API, um token de criação é necessário. Se você estiver usando o portal, o token pode ser gerado automaticamente.

* Solução   
Aumente o tamanho do token de criação. Por exemplo, você pode adicionar outra palavra no início ou no final do token de criação.

* Solução alternativa   
O mínimo necessário de comprimento do token de criação não pode ser ignorado.  Você pode usar um prefixo ou sufixo para aumentar o tamanho de token de criação.


<a name="error_04"></a>***Erro ao excluir um volume que não foi encontrado no serviço arquivos do Azure NetApp.***   
Esse erro ocorreu porque o registro interno de recursos está fora de sincronia.

* Causa   
O volume pode permanecer exibido no portal por algum tempo depois que ele foi excluído. Se você excluir o volume por meio da API, é possível que o volume não foi especificado corretamente. O erro também pode ser causado pelo cache de navegador desatualizado.

* Solução   
Se você estiver usando o portal de cache do navegador não criptografado. Também há um cache interno que é atualizado a cada 10 minutos.  Você pode tentar limpar o cache novamente.  Se o problema persistir após 10 minutos, você pode criar um tíquete de suporte.

* Solução alternativa   
Usar um volume diferente nesse meio tempo e ignorar o já existente.


<a name="error_05"></a>***Erro ao inserir um novo Volume encontrado em arquivos do Azure NetApp.***   
Esse erro ocorre porque o registro interno de recursos está fora de sincronizado.

* Causa   
O volume pode continuam sendo exibido no portal por algum tempo depois que ele foi excluído. Se você excluir o volume por meio da API, é possível que o volume não foi especificado corretamente.

* Solução   
Se você estiver usando o portal, o volume já foi criado.  O volume deve aparecer automaticamente. Se o problema persistir, você pode criar um tíquete de suporte.

* Solução alternativa   
Você pode criar um volume com um nome diferente e um token de criação diferentes.


<a name="error_06"></a>***O nome do caminho de arquivo pode conter letras, números e hifens (""-"") apenas.***   
Esse erro ocorre quando o caminho do arquivo contém caracteres sem suporte, por exemplo, um ponto ("."), vírgula (","), sublinhado ("\_"), ou o sinal de cifrão ("$").

* Causa   
O caminho do arquivo contém caracteres sem suporte, por exemplo, um ponto ("."), vírgula (","), sublinhado ("\_"), ou o sinal de cifrão ("$").

* Solução   
Remova os caracteres que não estão em ordem alfabética letras, números ou hifens ("-") do caminho de arquivo que você inseriu.

* Solução alternativa   
Você pode substituir um sublinhado com um hífen ou use maiusculas e minúsculas em vez de espaços para indicar o início de novas palavras (por exemplo, usando "NewVolume" em vez de "novo volume").


<a name="error_07"></a>***ID do volume não pode ser alterado.***   
Esse erro ocorre quando você tenta alterar a ID do volume.  Alterar a ID de volume não é uma operação com suporte.

* Causa   
A ID do sistema de arquivos é definida quando o volume é criado. A ID de volume não pode ser alterada posteriormente.

* Solução   
Nenhuma.

* Solução alternativa   
Nenhuma.  A ID do volume é gerada quando o volume é criado e não pode ser alterado posteriormente.


<a name="error_08"></a>***Um valor inválido '{0}' foi recebida para {1}.***   
Esta mensagem indica um erro nos campos para RuleIndex, AllowedClients, UnixReadOnly, UnixReadWrite, NFS V3 e Nfsv4.

* Causa   
A solicitação de validação de entrada falhou para pelo menos um dos seguintes campos: RuleIndex, AllowedClients, UnixReadOnly, UnixReadWrite, NFS V3 e Nfsv4.

* Solução   
Certifique-se de definir todos os parâmetros necessários e não conflitantes na linha de comando. Por exemplo, é possível definir os UnixReadOnly UnixReadWrite parâmetros e ao mesmo tempo.

* Solução alternativa   
Consulte a seção de solução.  


<a name="error_09"></a>***Valor ausente para '{0}'.***   
Esse erro indica que um atributo obrigatório está ausente na solicitação para pelo menos um dos seguintes parâmetros: RuleIndex, AllowedClients, UnixReadOnly, UnixReadWrite, NFS V3 e Nfsv4.

* Causa   
A solicitação de validação de entrada falhou para pelo menos um dos seguintes campos: RuleIndex, AllowedClients, UnixReadOnly, UnixReadWrite, NFS V3 e Nfsv4.

* Solução   
Certifique-se de definir todos os parâmetros necessários e não conflitantes na linha de comando. Por exemplo, você não pode definir os UnixReadOnly UnixReadWrite parâmetros e ao mesmo tempo

* Solução alternativa   
Consulte a seção de solução.  


<a name="error_10"></a> ***{0} já está em uso.***   
Esse erro indica que o nome do recurso já foi usado.

* Causa   
Você está tentando criar um volume com um nome que é o mesmo que um volume existente.

* Solução   
Use um nome exclusivo durante a criação de um volume.

* Solução alternativa   
Se necessário, você pode alterar o nome do volume existente para que o novo volume pode usar o nome desejado.


<a name="error_11"></a> ***{0} muito curto.***   
Esse erro indica que o nome do volume não atende o requisito de comprimento mínimo.

* Causa   
O nome do volume é muito curto.

* Solução   
Aumente o comprimento do nome do volume.  

* Solução alternativa   
Você pode adicionar um prefixo ou sufixo comum para o nome do volume.


<a name="error_12"></a>***API de arquivos NetApp Azure inacessível.***   
A API do Azure conta com a API de arquivos NetApp do Azure para gerenciar volumes.  Esse erro indica um problema com a conexão de API.

* Causa   
A API subjacente não está respondendo, resultando em um erro interno. Esse erro é provavelmente será temporária.

* Solução   
O problema é provavelmente será temporária.  A solicitação deve ter sucesso após algum tempo.

* Solução alternativa   
Nenhuma. A API subjacente é essencial para o gerenciamento de volumes.  


<a name="error_13"></a>***Nenhuma credencial encontrada para a assinatura '{0}'.***   
Esse erro indica que as credenciais fornecidas são inválidas ou não foram definidas corretamente na assinatura.

* Causa   
As credenciais que são inválidos ou definida incorretamente impedem o acesso ao serviço de gerenciamento de volumes.

* Solução   
Certifique-se de que as credenciais são definidas e digitadas corretamente na linha de comando.

* Solução alternativa   
Nenhuma.  Configurando credenciais corretamente é essencial para o uso de arquivos do Azure NetApp.  


<a name="error_14"></a>***Nenhuma id de resultado de operação encontrada para '{0}'.***   
Esse erro indica que um erro interno está impedindo a conclusão da operação.

* Causa   
Um erro interno ocorreu e impediu a conclusão da operação.

* Solução   
Esse erro é provavelmente será temporária.  Aguarde alguns minutos e tente novamente. Se o problema persistir, crie um tíquete para ter suporte técnico a investigar o problema.

* Solução alternativa   
Aguarde alguns minutos e verifique se o problema persistir.


<a name="error_15"></a>***Operação '{0}' não tem suporte.***   
Esse erro indica que o comando não está disponível para a assinatura ativa ou o recurso.

* Causa   
A operação não está disponível para a assinatura ou o recurso.

* Solução   
Certifique-se de que o comando foi digitado corretamente e disponível para o recurso e a assinatura que você está usando.

* Solução alternativa   
Consulte a seção de solução.  


<a name="error_16"></a>***Não há suporte para a operação de patch para esse tipo de recurso.***   
Esse erro ocorre quando você tenta alterar o destino de montagem ou instantâneo.

* Causa   
O destino de montagem é definido quando ele é criado, e não pode ser alterado posteriormente.

* Solução   
Nenhuma.  O destino de montagem não pode ser alterado depois que o volume é criado.

* Solução alternativa   
Nenhuma.


<a name="error_17"></a>***Recebido um valor para a propriedade somente leitura '{0}'.***   
Esse erro ocorre quando você define um valor para uma propriedade que não pode ser alterado. Por exemplo, você não pode alterar a ID do volume.

* Causa   
Tentativa de modificar um parâmetro (por exemplo, a ID de volume) que não pode ser alterado.

* Solução   
Nenhuma. O parâmetro para a ID de volume não pode ser modificado.

* Solução alternativa   
A ID de volume não deve exigir modificação.  Portanto, uma solução alternativa não é necessária.

<a name="error_18"></a>***A solicitação {0} não foi encontrado.***   
Esse erro ocorre quando você tenta fazer referência a um recurso inexistente, por exemplo, um volume ou instantâneo. O recurso pode ter sido excluído ou tem um nome de recurso de erro de digitação.

* Causa   
Você está tentando fazer referência a um recurso não existente (por exemplo, um volume ou um instantâneo) que já foi excluído ou tem um nome de recurso escritas de forma incorreta.

* Solução   
Verifique a solicitação para erros de ortografia certificar-se de que ele é referenciado corretamente.

* Solução alternativa   
Consulte a seção de solução.

<a name="error_19"></a>***Não é possível obter as credenciais para a assinatura '{0}'.***   
Esse erro indica que as credenciais fornecidas são inválidas ou definem incorretamente na assinatura.

* Causa   
As credenciais que são inválidas ou incorretamente conjunto na assinatura impedir o acesso ao serviço de gerenciamento de volumes.

* Solução   
Certifique-se de que as credenciais são definidas e digitadas corretamente na linha de comando.

* Solução alternativa   
Nenhuma.  Corretamente definir credenciais são essenciais para o uso de arquivos do Azure NetApp.

<a name="error_20"></a>***Erro de arquivos NetApp Azure desconhecido.***   
A API do Azure conta com a API de arquivos NetApp do Azure para gerenciar volumes. O erro indica um problema na comunicação para a API.

* Causa   
A API básica está enviando um erro desconhecido.  Esse erro é provavelmente será temporária.

* Solução   
O problema é provavelmente será temporária e a solicitação deve ter sucesso após algum tempo. Se o problema persistir, crie um tíquete de suporte para apresentar o problema investigado.

* Solução alternativa   
Nenhuma.  A API subjacente é essencial para o gerenciamento de volumes.

<a name="error_21"></a>***Valor recebido para uma propriedade desconhecida '{0}'.***   
Esse erro ocorre quando as propriedades inexistentes são fornecidas para um recurso, como o volume, o instantâneo ou o destino de montagem.

* Causa   
A solicitação tem um conjunto de propriedades que podem ser usados com cada recurso.  Você não pode incluir quaisquer propriedades inexistentes na solicitação.

* Solução   
Certifique-se de que todos os nomes de propriedade foram escritos corretamente e as propriedades estão disponíveis para a assinatura e recursos.

* Solução alternativa   
Reduza o número de propriedades definidas na solicitação para eliminar a propriedade que está causando o erro.


<a name="error_22"></a>***Não há suporte para a operação de atualização para esse tipo de recurso.***   
Somente os volumes podem ser atualizados. Esse erro ocorre quando você tenta executar uma operação de atualização sem suporte, por exemplo, a atualização de um instantâneo.

* Causa   
O recurso que você está tentando atualizar não oferece suporte à operação de atualização.  Somente os volumes podem ter suas propriedades modificadas.

* Solução   
Nenhuma.  O recurso que você está tentando atualizar não oferece suporte à operação de atualização. Portanto, ele não pode ser alterado.

* Solução alternativa   
Para um volume, criar um novo recurso com a atualização in-loco e migrar os dados.


<a name="error_23"></a>***Número de itens: {0} do objeto: {1} é intervalo fora de Mín-Máx.***   
Esse erro ocorre quando as regras de política de exportação não atende ao requisito de intervalo mínimo ou máximo.  Se você definir a política de exportação, ele deve ter uma exportação regra de política no mínimo e cinco regras de política de exportação no máximo.

* Causa   
A política de exportação que você definiu não atende o intervalo necessário.  

* Solução   
Certifique-se de que o índice já não é usado e que está no intervalo de 1 a 5.

* Solução alternativa   
Não é obrigatório para usar a política de exportação nos volumes. Portanto, você pode omitir a política de exportação inteiramente caso você não precisa ter as regras de política de exportação.


<a name="error_24"></a>***Erro de valor para o objeto duplicado {0}.***   
Esse erro ocorre quando a política de exportação não está definida com um índice exclusivo.  Quando você define políticas de exportação, todas as regras de política de exportação devem ter um índice exclusivo entre 1 e 5.

* Causa   
A política de exportação definido não atende o requisito para regras de política de exportação. Você deve ter uma exportação regra de política no mínimo e cinco regras de política de exportação no máximo.  

* Solução   
Certifique-se de que o índice já não é usado e que ele está no intervalo de 1 a 5.

* Solução alternativa   
Use um índice diferente para a regra que você está tentando definir.


