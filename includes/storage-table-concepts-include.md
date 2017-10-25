## <a name="what-is-table-storage"></a>O que é o Armazenamento de Tabelas
O Armazenamento de Tabelas do Microsoft Azure armazena grandes quantidades de dados estruturados. O serviço é um repositório de dados NoSQL que aceita chamadas autenticadas de dentro e de fora da nuvem do Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais. Os usos comuns do armazenamento de tabelas incluem:

* Armazenamento de TBs de dados estruturados capazes de atender a aplicativos de dimensionamento da Web
* Armazenando conjuntos de dados que não exigem junções complexas, chaves estrangeiras ou procedimentos armazenados e que podem ser desnormalizados para acesso rápido
* Consulta rápida de dados usando um índice clusterizado
* Acesso a dados usando o protocolo OData e consultas LINQ com bibliotecas WCF Data Service .NET

Você pode usar o armazenamento de tabelas para armazenar e consultar grandes conjuntos de dados estruturados não relacionais, e suas tabelas serão dimensionadas conforme a demanda aumentar.

## <a name="table-storage-concepts"></a>Conceitos de armazenamento de tabelas
O armazenamento de tabelas contém os seguintes componentes:

![Diagrama de componentes do Armazenamento de Tabelas][Table1]

* **Formato da URL:** o código aborda as tabelas em uma conta usando o formato desse endereço:   
  http://`<storage account>`.table.core.windows.net/`<table>`  
  
  Você pode endereçar as tabelas do Azure diretamente usando esse endereço com o protocolo OData. Para saber mais, veja [OData.org][OData.org].
* **Conta de Armazenamento:** todo o acesso ao Armazenamento do Azure é feito através de uma conta de armazenamento. Consulte [Escalabilidade e Metas de Desempenho do Armazenamento do Azure](../articles/storage/common/storage-scalability-targets.md) para obter detalhes sobre a capacidade da conta de armazenamento.
* **Tabela**: uma tabela é uma coleção de entidades. As tabelas não impõem um esquema nas entidades, o que significa que uma única tabela pode conter entidades com diferentes conjuntos de propriedades. O número de tabelas que uma conta de armazenamento pode conter é limitado apenas pelo limite de capacidade da conta de armazenamento.
* **Entidade**: uma entidade é um conjunto de propriedades, semelhantes a uma linha do banco de dados. Uma entidade pode ser de até 1MB.
* **Propriedades**: uma propriedade é um par de nome-valor. Cada entidade pode incluir até 252 propriedades para armazenar dados. Cada entidade possui também três propriedades do sistema que especificam uma chave de partição, uma chave de linha e um carimbo de hora. As entidades com a mesma chave de partição podem ser consultadas mais rapidamente e inseridas/atualizadas em operações atômicas. A chave de linha de uma entidade é seu identificador exclusivo dentro de uma partição.

Para obter detalhes sobre a nomenclatura de tabelas e propriedades, confira [Noções básicas sobre o modelo de dados do serviço Tabela](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

[Table1]: ./media/storage-table-concepts-include/table1.png
[OData.org]: http://www.odata.org/
