---
title: Rotação de certificado para o banco de dados SQL do Azure & SQL Instância Gerenciada
description: Saiba mais sobre as alterações futuras de alterações de certificado raiz que afetarão o banco de dados SQL do Azure e o Azure SQL Instância Gerenciada
author: srdan-bozovic-msft
ms.author: srbozovi
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
ms.date: 09/13/2020
ms.openlocfilehash: 96367b143711c4ec5f3f8d609f048c72c6fded16
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590844"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-sql-database--sql-managed-instance"></a>Noções básicas sobre as alterações na autoridade de certificação raiz para o banco de dados SQL do Azure & SQL Instância Gerenciada

O banco de dados SQL do Azure & SQL Instância Gerenciada mudará o certificado raiz para o aplicativo/driver cliente habilitado com SSL, usado para estabelecer uma conexão TDS segura. O [certificado raiz atual](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) está definido para expirar em 26 de outubro de 2020 como parte das práticas recomendadas de manutenção e segurança padrão. Este artigo fornece mais detalhes sobre as alterações futuras, os recursos que serão afetados e as etapas necessárias para garantir que seu aplicativo mantenha a conectividade com o servidor de banco de dados.

## <a name="what-update-is-going-to-happen"></a>Qual atualização vai acontecer?

[Fórum do navegador da AC (autoridade](https://cabforum.org/) de certificação) publicado recentemente relatórios de vários certificados emitidos por fornecedores de autoridade de certificação para não estar em conformidade.

De acordo com os requisitos de conformidade do setor, os fornecedores de CA começaram a revogar certificados de CA para CAs não compatíveis, exigindo que os servidores usem certificados emitidos por CAs compatíveis e assinados por certificados de autoridade de certificação dessas CAs em conformidade. Como o banco de dados SQL do Azure & SQL Instância Gerenciada atualmente usa um desses certificados não compatíveis, que aplicativos cliente usam para validar suas conexões SSL, precisamos garantir que as ações apropriadas sejam tomadas (descritas abaixo) para minimizar o impacto potencial nos servidores SQL do Azure.

O novo certificado será usado a partir de 26 de outubro de 2020. Se você usar a validação completa do certificado do servidor ao conectar-se de um cliente SQL (TrustServerCertificate = true), será necessário garantir que o cliente SQL seja capaz de validar o novo certificado raiz antes de 26 de outubro de 2020.

## <a name="how-do-i-know-if-my-application-might-be-affected"></a>Como fazer saber se meu aplicativo pode ser afetado?

Todos os aplicativos que usam SSL/TLS e verificam se o certificado raiz precisa atualizar o certificado raiz para se conectar ao banco de dados SQL do Azure & SQL Instância Gerenciada. 

Se você não estiver usando o SSL/TLS no momento, não haverá nenhum impacto na disponibilidade do aplicativo. Você pode verificar se o aplicativo cliente está tentando verificar o certificado raiz examinando a cadeia de conexão. Se TrustServerCertificate for definido explicitamente como true, você não será afetado.

Se o seu driver de cliente utilizar o repositório de certificados do sistema operacional, como a maioria dos drivers, e seu sistema operacional for mantido regularmente, essa alteração provavelmente não afetará você, pois o certificado raiz para o qual estamos mudando já estará disponível em seu repositório de certificados raiz confiáveis. Verifique se há Baltimore CyberDigiCert GlobalRoot G2 e valide se ele está presente.

Se o seu driver de cliente utilizar o repositório de certificados de arquivo local, para evitar que a disponibilidade do aplicativo seja interrompida devido aos certificados serem revogados inesperadamente ou para atualizar um certificado, que foi revogado, consulte a seção [**o que preciso fazer para manter a conectividade**](./ssl-root-certificate-expiring.md#what-do-i-need-to-do-to-maintain-connectivity) .

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>O que preciso fazer para manter a conectividade

Para evitar que a disponibilidade do aplicativo seja interrompida devido aos certificados serem revogados inesperadamente ou para atualizar um certificado, que foi revogado, siga as etapas abaixo:

*   Baixe Baltimore CyberTrust root & DigiCert GlobalRoot G2 AC raiz dos links abaixo:
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   Gere um repositório de certificados de autoridade de certificação combinado com os certificados **BaltimoreCyberTrustRoot** e **DigiCertGlobalRootG2** incluídos.

## <a name="what-can-be-the-impact"></a>Qual pode ser o impacto?
Se você estiver validando certificados de servidor conforme documentado aqui, a disponibilidade do aplicativo poderá ser interrompida, pois o banco de dados não estará acessível. Dependendo do seu aplicativo, você pode receber uma variedade de mensagens de erro, incluindo, mas não se limitando a:
*   Certificado inválido/certificado revogado
*   A conexão atingiu o tempo limite
*   Erro se aplicável

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>Se eu não estiver usando SSL/TLS, ainda precisarei atualizar a autoridade de certificação raiz?
Nenhuma ação relacionada a essa alteração será necessária se você não estiver usando SSL/TLS. Ainda assim, você deve definir um plano para começar a usar a versão mais recente do TLS conforme planejamos para a imposição de TLS em um futuro próximo.

### <a name="what-will-happen-if-i-do-not-update-the-root-certificate-before-october-26-2020"></a>O que acontecerá se eu não atualizar o certificado raiz antes de 26 de outubro de 2020?
Se você não atualizar o certificado raiz antes de 30 de novembro de 2020, seus aplicativos que se conectam via SSL/TLS e a verificação do certificado raiz não poderão se comunicar com o banco de dados SQL do Azure & o SQL Instância Gerenciada e o aplicativo terão problemas de conectividade com o banco de dados SQL do Azure & o SQL Instância Gerenciada.

### <a name="do-i-need-to-plan-a-maintenance-downtime-for-this-changebr"></a>É necessário planejar um tempo de inatividade de manutenção para essa alteração?<BR>
Não. Como a alteração aqui só está no lado do cliente para se conectar ao servidor, não há nenhum tempo de inatividade de manutenção necessário aqui para essa alteração.

### <a name="what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-october-26-2020"></a>E se eu não conseguir um tempo de inatividade agendado para essa alteração antes de 26 de outubro de 2020?
Como os clientes usados para se conectar ao servidor precisam atualizar as informações do certificado, conforme descrito na seção corrigir [aqui](./ssl-root-certificate-expiring.md#what-do-i-need-to-do-to-maintain-connectivity), não precisamos de um tempo de inatividade para o servidor nesse caso.

### <a name="if-i-create-a-new-server-after-november-30-2020-will-i-be-impacted"></a>Se eu criar um novo servidor após 30 de novembro de 2020, serei afetado?
Para servidores criados após 26 de outubro de 2020, você pode usar o certificado emitido recentemente para seus aplicativos se conectarem usando SSL.

### <a name="how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>Com que frequência o Microsoft atualiza seus certificados ou qual é a política de expiração?
Esses certificados usados pelo banco de dados SQL do Azure & SQL Instância Gerenciada são fornecidos por autoridades de certificação confiáveis (CA). Portanto, o suporte desses certificados no banco de dados SQL do Azure & SQL Instância Gerenciada está vinculado ao suporte desses certificados pela CA. No entanto, como nesse caso, pode haver bugs imprevistos nesses certificados predefinidos, que precisam ser corrigidos no início.

### <a name="if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-primary-server-or-all-the-read-replicas"></a>Se eu estiver usando réplicas de leitura, preciso executar essa atualização somente no servidor primário ou em todas as réplicas de leitura?
Como essa atualização é uma alteração no lado do cliente, se o cliente usado para ler dados do servidor de réplica, precisaremos aplicar as alterações para esses clientes também. 

### <a name="do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>Temos uma consulta do lado do servidor para verificar se o SSL está sendo usado?
Como essa configuração é do lado do cliente, as informações não estão disponíveis no lado do servidor.

### <a name="what-if-i-have-further-questions"></a>E se eu tiver outras dúvidas?
Se você tiver um plano de suporte e precisar de ajuda técnica, crie uma solicitação de suporte do Azure, consulte [como criar uma solicitação de suporte do Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md).