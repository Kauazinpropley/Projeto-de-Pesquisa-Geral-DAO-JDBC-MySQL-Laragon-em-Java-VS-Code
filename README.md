# Projeto-de-Pesquisa-Geral-DAO-JDBC-MySQL-Laragon-em-Java-VS-Code
Projeto de pesquisa e pratica do dao, jdbc e mysql em java

# **Padrão DAO (Data Access Object) E sua importância**
O padrão de projeto DAO surgiu com a necessidade de separarmos a lógica de negócios da lógica de persistência de dados. Este padrão permite que possamos mudar a forma de persistência sem que isso influencie em nada na lógica de negócio, além de tornar nossas classes mais legíveis.

Classes DAO são responsáveis por trocar informações com o SGBD e fornecer operações CRUD e de pesquisas, elas devem ser capazes de buscar dados no banco e transformar esses em objetos ou lista de objetos, fazendo uso de listas genéricas (BOX 3), também deverão receber os objetos, converter em instruções SQL e mandar para o banco de dados.

Toda interação com a base de dados se dará através destas classes, nunca das classes de negócio, muito menos de formulários.

Se aplicarmos este padrão corretamente ele vai abstrair completamente o modo de busca e gravação dos dados, tornando isso transparente para aplicação e facilitando muito na hora de fazermos manutenção na aplicação ou migrarmos de banco de dados.

Também conseguimos centralizar a troca de dados com o SGBD (Sistema Gerenciador de Banco de Dados), teremos um ponto único de acesso a dados, tendo assim nossa aplicação um ótimo design orientado a objeto.

# **Fontes:**
PADRAO DAO E SUA IMPORTANCIA: https://www.devmedia.com.br/dao-pattern-persistencia-de-dados-utilizando-o-padrao-dao/30999
PADRAO DAO EXPLICAÇÂO: https://dev.to/diariodeumacdf/padroes-dao-e-repository-13nj
