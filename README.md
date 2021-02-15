# jenkins-with-dotnet
Criação do Jenkins com Blueocean já instalando o dotnet cli com o SDK 3.1 na imagem.

# Etapas para montar o ambiente do Jenkins via docker
# Build Docker Image
# Executar o comando abaixo na pasta onde está o arquivo Dockerfile
docker build -t jenkins-with-dotnet .
# O arquivo Dockerfile está configurado para:
# 1 - Instalar o Jenkins via imagem oficial
# 2 - Instalar o Plugin Blue Ocean do Jenkins
# 3 - Instalar o Dotnet Core SDK 3.1 (Usado no pipeline do Jenkins)

# Criação de um container com o Docker in Docker (DIND)
# O container do Jenkins irá utilizar esse container
docker run --name jenkins-docker --detach ^
  --privileged --network jenkins --network-alias docker ^
  --env DOCKER_TLS_CERTDIR=/certs ^
  --volume jenkins-docker-certs:/certs/client ^
  --volume jenkins-data:/var/jenkins_home ^
  docker:dind

# Run the container based on Image jenkins-with-dotnet
docker run --name jenkins-blueocean --detach ^
  --network jenkins --env DOCKER_HOST=tcp://docker:2376 ^
  --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 ^
  --volume jenkins-data:/var/jenkins_home ^
  --volume jenkins-docker-certs:/certs/client:ro ^
  --publish 8080:8080 --publish 50000:50000 jenkins-with-dotnet


# Ao iniciar o Jenkins pela primeira vez, é preciso habilitar o admin usando a adminInitialPassword
# Geralmente, a senha é exibida no log do container executado no script acima docker
# Porém, existe a possibilidade dele não exibir a senha nesse log
# Portanto, executar docker logs < Container name > para verificar o LOG
# Esse Log pode ser visualizado também no Docker Desktop para Windows
# Caso não consiga identificar a chave de jeito nenhum
# Entre na linha de comando do container usando "docker exec -it <id do container> /bin/sh"
# Em seguida, executar: sudo cat /var/jenkins_home/secrets/initialAdminPassword