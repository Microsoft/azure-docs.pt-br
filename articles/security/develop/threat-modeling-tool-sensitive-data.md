---
title: Dados confidenciais - Microsoft Threat Modeling Tool - Azure | Microsoft Docs
description: Saiba mais sobre a mitigação de dados confidenciais no Threat Modeling Tool. Veja informações de mitigação e exiba exemplos de código.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 0bcbe35fc6d9f104325bec8a3404ad57a6376cf2
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94518117"
---
# <a name="security-frame-sensitive-data--mitigations"></a>Estrutura de segurança: Dados confidenciais | Atenuações 
| Produto/Serviço | Artigo |
| --------------- | ------- |
| **Limite de confiança de computador** | <ul><li>[Garantir que os binários sejam obscurecidos se contiverem informações confidenciais](#binaries-info)</li><li>[Considere usar o EFS (sistema de arquivos criptografados) é usado para proteger dados confidenciais específicos do usuário](#efs-user)</li><li>[Garantir que os dados confidenciais armazenados pelo aplicativo no sistema de arquivos sejam criptografados](#filesystem)</li></ul> | 
| **Aplicativo Web** | <ul><li>[Garantir que o conteúdo confidencial não seja armazenado em cache no navegador](#cache-browser)</li><li>[Criptografar as seções dos arquivos de configuração do aplicativo Web que contêm dados confidenciais](#encrypt-data)</li><li>[Desabilitar explicitamente o atributo HTML de preenchimento automático em formulários e entradas confidenciais](#autocomplete-input)</li><li>[Garantir que os dados confidenciais exibidos na tela do usuário sejam mascarados](#data-mask)</li></ul> | 
| **Backup de banco de dados** | <ul><li>[Implementar a máscara de dados dinâmicos para limitar a exposição de dados confidenciais usuários sem privilégios](#dynamic-users)</li><li>[Verifique se as senhas estão armazenadas no formato de hash salted](#salted-hash)</li><li>[Certifique-se de que os dados confidenciais nas colunas de dados estejam criptografados](#db-encrypted)</li><li>[Verifique se a criptografia no nível do banco de dados (TDE) está habilitada](#tde-enabled)</li><li>[Garantir que os backups de banco de dados sejam criptografados](#backup)</li></ul> | 
| **API da Web** | <ul><li>[Garantir que os dados confidenciais relevantes para a API da Web não sejam armazenados no armazenamento do navegador](#api-browser)</li></ul> | 
| Azure Document DB | <ul><li>[Criptografar dados confidenciais armazenados no Azure Cosmos DB](#encrypt-docdb)</li></ul> | 
| **Limite de confiança da VM da IaaS do Azure** | <ul><li>[Usar Azure Disk Encryption para criptografar discos usados por máquinas virtuais](#disk-vm)</li></ul> | 
| **Limite de confiança do Service Fabric** | <ul><li>[Criptografar segredos nos aplicativos do Service Fabric](#fabric-apps)</li></ul> | 
| **Dynamics CRM** | <ul><li>[Executar a modelagem de segurança e usar unidades de negócios/equipes onde for necessário](#modeling-teams)</li><li>[Minimizar o acesso para compartilhar recursos em entidades críticas](#entities)</li><li>[Treine os usuários sobre os riscos associados ao recurso de compartilhamento do Dynamics CRM e boas práticas de segurança](#good-practices)</li><li>[Incluir uma regra de padrões de desenvolvimento proscreva-se mostrando detalhes da configuração no gerenciamento de exceções](#exception-mgmt)</li></ul> | 
| **Armazenamento do Azure** | <ul><li>[Usar o Azure Storage Service Encryption (SSE) para dados em repouso (visualização)](#sse-preview)</li><li>[Criptografar o cliente para armazenar dados confidenciais no Armazenamento do Azure](#client-storage)</li></ul> | 
| **Cliente móvel** | <ul><li>[Criptografar os dados confidenciais ou de informações de identificação pessoal (PII) gravados no armazenamento local de telefones](#pii-phones)</li><li>[Ofuscar binários gerados antes de distribuir aos usuários finais](#binaries-end)</li></ul> | 
| **WCF** | <ul><li>[Definir clientCredentialtype como certificado ou Windows](#cert)</li><li>[O modo de segurança do WCF não está habilitado](#security)</li></ul> | 

## <a name="ensure-that-binaries-are-obfuscated-if-they-contain-sensitive-information"></a><a id="binaries-info"></a>Garantir que os binários sejam obscurecidos se contiverem informações confidenciais

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança de máquina | 
| **Fase do SDL**               | Implantação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Garanta que os binários sejam obscurecidos se eles contiverem informações confidenciais, como segredos comerciais ou uma lógica de negócios confidenciais que não deve ser revertida. O objetivo é impedir a engenharia reversa de assemblies. Ferramentas como o `CryptoObfuscator` podem ser utilizadas para isso. |

## <a name="consider-using-encrypted-file-system-efs-is-used-to-protect-confidential-user-specific-data"></a><a id="efs-user"></a>Considerar a utilização do sistema de arquivos criptografados (EFS) para proteger dados confidenciais específicos dos usuários

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança de máquina | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Considere utilizar o sistema de arquivos criptografados (EFS) para proteger dados confidenciais específicos dos usuários contra pessoas mal intencionadas que tenham acesso físico ao computador. |

## <a name="ensure-that-sensitive-data-stored-by-the-application-on-the-file-system-is-encrypted"></a><a id="filesystem"></a>Garantir que os dados confidenciais armazenados pelo aplicativo no sistema de arquivos sejam criptografados

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança de máquina | 
| **Fase do SDL**               | Implantação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Garanta que os dados confidenciais armazenados pelo aplicativo no sistema de arquivos sejam criptografados (usando DPAPI, por exemplo), se o EFS não puder ser aplicado. |

## <a name="ensure-that-sensitive-content-is-not-cached-on-the-browser"></a><a id="cache-browser"></a>Garantir que conteúdos confidenciais não sejam armazenados em cache no navegador

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico, Formulários da Web, MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Os navegadores podem armazenar informações em seus caches e históricos. Esses arquivos armazenados em cache são salvos em uma pasta, como a pasta Arquivos de Internet Temporários no caso do Internet Explorer. Quando essas páginas são chamadas novamente, o navegador as exibe por meio do seu cache. Se informações confidenciais forem exibidas para o usuário (como seu endereço, detalhes de cartão de crédito, número do seguro social ou nome de usuário), essas informações poderão ser armazenadas no cache do navegador e, portanto, podem ser recuperadas por meio do exame do cache do navegador ou simplesmente pressionando o botão "voltar" do navegador. Defina o valor do cabeçalho de resposta de controle de cache como "no-Store" para todas as páginas. |

### <a name="example"></a>Exemplo
```XML
<configuration>
  <system.webServer>
   <httpProtocol>
    <customHeaders>
        <add name="Cache-Control" value="no-store" />
        <add name="Pragma" value="no-cache" />
        <add name="Expires" value="-1" />
    </customHeaders>
  </httpProtocol>
 </system.webServer>
</configuration>
```

### <a name="example"></a>Exemplo
Também é possível fazer isso usando um filtro. O exemplo abaixo pode ser usado: 
```csharp
public override void OnActionExecuting(ActionExecutingContext filterContext)
        {
            if (filterContext == null || (filterContext.HttpContext != null && filterContext.HttpContext.Response != null && filterContext.HttpContext.Response.IsRequestBeingRedirected))
            {
                //// Since this is MVC pipeline, this should never be null.
                return;
            }

            var attributes = filterContext.ActionDescriptor.GetCustomAttributes(typeof(System.Web.Mvc.OutputCacheAttribute), false);
            if (attributes == null || **Attributes**.Count() == 0)
            {
                filterContext.HttpContext.Response.Cache.SetNoStore();
                filterContext.HttpContext.Response.Cache.SetCacheability(HttpCacheability.NoCache);
                filterContext.HttpContext.Response.Cache.SetExpires(DateTime.UtcNow.AddHours(-1));
                if (!filterContext.IsChildAction)
                {
                    filterContext.HttpContext.Response.AppendHeader("Pragma", "no-cache");
                }
            }

            base.OnActionExecuting(filterContext);
        }
``` 

## <a name="encrypt-sections-of-web-apps-configuration-files-that-contain-sensitive-data"></a><a id="encrypt-data"></a>Criptografar as seções dos arquivos de configuração do aplicativo Web que contêm dados confidenciais

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Como Criptografar seções de configuração no ASP.NET 2.0 usando DPAPI](/previous-versions/msp-n-p/ff647398(v=pandp.10)), [Especificar um provedor de configuração protegido](/previous-versions/68ze1hb2(v=vs.140)), [Como usar o Azure Key Vault para proteger os segredos do aplicativo](/azure/architecture/multitenant-identity/web-api) |
| **Etapas** | Arquivos de configuração, tais como Web. config e appsettings.json geralmente são usados para armazenar informações confidenciais, incluindo nomes de usuários, senhas, cadeias de conexão de banco de dados e chaves de criptografia. Se você não proteger essas informações, o aplicativo ficará vulnerável a usuários mal-intencionados, que podem obter informações sigilosas, como nomes usuários e senhas de contas, nomes de bancos de dados e nomes de servidores. Com base no tipo de implantação (no Azure ou local), criptografe as seções confidenciais dos arquivos de configuração usando a DPAPI ou serviços, como o Azure Key Vault. |

## <a name="explicitly-disable-the-autocomplete-html-attribute-in-sensitive-forms-and-inputs"></a><a id="autocomplete-input"></a>Desabilitar explicitamente o atributo HTML de preenchimento automático em formulários e entradas com informações confidenciais

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN: o atributo de preenchimento automático](https://msdn.microsoft.com/library/ms533486(VS.85).aspx), [Usando o preenchimento automático com HTML](/previous-versions/windows/internet-explorer/ie-developer/), [Vulnerabilidade de limpeza de HTML](/security-updates/SecurityBulletins/2010/ms10-071), [Preencher automaticamente de novo?](https://blog.mindedsecurity.com/2011/10/autocompleteagain.html) |
| **Etapas** | O atributo de preenchimento automático especifica se o preenchimento automático estará ativado ou desativado em um formulário. Quando o preenchimento automático está ativado, o navegador preenche automaticamente o formulário com os valores inseridos pelo usuário em uma ocasião anterior. Por exemplo, quando um novo nome de usuário e senha são inseridos em um formulário e o formulário é enviado, o navegador pergunta se a senha deve ser salva. Da próxima vez que o formulário for exibido, o nome de usuário e a senha serão preenchidos automaticamente ou quando o nome de usuário for digitado. Um invasor com acesso local poderia obter o texto não criptografado da senha pelo cache do navegador. Por padrão, o preenchimento automático está habilitado e deve ser desabilitado explicitamente. |

### <a name="example"></a>Exemplo
```csharp
<form action="Login.aspx" method="post " autocomplete="off" >
      Social Security Number: <input type="text" name="ssn" />
      <input type="submit" value="Submit" />    
</form>
```

## <a name="ensure-that-sensitive-data-displayed-on-the-user-screen-is-masked"></a><a id="data-mask"></a>Garantir que os dados confidenciais exibidos na tela do usuário sejam mascarados

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicativo Web | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Dados confidenciais, como senhas, números de cartão de crédito, CPF, etc., devem ser mascarados quando forem exibidos na tela. Isso impede que pessoas não autorizadas tenham acesso a esses dados (evitando, por exemplo, que senhas sejam visualizadas por terceiros e que o pessoal do suporte veja o CPF dos usuários). Garanta que esses elementos de dados não estejam visíveis em texto sem formatação e sejam mascarados adequadamente. Você deve fazer isso quando aceitá-los como entradas (por exemplo, digitando type= "password") e quando eles forem novamente exibidos na tela (por exemplo, exibir apenas os últimos quatro dígitos do número do cartão de crédito). |

## <a name="implement-dynamic-data-masking-to-limit-sensitive-data-exposure-non-privileged-users"></a><a id="dynamic-users"></a>Implementar a máscara de dados dinâmicos para evitar a exposição de dados confidenciais a usuários sem privilégios

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Banco de dados | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | SQL Azure, OnPrem |
| **Atributos**              | Versão do SQL - V12, Versão do SQL - MsSQL2016 |
| **Referências**              | [Mascaramento de dados dinâmicos](/sql/relational-databases/security/dynamic-data-masking) |
| **Etapas** | A finalidade do mascaramento de dados dinâmicos é limitar a exposição de dados confidenciais, impedindo que os usuários que não devem ter acesso a esses dados os visualizem. O mascaramento de dados dinâmicos não pretende impedir que usuários de banco de dados se conectem diretamente ao banco de dados e executem consultas abrangentes que exponham dados confidenciais. Ele é complementar aos outros recursos de segurança do SQL Server (auditoria, criptografia, segurança no nível da linha, etc.). É extremamente recomendável usar o mascaramento de dados dinâmicos junto com outros recursos para melhor proteger ainda mais os dados confidenciais presentes no banco de dados. Observe que esse recurso tem suporte apenas a partir do SQL Server 2016 e do Banco de Dados SQL do Azure. |

## <a name="ensure-that-passwords-are-stored-in-salted-hash-format"></a><a id="salted-hash"></a>Garantir que as senhas sejam armazenadas em um formato hash salgado

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Banco de dados | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Hash de senha usando APIs de criptografia do .NET](https://docs.asp.net/en/latest/security/data-protection/consumer-apis/password-hashing.html) |
| **Etapas** | As senhas não devem ser armazenadas em bancos de dados de repositório de usuários personalizados. Os hashes de senha devem ser armazenados com valores de sal em vez disso. Garanta que o valor de sal do usuário seja sempre exclusivo e que você aplique b-crypt, s-crypt ou PBKDF2 antes de armazenar a senha, com uma iteração de fator de trabalho mínima de 150.000 loops para eliminar a possibilidade de forçamento bruto.| 

## <a name="ensure-that-sensitive-data-in-database-columns-is-encrypted"></a><a id="db-encrypted"></a>Garantir que os dados confidenciais nas colunas do banco de dados sejam criptografados

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Banco de dados | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Versão do SQL - Todas |
| **Referências**              | [Criptografando dados confidenciais no SQL Server](/previous-versions/sql/sql-server-2008-r2/ff848751(v=sql.105)), [Como: criptografar uma coluna de dados no SQL Server](/sql/relational-databases/security/encryption/encrypt-a-column-of-data), [Criptografar por certificado](/sql/t-sql/functions/encryptbycert-transact-sql) |
| **Etapas** | Dados confidenciais, como números de cartão de crédito, devem ser criptografados no banco de dados. Isso pode ser feito usando a criptografia no nível da coluna ou uma função de aplicativo que utilize as funções de criptografia. |

## <a name="ensure-that-database-level-encryption-tde-is-enabled"></a><a id="tde-enabled"></a>Garantir que a criptografia no nível do banco de dados (TDE) esteja habilitada

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Banco de dados | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Noções básicas sobre a TDE (Transparent Data Encryption) do SQL Server](/previous-versions/sql/sql-server-2008-r2/bb934049(v=sql.105)) |
| **Etapas** | O recurso Transparent Data Encryption do SQL Server ajuda a criptografar dados confidenciais em um banco de dados e protege as chaves usadas para criptografar os dados com um certificado. Isso impede que alguém sem as chaves use os dados. A TDE protege os dados “em repouso”, ou seja, os dados e arquivos de log. Fornece a capacidade de se adequar a muitas leis, regulamentos e diretrizes estabelecidos em vários setores. |

## <a name="ensure-that-database-backups-are-encrypted"></a><a id="backup"></a>Garantir que os backups de banco de dados estejam criptografados

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Banco de dados | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | SQL Azure, OnPrem |
| **Atributos**              | Versão do SQL - V12, Versão do SQL - MsSQL2014 |
| **Referências**              | [Criptografia do backup do banco de dados SQL](/sql/relational-databases/backup-restore/backup-encryption) |
| **Etapas** | O SQL Server tem a capacidade de criptografar os dados durante a criação de um backup. Especificar o algoritmo de criptografia e o criptografador (um certificado ou uma chave assimétrica) durante a criação de um backup permite criar um arquivo de backup criptografado. |

## <a name="ensure-that-sensitive-data-relevant-to-web-api-is-not-stored-in-browsers-storage"></a><a id="api-browser"></a>Garantir que os dados confidenciais relevantes para a API Web não sejam salvos no armazenamento do navegador

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | MVC 5, MVC 6 |
| **Atributos**              | Provedor de Identidade - ADFS, Provedor de Identidade - Azure AD |
| **Referências**              | N/D  |
| **Etapas** | <p>Em determinadas implementações, os artefatos confidenciais relevantes para a autenticação da API Web são salvos no armazenamento local do navegador. Por exemplo, os artefatos de autenticação do Azure AD, como adal.idtoken, adal.nonce.idtoken, adal.access.token.key, adal.token.keys, adal.state.login, adal.session.state, adal.expiration.key etc.</p><p>Todos esses artefatos estão disponíveis mesmo após sair ou depois que o navegador é fechado. Se um invasor obtiver acesso a esses artefatos, ele/ela poderá reutilizá-los para acessar os recursos protegidos (APIs). Garanta que os artefatos confidenciais referentes à API Web não sejam salvos no armazenamento do navegador. Quando o armazenamento no cliente for inevitável (como no caso dos aplicativos de página única (SPA), que aproveitam os fluxos do OpenIdConnect/OAuth implícitos e que precisam armazenar os tokens de acesso localmente), use opções de armazenamento sem persistência. Por exemplo, prefira SessionStorage a LocalStorage.</p>| 

### <a name="example"></a>Exemplo
O snippet do JavaScript abaixo pertence a uma biblioteca de autenticação personalizada que salva os artefatos de autenticação no armazenamento local. Implementações desse tipo devem ser evitadas. 
```javascript
ns.AuthHelper.Authenticate = function () {
window.config = {
instance: 'https://login.microsoftonline.com/',
tenant: ns.Configurations.Tenant,
clientId: ns.Configurations.AADApplicationClientID,
postLogoutRedirectUri: window.location.origin,
cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
};
```

## <a name="encrypt-sensitive-data-stored-in-cosmos-db"></a><a id="encrypt-docdb"></a>Criptografar dados confidenciais armazenados no Cosmos DB

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure Document DB | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Criptografe dados confidenciais no nível do aplicativo antes de armazenar em um banco de dados do documento ou de armazenar quaisquer dados confidenciais em outras soluções de armazenamento, como o Armazenamento do Azure ou o SQL Azure.| 

## <a name="use-azure-disk-encryption-to-encrypt-disks-used-by-virtual-machines"></a><a id="disk-vm"></a>Usar o Azure Disk Encryption para criptografar os discos utilizados por máquinas virtuais

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança da VM da IaaS do Azure | 
| **Fase do SDL**               | Implantação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Usando o Azure Disk Encryption para criptografar discos usados pelas máquinas virtuais](../../storage/blobs/security-recommendations.md#data-protection) |
| **Etapas** | <p>O Azure Disk Encryption é um novo recurso que, atualmente, está na versão de visualização. Esse recurso permite criptografar os discos do sistema operacional e de dados usados por uma Máquina Virtual IaaS. No Windows, as unidades são criptografadas usando a tecnologia de criptografia BitLocker padrão do setor. No Linux, os discos são criptografados usando a tecnologia DM-Crypt. Esse recurso é integrado ao Cofre de Chaves do Azure para permitir que você controle e gerencie as chaves de criptografia de disco. A solução Azure Disk Encryption é compatível com os três cenários de criptografia do cliente a seguir:</p><ul><li>Habilite a criptografia em novas VMs IaaS criadas de arquivos VHD criptografados pelo cliente e chaves de criptografia fornecidas pelo cliente, que são armazenados no Cofre de Chaves do Azure.</li><li>Habilite a criptografia em novas VMs IaaS criadas no Marketplace do Azure.</li><li>Habilite a criptografia em VMs IaaS existentes já em execução no Azure.</li></ul>| 

## <a name="encrypt-secrets-in-service-fabric-applications"></a><a id="fabric-apps"></a>Criptografar segredos nos aplicativos do Service Fabric

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança do Service Fabric | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Ambiente - Azure |
| **Referências**              | [Gerenciando segredos em aplicativos do Service Fabric](../../service-fabric/service-fabric-application-secret-management.md) |
| **Etapas** | Os segredos podem ser informações confidenciais, como cadeias de conexão de armazenamento, senhas ou outros valores que não devem ser tratados como texto sem formatação. Use o Azure Key Vault para gerenciar chaves e segredos em aplicativos do Service Fabric. |

## <a name="perform-security-modeling-and-use-business-unitsteams-where-required"></a><a id="modeling-teams"></a>Executar a modelagem de segurança e usar unidades de negócios/equipes onde for necessário

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Execute a modelagem de segurança e use unidades de negócios/equipes onde for necessário. |

## <a name="minimize-access-to-share-feature-on-critical-entities"></a><a id="entities"></a>Minimizar o acesso ao recurso de compartilhamento em entidades críticas

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase do SDL**               | Implantação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Minimizar o acesso ao recurso de compartilhamento em entidades críticas |

## <a name="train-users-on-the-risks-associated-with-the-dynamics-crm-share-feature-and-good-security-practices"></a><a id="good-practices"></a>Instruir os usuários sobre os riscos associados ao recurso de compartilhamento do Dynamics CRM e as práticas recomendadas de segurança

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase do SDL**               | Implantação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Instruir os usuários sobre os riscos associados ao recurso de compartilhamento do Dynamics CRM e as práticas recomendadas de segurança |

## <a name="include-a-development-standards-rule-proscribing-showing-config-details-in-exception-management"></a><a id="exception-mgmt"></a>Incluir uma regra de padrões de desenvolvimento que impeça a exibição dos detalhes de configuração do gerenciamento de exceções

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase do SDL**               | Implantação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Etapas** | Inclua uma regra de padrões de desenvolvimento que impeça a exibição dos detalhes de configuração do gerenciamento de exceções. Teste isso como parte da inspeção periódica ou das revisões de código.|

## <a name="use-azure-storage-service-encryption-sse-for-data-at-rest-preview"></a><a id="sse-preview"></a>Usar o Azure Storage Service Encryption (SSE) para dados em repouso (visualização)

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Armazenamento do Azure | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | StorageType - Blob |
| **Referências**              | [Criptografia do Serviço de Armazenamento do Azure para dados em repouso (visualização)](../../storage/common/storage-service-encryption.md) |
| **Etapas** | <p>A SSE (Criptografia do Serviço de Armazenamento) do Azure para dados em repouso ajuda a proteger seus dados a fim de atender aos compromissos de conformidade e segurança da organização. Com esse recurso, o Armazenamento do Azure criptografa automaticamente seus dados antes de persistir no armazenamento e os descriptografa antes da recuperação. A criptografia, descriptografia e o gerenciamento de chaves é totalmente transparente para os usuários. A SSE se aplica somente aos blobs de blocos, aos blobs de páginas e aos blobs de acréscimo. Os outros tipos de dados, incluindo tabelas, filas e arquivos, não serão criptografados.</p><p>Fluxo de trabalho de criptografia e descriptografia:</p><ul><li>O cliente habilita a criptografia na conta de armazenamento.</li><li>Quando o cliente grava os novos dados (PUT Blob, PUT Block, PUT Page etc.) no armazenamento de blobs, todas as gravações são criptografadas com a criptografia AES de 256 bits, uma das codificações de bloco mais fortes disponíveis.</li><li>Quando o cliente precisa acessar os dados (GET Blob etc.), esses dados são descriptografados automaticamente antes de serem retornados para o usuário.</li><li>Se a criptografia estiver desabilitada, as novas gravações não serão criptografadas, e os dados criptografados existentes permanecerão criptografados até que sejam regravados pelo usuário. Enquanto a criptografia estiver habilitada, as gravações no Armazenamento de Blobs serão criptografadas. O estado dos dados não muda quando o usuário habilita/desabilita a criptografia na conta de armazenamento.</li><li>Todas as chaves de criptografia são armazenadas, criptografadas e gerenciadas pela Microsoft.</li></ul><p>No momento, as chaves usadas para criptografia são gerenciadas pela Microsoft. Podemos gerar as chaves originalmente e gerenciar o armazenamento seguro delas, bem como sua rotatividade regular, conforme definido pela política interna da Microsoft. No futuro, os usuários poderão gerenciar suas próprias chaves de criptografia e fornecer um caminho de migração de chaves gerenciadas pela Microsoft para as chaves gerenciadas pelos usuários.</p>| 

## <a name="use-client-side-encryption-to-store-sensitive-data-in-azure-storage"></a><a id="client-storage"></a>Criptografar o cliente para armazenar dados confidenciais no Armazenamento do Azure

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Armazenamento do Azure | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Criptografia no cliente e Azure Key Vault para o Armazenamento do Microsoft Azure](../../storage/common/storage-client-side-encryption.md), [Tutorial: criptografar e descriptografar blobs no Armazenamento do Microsoft Azure usando o Azure Key Vault](../../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md), [Armazenando dados com segurança no Armazenamento de Blobs do Azure com as extensões de criptografia do Azure](/archive/blogs/partnercatalystteam/storing-data-securely-in-azure-blob-storage-with-azure-encryption-extensions) |
| **Etapas** | <p>O pacote Nuget da Biblioteca de Clientes do Armazenamento do Azure para .NET dá suporte à criptografia de dados em aplicativos clientes antes do carregamento para o Armazenamento do Azure e à descriptografia de dados durante o download para o cliente. A biblioteca também dá suporte à integração com o Cofre de Chaves do Azure para o gerenciamento de chaves de contas de armazenamento. Aqui está uma breve descrição de como funciona a criptografia do lado do cliente:</p><ul><li>O SDK do cliente do Armazenamento do Azure gera uma CEK (chave de criptografia de conteúdo), que é uma chave simétrica de uso único.</li><li>Os dados do cliente são criptografados com essa CEK</li><li>O CEK é empacotada (criptografada) usando o KEK (Chave de Criptografia de Chave). A KEK é identificada por um identificador de chave e pode ser um par de chaves assimétricas ou uma chave simétrica, podendo ser gerenciada localmente ou armazenada no Cofre da Chave do Azure. O cliente de Armazenamento, por si só, nunca tem acesso à KEK. Ele simplesmente chama o algoritmo de quebra de chave fornecido pelo Cofre da Chave. Os clientes podem escolher usar provedores personalizados para encapsular/desencapsular a chave, se desejado.</li><li>Os dados criptografados, em seguida, são carregados para o serviço de Armazenamento do Azure. Consulte os links na seção de referências para obter detalhes sobre a implementação de nível baixo.</li></ul>|

## <a name="encrypt-sensitive-or-pii-data-written-to-phones-local-storage"></a><a id="pii-phones"></a>Criptografar os dados confidenciais ou de informações de identificação pessoal (PII) gravados no armazenamento local de telefones

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente móvel | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genéricas, Xamarin  |
| **Atributos**              | N/D  |
| **Referências**              | [Gerenciar configurações e recursos em dispositivos com as políticas do Microsoft Intune](/mem/intune/configuration/), [Keychain Valet](https://components.xamarin.com/view/square.valet) |
| **Etapas** | <p>Se o aplicativo gravar informações confidenciais, como informações de identificação pessoal do usuário (email, número de telefone, nome, sobrenome, preferências etc.), no sistema de arquivos do dispositivo móvel, ele precisará ser criptografado antes de gravar dados no sistema de arquivos local. Se o aplicativo for um aplicativo empresarial, verifique a possibilidade de publicá-lo usando o Windows Intune.</p>|

### <a name="example"></a>Exemplo
O Intune pode ser configurado com as seguintes políticas de segurança para proteger dados confidenciais: 
```csharp
Require encryption on mobile device    
Require encryption on storage cards
Allow screen capture
```

### <a name="example"></a>Exemplo
Se o aplicativo não for empresarial, use o armazenamento de chaves ou os conjuntos de chaves fornecidos pela plataforma para armazenar chaves de criptografia, com os quais a operação de criptografia pode ser executada no sistema de arquivos. O snippet de código a seguir mostra como acessar a chave do conjunto de chaves usando Xamarin: 
```csharp
        protected static string EncryptionKey
        {
            get
            {
                if (String.IsNullOrEmpty(_Key))
                {
                    var query = new SecRecord(SecKind.GenericPassword);
                    query.Service = NSBundle.MainBundle.BundleIdentifier;
                    query.Account = "UniqueID";

                    NSData uniqueId = SecKeyChain.QueryAsData(query);
                    if (uniqueId == null)
                    {
                        query.ValueData = NSData.FromString(System.Guid.NewGuid().ToString());
                        var err = SecKeyChain.Add(query);
                        _Key = query.ValueData.ToString();
                    }
                    else
                    {
                        _Key = uniqueId.ToString();
                    }
                }

                return _Key;
            }
        }
```

## <a name="obfuscate-generated-binaries-before-distributing-to-end-users"></a><a id="binaries-end"></a>Obscurecer os binários gerados antes de distribuir os dispositivos para os usuários finais

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente móvel | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Crypto Obfuscator para .NET](https://www.ssware.com/cryptoobfuscator/obfuscator-net.htm) |
| **Etapas** | Os binários gerados (assemblies no apk) devem ser ocultados para impedir a engenharia reversa de assemblies. Ferramentas como o `CryptoObfuscator` podem ser usadas para isso. |

## <a name="set-clientcredentialtype-to-certificate-or-windows"></a><a id="cert"></a>Definir clientCredentialType para o certificado ou o Windows

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | .NET Framework 3 |
| **Atributos**              | N/D  |
| **Referências**              | [Fortify](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_token) |
| **Etapas** | Usar o token UserName com um texto de senha sem formatação em um canal não criptografado expõe a senha para invasores que podem descobrir as mensagens do SOAP. Os provedores de serviços que usam o token UserName podem aceitar senhas enviadas em texto sem formatação. Enviar textos de senha sem formatação por um canal não criptografado pode expor a credencial para invasores que podem descobrir a mensagem do SOAP. | 

### <a name="example"></a>Exemplo
A configuração de provedor de serviço do WCF mostrada abaixo usa o token de nome de usuário: 
```
<security mode="Message"> 
<message clientCredentialType="UserName" />
``` 
Defina clientCredentialType para o certificado ou o Windows. 

## <a name="wcf-security-mode-is-not-enabled"></a><a id="security"></a>O modo de segurança do WCF não está habilitado

| Title                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Build |  
| **Tecnologias aplicáveis** | Genérico, .NET Framework 3 |
| **Atributos**              | Modo de segurança - Transport, Modo de segurança - Message |
| **Referências**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference), [Noções básicas da segurança do WCF (CoDe Magazine)](https://www.codemag.com/article/0611051) |
| **Etapas** | Nenhuma segurança foi definida para transporte ou mensagens. Os aplicativos que transmitem mensagens sem segurança de transporte ou de mensagem não podem garantir a integridade ou a confidenciabilidade das mensagens. Quando uma associação de segurança do WCF é definida como None, as seguranças de transporte e de mensagem são desabilitadas. |

### <a name="example"></a>Exemplo
A configuração mostrada abaixo define o modo de segurança como None. 
```
<system.serviceModel> 
  <bindings> 
    <wsHttpBinding> 
      <binding name=""MyBinding""> 
        <security mode=""None""/> 
      </binding> 
  </bindings> 
</system.serviceModel> 
```

### <a name="example"></a>Exemplo
Há cinco modos de segurança disponíveis para todas as associações de serviço, são eles: 
* nenhuma. desativa a segurança. 
* Transport: usa a segurança de transporte para a proteção de autenticações e mensagens. 
* Message. usa a segurança de mensagem para a proteção de autenticações e mensagens. 
* Both: permite que você defina configurações de transporte e mensagem (somente o MSMQ oferece suporte a esse modo). 
* TransportWithMessageCredential: as credenciais são transmitidas com a mensagem, e a proteção de mensagem e a autenticação de servidor são fornecidas pela camada de transporte. 
* TransportCredentialOnly: as credenciais do cliente são transmitidas com a camada de transporte e nenhuma proteção de mensagem é aplicada. Use segurança de transporte e de mensagem para proteger a integridade e a confidencialidade das mensagens. A configuração abaixo solicita que o serviço use a segurança de transporte com as credenciais de mensagem.
  ```
  <system.serviceModel>
  <bindings>
    <wsHttpBinding>
    <binding name=""MyBinding""> 
    <security mode=""TransportWithMessageCredential""/> 
    <message clientCredentialType=""Windows""/> 
    </binding> 
  </bindings> 
  </system.serviceModel> 
  ```