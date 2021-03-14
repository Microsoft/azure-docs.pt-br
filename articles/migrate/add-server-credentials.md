---
title: Forneça credenciais de servidor para descobrir aplicativos, dependências e SQL Server instâncias e bancos de dados
description: Saiba como fornecer credenciais de servidor no Gerenciador de configurações de dispositivo
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 01/26/2021
ms.openlocfilehash: 2359855ce3949eb022a03f6e8e2dbc05f98907db
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054445"
---
# <a name="provide-server-credentials-to-discover-applications-dependencies-and-sql-server-instances-and-databases"></a>Forneça credenciais de servidor para descobrir aplicativos, dependências e SQL Server instâncias e bancos de dados

Siga este artigo para saber como adicionar várias credenciais de servidor no Gerenciador de configuração de dispositivo para executar o inventário de software (descobrir aplicativos instalados), a análise de dependência sem agente e descobrir SQL Server instâncias e bancos de dados.

> [!Note]
> A descoberta e a avaliação de instâncias de SQL Server e bancos de dados em execução em seu ambiente VMware agora estão em versão prévia. Para experimentar esse recurso, use [**este link**](https://aka.ms/AzureMigrate/SQL) para criar um projeto na região **leste da Austrália** . Se você já tiver um projeto no leste da Austrália e quiser experimentar esse recurso, verifique se você concluiu esses [**pré-requisitos**](how-to-discover-sql-existing-project.md) no Portal.

O [dispositivo de migrações para Azure](migrate-appliance.md) é um dispositivo leve usado pelas migrações para Azure: avaliação do servidor para descobrir servidores locais em execução no ambiente VMware e enviar metadados de configuração e desempenho do servidor para o Azure. O dispositivo também pode ser usado para executar o inventário de software, a análise de dependência sem agente e a descoberta de instâncias de SQL Server e bancos de dados.

Se você quiser aproveitar esses recursos, poderá fornecer credenciais de servidor seguindo as etapas abaixo. O dispositivo tentará mapear automaticamente as credenciais para os servidores para executar os recursos de descoberta.

## <a name="add-credentials-for-servers-running-in-vmware-environment"></a>Adicionar credenciais para servidores em execução no ambiente VMware

### <a name="types-of-server-credentials-supported"></a>Tipos de credenciais de servidor com suporte

Você pode adicionar várias credenciais de servidor no Gerenciador de configuração de dispositivo que pode ser domínio, não domínio (Windows ou Linux) ou SQL Server credenciais de autenticação.

Os tipos de credenciais de servidor com suporte são listados na tabela a seguir:

Tipo de credenciais | Descrição
--- | ---
**Credenciais de domínio** | Você pode adicionar **credenciais de domínio** selecionando a opção na lista suspensa em **Adicionar credenciais** modal. <br/><br/> Para fornecer credenciais de domínio, você precisa especificar o **nome de domínio** que deve ser fornecido no formato FQDN (por exemplo, prod.Corp.contoso.com). <br/><br/> Você também precisa especificar um nome amigável para credenciais, nome de usuário e senha. <br/><br/> As credenciais de domínio adicionadas serão validadas automaticamente para autenticidade em relação à Active Directory do domínio. Isso é para evitar qualquer bloqueio de conta quando o dispositivo tentar mapear as credenciais de domínio nos servidores descobertos. <br/><br/> O dispositivo não tentará mapear as credenciais de domínio que falharam na validação. Você precisa ter pelo menos uma credencial de domínio validada com êxito ou pelo menos uma credencial que não seja de domínio para prosseguir com o inventário de software.<br/><br/>As credenciais de domínio mapeadas automaticamente nos servidores Windows serão usadas para executar o inventário de software e também podem ser usadas para descobrir SQL Server instâncias e bancos _de dados (se você tiver configurado o modo de autenticação do Windows em seus servidores SQL)_.<br/> [Saiba mais](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authentication-in-sql-server) sobre os tipos de modos de autenticação com suporte nos SQL Servers.
**Credenciais de não domínio (Windows/Linux)** | Você pode adicionar o **Windows (não domínio)** ou **Linux (não domínio)** selecionando a opção necessária na lista suspensa em **Adicionar credenciais** modal. <br/><br/> Você precisa especificar um nome amigável para credenciais, nome de usuário e senha.
**SQL Server credenciais de autenticação** | Você pode adicionar **SQL Server** credenciais de autenticação selecionando a opção na lista suspensa em **Adicionar credenciais** modal. <br/><br/> Você precisa especificar um nome amigável para credenciais, nome de usuário e senha. <br/><br/> Você pode adicionar esse tipo de credenciais para descobrir SQL Server instâncias e bancos de dados em execução em seu ambiente VMware, se você tiver configurado SQL Server modo de autenticação em seus SQL Servers.<br/> [Saiba mais](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authentication-in-sql-server) sobre os tipos de modos de autenticação com suporte nos SQL Servers.<br/><br/> Você precisa fornecer pelo menos uma credencial de domínio validada com êxito ou pelo menos uma credencial do Windows (não domínio) para que o dispositivo possa concluir o inventário de software para descobrir o SQL instalado nos servidores antes de usar as credenciais de autenticação SQL Server para descobrir as instâncias de SQL Server e bancos de dados.

Verifique as permissões necessárias nas credenciais do Windows/Linux para executar o inventário de software, a análise de dependência sem agente e descobrir SQL Server instâncias e bancos de dados.

### <a name="required-permissions"></a>Permissões necessárias

A tabela a seguir lista as permissões necessárias nas credenciais do servidor fornecidas no dispositivo para executar os respectivos recursos:

Recurso | Credenciais do Windows | Credenciais do Linux
---| ---| ---
**Inventário de software** | Conta de usuário convidado | Conta de usuário regular/normal (permissões de acesso não sudo)
**Descoberta de instâncias de SQL Server e bancos de dados** | Conta de usuário que é membro da função de servidor sysadmin. | _Sem suporte no momento_
**Análise de dependência sem agente** | Conta de domínio ou não domínio (local) com permissões administrativas | Conta de usuário raiz ou <br/> uma conta com essas permissões em arquivos/bin/netstat e/bin/ls: CAP_DAC_READ_SEARCH e CAP_SYS_PTRACE.<br/><br/> Defina esses recursos usando os seguintes comandos: <br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/ls<br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/netstat

### <a name="recommended-practices-to-provide-credentials"></a>Práticas recomendadas para fornecer credenciais

- É recomendável criar uma conta de usuário de domínio dedicado com as [permissões necessárias](add-server-credentials.md#required-permissions), que tem como escopo executar o inventário de software, a análise de dependência sem agente e a descoberta de instâncias de SQL Server e bancos de dados nos servidores desejados.
- É recomendável fornecer pelo menos uma credencial de domínio validada com êxito ou pelo menos uma credencial que não seja de domínio para iniciar o inventário de software.
- Para descobrir SQL Server instâncias e bancos de dados, você pode fornecer credenciais de domínio, se tiver configurado o modo de autenticação do Windows em seus servidores SQL.
-  Você também pode fornecer SQL Server credenciais de autenticação se tiver configurado SQL Server modo de autenticação em seus SQL Servers, mas é recomendável fornecer pelo menos uma credencial de domínio validada com êxito ou pelo menos uma credencial do Windows (não domínio) para que o dispositivo possa primeiro concluir o inventário de software.

## <a name="credentials-handling-on-appliance"></a>Tratamento de credenciais no dispositivo

- Todas as credenciais fornecidas no Gerenciador de configuração do dispositivo são armazenadas localmente no servidor do dispositivo e não são enviadas para o Azure.
- As credenciais armazenadas no servidor do dispositivo são criptografadas usando a DPAPI (API de proteção de dados).
- Depois que você adicionar credenciais, o dispositivo tentará mapear automaticamente as credenciais para executar a descoberta nos respectivos servidores.
- O dispositivo usa as credenciais mapeadas automaticamente em um servidor para todos os ciclos de descoberta subsequentes até que as credenciais sejam capazes de buscar os dados de descoberta necessários. Se as credenciais parar de funcionar, o dispositivo tentará novamente mapear a partir da lista de credenciais adicionadas e continuará a descoberta em andamento no servidor.
- As credenciais de domínio adicionadas serão validadas automaticamente para autenticidade em relação à Active Directory do domínio. Isso é para evitar qualquer bloqueio de conta quando o dispositivo tentar mapear as credenciais de domínio nos servidores descobertos. O dispositivo não tentará mapear as credenciais de domínio que falharam na validação.
- Se o dispositivo não puder mapear nenhuma credencial de domínio ou não domínio em um servidor, você verá o status "credenciais não disponíveis" em relação ao servidor em seu projeto.

## <a name="next-steps"></a>Próximas etapas

Examine os tutoriais para [descoberta de servidores em execução em seu ambiente VMware](tutorial-discover-vmware.md)