## Instalação no Linux

```bash
#!/bin/bash
wget https://dlcdn.apache.org/maven/maven-3/3.8.7/binaries/apache-maven-3.8.7-bin.tar.gz

tar -xf apache-maven-3.8.7-bin.tar.gz
sudo mv apache-maven-3.8.7/ /opt/

#Verifica se tem a linha no .bashrc

has_key() {
  grep -i -q ^export.\*apache-maven ~/.bashrc
}

has_key && {
  gedit ~/.bashrc
}

has_key || {
  echo '
  export PATH=/opt/apache-maven-3.8.7/bin:$PATH
  ' >> ~/.bashrc
  source ~/.bashrc
}

mvn --version
```

## Criando um projeto na linha de comando

mvn archetype:generate -DgroupId=one.digitalinnovation -DartifactId=quick-start-maven -Darchetype=maven-archetype-quickstart -DinterativeMode=false

## Comandos

### Todas classes são compiladas e inseridas na pasta ../target/classes

mvn compile

### Testa a aplicação

mvn test

### Cria o jar da aplicação

mvn package

### Apagar a pasta ../target

mvn clean

## Criando diferentes tipos de projetos com o maven-archetype

Pesquisar: "maven archetype list"

[ maven archetype big list ](./maven-archetype.list)

Procure pelas instruções de execução do comando mvn archetype

[ Maven archetype list ](https://maven.apache.org/archetypes/)

## Web archetype

mvn archetype:generate -DarchetypeGroupId=org.apache.maven.archetypes -DarchetypeArtifactId=maven-archetype-webapp -DarchetypeVersion=1.4

[ mvnrepository ](https://mvnrepository.com/)

# POM (Project Object Model)

## Pom, dependências e repositórios

Principal arquivo do Maven.
Armazena a configuração do software e suas dependências.

- Nome do projeto
- Dependências
- Módulos
- Configurações de build
- Detalhes do projeto(nome, descrição, licença, url, etc...)
- Configurações de ambientes(repositórios, tracking, profiles)

## POM básico:

```xml
<project>
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.mycompany.app</groupId>
  <artefactId>my-app</artefactId>
  <version>1</version>
</project>
```

O Maven trabalha com o conceito de herança. Se instalar o POM básico o Maven busca as informações necessárias no [ super-POM ](./super-POM.xml).

## Repositórios

Repositórios são locais onde podemos encontrar plugins e bibliotecas que o Maven provê.

Eles são de dois tipos:

- Locais
- Remotos

O repositório remoto é configurado no POM como padrão para utilizar o Maven Center.

É possível alterar a configuração via settings.xml

O repositório remoto é configurado na tag <mirror></mirror>

```bash
#!/bin/bash
gedit /opt/apache-maven-3.8.7/conf/settings.xml
```

O repositório local é um estratégia de caching e está localizado na pasta:

```bash
cd ~/.m2/repository
```

Adicionamos um dependência e executamos mvn compile.

Caso as dependências não existam no repositório local, elas serão baixadas.

## Como adicionar dependências

Ir no arquivo pom.xml e procurar pela tag:

```xml
<dependencies>
    <dependency>
        <groupId>com.google.code.gson</groupId>
        <artifactId>gson</artifactId>
        <version>2.8.9</version>
    </dependency>
    <groupId>org.hibernate</groupId>
    <artefactId>hibernate-search-orm</artefactId>
    <version>5.11.9.Final</version>
  </dependency>
</dependencies>
```

## Tipos de dependências

```xml
<!-- https://mvnrepository.com/artifact/com.google.code.gson/gson -->
<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
    <version>2.8.9</version>
</dependency>
```

## Publicar componentes utilizando o Maven

Cria uma dependência no repositório local

```bash
#!/bin/bash
mvn install
```

Em outro projeto adiocione a tag dependencie e o:

```java
 import one.digitalinnovation.component.NomeDaClasse
```

Existem dois tipos de dependências:

- Diretas: dependências declaradas no pom.xml
- Transitiva: dependências obrigatórias das dependẽncias declaradas no pom.xml

## Classpath

- Runtime
- Test
- Compile

## Transitividade e Escopo

mvn install // adiociona as dependência diretas e transitivas no repositório local

Para lidar com esse problema, o Maven provê escopos para limitar a transitividade das dependências.

Existem 6 tipos de escopo - tag <scope>:

- compile : Escopo padrão. Está disponível em todos os classpaths. É transitivo.

```xml
<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
    <version>2.8.9</version>
    <scope>compile</scope>
</dependency>
```

- provided : A dependência será fornecida em tempo de execução. Ex. Servlet API, Java EE APIs. Disponível nos classpath compile e test(não disponível em runtime). Não é transitiva.

```xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>3.1.0</version>
    <scope>provided</scope>
</dependency>
```

- runtime : dependência necessária somente para execução e não compilação.

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>6.0.6</version>
    <scope>runtime</scope>
</dependency>
```

- test : disponível somente para compilação e execução de testes. Não é transitivo.

```xml
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-engine</artifactId>
    <version>${junit.jupiter.version}</version>
    <scope>test</scope>
</dependency>
```

- system : similar ao escopo provided exceto por ser necessário prover o JAR explicitamente. Classpath compile e test. Não é transitiva

```xml
<dependency>
    <groupId>com.baeldung</groupId>
    <artifactId>custom-dependency</artifactId>
    <version>1.3.2</version>
    <scope>system</scope>
    <systemPath>${project.basedir}/libs/custeom-dependency-1.3.2.jar</systemPath>
</dependency>
```

- import : disponível apenas com uma dependência do tipo pom e com tag \<dependencyManagement\>. Indica um processo de reutilizar dependências de um projeto.

```xml
<dependencyManagement>
  <dependencies>
    <dependency>
        <groupId>com.programmergirl</groupId>
        <artifactId>my-project</artifactId>
        <version>1.1</version>
        <type>pom</type>
        <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>
```

## Ver o classpath. Como o Maven está construindo cada escopo

```bash
#!/bin/bash
mvn dependency:build-classpath -DincludeScope=compile
mvn dependency:build-classpath -DincludeScope=test
mvn dependency:build-classpath -DincludeScope=rutime
```

## Dependências optionais

Dependências que não precisam e nem devem ser herdadas pelos projetos que irão utilizar o componente. Evitar que venham as dependências transitivas.

```xml
<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
    <version>2.8.9</version>
    <optional>true</optional>
</dependency>
```

## Exclusions

Utilizado quando o componente que você usa compartilha uma biblioteca que você já tem ou não quer ter disponível.

```xml
<project>
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.mycompany.app</groupId>
  <artefactId>my-app</artefactId>
  <version>1</version>
  <exclusions>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
  </exclusions>
</project>
```

## Clico de vida de Construção

É composto de 3 ciclos de vida
Cada ciclo possui fases(Maven Phases)
Cada fase possui objetivos(Maven Goals)

- Default Lifecycle

  - Principal ciclo
  - Responsável pelo deploy local
  - Composto por 23 fases
    - mvn validate
    - mvn compile
    - mvn test-compile
    - mvn test
    - mvn integration-test
    - mvn package
    - mvn install
    - mvn deploy
    - etc...

- Clean Lifecycle

  - Ciclo intermediário
  - Responsável pela limpeza do projeto
  - Composto por 3 fases
    - mvn pre-clean
    - mvn clean
    - mvn post-clean

- Site Lifecycle
  - Ciclo final
  - Responsável pela criação do site de documentação do projeto
  - Composto de 4 fases
    - mvn pre-site
    - mvn site
    - mvn post-site
    - mvn site-deploy

## Projetos Multi-Módulos

- Projeto
  - core
  - service
  - webapp
  - controller

Primeiro construir o projeto agregador
mvn archetype:generate -DgroupId=one.digital.innovation -DartefactId=project-parent -Darchetype=maven-quick-start

adicionar a tag \<packaging>pom\</packaging\>

```xml
  <groupId>one.digital.innovation</groupId>
  <artifactId>multi-app</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>pom</packaging>
```

executar o generate com alterações
mvn archetype:generate -DgroupId=one.digital.innovation -DartefactId=core -Darchetype=maven-quick-start -DinteractiveMode=false

mvn archetype:generate -DgroupId=one.digital.innovation -DartefactId=service -Darchetype=maven-quick-start -DinteractiveMode=false

mvn archetype:generate -DgroupId=one.digital.innovation -DartefactId=controller -Darchetype=maven-quick-start -DinteractiveMode=false

## Plugins

- A maioria das funcionalidades são providas por plugins
- Estilo arquitetural para extensibilidade(criar seu próprio plugin)
- Escrito prioritariamente em Java e disponibilizdos em JARs

Plugins mais utilizados:

- eclipse
- jacoco
- ear
- war
- compile
- clean
- checkstyle
- javadoc

exemplo de uso:
mvn [plugin-name]:[goal-name]
mvn dependency:help

## Configuração do plugin

A configuração do plugin é feita pela tag \<build>

```xml
<build>
  <plugins>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artefactId>maven-compiler-plugin</artefactId>
      <version>3.8.0</version>
      <configuration>
        <release>11</release>
      </configuration>
    </plugin>
  </plugins>
</build>
```

Gerar javadoc

```xml
<build>
  <plugins>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artefactId>maven-javadoc-plugin</artefactId>
      <version>3.3.1</version>
    </plugin>
  </plugins>
</build>
```
