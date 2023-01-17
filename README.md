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
    <groupId>one.digitalinnovation</groupId>
    <artefactId>app-spring</artefactId>
    <version>1.0.0</version>
  </dependency>
  <dependency>
    <groupId>org.hibernate</groupId>
    <artefactId>hibernate-search-orm</artefactId>
    <version>5.11.9.Final</version>
  </dependency>
</dependencies>
```
