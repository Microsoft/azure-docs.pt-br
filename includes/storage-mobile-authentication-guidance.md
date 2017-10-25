## <a name="configure-your-application-to-access-azure-storage"></a>Configurar seu aplicativo para acessar o Armazenamento de Blob
Há duas maneiras de autenticar o aplicativo para acessar os serviços de Armazenamento:

* Chave compartilhada: usar a chave compartilhada somente para fins de teste
* SAS (Assinatura de Acesso Compartilhado): use a SAS para aplicativos de produção

### <a name="shared-key"></a>Chave compartilhada
A autenticação de Chave Compartilhada significa que o aplicativo usará seu nome da conta e chave de conta para acessar os serviços de Armazenamento. Para mostrar rapidamente como usar essa biblioteca, vamos usar a autenticação de Chave Compartilhada nesta introdução.

> [!WARNING] 
> **Use autenticação de chave compartilhada somente para fins de teste!** Seu nome de conta e chave de conta, que dão acesso completo de leitura/gravação à conta de Armazenamento associada, serão distribuídos a todas as pessoas que baixarem o aplicativo. Isso **não** é uma prática recomendada, pois você corre o risco de ter sua chave comprometida por clientes não confiáveis.
> 
> 

Ao usar a autenticação de Chave Compartilhada, você criará uma [cadeia de conexão](../articles/storage/common/storage-configure-connection-string.md). A cadeia de conexão é composta dos seguintes itens:  

* O **DefaultEndpointsProtocol** -você pode escolher HTTP ou HTTPS. No entanto, é altamente recomendável usar HTTPS.
* O **Nome da Conta** - o nome de sua conta de armazenamento
* A **Chave da Conta** - no [Portal do Azure](https://portal.azure.com), navegue até a conta de armazenamento e clique no ícone **Chaves** para encontrar essa informação.
* (Opcional) **EndpointSuffix** - é usado para serviços de armazenamento em regiões com sufixos de ponto de extremidade diferentes, como o Azure China ou a Governança do Azure.

Veja um exemplo de cadeia de conexão usando a autenticação de Chave Compartilhada:

`"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"`

### <a name="shared-access-signatures-sas"></a>SAS (Assinaturas de Acesso Compartilhado)
Para um aplicativo móvel, o método recomendado para autenticar uma solicitação por um cliente em relação ao serviço de Armazenamento do Azure é usando uma SAS (Assinatura de Acesso Compartilhado). A SAS permite conceder a um cliente acesso a um recurso por um período de tempo especificado, com um conjunto de permissões especificado.
Como proprietário da conta de armazenamento, você precisará gerar uma SAS a ser consumida por seus clientes móveis. Para gerar a SAS, provavelmente você desejará escrever um serviço separado que gere a SAS a ser distribuída a seus clientes. Para fins de teste, você pode usar o [Gerenciador de Armazenamento do Microsoft Azure](http://storageexplorer.com) ou o [Portal do Azure](https://portal.azure.com) para gerar um SAS. Ao criar a SAS, você pode especificar o intervalo de tempo em que a SAS é válida e as permissões que a SAS concede ao cliente.

O exemplo a seguir mostra como usar o Gerenciador de Armazenamento do Microsoft Azure para gerar uma SAS.

1. Se você ainda não fez isso, [Instale o Gerenciador de Armazenamento do Microsoft Azure](http://storageexplorer.com)
2. Conecte-se à sua assinatura.
3. Clique em sua conta de armazenamento e clique na guia "Ações" na parte inferior esquerda. Clique em "Get Shared Access Signature" (Obter Assinatura de Acesso Compartilhado) para gerar uma "cadeia de conexão" para a SAS.
4. Aqui está um exemplo de uma cadeia de conexão de SAS que concede permissões leitura e gravação no serviço, contêiner e nível de objeto para o serviço Blob da conta de armazenamento.
   
   `"SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net"`

Como você pode ver, ao usar um SAS, você não expõe a chave de sua conta em seu aplicativo. Saiba mais sobre a SAS e as práticas recomendadas sobre o uso do SAS consultando [Assinaturas de Acesso Compartilhado: entendendo o modelo SAS](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md).

