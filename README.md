# assijus-docker

Este repositório contém os artefatos necessários para executar o Assijus utilizando o Docker. 
Antes de mais nada, será necessário instalar alguns pré-requisitos, se ainda não estiverem instalados:

- Instale o [Git](https://gist.github.com/derhuerst/1b15ff4652a867391f03)
- Instale o [Docker](https://docs.docker.com/install/)

Faça o checkout deste repositório em um diretório qualquer:

```
$ cd /var/lib
$ git clone https://github.com/assijus/assijus-docker.git assijus-docker
$ cd assijus-docker
```

Utilize o Docker para carregar e depois disponibilizar todos os serviços necessários ao funcionamento do Assijus:

```
$ docker-compose up
```

Pronto, o Assijus estará ativo. Para acessá-lo, aponte o navegador Google Chrome para http://localhost:8080/assijus

## Customizando

O funcionamento do Assijus pode ser customizado 
para as necessidades específicas de cada empresa através de parâmetros de ambiente.
Estes parâmetros estão definidos dentro do arquivo docker-compose.yml. 

```
PROP_ASSIJUS_REDIS_MASTER_HOST: redis
PROP_ASSIJUS_REDIS_MASTER_PORT: 6379
PROP_ASSIJUS_REDIS_SLAVE_HOST:
PROP_ASSIJUS_REDIS_SLAVE_PORT:
PROP_ASSIJUS_REDIS_PASSWORD:
PROP_ASSIJUS_REDIS_DATABASE: 1
PROP_BLUCSERVICE_URL: http://bluc:8080/blucservice/api/v1
PROP_ASSIJUS_POPUP_URLS: http://localhost:8080
PROP_ASSIJUS_SYSTEMS: testsigner
PROP_TESTSIGNER_URL: http://testsigner:8080/testsigner/api/v1
PROP_TESTSIGNER_PASSWORD: substitua esse texto por uma GUID aleatória para proteger o testsigner
# PROP_ASSIJUS_TIMESTAMP_PUBLIC_KEY:
# PROP_ASSIJUS_TIMESTAMP_PRIVATE_KEY:
```

A seguir, descreveremos os parâmetros que podem ser customizados e suas funções.

#### PROP_ASSIJUS_POPUP_URLS

Sistemas que se integram ao Assijus através do JavaScript precisam ter suas URLs identificadas neste parâmetro.
No exemplo acima, apenas o localhost pode chamar o Assijus via webservices. Se quiséssemos acrescentar outro sistema,
o parâmetro ficaria assim:

```
PROP_ASSIJUS_POPUP_URLS: http://localhost:8080,https://apolo.trf2.jus.br
```

#### PROP_ASSIJUS_SYSTEMS

Este parâmetro define quais são os sistemas aos quais o Assijus se conectará via webservices. 
No exemplo acima, ele está conectado apenas ao "testsigner". Caso quiséssemos conectá-lo também ao
"apolosigner", o parâmetro deveria ficar assim:

```
PROP_ASSIJUS_SYSTEMS: testsigner,apolosigner
```

Para cada sistema que é conectado via webservices, devem existir dois outros parâmetros, um para indicar a URL
do webservice REST e outro para indicar uma senha que será passada no _header HTTP_ "Authorization". 
Para proteger o sistema em questão contra acessos não autorizados, esta senha deve ser complexa e precisa 
ser mantida em sigilo. O webservice do sistema em questão não deve responder nenhuma chamada que não apresente
a senha correta em seu cabeçalho "Authorization".

Dando continuidade ao exemplo acima, para incluirmos o novo sistema apolosigner, além das propriedades 
do testsigner precisaríamos incluir mas duas:

```
PROP_TESTSIGNER_URL: http://testsigner:8080/testsigner/api/v1
PROP_TESTSIGNER_PASSWORD: substitua esse texto por uma GUID aleatória para proteger o testsigner
PROP_TESTSIGNER_URL: url do apolosigner
PROP_TESTSIGNER_PASSWORD: senha do apolosigner
```

Os sistemas conectados deverão responder aos métodos listados na [documentação](https://github.com/assijus/assijus#utilizando-webservices) do Assijus.

#### PROP_ASSIJUS_TIMESTAMP_PUBLIC_KEY e PROP_ASSIJUS_TIMESTAMP_PRIVATE_KEY

Além de produzir assinaturas digitais, o Assijus também pode criar um _JWT_ com o _hash_ de um documento 
e uma _timestamp_ para aumentar a segurança de assinaturas que sejam feitas apenas com _login_ e senha.
Caso haja interesse em usar essa funcionalidade, será necessário configurar os parâmetros acima com chaves
pública e privada aleatórias que podem ser obtidas através de uma chamada ao próprio Assijus:

```
POST http://.../assijus/api/v1/timestamp/generate-key-pair
```
