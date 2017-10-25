
<!--
includes/sql-database-include-connection-string-20-portalshots.md

Latest Freshness check:  2015-09-02 , GeneMi.

## Connection string
-->


### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Obter a cadeia de conexão no portal do Azure
Obtenha no [portal do Azure](https://portal.azure.com/) a cadeia de conexão necessária para o seu programa cliente interagir com o Banco de Dados SQL do Azure: 

1. Clique em **PROCURAR** > **bancos de dados SQL**.
2. Insira o nome do banco de dados na caixa de texto de filtro próximo ao canto superior esquerdo da folha dos **bancos de dados SQL** .
3. Clique na linha do banco de dados.
4. Depois que a folha aparecer para o banco de dados, para conveniência visual, você pode clicar nos controles padrão de minimização para recolher as folhas usadas para navegação e filtragem do banco de dados. 
   
    ![Filtrar para isolar o banco de dados][10-FilterDatabase]
5. Na folha do banco de dados, clique em **Mostrar cadeias de conexão de banco de dados**.
6. Se você pretende usar a biblioteca de conexão do ADO.NET, copie a cadeia de caracteres rotulada **ADO**. 
   
    ![Copie a cadeia de conexão do ADO do banco de dados][20-CopyAdoConnectionString]
7. Em um formato ou outro, cole as informações da cadeia de conexão no código do programa cliente.

Para obter mais informações, veja:<br/>[Cadeias de conexão e arquivos de configuração](http://msdn.microsoft.com/library/ms254494.aspx).

<!-- Image references. -->

[10-FilterDatabase]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-a.png

[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
