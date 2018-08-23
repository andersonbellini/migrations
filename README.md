# migrations

Migrações
29/10/2017
8 minutos para ler
Colaboradores
Brice Lambson  olprod
Migrações oferecem uma maneira de aplicar incrementalmente alterações de esquema ao banco de dados para mantê-lo em sincronia com seu modelo EF Core enquanto preserva os dados existentes no banco de dados.
Criando o banco de dados
Depois de ter definido seu modelo inicial, é hora de criar o banco de dados. Para fazer isso, adicione uma migração inicial. Instalar as Ferramentas do EF Core e execute o comando apropriado.
PowerShell

Copiar
Add-Migration InitialCreate
Console

Copiar
dotnet ef migrations add InitialCreate
Três arquivos são adicionados ao seu projeto no diretório Migrações:
00000000000000_InitialCreate.CS– o arquivo principal de migrações. Contém as operações necessárias para aplicar a migração (em Up()) e revertê-la (em Down()).
00000000000000_InitialCreate.Designer.CS– o arquivo de metadados de migrações. Contém informações usadas pelo EF.
MyContextModelSnapshot.cs– um instantâneo do seu modelo atual. Usado para determinar o que mudou ao adicionar a próxima migração.
O carimbo de data/hora no nome de arquivo ajuda a mantê-lo organizado por ordem cronológica para que você possa ver o andamento das alterações.
Dica

Você é livre para mover os arquivos de Migrações e alterar o namespace deles. Novas migrações são criadas como irmãs da última migração.
Em seguida, aplique a migração ao banco de dados para criar o esquema.
PowerShell

Copiar
Update-Database
Console

Copiar
dotnet ef database update
Adicionar outra migração
Depois de fazer alterações ao seu modelo do EF Core, o esquema de banco de dados ficará fora de sincronia. Para atualizá-las, adicione outra migração. O nome da migração pode ser usado como uma mensagem de confirmação em um sistema de controle de versão. Por exemplo, se eu tiver feito alterações para salvar as revisões do cliente de produtos, poderei escolher algo como AddProductReviews.
PowerShell

Copiar
Add-Migration AddProductReviews
Console

Copiar
dotnet ef migrations add AddProductReviews
Depois do scaffolding da migração, você deverá examinar sua precisão e adicionar todas as operações extras necessárias para aplicá-la corretamente. Por exemplo, sua migração pode conter as seguintes operações:
C#

Copiar
migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");

migrationBuilder.AddColumn<string>(
    name: "Name",
    table: "Customer",
    nullable: true);
Embora essas operações tornem o esquema de banco de dados compatível, elas não preservam os nomes de cliente existentes. Para torná-las melhor, regrave-as da seguinte maneira.
C#

Copiar
migrationBuilder.AddColumn<string>(
    name: "Name",
    table: "Customer",
    nullable: true);

migrationBuilder.Sql(
@"
    UPDATE Customer
    SET Name = FirstName + ' ' + LastName;
");

migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");
Dica

Adicionar uma nova migração avisa quando é feito o scaffolding da operação que pode resultar em perda de dados (como remover uma coluna). Não deixe de examinar especialmente a precisão dessas migrações.
Aplique a migração ao banco de dados usando o comando apropriado.
PowerShell

Copiar
Update-Database
Console

Copiar
dotnet ef database update
Remover uma migração
Às vezes, você adiciona uma migração e percebe que precisa fazer alterações adicionais ao modelo do EF Core antes de aplicá-lo. Para remover a última migração, use este comando.
PowerShell

Copiar
Remove-Migration
Console

Copiar
dotnet ef migrations remove
Após removê-la, você poderá fazer as alterações adicionais ao modelo e adicione-lo novamente.
Reverter a migração
Se você já tiver aplicado uma migração (ou várias migrações) no banco de dados, mas precisar revertê-la, poderá usar o mesmo comando usado para aplicar as migrações, mas especificando o nome da migração para a qual você deseja reverter.
PowerShell

Copiar
Update-Database LastGoodMigration
Console

Copiar
dotnet ef database update LastGoodMigration
Migrações vazias
Às vezes é útil adicionar uma migração sem fazer nenhuma alteração ao modelo. Nesse caso, adicionar uma nova migração cria uma vazia. Você pode personalizar essa migração para executar operações que não estejam diretamente relacionadas ao modelo EF Core. Alguns elementos que talvez você queira gerenciar assim são:
Pesquisa de texto completo
Funções
Procedimentos armazenados
Gatilhos
Exibições
etc.
Gerando um script SQL
Ao depurar suas migrações ou implantá-las em um banco de dados de produção, é útil gerar um script SQL. O script então pode ser verificado em mais detalhes quanto à precisão e ajustado para atender às necessidades de um banco de dados de produção. O script também pode ser usado em conjunto com uma tecnologia de implantação. O comando básico é o seguinte.
PowerShell

Copiar
Script-Migration
Console

Copiar
dotnet ef migrations script
Há várias opções para este comando.
A migração de deve ser a última migração aplicada ao banco de dados antes de executar o script. Se nenhuma migração tiver sido aplicada, especifique 0 (esse é o padrão).
A migração para é a última migração que será aplicada ao banco de dados após a execução do script. O padrão é a última migração em seu projeto.
Um script idempotente pode ser gerado como opção. Esse script aplicará migrações somente se elas ainda não tiverem sido aplicadas ao banco de dados. Isso é útil se você não souber exatamente qual foi a última migração aplicada ao banco de dados ou estiver implantando em vários bancos de dados que podem estar, cada um, em uma migração diferente.
Aplicar migrações em tempo de execução
Alguns aplicativos talvez queiram aplicar migrações no tempo de execução durante a inicialização ou a primeira execução. Faça isso usando o método Migrate().
Cuidado, essa abordagem não é para todos. Embora seja ótimo para aplicativos com um banco de dados local, a maioria dos aplicativos exigirá uma estratégia de implantação mais robusta, como gerar scripts SQL.
C#

Copiar
myDbContext.Database.Migrate();
Aviso

Não chame EnsureCreated() antes de Migrate(). O EnsureCreated() ignora as Migrações para criar o esquema e causa falha no Migrate().
Observação

Este método é criado sobre o serviço IMigrator, que pode ser usado em cenários mais avançados. Use DbContext.GetService<IMigrator>() para acessá-lo.


Referência: https://docs.microsoft.com/pt-br/ef/core/managing-schemas/migrations/
