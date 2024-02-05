# Instalação do Carla Server 0.9.14

O Carla Simulator é um simulador de condução autonoma que possibilita o estudo de sensoriamento veicular, sistemas de autonomos de direção, desenvolvimento de testes em situações adversas que apresentam riscos ao condutor. Tudo isso de forma gratuita (open-source), robusta e flexivel, o que o torna ideal para o desenvolvimento de projetos focados em sistemas de segurança veicular. 

Neste tutorial, buscarei desmistificar a instalação deste simulator. Confesso que também foi desafiador quando tentei instalar pela primeira vez, mas creio que encontrei uma forma que descreverei neste tutorial.

### 1. Instação de requisitos

Verifique o seu sistema operacional, para o bom funcionamento deste tutorial. As configuraçõs da máquina que foi utilizada para a instalação foi:

- Ubuntu 20.04
- RTX 4060 TI 12GB  

De acordo com a documentação, é necessário possuir uma GPU de pelo menos 6GB dedicada para a utilização do simulado. Para o bom funcionamento e processamento dos gráficos, instale o cuda seguindo o [tutorial oficial](https://developer.nvidia.com/cuda-downloads). Siga os comandos abaixo para a instalação dos requisitos.

```bash
sudo apt-get update &&
sudo apt-get install wget software-properties-common &&
sudo add-apt-repository ppa:ubuntu-toolchain-r/test &&
wget -O - https://apt.llvm.org/llvm-snapshot.gpg.key|sudo apt-key add - &&
sudo apt-add-repository "deb http://apt.llvm.org/xenial/ llvm-toolchain-xenial-8 main" &&
sudo apt-get update

# Ubuntu 20.04
sudo apt-add-repository "deb http://apt.llvm.org/focal/ llvm-toolchain-focal main"
sudo apt-get install build-essential clang-10 lld-10 g++-7 cmake ninja-build libvulkan1 python python-dev python3-dev python3-pip libpng-dev libtiff5-dev libjpeg-dev tzdata sed curl unzip autoconf libtool rsync libxml2-dev git
sudo update-alternatives --install /usr/bin/clang++ clang++ /usr/lib/llvm-10/bin/clang++ 180 &&
sudo update-alternatives --install /usr/bin/clang clang /usr/lib/llvm-10/bin/clang 180
```

Verifique a versão do pip que está instalado na sua máquina, ele deve ser superios a versãop 20.3.

```python

# Python 3
pip3 -V

# Python 2
pip -V
```

Se for necessário atualizar a sua versão do pip, execute:

```python

# Python 3
pip3 install --upgrade pip

# Python 2
pip install --upgrade pip

```

Instale as dependências Python:

```python
python -m pip install --user setuptools &&
pip3 install --user -Iv setuptools==47.3.1 &&
python -m  pip install --user distro &&
pip3 install --user distro &&
python -m pip install --user wheel &&
pip3 install --user wheel auditwheel
```

### 2. Instalando a Unreal Engine

Para executar o simulador na versão server, é necessário que você tenha acesso ao repositório oficial da Unreal, para isso siga o [tutorial](https://www.unrealengine.com/en-US/ue-on-github) para conseguir acesso a organização da Unreal. Em seguida, após você ser aceito e o acesso liberado a organização no GitHub, execute os comandos abaixo:

```bash
# Clonando o repositório oficial da Ureal para o funcionamento do Carla Server
git clone --depth 1 -b carla https://github.com/CarlaUnreal/UnrealEngine.git ~/UnrealEngine_4.26

# Entre na pasta do repositório
cd ~/UnrealEngine_4.26

# Faça o build da engine
./Setup.sh && ./GenerateProjectFiles.sh && make

# Execute o modo de incialização da engine
cd ~/UnrealEngine_4.26/Engine/Binaries/Linux && ./UE4Editor

# Defina a path ao ambiente
nano ~/.bashrc

# Adcione o comando ao final do arquivo e salve
export UE4_ROOT=~/UnrealEngine_4.26 

```

### 3. Build do Simulador

Clone a o repositório oficial do carla, neste momento precisamos ficar atentos, pois a versão que vamos instalar é o Carla 0.9.14. Na branch principal, a que vem como padrão ao darmos o comando clone, possui a versão mais recente do simulador, ela possui alguma imcompatibilidades com um recurso necessário (a ROS Bridge0). Siga os comandos abaixo para instalar a versão correta. Para facilitar a instalação, modifiquei os arquivos que davam falha na instalação e subi neste repositório.

##### 
- Copiar o arquipo [Setup.sh](https://github.com/carla-simulator/carla/blob/master/Util/BuildTools/Setup.sh) da branch latest do carla oficial substituir a Util/BuildTools/Setup.sh do repositório da versão que vamos utilizar.

```bash
git clone https://github.com/carla-simulator/carla.git

# Mude para a versão 0.9.14
git checkout 0.9.14

# Atualizae
./Update.sh

# Copilar a api Python do Simulador
make PythonApi

# Copilar o Simulador
make launch
```

#### 3.1 Testando

Realize o teste abaixo para ver se tudo está funcionando.

``` bash
# Terminal A 
cd PythonAPI/examples
python3 -m pip install -r requirements.txt
python3 generate_traffic.py  

# Terminal B
cd PythonAPI/examples
python3 dynamic_weather.py 
```

### 4 - Build do ROS_BRIDGE

Realize a instalação do ROS na versão Noetic seguindo este [tutorial](http://wiki.ros.org/noetic/Installation/Ubuntu)
