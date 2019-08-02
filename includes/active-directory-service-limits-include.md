---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 05/22/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: e6e1014ac20ef70f21344ec6ece5627eccb7ba66
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148392"
---
Aqui estão as restrições de uso e outros limites de serviço para o serviço Microsoft Azure Active Directory (Azure AD).

| Categoria | Limites |
| --- | --- |
| Diretórios | Um único usuário pode pertencer a um máximo de 500 diretórios do Microsoft Azure Active Directory como um membro ou convidado.<br/>Um único usuário pode criar no máximo 20 diretórios. |
| Domínios | É possível adicionar no máximo 900 nomes de domínio gerenciados. Ao configurar todos os domínios para a federação com o Active Directory local, você poderá adicionar no máximo 450 nomes de domínio em cada diretório. |
| Objetos |<ul><li>Um máximo de 50.000 objetos pode ser criado em um único diretório por usuários da edição gratuita do Azure Active Directory por padrão. Se você tiver pelo menos um domínio verificado, a cota de serviço de diretório padrão no Azure AD é estendida para 300.000 objetos. </li><li>Um usuário não administrador pode criar até 250 objetos. Os objetos ativos e os objetos excluídos que estão disponíveis para restauração contam para essa cota. Somente os objetos excluídos há menos de 30 dias estão disponíveis para restauração. Os objetos excluídos que não estão mais disponíveis para restauração contam para essa cota a um valor de um quarto por 30 dias. Talvez [atribuir uma função de administrador](../articles/active-directory/users-groups-roles/directory-assign-admin-roles.md) aos usuários não administradores que tenham grande probabilidade de ultrapassar essa cota repetidamente no decorrer de suas obrigações regulares.</li></ul> |
| Extensões de esquema |<ul><li>As extensões do tipo cadeia de caracteres podem ter no máximo 256 caracteres. </li><li>As extensões do tipo binário são limitadas a 256 bytes.</li><li>Apenas 100 valores de extensão, entre *todos* os tipos e *todos* os aplicativos, podem ser gravados em um único objeto.</li><li>Somente as entidades Grupo, TenantDetail, Dispositivo, Aplicativo e ServicePrincipal podem ser estendidas com atributos de valor único do tipo cadeia de caracteres ou binário.</li><li>As extensões de esquema estão disponíveis somente na API do Graph versão 1.21 – versão prévia. O aplicativo precisa obter acesso de gravação para registrar uma extensão.</li></ul> |
| Aplicativos |Um máximo de 100 usuários podem ser proprietários de um único aplicativo. |
| Grupos |<ul><li>Um máximo de 100 usuários podem ser proprietários de um único grupo.</li><li>Qualquer número de objetos pode ser membro de um único grupo.</li><li>Um usuário pode ser um membro de qualquer número de grupos.</li><li>O número de membros em um grupo que podem ser sincronizados do Active Directory local para o Azure Active Directory usando o Azure AD Connect é limitado a 50 mil membros.</li></ul> |
| Proxy de Aplicativo | <ul><li>Um máximo de 500 transações por segundo por aplicativo de Proxy de aplicativo</li><li>Um máximo de 750 transações por segundo para o locatário</li></ul><br/>Uma transação é definida como uma única solicitação e resposta http para um recurso exclusivo. Se estiver limitado, os clientes receberão uma resposta 429 (número excessivo de solicitações). |
| Painel de acesso |<ul><li>Não há nenhum limite para o número de aplicativos que podem ser vistos no Painel de Acesso por usuário. Isso se aplica às licenças do Azure AD Premium ou do Enterprise Mobility + Security E3 atribuídas aos usuários.</li><li>No máximo 10 blocos de aplicativos podem ser vistos no Painel de Acesso para cada usuário. Esse limite aplica-se aos usuários com licenças atribuídas das edições Gratuita ou Azure AD Basic do Azure Active Directory. Exemplos de blocos de aplicativos incluem o Box, o Salesforce ou o Dropbox. Esse limite não se aplica às contas de administrador.</li></ul> |
| Relatórios | Um máximo de 1.000 linhas podem ser exibidas ou baixadas em qualquer relatório. Todos os dados adicionais serão truncados. |
| Unidades administrativas | Um objeto pode ser um membro de até 30 unidades administrativas. |
| Funções e permissões do administrador | <ul><li>Um grupo não pode ser adicionado como um [proprietário](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#object-ownership).</li><li>Um grupo não pode ser atribuído a um [função](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).</li><li>A capacidade dos usuários para ler as informações de diretório de outros usuários não pode ser restrita fora o comutador de locatários para desabilitar o acesso de todos os usuários não administradores a todas as informações de diretório (não recomendados). Obter mais informações sobre as permissões padrão [aqui](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#to-restrict-the-default-permissions-for-member-users).</li><li>Pode levar até 15 minutos ou out/entrar antes de administrador de assinatura revogações e adições de associação de função tenham efeito.</li></ul> |
