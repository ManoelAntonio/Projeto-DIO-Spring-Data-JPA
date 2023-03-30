Projeto

Para demonstrar a integração entre as tecnologias apresentadas neste projeto, será criado um projeto Java onde serão implementadas as quatro operações de um CRUD a partir de serviços da JPA em conjunto com o Spring Framework.
A implementação destes métodos será feita utilizando a IDE Eclipse Luna e iremos usar o Maven (seção Links)para gerenciar a configuração do projeto.

O primeiro passo é a criação de um novo projeto Maven: para isso devemos acessar o menu File > New > Order > Maven Project. 

Camadas do projeto

O projeto será desenvolvido em três camadas:
-model
-persistence
-business
Essa divisão tem como objetivo realizar uma separação por função das classes.

1- A camada model será responsável pelas entidades de persistência da aplicação
2- A camada persistence terá as classes que realizam as ações de acesso à base de dados (persistir, atualizar, remover e buscar)
3- A camada business será formada pelas classes de negócio, ou seja, responsáveis por realizar a regra de negócio da aplicação. 

Devido a isso, para melhor organizar o projeto iremos criar quatro pacotes:
br.com.cazuzaneto.model 
br.com.cazuzaneto.persistence
br.com.cazuzaneto.business
br.com.cazuzaneto.run 

Configuração do projeto

Antes do processo de implementação ser iniciado é necessário que o projeto seja configurado a partir de dois arquivos XML:
pom.xml 
spring-context.xml

* O arquivo pom.xml, apresentado na Listagem 1, é usado para realizar as configurações que serão gerenciadas pelo Maven.
Nele podemos inserir as dependências do projeto, bem como os plugins que serão instalados.
No exemplo que será desenvolvido neste artigo serão usadas etedependências:
spring-context: módulo do Spring Framework responsável por construir uma base sólida em aplicações Spring e é baseado nos módulos Core e Beans. 
O ponto principal deste módulo é a interface ApplicationContext, que herda BeansFactory, porém apresentando recursos adicionais a esta última. 
Um exemplo disso é o fato do módulo Context gerenciar também o ciclo de vida de beans de outros fornecedores, 
tais como algumas implementações de especificações da plataforma Java EE e o provedor Hibernate;

* spring-tx: módulo do Spring Framework responsável pela programação de transações para API diversas, tais como JTA, JDBC, Hibernate, JPA e JDO;

* spring-orm: módulo responsável por trabalhar com mapeamento objeto/relacional de uma aplicação.
Possui integração com soluções como Hibernate, JPA e JDO;

* spring-aop: módulo do Spring responsável pela programação orientada a aspectos;

* hibernate-entitymanager: Implementação fornecida pelo Hibernate da especificação JPA;

* commons-dbcp2: módulo fornecido para fundação Apache (seção Links) e responsável e pela criação de Pools de conexão para bancos de dados;

* mysql-connector-java: implementação JDBC para o SGBD MySQL.


Além das dependências também foram configurados dois plugins que serão usados no projeto:
maven-compiler-plugin
maven-jar-plugin

O primeiro é responsável, dentre outras coisas, por estabelecer a versão do JDK que será usada no projeto,
enquanto o segundo gerencia a execução da aplicação, inclusive definindo a classe principal a ser executada.

O segundo arquivo a ser configurado é o spring-context.xml, apresentado na Listagem 2.
Ele deve ser criado dentro do pacote src/main/resources e estabelece as configurações de contexto do Spring Framework que,
nesse projeto, foram feitas através da injeção de beans e da definição de tags específicas. 
Os beans configurados foram:

 * mysqlDataSource: define a conexão com o SGBD MySQL;
 
 * entityManagerFactory: define o EntityManager, que será responsável pelo gerenciamento das entidades da aplicação.
 Nesse bean também são passadas informações relacionadas à unidade de persistência e aos dados da conexão com o SGBD;
 
 * transactionManager: define o bean responsável pelo gerenciamento das transações realizadas pelo EntityManager;
 
 
 Implementação das classes do projeto
 
 As configurações elementares do projeto já foram realizadas e o próximo passo é dar início a implementação das classes.
 A primeira delas a ser desenvolvida será a AlunoEntity, apresentada na Listagem 3. Essa é, na verdade, uma entidade de persistência gerenciada pelo container JPA
 e responsável por conduzir os dados entre a aplicação e o SGBD, já que a JPA utiliza metadados, ou seja, anotações para realizar o mapeamento objeto/relacional.
 Na classe estão presentes as seguintes anotações JPA:
 
*Entity: anotação de classe usada para definir uma nova entidade JPA;

*Table: anotação de classe usada para definir as configurações que a classe terá quando mapeada como tabela de uma SGBD;

*Id: anotação de atributo ou método responsável por definir a chave primária da tabela;

*GeneratedValue: anotação de atributo ou método responsável por definir a estratégia de geração de chave primária.
Nesse caso define que as chaves primárias serão criadas pelo próprio SGBD;

*Column: anotação de atributo ou método responsável por definir as configurações de uma coluna, tais como nome, tamanho, etc.


 Após a criação da classe AlunoEntity é necessário criar e configurar o arquivo persistence.xml,
que deverá conter as informações da unidade de persistência que, por sua vez, é usada pela implementação da JPA. 
Ele deve ser criado dentro da pasta META-INF, localizada dento do pacote src/main/resources e o seu conteúdo deve ser o mesmo da Listagem 4.

A segunda classe a ser implementada é a AlunoPersistence, apresentada na Listagem 5. Essa classe é responsável por realizar as ações de acesso ao banco de dados.
Repare que duas anotações estão presentes: a PersistenceContext e Repository.
A primeira define o atributo que receberá a injeção do EntityManager, configurado no arquivo spring-context.xml.
Já a segunda define que a classe é estereotipada como uma classe de repositório e poderá ser injetada em outras classes gerenciadas pelo container do Spring.

Os métodos da classe AlunoPersistence são aqueles que fazem acesso ao banco de dados e utilizam o EntityManager.

A terceira classe é a AlunoBusiness, apresentada na Listagem 6. Ela é responsável por definir a regra de negócio relacionada a aluno. 
Nela pode-se observar três anotações: Transactional, Service e Autowired. A anotação Transactional define que todos os métodos dessa classe que realizem transações JPA serão gerenciados pelo Spring Framework, 
tendo em vista a configuração realizada no arquivo spring-context.xml. A anotação Service está estereotipando a classe como sendo de serviço
e define que a mesma possa ser injetada em outras classes gerenciadas pelo contêiner do Spring. 
Já a anotação Autowired define que o atributo anotado deve ser injetado, tendo em vista as configurações já realizadas, no caso do nosso exemplo, o atributo alunoPersistence será injetado automaticamente
porque a classe AlunoPersistence foi anotada com Repository, o que permite a sua injeção em outras classes.

A quarta e última classe anotada é a Run, apresentada na Listagem 7, que é responsável por iniciar a execução do projeto
e onde o contexto do Spring é iniciado através da criação da classe ClassPathXmlApplicationContext.
Esta recebe como parâmetro o nome do arquivo de configuração do contexto do Spring, spring-context.xml.
Assim, quando o método getBean no atributo context é invocado, todas as injeções de dependência relacionadas a este bean serão realizadas.

Na classe Run é apresentado um exemplo de persistência de uma nova entidade de aluno.

Perceba que a integração de duas tecnologias muito utilizadas no mundo Java facilita a criação de um projeto com várias atividades, mas sem que para isso o desenvolvedor gaste muito tempo e código.

A aplicação do conhecimento adquirido neste artigo no seu dia a dia como desenvolvedor Java te proporcionará uma profunda abstração da complexidade presente nas tarefas de conexão a banco de dados,
bem como no trabalho com os dados relacionais em aplicações orientadas objetos, isso porque a JPA oferece, além de recursos para configuração de conexões com diversos SGBDs, a possibilidade de realizar um mapeamento 
objeto/relacional dentro do seu próprio código fonte.

Você ainda poderá realizar tudo isso em conjunto com a infinidade de recursos que o Spring Framework te oferece, inclusive delegando a ele o papel de gerenciar as transações do JPA,
além de usufruir da injeção de dependência e a integração com diversas especificações Java e tecnologias de outros fornecedores.

LINKS:
http://hibernate.org/orm/ 
http://www.eclipse.org/eclipselink/ 
https://commons.apache.org/proper/commons-dbcp/ 
https://www.mysql.com/ 
https://maven.apache.org/
https://www.eclipse.org/luna/
https://docs.oracle.com/javaee/7/tutorial/persistence-intro.htm#BNBPZ
https://download.oracle.com/otndocs/jcp/persistence-2_1-fr-eval-spec/index.html
https://docs.spring.io/spring/docs/4.2.4.RELEASE/spring-framework-reference/htmlsingle/














