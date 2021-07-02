#<center>1ª Parte: Tutoriais ROS</center>

<br/>

##_Instalação da distribuição Melodic_

<br/>

###Configurando o source.list
Configurando o computador para aceitar o software de "packages.ros.org".

	sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'

###Configurando as keys
	sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654

Caso esteja tendo problemas em conectar com o _keyserver_, você pode tentar substiuindo `hkp://pgp.mit.edu:80` ou `hkp://keyserver.ubuntu.com:80` no comando anterior.

Alternativamente, você pode usar o comando `curl` ao invés do comando `apt-key`. Podendo ser útil se estiver atrás de um servidor proxy.

	curl -sSL 'http://keyserver.ubuntu.com/pks/lookup?op=get&search=0xC1CF6E31E6BADE8868B172B4F42ED6FBAB17C654' | sudo apt-key add -

###Instalação 
Primeiramente, atualize seu pacote Debian

	sudo apt update

Agora faça a instalação completa da distribuição. Nela está incluso: ROS, rqt, rviz, bibliotecas robot-generic, simuladores 2D/3D e percepção 2D/3D

	sudo apt install ros-melodic-desktop-full

###Configurando o ambiente
É conveniente que as variáveis do ambiente do ROS sejam automaticamente adicionadas à sua sessão _bash_ sempre que uma nova _shell_ é inicializada.

	echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc
	source ~/.bashrc

###Dependências para a "build" dos pacotes
É conveniente instalar ferramentas de comando que permitem que faça downloads de arquivos relacionados ao ROS mais facilmente, com apenas um comando.

	 sudo apt install python-rosdep python-rosinstall python-rosinstall-generator python-wstool build-essential

Antes de utilizar essas ferramentas é necessário iniciar o `rosdep`. Este permite que o sistema faça instalações de dependências das fontes que deseja compilar e ele é necessário para "rodar" alguns componentes do ROS.

	sudo apt install python-rosdep
	sudo rosdep init


	rosdep update


<br/>

##_Instalação e configuração do Ambiente do ROS_

<br/>


###Criando um Workspace para o ROS
Criando e fazendo a _build_ de um catkin workspace

	mkdir -p ~/catkin_ws/src
	cd ~/catkin_ws/
	catkin_make
	source devel/setup.bash

Para ter certeza que seu workspace está propriamente configurado, tenha certeza que a variável do ambiente `ROS_PACKAGE_PATH` inclui o diretório em que você está no momento

	echo $ROS_PACKAGE_PATH


<br/>

##_Criando e construindo uma Package_

<br/>


###Criando uma Package no catkin worskpace
Para ser considerada uma Package, o diretório precisa conter os arquivos `package.xml` e `CMakeLists.txt`.
<br/>

E para cria-la é necessário especificar no comando `catkin_create_pkg` o `nome do pacote` e a lista de dependências.

	# Esse é um exemplo, não tente rodar esse código
	# catkin_create_pkg <nome_da_package> [dependência1] [dependência2] [dependência3]

Portanto, vamos criar uma Package chamada `curso`.

	cd ~/catkin_ws/src
	catkin_create_pkg curso std_msgs rospy roscpp

###Realizando a "build" e gerando uma fonte para o arquivo de "setup"
Fazendo a _build_ das packages presentes no catkin workspace.

	cd ~/catkin_ws
	catkin_make


<br/>

##_Conhecendo ROS Nodes_

<br/>

###Download de simuladores simples

	sudo apt-get install ros-melodic-ros-tutorials

###Roscore
A primeira ação a ser feita sempre que for usar o ROS é inicializar o Roscore

	roscore

Caso o Roscore não inicialize e o terminal retorne uma mensagem sobre falta de permissões, mude recursivamente o "dono" da pasta

	sudo chown -R <seu_username> ~/.ros

###Utilizando o rosrun
O comando rosrun permite que execute uma node em uma determinada package
<br/>

Usabilidade:

	# Esse é um exemplo, não tente rodar esse código
	# rosrun [nome_da_package] [nome_da_node]

Agora em um novo terminal, vamos executar a node chamada `turtlesim_node` que inicializa o simulador Turtle Sim.

	rosrun turtlesim turtlesim_node

###Utilizando o rosnode
O comando `rosnode` mostra informações sobre as nodes que estão funcionando no momento.
<br/>

Você pode conferir que ações efetuar pelo comando (em um novo terminal):

	rosnode -h


<br/>

##_Conhecendo ROS Topics_

<br/>

###Controlando a tartaruguinha do Turtle Sim pelo teclado

Ainda com o Turtle Sim aberto, em um novo terminal execute a node chamada `turtle_teleop_key`.

	rosrun turtlesim turtle_teleop_key

Agora você pode utilizar o teclado para movimentar a tartaruga. Mas para isso, é necessário que a tela do terminal onde digitou o comando acima esteja selecionada.

###Utilizando o _rqt_graph_
As nodes `turtlesim_node` e `turtle_teleop_key` estão comunicando uma com a outra por meio de um ROS Topic. `turtle_teleop_key` está "publicando" (_publishing_) as entradas em um Topic de quando acionamos o teclado, enquanto `turtlesim_node` está "lendo" ou "inscrevendo" (_subscribing_) no mesmo Topic para receber o sinal do acionamento do teclado e realizar a ação no simulador.
<br/>

Podemos perceber isso utilizando o `rqt_graph` que foi instalado quando instalamos a distribuição Melodic. Ele possui a função de mostrar por meio de um gráfico dinâmico o que está acontecendo no sistema.
<br/> 

Em um novo terminal digite:

	rosrun rqt_graph rqt_graph

###Utilizando o _rostopic echo_
O comando `rostopic` mostra informações sobre ROS Topics
<br/>

Você pode conferir que ações efetuar pelo comando:

	rostopic -h

`rostopic echo` mostra os dados publicados em um Topic
<br/>

Usabilidade:

	# Esse é um exemplo, não tente rodar esse código
	# rostopic echo [topic]

Vamos observar os dados do `command_velocity` publicado pela node `turtle_teleop_key`.

	rostopic echo /turtle1/cmd_vel

###Utilizando o _rostopic type_
O comando `rostopic_type` retorna o tipo da Message de qualquer Topic sendo publicado.
<br/>

Usabilidade:

	# Esse é um exemplo, não tente rodar esse código
	# rostopic type [topic]

Agora então digite:

	rostopic type /turtle1/cmd_vel

Você deve receber: `geometry_msgs/Twist`. Com essa informação, nós conseguimos ver os detalhes da Message usando o comando `rosmsg`:

	rosmsg show geometry_msgs/Twist

###Utilizando o _rostopic pub_
O comando `rostopic pub` publica algum dado em um Topic funcionando atualmente.
<br/>

Usabilidade:

	# Esse é um exemplo, não tente rodar esse código
	# rostopic pub [topic] [msg_type] [argumentos]

Vamos enviar uma única mensagem para o Turtle Sim dizendo a ele para se mover com uma velocidade linear de 2.0 e velocidade angular de 1.8.

	rostopic pub -1 /turtle1/cmd_vel geometry_msgs/Twist -- '[2.0, 0.0, 0.0]' '[0.0, 0.0, 1.8]'

Podemos fazer com que a tartaruga continue se movimentando infinitamente adicionando `-r` no comando:

	rostopic pub /turtle1/cmd_vel geometry_msgs/Twist -r 1 -- '[2.0, 0.0, 0.0]' '[0.0, 0.0, -1.8]'

###Utilizando o _rqt_plot_
O `rqt_plot` gera um gráfico que mostra os dados publicados nos Topics ao longo do tempo:

	rosrun rqt_plot rqt_plot

Adicione na caixa de texto os Topics `/turtle1/pose/x` e `/turtle1/pose/y` para ver no gráfico as posições da tartaruga no gráfico ao longo do tempo.


<br/>

##_Conhecendo ROS Services e Parameters_

<br/>

###Utilizando o _rosservice call_
O comando `rosservice` mostra informações sobre ROS Services
<br/>

Você pode conferir que ações efetuar pelo comando:

	rosservice -h

O `rosservice call` chama um Service com seus respectivos argumentos
<br/>

Usabilidade:

	# Esse é um exemplo, não tente rodar esse código
	# rosservice call [service] [argumentos]

Vamos chamar um Service que limpa o fundo da node `turtlesim_node`. Esta ação não necessita de argumentos.

	rosservice call /clear

Agora em um caso onde o Service possui argumentos. Vamos ver quais são eles:

	rosservice type /spawn | rossrv show

Esse Service `/spawn` nos permite "spawnar" uma nova tartaruga em um dado local e orientação. Agora que sabemos os argumentos necessários, vamos "spawnar" mais uma tartaruga no simulador:

	rosservice call /spawn 2 2 0.2 ""

###Utilizando o _rosparam set_ e o _rosparam get_
O comando `rosparam` mostra informações sobre ROS Parameter Services e permite armazenar e manipular seus dados
<br/>

Você pode conferir que ações efetuar pelo comando:

	rosparam -h

O `rosparam list` mostra os parâmetros da node `turtlesim_node`

	rosparam list

Agora, podemos mudar e resgatar um dos valores utilizando o `rosparam set` e `rosparam get` respectivamente:
<br/>

Usabilidade:

	# Esse é um exemplo, não tente rodar esse código
	# rosparam set [nome_do_parâmetro]
	# rosparam get [nome_do_parâmetro]

Vamos mudar o valor do "red channel" do sistema de cor RGB do plano de fundo do simulador:

	rosparam set /turtlesim/background_r 150

Para que a mudança faça efeito é necessário chamar o Service `clear`:

	rosservice call /clear

E para visualizar o conteúdo do todo Parameter Server utilizamos:

	rosparam get /


<br/>

##_Conhecendo o ROS Launch_

<br/>

###Utilizando o _roslaunch_
O comando `roslaunch` inicializa nodes definidas em um launch file
<br/>

Usabilidade:

	# Esse é um exemplo, não tente rodar esse código
	# roslaunch [package] [nome_do_arquivo.launch]

Para uma maior organização vamos criar primeiramente um diretório chamado "launch" no qual armazenará os "launch files"

	source devel/setup.bash
	roscd curso
	mkdir launch
	cd launch

Agora criamos um launch file chamado `turtlemimic.launch`

	touch turtlemimic.launch

Copie e cole no arquivo o seguinte código:

	<launch>

  	  <group ns="turtlesim1">
    	    <node pkg="turtlesim" name="sim" type="turtlesim_node"/>
  	  </group>

  	  <group ns="turtlesim2">
    	    <node pkg="turtlesim" name="sim" type="turtlesim_node"/>
  	  </group>

	  <node pkg="turtlesim" name="mimic" type="mimic">
	    <remap from="input" to="turtlesim1/turtle1"/>
	    <remap from="output" to="turtlesim2/turtle1"/>
	  </node>

	</launch>

Você pode editar o arquivo a partir do comando `rosed` abaixo. (Da mesma forma, abaixo está o comando de instalação do editor de texto Vim - editor padrão do ROS)

	sudo apt install vim
	rosed curso turtlemimic.launch

Após editado o arquivo, vamos executá-lo.

	roslaunch curso turtlemimic.launch

E em um novo terminal vamos digitar um novo comando `rostopic`

	rostopic pub /turtlesim1/turtle1/cmd_vel geometry_msgs/Twist -r 1 -- '[2.0, 0.0, 0.0]' '[0.0, 0.0, -1.8]'

Podemos entender como foi feito a "mímica" abrindo o `rqt_graph`

	rqt_graph


<br/>

##_Criando um ROS msg e srv_

<br/>

###Criando uma msg
Uma msg é armazenada no diretório "msg" específico. Portanto, vamos criar um.

	source devel/setup.bash
	roscd curso
	mkdir msg
	cd msg

Vamos agora criar e editar uma msg chamada `Num.msg`

	touch Num.msg
	rosed curso Num.msg

E cole as seguintes tipagens:

	int64 num
	string first_name
	string last_name
	uint8 age
	uint32 score

###Editando o _package.xml_ e _CMakeLists.txt_
Após criada a msg, tenha certeza de que no arquivo `package.xml`, as seguintes linhas não estão comentadas

	<build_depend>message_generation</build_depend>
	<exec_depend>message_runtime</exec_depend> 

E no arquivo `CMakeLists.txt`, adicione a dependência `message generation` para que as Messages possam ser geradas.
<br/>

Você pode fazer isso adicionando na lista de `COMPONENTS` como mostrado abaixo:

	# Não só adicione isso no arquivo, modifique o texto existente adicionando "message_generation" antes dos parênteses de fechamento.
	find_package(catkin REQUIRED COMPONENTS
   		roscpp
   		rospy
   		std_msgs
   		message_generation
	)

Da mesma forma, tenha certeza de que esteja exportando a execução da dependência (removendo o comentário e/ou adicionando as linhas abaixo):

	catkin_package(
  	...
  	CATKIN_DEPENDS message_runtime ...
  	...)

Agora remova o comentário das linhas abaixo e substitua as `Message*.msg` por `Num.msg`

	#Antes
	
	# add_message_files(
	#   FILES
	#   Message1.msg
	#   Message2.msg
	# )

	#Depois
	
	add_message_files(
	  FILES
	  Num.msg
	)

Por fim, precisamos garantir que a funcção `generate_messages ()` é chamada. Para isso, remova os comentários das seguintes linhas:

	generate_messages(
  	DEPENDENCIES
  	std_msgs
	)

###Testando a msg criada
Vamos ter certeza de que o ROS possa reconhecer a msg usando o comando `rosmsg show`
<br/>

Usabilidade:

	# Esse é um exemplo, não tente rodar esse código
	# rosmsg show [tipo da message]

Testando então:

	rosmsg show curso/Num

Você receberá como resposta:

	int64 num
	string first_name
	string last_name
	uint8 age
	uint32 score

###Criando um srv
Uma msg é armazenada no diretório "srv" específico. Portanto, vamos criar um.

	roscd curso
	mkdir srv
	cd srv

Ao invés de criar um srv por nós mesmos, vamos copiar um já existente de uma outra package.
<br/>

Para isso, o comando `roscp` é útil para copiar arquivos de uma package a outra
<br/>

Usabilidade:

	# Esse é um exemplo, não tente rodar esse código
	# roscp [nome_da_package] [arquivo_a_copiar] [local_do_arquivo_a_copiar]

Agora podemos copiar um srv da package `ropsy_tutorials`

	roscp rospy_tutorials AddTwoInts.srv AddTwoInts.srv

###Editando o _CMake_Lists.txt_
A dependência `message_generation` funciona tanto para msg quanto para srv, como anteriormente já foi configurada, não precisamos realizá-la novamente.
<br/>

A mudança necessária será a remoção dos comentários das linhas abaixo e a substituição dos `Service*.srv` por `AddTwoInts.srv` que é o nome do srv copiado.

	#Antes
	
	# add_service_files(
	#   FILES
	#   Service1.srv
	#   Service2.srv
	# )

	#Depois
	
	add_service_files(
	  FILES
	  AddTwoInts.srv
	)

###Testando o srv criado
Vamos ter certeza de que o ROS possa reconhecer o srv usando o comando `rossrv show`
<br/>

Usabilidade:

	# Esse é um exemplo, não tente rodar esse código
	# rossrv show [tipo do service]

Testando então:

	rossrv show curso/AddTwoInts

Você receberá como resposta:

	int64 a
	int64 b
	---
	int64 sum

###Construção dos arquivos criados
Como criou novas Messages e Services, precisamos fazer a "build" da nossa package novamente:

	cd
	cd catkin_ws
	catkin_make


<br/>

##_Escrevendo um Publisher e Subscriber simples_

<br/>

###Escrevendo uma Publisher Node
Vamos criar uma node que irá continuamente transmitir uma Message. Para isso, vamos criar um diretório na nossa package onde irá conter os arquivos fontes ou _source files_

	roscd curso
	mkdir -p src
	cd src

Vamos agora criar um arquivo chamado `talker.cpp` retirado do site:
<br/>

<https://raw.github.com/ros/ros_tutorials/kinetic-devel/roscpp_tutorials/talker/talker.cpp>
<br/>

Da mesma forma, vamos abri-lo para podermos analisar o código presente e transforma-lo em um arquivo executável.

	wget https://raw.github.com/ros/ros_tutorials/kinetic-devel/roscpp_tutorials/talker/talker.cpp
	chmod +x talker.cpp
	rosed curso talker.cpp

Caso recebam algum tipo de aviso, role a página até o final e tecle o botão "a" no seu teclado para que possam visualizar o código

###Analisando a Publisher
Agora vamos entender o código

	27 #include "ros/ros.h"
	28 

`ros/ros.h` é uma conveniente inclusão que inclui todas as _headers_ necessárias para usar as peças públicas mais comuns do sistema do ROS

	28 #include "std_msgs/String.h"
	29 

As linhas acima incluem a Message `std_msgs/String` que reside na package `std_msgs`. Essa é uma _header_ gerada automaticamente pelo arquivo `String.msg` presente na package

	47   ros::init(argc, argv, "talker");

Inicializa o ROS. É onde também especificamos o nome da nossa node. (Nomes de nodes precisam ser únicas em um sistema a funcionar)

	54   ros::NodeHandle n;

Cria um manipulador ou _handle_ para o processamento da node. O primeiro _NodeHandle_ criado vai inicializar a node e o último irá destruí-la limpando os recursos que a node estava usando

	73   ros::Publisher chatter_pub = n.advertise<std_msgs::String>("chatter", 1000);

Diz para o Master que estaremos publicando uma Message do tipo `std_msgs/String` no Topic _chatter_. Permite que o Master diga a qualquer node "escutando" no Topic _chatter_ que estaremos publicando dados nesse tópico. O segundo argumento, é o tamanho da fila que estamos publicando. Nesse caso, se estamos publicando muito rapidamente, ele irá "buffar" no máximo 1000 Messages antes de começar a descartar as antigas.
<br/>

`NodeHandle::advertise()` retorna um objeto `ros::Publisher` que serve para dois propósitos:

* Conter um método `publish()` que permite que publiquemos Messages dentro do tópico que foi criado junto
* Quando sair do escopo, irá automaticamente desavisar (_unadvertise_)

	75   ros::Rate loop_rate(10);

Um objeto `ros::Rate` permite especificar a frequência que você gostaria que o ciclo possua. Ele irá verificar quanto tempo se passou até que a última chamada ao `ros::sleep()` foi feita. Da mesma forma, "dorme" (_sleep_) por um correta quantidade de tempo.
<br/>

Nesse caso, dizemos a ele que queremos funcionar a 10Hz.

	81   int count = 0;
	82   while (ros::ok())
	83   {

Por padrão, `roscpp` instalará um manipulador SIGINT que providenciará o desligamento da manipulação, que no caso irá causar `ros::ok()` retornar falso.
<br/>

`ros::ok()` retornará falso se:

* Um SIGINT é recebido;
* Fomos expulsos da rede por uma outra node de mesmo nome;
* `ros::shutdown()` foi chamado por outra parte da aplicação;
* Todos os `ros::NodeHandles` foram destruídos.

Uma vez que `ros::ok` retorne falso, todas as chamadas do ROS irão falhar

	87     std_msgs::String msg;
	88 
	89     std::stringstream ss;
	90     ss << "hello world " << count;
	91     msg.data = ss.str();

Transmitimos uma mensagem no ROS usando uma classe de mensagens-adaptadas, usualmente gerada por um arquivo msg. 
<br/>

_datatypes_ mais complicados são possíveis, mas por agora iremos usar uma Message String padrão, que possui um membro "dado".

	101     chatter_pub.publish(msg);

Agora nós estamos realmente transmitindo a Message a todos que estão conectados

	93     ROS_INFO("%s", msg.data.c_str());

`ROS_INFO` e amigos são os os nossos substitutos para o _printf/cout_

	103     ros::spinOnce();

Chamando o `ros::spinOnce()` aqui, não é necessário para este programa simples pois não estamos recebendo nenhum _callback_. Entretanto, se você for adicionar uma "subscription" nessa aplicação, e não tem o `ros::spinOnce()` aqui, seus _callbacks_ nunca serão chamados. Então , adicioná-lo é uma boa medida

	105     loop_rate.sleep();

Agora usamos o objeto `ros::Rate` para "dormir" (_sleep_) pelo tempo restante que nos permite alcançar a taxa de "publish" de 10Hz desejada.
<br/>

Abaixo há uma versão condensada do que está acontecendo

* Inicializa o sistema do ROS
* Avisa se vamos publicar Messages `std_msgs/String` no Topic _chatter_ para o Master
* "Loop" enquanto publicamos Messages ao _chatter_ 10 vezes por segundo

###Escrevendo um Subscriber Node
Agora precisamos escrever uma Node que receba as Messages
<br/>

Dentro do diretório src anteriormente criado vamos criar um arquivo chamado `listener.cpp` retirado do site:
<br/>

<https://raw.github.com/ros/ros_tutorials/kinetic-devel/roscpp_tutorials/listener/listener.cpp>
<br/>

Da mesma forma, vamos abri-lo para podermos analisar o código presente e transforma-lo em um arquivo executável.

	wget https://raw.github.com/ros/ros_tutorials/kinetic-devel/roscpp_tutorials/listener/listener.cpp
	chmod +x listener.cpp
	rosed curso listener.cpp

Caso recebam algum tipo de aviso, role a página até o final e tecle o botão "a" no seu teclado para que possam visualizar o código

###Analisando o Subscriber
Vamos analisar o código ignorando algumas partes que já foram explicadas anteriormente

	34 void chatterCallback(const std_msgs::String::ConstPtr& msg)
	35 {
	36   ROS_INFO("I heard: [%s]", msg->data.c_str());
	37 }

Essa é a função de _callback_ que será chamada quando uma nova Message chegar no Topic _chatter_. A Message é passada em um _boost shared_ptr_ que significa que você pode armazená-lo se quiser, sem preocupar se está sendo deletado abaixo de você e sem copiar o dado subjacente.

	75   ros::Subscriber sub = n.subscribe("chatter", 1000, chatterCallback);

"Subscribe" no Topic _chatter_ com o Master. ROS irá chamar a função `chatterCallback()` sempre que uma nova mensagem chega. O segundo argumento é o tamanho da fila, no caso de não ser possível processar Messages rápido o suficiente. Nesse caso, se a fila chega a 1000 Messages, começaremos a descartar velhas Messages enquanto novas chegam.
<br/>

`NodeHandle::subscribe()` retorna um objeto `ros::Subscriber` que você precisa manter até o momento que desejar se desinscrever (_unsubscribe_). Quando o objeto Subscriber é destruído, ele irá automaticamente se desinscrever do Topic _chatter_
<br/>

Existem versões da função `NodeHandle::subscribe()` que permite especificar membros da classe, ou até qualquer coisa capaz de ser chamada pelo objeto _Boost.Function_

	82   ros::spin();

`ros::spin()` entra em um loop, chamando _callbacks_ de Messages o mais rápido possível. Mas não se preocupe, se não existe nada a ser feito ele não irá usar muita CPU. `ros::spin()` irá sair uma vez que `ros::ok()` retorne falso, o que significa que `ros::shutdown()` foi chamado, ou pelo _handler_ de desligamento (o Master nos dizendo para desligar), ou sendo chamado manualmente.
<br/>

Existem outras formas de puxar _callbacks_, mas não iremos preocupar com elas por agora.
<br/>

Novamente, abaixo está uma versão condensada do que está acontecendo:

* Inicia o sistema do ROS;
* Se inscreve no Topic _chatter_;
* "Spin", esperando a chegada de Messages;
* Quando uma Message chega, a função `chatterCallback()`é chamada.

###Construindo as Nodes
Editando novamente o arquivo `CMakeLists.txt`, retirando comentários e adicionando linhas de código, o conteúdo deve estar mais ou menos assim: 
<br/>

(Mantendo as modificações feitas nos passos anteriores)

	cmake_minimum_required(VERSION 2.8.3)
	project(curso)

	## Find catkin and any catkin packages
	find_package(catkin REQUIRED COMPONENTS roscpp rospy std_msgs message_generation)

	## Declare ROS messages and services
	add_message_files(FILES Num.msg)
	add_service_files(FILES AddTwoInts.srv)
	
	## Generate added messages and services
	generate_messages(DEPENDENCIES std_msgs)
	
	## Declare a catkin package
	catkin_package()
	
	## Build talker and listener
	include_directories(include ${catkin_INCLUDE_DIRS})
	
	add_executable(talker src/talker.cpp)
	target_link_libraries(talker ${catkin_LIBRARIES})
	add_dependencies(talker curso_generate_messages_cpp)
	
	add_executable(listener src/listener.cpp)
	target_link_libraries(listener ${catkin_LIBRARIES})
	add_dependencies(listener curso_generate_messages_cpp)

As últimas 6 linhas de código adicionadas irão criar dois executáveis, `talker` e `listener` que por padrão irão para o diretório `devel`
<br/>

Note que voce teve que adicionar duas dependências para os "executable targets" aos "message generation targets"

	add_dependencies(talker curso_generate_messages_cpp)

Isso garante que as "messages headers" dessa package sejam geradas antes de usá-las. Se você usar Messages de outras Packages de dentro do seu catkin workspace, você precisa adicionar dependências para os seus respectivos "generations targets" também, pois o catkin constroi todos os projetos em paralelo. Assim como _Groovy_ você pode usar a seguinte variável para depender de todos os alvos necessários:

	target_link_libraries(talker ${catkin_LIBRARIES})

Então agora você pode faze a "build"

	cd
	cd catkin_ws
	catkin_make 

###Testando o Publisher e Subscriber criados
Tenha certeza que roscore esteja ativo. Em um novo terminal

	roscore

Tenha certeza que fez a "source" do seu arquivo do workspace `setup.sh`

	cd
	cd catkin_ws
	source devel/setup.bash

Vamos agora testar o Publisher `talker`

	rosrun curso talker

Você irá receber algo parecido com isso:

	[INFO] [WallTime: 1314931831.774057] hello world 1314931831.77
	[INFO] [WallTime: 1314931832.775497] hello world 1314931832.77
	[INFO] [WallTime: 1314931833.778937] hello world 1314931833.78
	[INFO] [WallTime: 1314931834.782059] hello world 1314931834.78
	[INFO] [WallTime: 1314931835.784853] hello world 1314931835.78
	[INFO] [WallTime: 1314931836.788106] hello world 1314931836.79

Agora em um novo terminal, vamos testar o Subscriber `listener`

	rosrun curso listener

Você receberá algo parecido com isso:

	[INFO] [WallTime: 1314931969.258941] /listener_17657_1314931968795I heard hello world 1314931969.26
	[INFO] [WallTime: 1314931970.262246] /listener_17657_1314931968795I heard hello world 1314931970.26
	[INFO] [WallTime: 1314931971.266348] /listener_17657_1314931968795I heard hello world 1314931971.26
	[INFO] [WallTime: 1314931972.270429] /listener_17657_1314931968795I heard hello world 1314931972.27
	[INFO] [WallTime: 1314931973.274382] /listener_17657_1314931968795I heard hello world 1314931973.27
	[INFO] [WallTime: 1314931974.277694] /listener_17657_1314931968795I heard hello world 1314931974.28
	[INFO] [WallTime: 1314931975.283708] /listener_17657_1314931968795I heard hello world 1314931975.28

Após feitos os testes pode apertar Ctrl-C para finalizar ambas as Nodes.


<br/>

##_Escrevendo um Service e Client simples_

<br/>

###Escrevendo um Service Node
Vamos criar um Service Node `add_two_ints_server` que receberá dois inteiros e retorna a soma. Ele estará armazenado no diretório src criado nos passos anteriores.

	roscd curso/src
	touch add_two_ints_server.cpp

Agora vamos editá-lo e transforma-lo em um arquivo executável.

	chmod +x add_two_ints_server.cpp
	rosed curso add_two_ints_server.cpp

Cole o seguinte código no arquivo:

	#include "ros/ros.h"
	#include "curso/AddTwoInts.h"

	bool add(curso::AddTwoInts::Request  &req,
	         curso::AddTwoInts::Response &res)
	{
	  res.sum = req.a + req.b;
	  ROS_INFO("request: x=%ld, y=%ld", (long int)req.a, (long int)req.b);
	  ROS_INFO("sending back response: [%ld]", (long int)res.sum);
	  return true;
	}
	
	int main(int argc, char **argv)
	{
	  ros::init(argc, argv, "add_two_ints_server");
	  ros::NodeHandle n;
	
	  ros::ServiceServer service = n.advertiseService("add_two_ints", add);
	  ROS_INFO("Ready to add two ints.");
	  ros::spin();
	
	  return 0;
	}

###Analisando o Service
Vamos agora entender o código

	1 #include "ros/ros.h"
	2 #include "curso/AddTwoInts.h"
	3 

`curso/AddTwoInts.h` é o arquivo _header_ gerado do arquivo srv que criamos nos passos anteriores.

	4 bool add(curso::AddTwoInts::Request  &req,
	5          curso::AddTwoInts::Response &res)

Essa função providencia o Service que adiciona dois inteiros. Ele recebe o tipo de "solicitação" (_request_) e de "resposta" (_response_) definido no arquivo srv e retorna um booleano.

	6 {
	7   res.sum = req.a + req.b;
	8   ROS_INFO("request: x=%ld, y=%ld", (long int)req.a, (long int)req.b);
	9   ROS_INFO("sending back response: [%ld]", (long int)res.sum);
	10   return true;
	11 }

Acima, os dois inteiros são adicionados e armazenados na "resposta". Depois, algumas informações sobre a "solicitação" e a "resposta" são registrados. Por fim, o Service retorna verdadeiro quando completo

	18   ros::ServiceServer service = n.advertiseService("add_two_ints", add);

Acima, o Service é criado e anunciado sobre o ROS

###Escrevendo um Client Node
Vamos criar um Service Node `add_two_ints_client`. Ele estará armazenado novamente no diretório src.
<br/>

	roscd curso/src
	touch add_two_ints_client.cpp

Agora vamos editá-lo e transforma-lo em um arquivo executável

	chmod +x add_two_ints_client.cpp
	rosed curso add_two_ints_client.cpp

Cole o seguinte código no arquivo:

	#include "ros/ros.h"
	#include "curso/AddTwoInts.h"
	#include <cstdlib>
	
	int main(int argc, char **argv)
	{
	  ros::init(argc, argv, "add_two_ints_client");
	  if (argc != 3)
	  {
	    ROS_INFO("usage: add_two_ints_client X Y");
	    return 1;
	  }

	  ros::NodeHandle n;
	  ros::ServiceClient client = n.serviceClient<curso::AddTwoInts>("add_two_ints");
	  curso::AddTwoInts srv;
	  srv.request.a = atoll(argv[1]);
	  srv.request.b = atoll(argv[2]);
	  if (client.call(srv))
	  {
	    ROS_INFO("Sum: %ld", (long int)srv.response.sum);
	  }
	  else
	  {
	    ROS_ERROR("Failed to call service add_two_ints");
	    return 1;
	  }
	
	  return 0;
	}

###Analisando o Client
Vamos agora entender o código

	15   ros::ServiceClient client = n.serviceClient<curso::AddTwoInts>("add_two_ints");

A linha acima cria um Client para o Service `add_two_ints`. O objeto `ros::ServiceClient` é usado para chamar o Service posteriormente

	16   curso::AddTwoInts srv;
	17   srv.request.a = atoll(argv[1]);
	18   srv.request.b = atoll(argv[2]);

Acima, nós instaciamos um Service Class que se "autogera" e atribui valores dentro de seu membro de "solicitação". Um Service Class contém dois membros, _request_ e _response_. Ele da mesma forma contém duas definições de classe _Request_ e _Response_.

	19   if (client.call(srv))

A linha acima realmente chama o Service. Desde que as chamadas do Service são bloqueadas, ele retornará uma vez que a chamada é finalizada. Se a chamada do Service for bem sucedida, `call()` irá retornar verdadeiro e o valor em `srv.response` será valido. Se a chamada não for bem sucedida , `call()` retornará falso e o valor em `srv.response` será inválido.

###Construindo as Nodes
Novamente, será necessário editar o arquivo `CMakeLists.txt`, adicionando no final do arquivo o seguinte código:

	add_executable(add_two_ints_server src/add_two_ints_server.cpp)
	target_link_libraries(add_two_ints_server ${catkin_LIBRARIES})
	add_dependencies(add_two_ints_server curso_gencpp)
	
	add_executable(add_two_ints_client src/add_two_ints_client.cpp)
	target_link_libraries(add_two_ints_client ${catkin_LIBRARIES})
	add_dependencies(add_two_ints_client curso_gencpp)

As linhas acima irão criar dois executáveis, `add_two_ints_server` e `add_two_ints_client` que por padrão irão para seu diretório `devel`.
<br/>

Agora, podemos fazer a "build" novamente

	cd
	cd catkin_ws
	catkin_make

###Testando o Service e Client criados
Tenha certeza que roscore esteja ativo. Em um novo terminal

	roscore

Tenha certeza que fez a "source" do seu arquivo do workspace `setup.sh`

	cd catkin_ws
	source devel/setup.bash

Vamos agora testar o server

	rosrun curso add_two_ints_server

Você irá receber algo parecido com isso:

	Ready to add two ints.

Agora em um outro terminal, vamos colocar para funcionar o client com os argumentos necessários

	rosrun curso add_two_ints_client 1 3

No terminal do client, você irá receber algo parecido com isso:

	Sum: 4

E no terminal do server, algo parecido com isso:

	request: x=1, y=3
	sending back response: [4]

<br/>

#<center>2ª Parte: Tutoriais Gazebo e Gazebo+ROS</center>

<br/>

##_Trabalhando com os Models_

<br/>

###Construindo um pequeno robô
Vamos experenciar a criação de objetos pelo Edit Model do Gazebo! No caso, vamos criar um pequeno robô. As alterações necessárias de suas dimensões e especificações, estão dispostas abaixo:

<br/>
####Divisão: Chaci

####_Link:_

Pose x: `0`
Pose y: `0`
Pose z: `0`

####_Visual:_

Geometry x: `0,4`
Geometry y: `0,2`
Geometry z: `0,1`

####_Caster_Visual:_

Pose x: `-0,15`
Pose y: `0`
Pose z: `-0,05`

Geometry Sphere Radius: `0,05`

####_Collision:_

Geometry x: `0,4`
Geometry y: `0,2`
Geometry z: `0,1`

####_Caster_Collision:_

Geometry x: `-0,15`
Geometry y: `0`
Geometry z: `-0,05`

Geometry Sphere Radius: `0,05`

Friction Mu: `0`
Friction Mu2: `0`
Friction Slip1: `1`
Friction Slip2: `1`

<br/>
####Divisão: Roda esquerda dianteira

####_Link:_

Pose x: `0,1`
Pose y: `0,13`
Pose z: `0,1`

Roll: `0,000048`
Pitch: `1,5707`
Yaw: `1,570748`

####_Visual:_

Geometry Cylinder Radius: `0,1`
Geometry Cylinder Length: `0,05`

####_Collision:_

Geometry Cylinder Radius: `0,1`
Geometry Cylinder Length: `0,05`

####Joint

####_Joint Axis:_

x: `0`
y: `1`
z: `0`

####_Joint Pose:_

Pose x: `0`
Pose y: `0`
Pose z: `-0,03`

Roll: `0`
Pitch: `0`
Yaw: `0`

<br/>
####Divisão: Roda direita dianteira

####_Link:_

Pose x: `0,1`
Pose y: `-0,13`
Pose z: `0,1`

Roll: `0,000048`
Pitch: `1,5707`
Yaw: `1,570748`

####_Visual:_

Geometry Cylinder Radius: `0,1`
Geometry Cylinder Length: `0,05`

####_Collision:_

Geometry Cylinder Radius: `0,1`
Geometry Cylinder Length: `0,05`

####Joint

####_Joint Axis:_

x: `0`
y: `1`
z: `0`

####_Joint Pose:_

Pose x: `0`
Pose y: `0`
Pose z: `0,03`

Roll: `0`
Pitch: `0`
Yaw: `0`

<br>
###Anexar uma mesh no robô
Vá para o diretório onde salvou o pequeno robô criado.

	cd ~/<caminho do diretório>/

Abra o arquivo `model.sdf` no seu editor favorito

	gedit ~/<caminho do dirtório>/model.sdf

Agora adicionemos uma mesh para o visual do chaci. Encontre a tag `<visual>` com o `name=visual`, que se parece com:

	<visual name='visual'>
	  <geometry>
	    <box>
	      <size>.4 .2 .1</size>
	    </box>
	  </geometry>
	</visual>

Nesse exemplo, usaremos uma mesh do model _pioneer2dx_. Mude o elemento visual da seguinte forma:

	<visual name='visual'>
	  <pose>0 0 0.05 0 0 0</pose>
	  <geometry>
	    <mesh>
	      <uri>model://pioneer2dx/meshes/chassis.dae</uri>
	      <scale>0.9 0.5 0.5</scale>
	    </mesh>
	  </geometry>
	</visual>

Agora seu pequeno robô estará perfeitamente encaixado com a mesh do _pioneer2dx_. Vá novamente para o Gazebo e veja como está seu visual. Caso seja necessário, vá em "Add_Path" e selecione o caminho para o diretório onde salvou o arquivo `model.sdf`. Assim, poderá visualizá-lo no simulador.

<br/>

##_Criando Puglins_

<br/>

###Criando um Model Plugins
Os models plugins permitem um completo acesso às propriedades físicas dos models e seus elementos como links, joints, collision e objects.
<br/>
Vamos criar um plugin que irá aplicar uma velocidade linear para um model!
<br/>
Primeiramente, vamos criar um diretório e um arquivo .cc para o novo plugin:

	mkdir ~/gazebo_plugin_tutorial
	cd ~/gazebo_plugin_tutorial
	gedit model_push.cc

Com o editor de texto aberto, copie o seguinte código e cole no arquivo criado `model_push.cc`

	#include <functional>
	#include <gazebo/gazebo.hh>
	#include <gazebo/physics/physics.hh>
	#include <gazebo/common/common.hh>
	#include <ignition/math/Vector3.hh>
	
	namespace gazebo
	{
	  class ModelPush : public ModelPlugin
	  {
	    public: void Load(physics::ModelPtr _parent, sdf::ElementPtr /*_sdf*/)
	    {
	      // Store the pointer to the model
	      this->model = _parent;
	
	      // Listen to the update event. This event is broadcast every
	      // simulation iteration.
	      this->updateConnection = event::Events::ConnectWorldUpdateBegin(
	          std::bind(&ModelPush::OnUpdate, this));
	    }
	
	    // Called by the world update start event
	    public: void OnUpdate()
	    {
	      // Apply a small linear velocity to the model.
	      this->model->SetLinearVel(ignition::math::Vector3d(.3, 0, 0));
	    }
	
	    // Pointer to the model
	    private: physics::ModelPtr model;
	
	    // Pointer to the update event connection
	    private: event::ConnectionPtr updateConnection;
	  };
	
	  // Register this plugin with the simulator
	  GZ_REGISTER_MODEL_PLUGIN(ModelPush)
	}

###Compilando o Plugin
Para que possamos utilizar o plugin, é necessário que ele seja compilado. Para isso, precisamos criar e alterar o arquivo `CMakeLists.txt`

	gedit ~/gazebo_plugin_tutorial/CMakeLists.txt

Adicione as seguintes linhas:

	cmake_minimum_required(VERSION 2.8 FATAL_ERROR)
	
	find_package(gazebo REQUIRED)
	include_directories(${GAZEBO_INCLUDE_DIRS})
	link_directories(${GAZEBO_LIBRARY_DIRS})
	list(APPEND CMAKE_CXX_FLAGS "${GAZEBO_CXX_FLAGS}")
	
	add_library(model_push SHARED model_push.cc)
	target_link_libraries(model_push ${GAZEBO_LIBRARIES})
	
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${GAZEBO_CXX_FLAGS}")

Agora, vamos criar um diretório `build` e compilar nosso plugin.

	mkdir ~/gazebo_plugin_tutorial/build
	cd ~/gazebo_plugin_tutorial/build
	cmake ../
	make

###Executando o Plugin
O Model Plugin será utilizado dentro de um arquivo World. Para isso, vamos criar um.

	cd ~/gazebo_plugin_tutorial
	gedit model_push.world

Adicione as seguintes linhas no arquivo `model_push.world` criado.

	<?xml version="1.0"?> 
	<sdf version="1.4">
	  <world name="default">
	
	    <!-- Ground Plane -->
	    <include>
	      <uri>model://ground_plane</uri>
	    </include>
	
	    <include>
	      <uri>model://sun</uri>
	    </include>
	
	    <model name="box">
	      <pose>0 0 0.5 0 0 0</pose>
	      <link name="link">
	        <collision name="collision">
	          <geometry>
	            <box>
	              <size>1 1 1</size>
	            </box>
	          </geometry>
	        </collision>
	
	        <visual name="visual">
	          <geometry>
	            <box>
	              <size>1 1 1</size>
	            </box>
	          </geometry>
	        </visual>
	      </link>
	
	      <plugin name="model_push" filename="libmodel_push.so"/>
	    </model>        
	  </world>
	</sdf>

O gancho para o anexo do plugin ao model é especificado no fim "bloco de elemento model" `<plugin name="model_push" filename="libmodel_push.so"/>`
<br/>
Adicionamos agora, o caminho da biblioteca para o `GAZEBO_PLUGIN_PATH`

	$ export GAZEBO_PLUGIN_PATH=$HOME/gazebo_plugin_tutorial/build:$GAZEBO_PLUGIN_PATH

Para executarmos a simulação, digitamos:

	cd ~/gazebo_plugin_tutorial/
	gazebo model_push.world

###Criando um World Plugin
Pode ser útil controlar quais models a existirem em uma simulação em execução, e quando eles devem ser inseridos. Vamos aprender a inserir Models predefinidos ou customizados no Gazebo.
<br/>
No mesmo diretório criado anteriormente, vamos criar um novo arquivo fonte:

	cd ~/gazebo_plugin_tutorial
	gedit factory.cc

Com o editor de texto aberto, copie o seguinte código no arquivo `factory.cc` criado:

	#include <ignition/math/Pose3.hh>
	#include "gazebo/physics/physics.hh"
	#include "gazebo/common/common.hh"
	#include "gazebo/gazebo.hh"
	
	namespace gazebo
	{
	class Factory : public WorldPlugin
	{
	  public: void Load(physics::WorldPtr _parent, sdf::ElementPtr /*_sdf*/)
	  {
	    // Option 1: Insert model from file via function call.
	    // The filename must be in the GAZEBO_MODEL_PATH environment variable.
	    _parent->InsertModelFile("model://box");
	
	    // Option 2: Insert model from string via function call.
	    // Insert a sphere model from string
	    sdf::SDF sphereSDF;
	    sphereSDF.SetFromString(
	       "<sdf version ='1.4'>\
	          <model name ='sphere'>\
	            <pose>1 0 0 0 0 0</pose>\
	            <link name ='link'>\
	              <pose>0 0 .5 0 0 0</pose>\
	              <collision name ='collision'>\
	                <geometry>\
	                  <sphere><radius>0.5</radius></sphere>\
	                </geometry>\
	              </collision>\
	              <visual name ='visual'>\
	                <geometry>\
	                  <sphere><radius>0.5</radius></sphere>\
	                </geometry>\
	              </visual>\
	            </link>\
	          </model>\
	        </sdf>");
	    // Demonstrate using a custom model name.
	    sdf::ElementPtr model = sphereSDF.Root()->GetElement("model");
	    model->GetAttribute("name")->SetFromString("unique_sphere");
	    _parent->InsertModelSDF(sphereSDF);
	
	    // Option 3: Insert model from file via message passing.
	    {
	      // Create a new transport node
	      transport::NodePtr node(new transport::Node());
	
	      // Initialize the node with the world name
	      node->Init(_parent->Name());
	
	      // Create a publisher on the ~/factory topic
	      transport::PublisherPtr factoryPub =
	      node->Advertise<msgs::Factory>("~/factory");
	
	      // Create the message
	      msgs::Factory msg;
	
	      // Model file to load
	      msg.set_sdf_filename("model://cylinder");
	
	      // Pose to initialize the model to
	      msgs::Set(msg.mutable_pose(),
	          ignition::math::Pose3d(
	            ignition::math::Vector3d(1, -2, 0),
	            ignition::math::Quaterniond(0, 0, 0)));
	
	      // Send the message
	      factoryPub->Publish(msg);
	    }
	  }
	};
	
	// Register this plugin with the simulator
	GZ_REGISTER_WORLD_PLUGIN(Factory)
	}


###Compilando o Plugin
Para a compilação do Plugin, é necesário adicionar mais algumas linhas no `CMakeLists.txt`

	gedit ~/gazebo_plugin_tutorial/CMakeLists.txt
	
Adicione:

	add_library(factory SHARED factory.cc)
	target_link_libraries(factory
	  ${GAZEBO_LIBRARIES}
	)

Agora, vamos compilar o plugin:

	cd ~/gazebo_plugin_tutorial/build
	cmake ../
	make


###Criando Models para o mundo
Vamos criar um diretório para armazenamento dos Models

	mkdir ~/gazebo_plugin_tutorial/models
	cd ~/gazebo_plugin_tutorial/models
	mkdir box cylinder

Vamos criar em seguida um model em formato de caixa

	cd box
	gedit model.sdf

Copie e cole o seguinte código no arquivo `model.sdf` criado

	<?xml version='1.0'?>
	<sdf version ='1.6'>
	  <model name ='box'>
	    <pose>1 2 0 0 0 0</pose>
	    <link name ='link'>
	      <pose>0 0 .5 0 0 0</pose>
	      <collision name ='collision'>
	        <geometry>
	          <box><size>1 1 1</size></box>
	        </geometry>
	      </collision>
	      <visual name ='visual'>
	        <geometry>
	          <box><size>1 1 1</size></box>
	        </geometry>
	      </visual>
	    </link>
	  </model>
	</sdf>

Vamos criar agora um arquivo `model.config`

	gedit model.config

Copie e cole o seguinte código no arquivo criado:

	<?xml version='1.0'?>
	
	<model>
	  <name>box</name>
	  <version>1.0</version>
	  <sdf >model.sdf</sdf>
	
	  <author>
	    <name>me</name>
	    <email>somebody@somewhere.com</email>
	  </author>
	
	  <description>
	    A simple Box.
	  </description>
	</model>

Agora, vamos criar uma model em formato cilíndrico

	cd ~/gazebo_plugin_tutorial/models/cylinder
	gedit model.sdf

Copie e cole o seguinte código no arquivo `model.sdf` criado

	<?xml version='1.0'?>
	<sdf version ='1.6'>
	  <model name ='cylinder'>
	    <pose>1 2 0 0 0 0</pose>
	    <link name ='link'>
	      <pose>0 0 .5 0 0 0</pose>
	      <collision name ='collision'>
	        <geometry>
	          <cylinder><radius>0.5</radius><length>1</length></cylinder>
	        </geometry>
	      </collision>
	      <visual name='visual'>
	        <geometry>
	          <cylinder><radius>0.5</radius><length>1</length></cylinder>
	        </geometry>
	      </visual>
	    </link>
	  </model>
	</sdf>

Vamos criar agora o arquivo `model,config`

	gedit model.config

Copie e cole o seguinte código no arquivo criado:

	<?xml version='1.0'?>
	
	<model>
	  <name>cylinder</name>
	  <version>1.0</version>
	  <sdf>model.sdf</sdf>
	
	  <author>
	    <name>me</name>
	    <email>somebody@somewhere.com</email>
	  </author>
	
	  <description>
	    A simple cylinder.
	  </description>
	</model>


###Executando o Plugin
Tenha certeza de que o `GAZEBO_MODEL_PATH_` se refere ao novo diretório dos models criado

	export GAZEBO_MODEL_PATH=$HOME/gazebo_plugin_tutorial/models:$GAZEBO_MODEL_PATH

Adicione o caminho da biblioteca para o `GAZEBO_PLUGIN_PATH`

	export GAZEBO_PLUGIN_PATH=$HOME/gazebo_plugin_tutorial/build:$GAZEBO_PLUGIN_PATH

E crie um arquivo World SDF onde se encontrará o plugin

	cd ~/gazebo_plugin_tutorial
	gedit factory.world

Copie o seguinte código e cole no arquivo `factory.world` criado.

	<?xml version="1.0"?>
	<sdf version="1.4">
	  <world name="default">
	    <include>
	      <uri>model://ground_plane</uri>
	    </include>
	
	    <include>
	      <uri>model://sun</uri>
	    </include>
	
	    <plugin name="factory" filename="libfactory.so"/>
	  </world>
	</sdf>

Execute agora por fim, o Gazebo!

	gazebo ~/gazebo_plugin_tutorial/factory.world


<br/>

##_Integração Gazebo + ROS_

<br/>

###Entendendo a organização interna
É possível alcançar uma integração do ROS com o Gazebo. Uma série de packages do ROS chamadas de `gazebo_ros_package` permitem esta possibilidade. Eles providenciam as interfaces necessárias para simular um robô no Gazebo, utilizando por exemplo, ROS messages e services.

<br/>
Podemos acessar o Gazebo integrando-o ao ROS por meio de alguns comandos, sendo os principais deles os seguintes:

	rosrun gazebo_ros gazebo
	roslaunch gazebo_ros <arq.launch>

Mas antes de começarmos a utilizar as propriedades do ROS ao Gazebo, precisamos primeiro entender a hierarquia dos arquivos de models, para que posteriormente seja possível realizarmos premissas.
<br/>
Tudo o que diz a respeito ao model do seu robô, está localizado na package`/MYROBOT_description`. E todos os arquivos launch e worlds estão localizados na package `/MYROBOT_gazebo`. Substitui-se "MYROBOT" pelo nome do nosso robô em letras minúsculas. Abaixo há uma representação dessa hierarquia.

	../catkin_ws/src
	    /MYROBOT_description
	        package.xml
	        CMakeLists.txt
	        /urdf
	            MYROBOT.urdf
	        /meshes
	            mesh1.dae
	            mesh2.dae
	            ...
	        /materials
	        /cad
	    /MYROBOT_gazebo
	        /launch
	            MYROBOT.launch
	        /worlds
	            MYROBOT.world
	        /models
	            world_object1.dae
	            world_object2.stl
	            world_object3.urdf
	        /materials
	        /plugins


###Criando um mundo integrado ao ROS
Vamos então criar estes diretórios no nosso catkin workspace

	cd ~/catkin_ws/src
	catkin_create_pkg robos_description
	cd robos_description
	mkdir urdf meshes materials cad
	cd ..
	catkin_create_pkg robos_gazebo
	cd robos_gazebo
	mkdir launch worlds models materials plugins

E agora, vamos criar um mundo costumizado integrado ao ROS. Primeiro, vamos criar um arquivo launch que iniciará o mundo no Gazebo

	cd launch
	gedit robo.launch

Copie e cole o seguinte código no arquivo `robo.launch` criado

	<launch>
	  <!-- We resume the logic in empty_world.launch, changing only the name of the world to be launched -->
	  <include file="$(find gazebo_ros)/launch/empty_world.launch">
	    <arg name="world_name" value="$(find robo_gazebo)/worlds/robo.world"/>
	    <!-- more default parameters can be changed here -->
	  </include>
	</launch>

E agora, vamos criar um arquivo `.world`

	cd ..
	cd worlds
	gedit robo.world

Copie e cole o seguinte código no arquivo `robo.world` criado

	<?xml version="1.0" ?>
	<sdf version="1.4">
	  <world name="default">
	    <include>
	      <uri>model://ground_plane</uri>
	    </include>
	    <include>
	      <uri>model://sun</uri>
	    </include>
	    <include>
	      <uri>model://gas_station</uri>
	      <name>gas_station</name>
	      <pose>-2.0 7.0 0 0 0 0</pose>
	    </include>
	  </world>
	</sdf>
	
É possível agora executar o mundo criado.

	. ~/catkin_ws/devel/setup.bash
	roslaunch robos_gazebo robo.launch


###Usando o roslaunch para _spawnar_ robôs
Utilizando o roslaunch, conseguimos spawnar robôs em um mundo integrado. Entretanto, o ROS descreve os elementos de um robô pelo formato URDF, diferente daquele utilizado pelo Gazebo, o SDF. Portanto, é necessário utilizar um model `model.urdf` para poder usá-lo integrado ao ROS. Existe também a possibilidade da conversão do formato SDF para o URDF por meio da package pysdf criado por andreasBihlmaier. Segue-se os passos para a conversão:

	cd ~/catkin_ws/src
	git clone https://github.com/andreasBihlmaier/pysdf
	cd ..
	catkin_make
	source ~/catkin_ws/devel/setup.bash

Agora, vá para o diretório onde está o arquivo `model.sdf` e abra um terminal no local. E em seguida, execute

	rosrun pysdf sdf2urdf.py model.sdf model.urdf

Por fim, podemos _spawnar_ o robô agora convertido, mas o mova primeiramente para o diretório `urdf` em `robos_description`

	rosrun gazebo_ros spawn_model -file `rospack find robos_description`/urdf/robo.urdf -urdf -x 0 -y 0 -z 1 -model robo


###ROS Puglin para câmera de profundidade
Dessa vez, vamos aprender a usar plugins de sensores do ROS. No caso, vamos conectar a câmera de profundidade do Gazebo com o plugin do ROS. 
<br>
Primeiramente, vamos fazer o download do `model.sdf` do sensor kinect

	wget http://github.com/osrf/gazebo_tutorials/raw/master/ros_depth_camera/files/kinect.zip

Em seguida, abra o `model.sdf` da câmera e adicione os códigos do plugin seguintes. No caso, adicione dentro da tag `<sensor>` imediatamente após o fechamento da tag `</camera>`

	        <plugin name="camera_plugin" filename="libgazebo_ros_openni_kinect.so">
	          <baseline>0.2</baseline>
	          <alwaysOn>true</alwaysOn>
	          <!-- Keep this zero, update_rate in the parent <sensor> tag
	            will control the frame rate. -->
	          <updateRate>0.0</updateRate>
	          <cameraName>camera_ir</cameraName>
	          <imageTopicName>/camera/color/image_raw</imageTopicName>
	          <cameraInfoTopicName>/camera/color/camera_info</cameraInfoTopicName>
	          <depthImageTopicName>/camera/depth/image_raw</depthImageTopicName>
	          <depthImageCameraInfoTopicName>/camera/depth/camera_info</depthImageCameraInfoTopicName>
	          <pointCloudTopicName>/camera/depth/points</pointCloudTopicName>
	          <frameName>camera_link</frameName>
	          <pointCloudCutoff>0.5</pointCloudCutoff>
	          <pointCloudCutoffMax>3.0</pointCloudCutoffMax>
	          <distortionK1>0</distortionK1>
	          <distortionK2>0</distortionK2>
	          <distortionK3>0</distortionK3>
	          <distortionT1>0</distortionT1>
	          <distortionT2>0</distortionT2>
	          <CxPrime>0</CxPrime>
	          <Cx>0</Cx>
	          <Cy>0</Cy>
	          <focalLength>0</focalLength>
	          <hackBaseline>0</hackBaseline>
	        </plugin>

Agora, execute o Gazebo integrado ao ROS

	roslaunch gazebo_ros empty_world.launch

Com o Gazebo aberto, vá em "Add_Path" e adicione o caminho do diretório onde se encontra o `model.sdf` do seu sensor. Adicione também alguns objetos a frente da câmera, para que possam ser analisados.
<br/>
Por fim, vamos observar a análise da câmera utilizando o simulador RViz

	rosrun rviz rviz

Com o simulador aberto, vá em "Fixed Frame" e coloque `camera_link`. Adicione também um display "PointCloud2" e em Topic coloque `/camera/depth/points`

###Simulação de desvios de obstáculos
Vamos programar um robô que que evite obstáculos. Para isso, vamos utilizar o modelo criado por _saharshleo_. 
<br/>
Primeiramente, iremos realizar a criação dos arquivos necessários. Criaremos uma package específica para este sistema.

	cd ~/catkin_ws/src
	catkin_create_pkg desvio_de_obstaculos

Em seguida, no diretório src, criaremos dois _scripts_ para o desvio de obstáculos e a leitura do sensor.

	cd ~/desvio_de_obstaculos/src
	wget https://github.com/saharshleo/obstacleAvoidanceRobot/blob/master/catkin_ws/src/motion_plan/scripts/obstacle_avoidance.py
	wget https://github.com/saharshleo/obstacleAvoidanceRobot/blob/master/catkin_ws/src/motion_plan/scripts/reading_laser.py

Após feitas as instalações, é necessário alterar as linhas do `CMakeLists.txt`. Descomente, altere e/ou adicione as seguintes linhas:

	cmake_minimum_required(VERSION 3.0.2)
	project(desvio_de_obstaculos)

	find_package(catkin REQUIRED COMPONENTS
	  geometry_msgs
	  rospy
	  sensor_msgs
	  std_msgs
	)

	include_directories(
	# include
	 ${catkin_INCLUDE_DIRS}
	)

Vamos agora, fazer a compilação da nova package criada

	cd
	cd catkin_ws
	catkin_make desvio_de_obstaculos

Agora, vamos adicionar os arquivos de descrição dos models e world. Para isso, vamos para o diretório `robos_description` criado anteriormente.

	cd ~/robo_description/urdf
	wget https://github.com/saharshleo/obstacleAvoidanceRobot/blob/master/simulation_ws/src/robot_description/urdf/gazebo.xacro
	wget https://github.com/saharshleo/obstacleAvoidanceRobot/blob/master/simulation_ws/src/robot_description/urdf/macro.xacro
	wget https://github.com/saharshleo/obstacleAvoidanceRobot/blob/master/simulation_ws/src/robot_description/urdf/materials.xacro
	wget https://github.com/saharshleo/obstacleAvoidanceRobot/blob/master/simulation_ws/src/robot_description/urdf/robot.xacro

Abra o arquivo `robot.xacro`

	gedit robot.xacro

E edite as linhas 6,7 e 8 para que fiquem da seguinte maneira:

	<xacro:include filename="$(find robos_description)/urdf/materials.xacro" />  
	<xacro:include filename="$(find robos_description)/urdf/gazebo.xacro" />
	<xacro:include filename="$(find robos_description)/urdf/macro.xacro" />

Agora, vamos criar um arquivo launch para execução da simulação

	cd ~/catkin_ws/src/robos_gazebo/launch
	wget https://github.com/saharshleo/obstacleAvoidanceRobot/blob/master/simulation_ws/src/robot_description/launch/spawn.launch
	wget https://github.com/saharshleo/obstacleAvoidanceRobot/blob/master/simulation_ws/src/my_worlds/launch/world2.launch

Abra o arquivo `world2.launch`

	gedit world2.launch

E edite a linha 11 para que fique da seguinte maneira:

	<arg name="world_name" value="$(find robos_gazebo)/worlds/$(arg world).world"/>

Para finalizar o adicionamento, vamos criar o arquivo `.world`

	cd ..
	cd worlds
	wget https://github.com/saharshleo/obstacleAvoidanceRobot/blob/master/simulation_ws/src/my_worlds/worlds/world02.world

E agora podemos executar a simulação!

	roslaunch robos_gazebo world2.launch

Em um outro terminal, execute o seguinte comando, para spawnar o robô

	roslaunch robos_gazebo spawn.launch

E por fim, vamos executar o código de desvio de obstáculos

	rosrun desvio_de_obstaculos obstacle_avoidance.py


###Simulação utilizando PX4 e MAVROS
Outra forma de usarmos a integração do ROS ao Gazebo, é utilizando códigos open source como a PX4 e uma node de comunicação como o MAVROS. Vamos então, trabalhar com ambos.
<br/>
Primeiramente, precisamos fazer a instalação do Firmware da PX4 e a instalação do MAVROS

	sudo apt-get install ros-melodic-mavros ros-melodic-mavros-extras
	wget https://raw.githubusercontent.com/mavlink/mavros/master/mavros/scripts install_geographiclib_datasets.sh
	sudo bash ./install_geographiclib_datasets.sh
	rm ./install_geographiclib_datasets.sh
	cd
	mkdir ~/src
	cd ~src
	git clone https://github.com/Skyrats/Firmware.git --recursive

Agora, vamos criar uma ROS Node em Python que publica um setpoint para uma posição desejada no topic `mavros/setpoint_position/local`. Para isso, vamos criar uma nova package para o sistema.

	cd ~/catkin_ws/src
	catkin_create_pkg square std_msgs mavros_msgs roscpp rospy
	cd ~/square/src
	wget https://raw.githubusercontent.com/risckaust/risc-documentations/master/src/gazebo-rover/square.py
	chmod +x square.py

Abra o arquivo `square.py`

	gedit square.py

E edite a seguinte linha, conforme mostrado

	sp_pub = rospy.Publisher('mavros/setpoint_position/local', PoseStamped , queue_size=1)

Em seguida, podemos executar nossa simulação. Entretanto, precisamos inicializá-lo com o "ROS Wrappers"

	cd ~/src/Firmware
	DONT_RUN=1 make px4_sitl_default gazebo
	source ~/catkin_ws/devel/setup.bash  
	source Tools/setup_gazebo.bash $(pwd) $(pwd)/build/px4_sitl_default
	export ROS_PACKAGE_PATH=$ROS_PACKAGE_PATH:$(pwd)
	export ROS_PACKAGE_PATH=$ROS_PACKAGE_PATH:$(pwd)/Tools/sitl_gazebo
	roslaunch px4 mavros_posix_sitl.launch
	rosrun square square.py


	


