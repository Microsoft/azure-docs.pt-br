---
title: Rotação de certificado para o banco de dados do Azure para PostgreSQL servidor único
description: Saiba mais sobre as alterações futuras de alterações de certificado raiz que afetarão o banco de dados do Azure para PostgreSQL servidor único
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 6dc687879eb646b4abd081b40bce292d20ff3186
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102123982"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-postgresql-single-server"></a>Noções básicas sobre as alterações na AC raiz alterar para o banco de dados do Azure para PostgreSQL servidor único

O banco de dados do Azure para PostgreSQL servidor único concluiu com êxito a alteração do certificado raiz em **15 de fevereiro de 2021 (02/15/2021)** como parte das práticas recomendadas de manutenção e segurança padrão. Este artigo fornece mais detalhes sobre as alterações, os recursos afetados e as etapas necessárias para garantir que seu aplicativo mantenha a conectividade com o servidor de banco de dados.

## <a name="why-root-certificate-update-is-required"></a>Por que a atualização do certificado raiz é necessária?

Os usuários do banco de dados do Azure para PostgreSQL só podem usar o certificado predefinido para se conectar ao servidor PostgreSQL, que está localizado [aqui](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem). No entanto, o [Fórum do navegador da AC (autoridade](https://cabforum.org/)   de certificação) publicou recentemente relatórios de vários certificados emitidos por fornecedores de autoridade de certificação para não estar em conformidade.

De acordo com os requisitos de conformidade do setor, os fornecedores de CA começaram a revogar certificados de CA para CAs não compatíveis, exigindo que os servidores usem certificados emitidos por CAs compatíveis e assinados por certificados de autoridade de certificação dessas CAs em conformidade. Como o banco de dados do Azure para MySQL usava um desses certificados não compatíveis, precisávamos girar o certificado para a versão compatível para minimizar a possível ameaça aos seus servidores MySQL.

O novo certificado é distribuído e em vigor a partir de 15 de fevereiro de 2021 (02/15/2021). 

## <a name="what-change-was-performed-on-february-15-2021-02152021"></a>Qual alteração foi executada em 15 de fevereiro de 2021 (02/15/2021)?

Em 15 de fevereiro de 2021, o [certificado raiz BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) foi substituído por uma **versão compatível** do mesmo [certificado raiz BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) para garantir que os clientes existentes não precisem alterar nada e não haja nenhum impacto em suas conexões com o servidor. Durante essa alteração, o [certificado raiz BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) **não foi substituído** por [DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) e essa alteração é adiada para permitir mais tempo para que os clientes façam a alteração.

## <a name="do-i-need-to-make-any-changes-on-my-client-to-maintain-connectivity"></a>É necessário fazer alterações no meu cliente para manter a conectividade?

Não há nenhuma alteração necessária no lado do cliente. Se você seguiu nossa recomendação anterior abaixo, ainda poderá continuar a se conectar, desde que o **certificado BaltimoreCyberTrustRoot não seja removido** do certificado de autoridade de certificação combinado. **É recomendável não remover o BaltimoreCyberTrustRoot do seu certificado de autoridade de certificação combinado até que haja um aviso adicional para manter a conectividade.**

### <a name="previous-recommendation"></a>Recomendação anterior

*   Baixe o BaltimoreCyberTrustRoot & DigiCertGlobalRootG2 root CA dos links abaixo:
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   Gere um repositório de certificados de autoridade de certificação combinado com os certificados **BaltimoreCyberTrustRoot** e **DigiCertGlobalRootG2** incluídos.
    *   Para usuários do Java (PostgreSQL JDBC) usando DefaultJavaSSLFactory, execute:

          ```console
          keytool -importcert -alias PostgreSQLServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
          ```

          ```console
          keytool -importcert -alias PostgreSQLServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
          ```

          Em seguida, substitua o arquivo de keystore original pelo novo gerado:
        *   System. SetProperty ("javax. net. SSL. trustStore", "path_to_truststore_file"); 
        *   System. SetProperty ("javax. net. SSL. trustStorePassword", "senha");
        
    *   Para usuários do .NET (Npgsql) no Windows, certifique-se de que **Baltimore Cybertrust raiz** e **DigiCert raiz global G2** existam no repositório de certificados do Windows, autoridades de certificação raiz confiáveis. Se não houver nenhum certificado, importe o certificado ausente.

        ![Banco de dados do Azure para PostgreSQL certificado .net](media/overview/netconnecter-cert.png)

    *   Para usuários do .NET (Npgsql) no Linux usando SSL_CERT_DIR, verifique se **BaltimoreCyberTrustRoot** e **DigiCertGlobalRootG2** existem no diretório indicado por SSL_CERT_DIR. Se não houver nenhum certificado, crie o arquivo de certificado ausente.

    *   Para outros usuários cliente do PostgreSQL, você pode mesclar dois arquivos de certificado de autoridade de certificação como este formato abaixo

        </br>-----INICIAR-----DE CERTIFICADO
 </br>(CA1 raiz: BaltimoreCyberTrustRoot. CRT. pem)
 </br>----------DE CERTIFICADO FINAL
 </br>-----INICIAR-----DE CERTIFICADO
 </br>(CA2 raiz: DigiCertGlobalRootG2. CRT. pem)
 </br>----------DE CERTIFICADO FINAL

*   Substitua o arquivo PEM da autoridade de certificação raiz original pelo arquivo de autoridade de certificação raiz combinado e reinicie o aplicativo/cliente.
*    No futuro, após o novo certificado implantado no lado do servidor, você poderá alterar o arquivo PEM da autoridade de certificação para DigiCertGlobalRootG2. CRT. PEM.

> [!NOTE]
> Não remova nem altere o **certificado Baltimore** até que a alteração de certificado seja feita. Enviaremos uma comunicação depois que a alteração for feita, após a qual é seguro descartar o certificado Baltimore. 

## <a name="why-was-baltimorecybertrustroot-certificate-not-replaced-to-digicertglobalrootg2-during-this-change-on-february-15-2021"></a>Por que o certificado BaltimoreCyberTrustRoot não foi substituído em DigiCertGlobalRootG2 durante essa alteração em 15 de fevereiro de 2021?

Avaliamos a preparação do cliente para essa alteração e percebemos que muitos clientes estavam procurando mais tempo de entrega para gerenciar essa alteração. No interesse de fornecer mais tempo de vida aos clientes para prontidão, decidimos adiar a alteração do certificado para DigiCertGlobalRootG2 por pelo menos um ano, fornecendo tempo de avanço suficiente aos clientes e usuários finais. 

Nossas recomendações para os usuários são, use as etapas mencionadas anteriormente para criar um certificado combinado e conectar-se ao servidor, mas não remova o certificado BaltimoreCyberTrustRoot até enviarmos uma comunicação para removê-lo. 

## <a name="what-if-we-removed-the-baltimorecybertrustroot-certificate"></a>E se removermos o certificado BaltimoreCyberTrustRoot?

Você começará a se conectar a erros de conectividade durante a conexão com o servidor do banco de dados do Azure para PostgreSQL. Você precisará configurar o SSL com o certificado [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) novamente para manter a conectividade.


## <a name="frequently-asked-questions"></a>Perguntas frequentes

###    <a name="1-if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. se eu não estiver usando SSL/TLS, ainda precisarei atualizar a autoridade de certificação raiz?
Nenhuma ação será necessária se você não estiver usando SSL/TLS. 

### <a name="2-if-i-am-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. se eu estiver usando SSL/TLS, preciso reiniciar meu servidor de banco de dados para atualizar a AC raiz?
Não, você não precisa reiniciar o servidor de banco de dados para começar a usar o novo certificado. Essa é uma alteração no lado do cliente e as conexões de entrada do cliente precisam usar o novo certificado para garantir que eles possam se conectar ao servidor de banco de dados.

### <a name="3-how-do-i-know-if-im-using-ssltls-with-root-certificate-verification"></a>3. Como fazer saber se estou usando SSL/TLS com a verificação de certificado raiz?

Você pode identificar se suas conexões verificam o certificado raiz examinando a cadeia de conexão.
-    Se a cadeia de conexão incluir `sslmode=verify-ca` ou `sslmode=verify-full` , você precisará atualizar o certificado.
-    Se a cadeia de conexão incluir `sslmode=disable` ,, `sslmode=allow` `sslmode=prefer` ou `sslmode=require` , você não precisará atualizar os certificados. 
-    Se a cadeia de conexão não especificar sslmode, você não precisará atualizar os certificados.

Se você estiver usando um cliente que abstrai a cadeia de conexão, examine a documentação do cliente para entender se ele verifica os certificados. Para entender o PostgreSQL sslmode, consulte as [descrições do modo SSL](https://www.postgresql.org/docs/11/libpq-ssl.html#ssl-mode-descriptions) na documentação do PostgreSQL.

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-postgresql"></a>4. qual é o impacto se estiver usando o serviço de aplicativo com o banco de dados do Azure para PostgreSQL?
Para os serviços de aplicativos do Azure, conectando-se ao banco de dados do Azure para PostgreSQL, podemos ter dois cenários possíveis e depende de como você está usando SSL com seu aplicativo.
*   Este novo certificado foi adicionado ao serviço de aplicativo no nível da plataforma. Se você estiver usando os certificados SSL incluídos na plataforma do serviço de aplicativo em seu aplicativo, nenhuma ação será necessária.
*   Se você estiver incluindo explicitamente o caminho para o arquivo de certificado SSL em seu código, precisará baixar o novo certificado e atualizar o código para usar o novo certificado. Um bom exemplo desse cenário é quando você usa contêineres personalizados no serviço de aplicativo como compartilhado na [documentação do serviço de aplicativo](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress)

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-postgresql"></a>5. qual é o impacto se estiver usando o AKS (serviços Kubernetess do Azure) com o banco de dados do Azure para PostgreSQL?
Se você estiver tentando se conectar ao banco de dados do Azure para PostgreSQL usando os AKS (serviços Kubernetess do Azure), ele será semelhante ao acesso de um ambiente de host de clientes dedicados. Consulte as etapas [aqui](../aks/ingress-own-tls.md).

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-postgresql"></a>6. qual é o impacto se você estiver usando Azure Data Factory para se conectar ao banco de dados do Azure para PostgreSQL?
Para o conector usando Azure Integration Runtime, o conector aproveita os certificados no repositório de certificados do Windows no ambiente hospedado do Azure. Esses certificados já são compatíveis com os certificados recém aplicados e, portanto, nenhuma ação é necessária.

Para o conector que usa o autohospedado Integration Runtime em que você inclui explicitamente o caminho para o arquivo de certificado SSL na cadeia de conexão, será necessário baixar o [novo certificado](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) e atualizar a cadeia de conexão para usá-lo.

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7. preciso planejar um tempo de inatividade de manutenção do servidor de banco de dados para essa alteração?
Não. Como a alteração aqui é apenas no lado do cliente para se conectar ao servidor de banco de dados, não há nenhum tempo de inatividade de manutenção necessário para o servidor de banco de dados para essa alteração.

### <a name="8-if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>8. se eu criar um novo servidor após 15 de fevereiro de 2021 (02/15/2021), serei afetado?
Para servidores criados após 15 de fevereiro de 2021 (02/15/2021), você continuará a usar o [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) para que seus aplicativos se conectem usando SSL.

### <a name="9-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>9. com que frequência o Microsoft atualiza seus certificados ou qual é a política de expiração?
Esses certificados usados pelo banco de dados do Azure para PostgreSQL são fornecidos por autoridades de certificação (CA) confiáveis. Portanto, o suporte desses certificados está vinculado ao suporte desses certificados pela CA. O certificado [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) está agendado para expirar em 2025, portanto, a Microsoft precisará executar uma alteração de certificado antes da expiração. Caso haja bugs imprevistos nesses certificados predefinidos, a Microsoft precisará fazer com que a rotação do certificado seja a mais antigamente semelhante à alteração realizada em 15 de fevereiro de 2021 para garantir que o serviço esteja seguro e em conformidade em todos os momentos.

### <a name="10-if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-the-primary-server-or-the-read-replicas"></a>10. se eu estiver usando réplicas de leitura, preciso executar essa atualização somente no servidor primário ou nas réplicas de leitura?
Como essa atualização é uma alteração no lado do cliente, se o cliente usado para ler dados do servidor de réplica, você também precisará aplicar as alterações para esses clientes. 

### <a name="11-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>11. temos uma consulta do lado do servidor para verificar se o SSL está sendo usado?
Para verificar se você está usando a conexão SSL para se conectar ao servidor, consulte [verificação de SSL](concepts-ssl-connection-security.md#applications-that-require-certificate-verification-for-tls-connectivity).

### <a name="12-is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>12. há uma ação necessária se eu já tiver o DigiCertGlobalRootG2 no meu arquivo de certificado?
Não. Não há nenhuma ação necessária se o arquivo de certificado já tiver o **DigiCertGlobalRootG2**.

### <a name="13-what-if-you-are-using-docker-image-of-pgbouncer-sidecar-provided-by-microsoft"></a>13. e se você estiver usando a imagem do Docker do PgBouncer sidecar fornecido pela Microsoft?
Uma nova imagem do Docker que dá suporte a [**Baltimore**](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) e [**DigiCert**](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) é publicada abaixo [aqui](https://hub.docker.com/_/microsoft-azure-oss-db-tools-pgbouncer-sidecar) (marca mais recente). Você pode extrair essa nova imagem para evitar qualquer interrupção na conectividade a partir de 15 de fevereiro de 2021. 

### <a name="14-what-if-i-have-further-questions"></a>14. e se eu tiver outras dúvidas?
Se você tiver dúvidas, obtenha respostas de especialistas da Comunidade no [Microsoft Q&A](mailto:AzureDatabaseforPostgreSQL@service.microsoft.com). Se você tiver um plano de suporte e precisar de ajuda técnica,  [entre em contato conosco](mailto:AzureDatabaseforPostgreSQL@service.microsoft.com)
