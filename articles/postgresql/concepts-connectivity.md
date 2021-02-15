---
title: Tratar erros de conectividade transitórios-banco de dados do Azure para PostgreSQL-servidor único
description: Saiba como lidar com erros de conectividade transitórios para o banco de dados do Azure para PostgreSQL-servidor único.
keywords: conexão do postgresql, cadeia de conexão, problemas de conectividade, erro transitório, erro de conexão
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 7c70c82615df111f265604ff0984aa452d68565f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91704343"
---
# <a name="handling-transient-connectivity-errors-for-azure-database-for-postgresql---single-server"></a>Manipulação de erros de conectividade transitória para Banco de Dados do Azure para PostgreSQL – servidor único

Este artigo descreve como lidar com erros transitórios se conectando ao banco de dados do Azure para PostgreSQL.

## <a name="transient-errors"></a>Erros transitórios

Um erro transitório, também conhecido como uma falha transitória, é um erro que será resolvido por si só. Geralmente, esses erros manifestam como uma conexão para o servidor de banco de dados que está sendo descartado. Além disso, as novas conexões com um servidor não podem ser abertas. Os erros transitórios podem ocorrer, por exemplo, quando ocorre uma falha de hardware ou de rede. Outro motivo pode ser uma nova versão de um serviço PaaS que está sendo distribuído. A maioria desses eventos é automaticamente mitigada pelo sistema em menos de 60 segundos. Uma prática recomendada para projetar e desenvolver aplicativos na nuvem é esperar erros transitórios. Suponha que pode acontecer em qualquer componente a qualquer momento e ter a lógica apropriada em vigor para lidar com essas situações.

## <a name="handling-transient-errors"></a>Tratamento de erros transitórios

Os erros transitórios devem ser manipulados usando a lógica de repetição. Situações em que devem ser consideradas:

* Ocorre um erro quando você tentar abrir uma conexão
* Uma conexão ociosa é descartada no lado do servidor. Quando você tenta emitir um comando, ele não pode ser executado
* Uma conexão ativa que esteja executando um comando é descartada.

A primeira e a segunda ocorrência são razoavelmente diretas de lidar. Tente abrir a conexão novamente. Quando você tiver êxito, o erro transitório terá sido reduzido pelo sistema. Você pode usar seu Banco de Dados do Azure para PostgreSQL novamente. Recomendamos ter esperas antes de tentar novamente a conexão. Desista se as tentativas iniciais falharem. Dessa forma, o sistema pode usar todos os recursos disponíveis para superar a situação de erro. Um bom padrão a seguir é:

* Aguarde cinco segundos até a primeira tentativa.
* Para cada próxima repetição, a espera aumenta exponencialmente, para até 60 segundos.
* Defina um número máximo de repetições no ponto em que seu aplicativo considera que a operação falhou.

Quando uma conexão com uma transação ativa falha, é mais difícil de lidar com a recuperação corretamente. Há dois casos: se a transação era somente leitura por natureza, é seguro para reabrir a conexão e tentar a transação novamente. Se, no entanto, a transação também estava gravando no banco de dados, você deve determinar se a transação foi revertida ou se ela foi bem-sucedida antes da ocorrência do erro transitório. Nesse caso, você pode simplesmente não ter recebido a confirmação de confirmação do servidor de banco de dados.

Uma maneira de fazer isso, é gerar uma ID exclusiva no cliente que é usado para todas as tentativas. Você pode passar essa ID exclusiva como parte da transação para o servidor e armazená-los em uma coluna com uma restrição exclusiva. Dessa forma, com segurança, você pode repetir a transação. Ela terá êxito se a transação anterior tiver sido revertida e a ID exclusiva do cliente gerada ainda não existir no sistema. Ocorrerá uma falha indicando que uma violação de chave duplicada se a ID exclusiva foi armazenada anteriormente porque a transação anterior foi concluída com êxito.

Quando o programa se comunica com o Banco de Dados do Azure para PostgreSQL por meio de um middleware de terceiros, pergunte ao fornecedor se o middleware contém lógica de repetição para erros transitórios.

Teste a lógica de repetição. Por exemplo, tente executar seu código durante o dimensionamento os recursos de computação do seu servidor Banco de Dados do Azure para PostgreSQL. Seu aplicativo deve lidar com o breve tempo de inatividade encontrado durante a operação sem qualquer problema.

## <a name="next-steps"></a>Próximas etapas

* [Solucionar problemas de conexão ao Banco de Dados do Azure para PostgreSQL](howto-troubleshoot-common-connection-issues.md)
