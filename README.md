# POM (Project Object Model)

Principal arquivo do Maven.
Armazena a configuração do software e suas dependências.

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

mvn compile

# Todas classes são compiladas e inseridas na pasta ../target/classes

mvn test
mvn package
