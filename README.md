# Projeto-de-Pesquisa-Geral-DAO-JDBC-MySQL-Laragon-em-Java-VS-Code
Projeto de pesquisa e pratica do dao, jdbc e mysql em java

#  **Data Access Object (DAO)**
##  1. O que é o padrão de projeto Data Access Object (DAO)?
O padrão de projeto DAO é uma forma de organizar o código para lidar com a comunicação entre o seu programa e um banco de dados.

O padrão de projeto DAO (Data Access Object) é como a planta de um arquiteto para lidar com dados em software. É um modelo que os desenvolvedores usam para criar um sistema estruturado e organizado para obter informações de uma fonte de dados, como um banco de dados. Em vez de lidar diretamente com os detalhes minuciosos de como os dados são armazenados e recuperados, o padrão DAO atua como um guia, abstraindo as complexidades e oferecendo uma maneira clara de interagir com os dados.


##  2. Padrão DAO (Data Access Object) E sua importância
O padrão de projeto DAO surgiu com a necessidade de separarmos a lógica de negócios da lógica de persistência de dados. Este padrão permite que possamos mudar a forma de persistência sem que isso influencie em nada na lógica de negócio, além de tornar nossas classes mais legíveis.

Classes DAO são responsáveis por trocar informações com o SGBD e fornecer operações CRUD e de pesquisas, elas devem ser capazes de buscar dados no banco e transformar esses em objetos ou lista de objetos, fazendo uso de listas genéricas (BOX 3), também deverão receber os objetos, converter em instruções SQL e mandar para o banco de dados.
Toda interação com a base de dados se dará através destas classes, nunca das classes de negócio, muito menos de formulários.

Se aplicarmos este padrão corretamente ele vai abstrair completamente o modo de busca e gravação dos dados, tornando isso transparente para aplicação e facilitando muito na hora de fazermos manutenção na aplicação ou migrarmos de banco de dados.

Também conseguimos centralizar a troca de dados com o SGBD (Sistema Gerenciador de Banco de Dados), teremos um ponto único de acesso a dados, tendo assim nossa aplicação um ótimo design orientado a objeto.

##  3. Funcionalidades do padrão de projeto Data Access Object (DAO)
O padrão DAO abstrai e encapsula os detalhes de como os dados são salvos, recuperados, atualizados ou excluídos em um banco de dados. Essa abstração protege o restante da aplicação da implementação do banco de dados.
Ele centraliza todo o código relacionado ao banco de dados em classes DAO dedicadas. Isso significa que o restante da aplicação não precisa espalhar as operações de banco de dados por todo o seu código; em vez disso, interage com os métodos DAO.


##  4. Exemplo Prático do padrao DAO: (Utilizando o modelo do mapa conceitual)
Imagina que o sistema é para gerenciar uma Biblioteca de Livros.

1. Camada Model (O Objeto)
Aqui definimos o que é um Livro. É uma classe simples que apenas guarda informações.

Arquivo: `src/main/java/br/escola/dao_base/model/Livro.java`


    public class Livro {
         private Integer id;
        private String titulo;
        private String autor;

    // Construtores, Getters e Setters
    public Livro() {}
    public Livro(Integer id, String titulo, String autor) {
        this.id = id;
        this.titulo = titulo;
        this.autor = autor;
    }
    // ... (getters e setters aqui)
    }
2. Camada DAO (O Contrato/Interface)
Aqui fazemos a lista de promessas. O que o nosso sistema de biblioteca deve ser capaz de fazer?

Arquivo: `src/main/java/br/escola/dao_base/dao/LivroDao.java`


    public interface LivroDao {
        void inserir(Livro obj);
        void atualizar(Livro obj);
        void deletarPorId(Integer id);
        Livro buscarPorId(Integer id);
        List<Livro> buscarTodos();
    }
3. Camada DAO.Impl (O Trabalho com JDBC)
Aqui é onde a mágica acontece. Usamos o JDBC para falar com o banco de dados de forma segura usando PreparedStatements.

Arquivo: `src/main/java/br/escola/dao_base/dao/impl/LivroDaoJDBC.java`


    public class LivroDaoJDBC implements LivroDao {
        private Connection conn;

    public LivroDaoJDBC(Connection conn) {
        this.conn = conn;
    }

    @Override
    public void inserir(Livro obj) {
        PreparedStatement st = null;
        try {
            // O "?" protege contra o SQL Injection
            st = conn.prepareStatement(
                "INSERT INTO livros (titulo, autor) VALUES (?, ?)");
            
            st.setString(1, obj.getTitulo());
            st.setString(2, obj.getAutor());
            st.executeUpdate();
        } 
        catch (SQLException e) {
            throw new DbException(e.getMessage());
        } 
        finally {
            // Checklist de qualidade: sempre fechar o comando!
            DB.closeStatement(st);
        }
    }
    // ... (outros métodos seguem a mesma lógica)
    }
4. Camada DB (A Conexão)
Esta classe é a "Chave" que abre a conexão com o banco.

Arquivo: `src/main/java/br/escola/dao_base/db/ConnectionFactory.java`


        public class ConnectionFactory {
            public static Connection getConnection() {
                // Lógica para ler o db.properties e conectar
                // ...
                return connection;
            }
        }
5. Camada App (O Menu/Orquestração)
É aqui que o usuário interage. O Main não sabe nada de SQL, ele só conhece o LivroDao.

Arquivo: `src/main/java/br/escola/dao_base/app/Main.java`


    public class Main {
        public static void main(String[] args) {
            // 1. Abre a conexão
            Connection conn = ConnectionFactory.getConnection();
        
        // 2. Instancia o ajudante (DAO)
        LivroDao livroDao = new LivroDaoJDBC(conn);

        System.out.println("--- Teste: Inserindo Livro ---");
        Livro novoLivro = new Livro(null, "O Pequeno Príncipe", "Antoine de Saint-Exupéry");
        livroDao.inserir(novoLivro);
        
        System.out.println("Livro salvo com sucesso!");
        
        // 3. Fecha a conexão no final de tudo
        DB.closeConnection();
        }
    }
# **Ciclo de Vida Detalhado da Conexão JDBC**
O JDBC funciona como uma ponte de comunicação de baixa latência. Compreender seu ciclo de vida evita vazamentos de memória (memory leaks) e queda de performance.

Carregamento do Driver: O Driver do MySQL (Connector/J) é registrado no DriverManager. No Java moderno, isso é feito via Service Provider Mechanism automaticamente ao adicionar a dependência no Maven.

Solicitação de Conexão: O DriverManager.getConnection() solicita um canal ao SGBD. Este é o passo mais "caro" em termos de tempo de processamento.

Criação do Statement: O Java cria um objeto que "empacota" a query SQL para envio.

Execução e Transmissão: A query é enviada pela rede até o MySQL (Laragon).

Cursor do ResultSet: Ao receber um SELECT, o JDBC cria um ponteiro (cursor) que aponta para as linhas no banco. Você deve percorrer esse cursor com rs.next().

Gerenciamento de Recursos: Cada objeto aberto (Connection, Statement, ResultSet) consome um "file descriptor" no Sistema Operacional. Por isso, o fechamento é crítico.

3. Segurança e Prevenção de Ataques
O banco de dados é o ativo mais valioso de uma empresa. Protegê-lo é dever do desenvolvedor backend.

Anatomia do SQL Injection
Um ataque de injeção ocorre quando o atacante manipula a estrutura da query através de uma entrada de dados comum.

Exemplo Vulnerável: "SELECT * FROM livros WHERE id = " + input_usuario;

Entrada Maliciosa: 1; DROP TABLE usuarios;

Resultado: O banco executa duas instruções: busca o livro 1 e deleta a tabela de usuários.

A Defesa: Prepared Statements
O PreparedStatement resolve isso através da Parametrização:

O SQL é enviado com espaços reservados (?).

O banco compila o plano de execução para aquele SQL antes de ver os dados.

Os dados são enviados separadamente. O banco sabe que aquele dado é apenas um valor literal e nunca parte do comando SQL.

4. Estrutura Completa do Projeto (Template Pro)
A. O Modelo de Dados (Entity)
Local: src/main/java/br/escola/dao_base/model/Livro.java

Java
public class Livro implements Serializable {
    private static final long serialVersionUID = 1L;
    private Integer id;
    private String titulo;
    private String autor;

    // Construtores, Getters, Setters, Equals e HashCode
}
B. O Contrato (Interface)
Local: src/main/java/br/escola/dao_base/dao/LivroDao.java

Java
public interface LivroDao {
    void insert(Livro obj);
    void update(Livro obj);
    void deleteById(Integer id);
    Livro findById(Integer id);
    List<Livro> findAll();
}
C. A Implementação JDBC
Local: src/main/java/br/escola/dao_base/dao/impl/LivroDaoJDBC.java

Java
public class LivroDaoJDBC implements LivroDao {
    private Connection conn;

    public LivroDaoJDBC(Connection conn) {
        this.conn = conn;
    }

    @Override
    public void insert(Livro obj) {
        // Uso de try-with-resources garante o fechamento do PreparedStatement
        String sql = "INSERT INTO livros (titulo, autor) VALUES (?, ?)";
        try (PreparedStatement st = conn.prepareStatement(sql, Statement.RETURN_GENERATED_KEYS)) {
            st.setString(1, obj.getTitulo());
            st.setString(2, obj.getAutor());

            int rowsAffected = st.executeUpdate();
            if (rowsAffected > 0) {
                try (ResultSet rs = st.getGeneratedKeys()) {
                    if (rs.next()) obj.setId(rs.getInt(1));
                }
            }
        } catch (SQLException e) {
            throw new DbException("Erro ao inserir livro: " + e.getMessage());
        }
    }
    // Outros métodos...
}
5. Checklist de Qualidade e Práticas de Produção
[ ] Try-with-resources: Sempre utilize para garantir que ResultSet e Statement sejam fechados mesmo em caso de erro.

[ ] Gestão de Transações: Para operações que envolvem mais de uma tabela, use conn.setAutoCommit(false) e conn.commit() ou conn.rollback().

[ ] Camada de Exceções: Nunca deixe uma SQLException subir até a interface do usuário. Encapsule-a em uma exceção de runtime própria (ex: DataAccessException).

[ ] Connection Pooling: Para projetos reais, não use DriverManager puro. Utilize bibliotecas como HikariCP para manter conexões abertas e prontas para uso.

[ ] Logging: Use bibliotecas como Log4j ou SLF4J em vez de System.out.println para rastrear erros no banco.

    

    
    



# **Fontes:**
1-https://www.devmedia.com.br/dao-pattern-persistencia-de-dados-utilizando-o-padrao-dao/30999

2-https://www.geeksforgeeks.org/system-design/data-access-object-pattern/
